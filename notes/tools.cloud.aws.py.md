---
id: 734pwgo5sevdpw8m3qrizwk
title: py
desc: ''
updated: 1749366300184
created: 1749321690005
---


https://github.com/michaelthomasletts/boto3-refresh-session/blob/main/boto3_refresh_session/session.py

```py
from warnings import warn

import boto3
from boto3.session import Session
from botocore.credentials import DeferredRefreshableCredentials, RefreshableCredentials

class AWSSession(Session):
    
    def __init__(
        self,
        sts_kwargs: dict[str, Any] | None = None,
        assume_role_kwargs: dict[str, Any],
        defer_refresh: bool = True,
        **session_kwargs,
    ):
        super().__init__(**kwargs)
        self.assume_role_kwargs = assume_role_kwargs
        self._init_sts_service(sts_kwargs=sts_kwargs)
        self._set_refresh_method(defer_refresh)
            
    
    def _init_sts_service(self, sts_kwargs: dict[str, Any] | None = None):
        if sts_kwargs: # If sts_kwargs is not None
            if "service_name" not in sts_kwargs:
                warn("'service_name' not needed in 'sts_kwargs' Removing...")
                del sts_kwargs["service_name"]
            self.sts_kwargs = sts_kwargs
            self.sts = boto3.client('sts', **sts_kwargs)
        else:
            self.sts = boto3.client('sts')
    
    def _set_refresh_method(self, defer_refresh:bool):
        # Override the '_credentials' attribute with the desired credential type based on refresh method
        if not defer_refresh:
            # Refresh as needed to keep credentials updated
            self._session._credentials = (
                # Metadata parameter expects a dict i.e {
                #    'access_key': ...,
                #    'secret_key': ...,
                #    'token': ...,
                #    'expiry_time': ...
                # }
                RefreshableCredentials.create_from_metadata(
                    refresh_using=self._get_credentials,
                    metadata=asdict(self._get_credentials()),
                    method="sts-assume-role"
                )
            )
        else:
            # Refresh only when about to expired or upon request
            self._session._credentials = DeferredRefreshableCredentials(
                method="sts-assume-role",
                refresh_using=self._get_credentials,
            )
    
    def _get_credentials(self) -> AwsTemporaryCredentials:
        try:
            response = self.sts.assume_role(**self.assume_role_kwargs)
            temp_creds = response["Credentials"]
            return AwsTemporaryCredentials(
                access_key_id=temp_creds.get("AccessKeyId"),
                secret_access_key=temp_creds("SecretAccessKey"),
                token=temp_creds("SessionToken"),
                expiry_time=temp_creds("Expiration"),
            )
        except Exception as exc:
            raise Exception(f"Error getting temporary credentials: {exc}") from exc

            
class AWSSecretsManager:
    service: str = "secretsmanager"
    
    def __init__(self, config_file_path: str | Path = SM_CONFIG_PATH) -> None:
        cfg: dict[str, Any] = get_config(APP_CURRENT_ENV, filepath=config_file_path)
        self.config = AwsSecretsManagerConfig(**cfg)
        self.session = AWSSession(
            sts_kwargs={
                "endpoint_url": self.config.sts_vpc_endpoint
            },
            assume_role_kwargs={
                "RoleArn": self.config.sts_iam_role,
                "RoleSessionName": "sm_session",
            },
            region_name=self.config.region
        )
        
        self.client = self._get_conn(config=self.config)
        
    def _get_conn(self, config):
        try:
            client = self.session.client(
                service_name=self.service,
                endpoint_url=config.sm_vpc_endpoint,
            )
            LOG.success(f"{self.__class__.__qualname__} successfully connected")
            return client
        except Exception as exc:
            raise ClientCantConnect(self.__class__) from exc
```