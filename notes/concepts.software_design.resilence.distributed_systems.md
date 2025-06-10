---
id: gxphbvfyhonophh5mjvg93i
title: distributed_systems
desc: ''
updated: 1746352026223
created: 1746351886642
---
## [Building Resilient Distributed Systems](https://dev.to/aaravjoshi/python-power-7-battle-tested-techniques-for-building-resilient-distributed-systems-5dbp)
### Circuit Breakers

- Circuit breakers protect systems from cascading failures by monitoring the health of dependent services.
- When a service starts failing, the circuit breaker temporarily stops sending requests, preventing system-wide degradation.

In my experience implementing payment processing systems, circuit breakers proved invaluable when third-party services experienced downtime. By quickly detecting problems and failing fast, we prevented customer-facing issues.

``` py
import time
from functools import wraps

class CircuitBreaker:
    def __init__(self, failure_threshold=5, recovery_timeout=30, 
                 exceptions=(Exception,)):
        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout
        self.exceptions = exceptions
        self.state = "CLOSED"
        self.failure_count = 0
        self.last_failure_time = None

    def __call__(self, func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            if self.state == "OPEN":
                if time.time() - self.last_failure_time > self.recovery_timeout:
                    self.state = "HALF-OPEN"
                else:
                    raise RuntimeError(f"Service unavailable - circuit breaker open")

            try:
                result = func(*args, **kwargs)

                # Reset on successful HALF-OPEN call
                if self.state == "HALF-OPEN":
                    self.failure_count = 0
                    self.state = "CLOSED"

                return result

            except self.exceptions as e:
                self.failure_count += 1
                self.last_failure_time = time.time()

                if self.failure_count >= self.failure_threshold:
                    self.state = "OPEN"

                raise e

        return wrapper

# Usage example
@CircuitBreaker(failure_threshold=3, recovery_timeout=60)
def call_external_api(endpoint):
    # Make API call that might fail
    response = requests.get(endpoint)
    response.raise_for_status()
    return response.json()
```

- This implementation includes three states:
  - CLOSED (normal operation),
  - OPEN (failing fast), and
  - HALF-OPEN (testing if the service has recovered).
- The decorator can be applied to any function that might fail.

### Distributed Locks

- When multiple processes or servers need to access shared resources, distributed locks prevent race conditions and data corruption.
- They ensure that only one entity can execute critical sections of code at a time.

I once worked on a billing system where we needed to ensure that only one worker processed specific account updates. Using Redis-based distributed locks prevented duplicate charges and maintained data integrity.

``` PY
import redis
import time
import uuid

class DistributedLock:
    def __init__(self, redis_client, lock_name, expire_seconds=10):
        self.redis = redis_client
        self.lock_name = lock_name
        self.expire_seconds = expire_seconds
        self.identifier = str(uuid.uuid4())

    def __enter__(self):
        self.acquired = self.acquire()
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.acquired:
            self.release()

    def acquire(self):
        # Use SET NX to acquire the lock atomically
        acquired = self.redis.set(
            self.lock_name, 
            self.identifier, 
            nx=True, 
            ex=self.expire_seconds
        )

        # Start lock refresh thread if acquired
        if acquired:
            self._start_refresh_thread()

        return acquired

    def release(self):
        # Release the lock only if we own it (using Lua script for atomicity)
        script = """
        if redis.call('get', KEYS[1]) == ARGV[1] then
            return redis.call('del', KEYS[1])
        else
            return 0
        end
        """
        self.redis.eval(script, 1, self.lock_name, self.identifier)

        if hasattr(self, 'refresh_thread'):
            self.refresh_thread_stop = True

    def _start_refresh_thread(self):
        import threading

        self.refresh_thread_stop = False

        def refresh_lock():
            while not self.refresh_thread_stop:
                # Refresh lock if we still own it
                script = """
                if redis.call('get', KEYS[1]) == ARGV[1] then
                    return redis.call('expire', KEYS[1], ARGV[2])
                else
                    return 0
                end
                """
                self.redis.eval(
                    script, 
                    1, 
                    self.lock_name, 
                    self.identifier, 
                    self.expire_seconds
                )
                time.sleep(self.expire_seconds / 3)

        self.refresh_thread = threading.Thread(target=refresh_lock)
        self.refresh_thread.daemon = True
        self.refresh_thread.start()

# Usage example

redis_client = redis.Redis(host='localhost', port=6379, db=0)

def process_account(account_id):
    lock_name = f"account_lock:{account_id}"

    with DistributedLock(redis_client, lock_name, expire_seconds=30) as lock:
        if not lock.acquired:
            print(f"Account {account_id} is being processed by another worker")
            return

        # Critical section - only one process will execute this at a time
        print(f"Processing account {account_id}")
        time.sleep(10)  # Simulate work
        print(f"Finished processing account {account_id}")
```

- This implementation uses Redis for lock management and includes automatic lock renewal through a background thread, preventing lock expiration while the operation is still running.

### Reliable Message Queues with Dead-Letter Handling

- Message queues enable asynchronous communication between distributed components.
- By implementing proper retry mechanisms and dead-letter queues, systems can recover from temporary failures and ensure message delivery.

While developing an order fulfillment system, I implemented a message queue architecture that gracefully handled downstream service failures. Orders that couldn't be processed immediately were retried automatically, and persistently failing orders were routed to a dead-letter queue for manual review.

- This implementation uses RabbitMQ to provide reliable message delivery with retry logic and dead-letter queuing. 
- It handles temporary failures gracefully while ensuring problematic messages don't block the entire system.
    ``` py
    import pika
    import json
    import time
    import logging

    class MessageProcessor:
        def __init__(self, amqp_url, queue_name, dead_letter_queue,
                    max_retries=3, retry_delay=5):
            self.amqp_url = amqp_url
            self.queue_name = queue_name
            self.dead_letter_queue = dead_letter_queue
            self.max_retries = max_retries
            self.retry_delay = retry_delay
            self.logger = logging.getLogger(__name__)

        def setup_queues(self):
            connection = pika.BlockingConnection(
                pika.URLParameters(self.amqp_url)
            )
            channel = connection.channel()

            # Declare the dead-letter queue
            channel.queue_declare(queue=self.dead_letter_queue, durable=True)

            # Declare the main queue with dead-letter exchange
            channel.queue_declare(
                queue=self.queue_name,
                durable=True,
                arguments={
                    'x-dead-letter-exchange': '',
                    'x-dead-letter-routing-key': self.dead_letter_queue
                }
            )

            connection.close()

        def publish_message(self, message):
            connection = pika.BlockingConnection(
                pika.URLParameters(self.amqp_url)
            )
            channel = connection.channel()

            channel.basic_publish(
                exchange='',
                routing_key=self.queue_name,
                body=json.dumps(message),
                properties=pika.BasicProperties(
                    delivery_mode=2,  # make message persistent
                    headers={'retry_count': 0}
                )
            )

            connection.close()

        def process_messages(self, callback):
            connection = pika.BlockingConnection(
                pika.URLParameters(self.amqp_url)
            )
            channel = connection.channel()

            def wrapped_callback(ch, method, properties, body):
                try:
                    # Extract retry count
                    headers = properties.headers or {}
                    retry_count = headers.get('retry_count', 0)

                    # Process the message
                    message = json.loads(body)
                    self.logger.info(f"Processing message: {message}")
                    callback(message)

                    # Acknowledge successful processing
                    ch.basic_ack(delivery_tag=method.delivery_tag)

                except Exception as e:
                    self.logger.error(f"Error processing message: {e}")

                    # Increment retry count
                    retry_count = retry_count + 1

                    if retry_count <= self.max_retries:
                        # Reject message for requeue with updated retry count
                        time.sleep(self.retry_delay)

                        # Republish with incremented retry count
                        ch.basic_ack(delivery_tag=method.delivery_tag)
                        channel.basic_publish(
                            exchange='',
                            routing_key=self.queue_name,
                            body=body,
                            properties=pika.BasicProperties(
                                delivery_mode=2,
                                headers={'retry_count': retry_count}
                            )
                        )
                    else:
                        # Max retries reached, send to dead-letter queue
                        self.logger.warning(
                            f"Max retries reached, sending to dead-letter queue"
                        )
                        ch.basic_ack(delivery_tag=method.delivery_tag)

                        # Add failure reason to the message
                        message = json.loads(body)
                        message['_failure_reason'] = str(e)

                        channel.basic_publish(
                            exchange='',
                            routing_key=self.dead_letter_queue,
                            body=json.dumps(message),
                            properties=pika.BasicProperties(delivery_mode=2)
                        )

            # Consume messages one at a time
            channel.basic_qos(prefetch_count=1)
            channel.basic_consume(
                queue=self.queue_name,
                on_message_callback=wrapped_callback
            )

            self.logger.info(f"Waiting for messages on {self.queue_name}")
            channel.start_consuming()

        def process_dead_letters(self, callback):
            connection = pika.BlockingConnection(
                pika.URLParameters(self.amqp_url)
            )
            channel = connection.channel()

            def wrapped_callback(ch, method, properties, body):
                try:
                    message = json.loads(body)
                    self.logger.info(f"Processing dead letter: {message}")
                    callback(message)
                    ch.basic_ack(delivery_tag=method.delivery_tag)
                except Exception as e:
                    self.logger.error(f"Error processing dead letter: {e}")
                    # Nack without requeue to keep in dead-letter queue
                    ch.basic_nack(
                        delivery_tag=method.delivery_tag,
                        requeue=False
                    )

            channel.basic_qos(prefetch_count=1)
            channel.basic_consume(
                queue=self.dead_letter_queue,
                on_message_callback=wrapped_callback
            )

            self.logger.info(f"Waiting for messages on {self.dead_letter_queue}")
            channel.start_consuming()
    ```


### Consensus Algorithms
- Consensus algorithms enable distributed systems to agree on shared state even when nodes fail or network partitions occur. 
- Python implementations of algorithms like Raft provide a foundation for building highly available distributed systems.

I implemented a distributed configuration service using Raft that allowed our microservices to maintain consistent configuration even during infrastructure outages. This ensured all services operated with the same parameters despite network partitions.
- This Raft implementation demonstrates core concepts like leader election, log replication, and term-based consensus. 
- In a production system, you'd need to add persistence and integrate with your application's state machine.
    ``` py
    import threading
    import time
    import random
    import requests
    from enum import Enum

    class NodeState(Enum):
        FOLLOWER = 1
        CANDIDATE = 2
        LEADER = 3

    class RaftNode:
        def __init__(self, node_id, cluster_nodes, election_timeout_range=(150, 300)):
            self.node_id = node_id
            self.cluster_nodes = cluster_nodes
            self.election_timeout_range = election_timeout_range

            # Persistent state
            self.current_term = 0
            self.voted_for = None
            self.log = []

            # Volatile state
            self.state = NodeState.FOLLOWER
            self.commit_index = 0
            self.last_applied = 0

            # Leader state
            self.next_index = {node: 1 for node in cluster_nodes if node != node_id}
            self.match_index = {node: 0 for node in cluster_nodes if node != node_id}

            # Initialize timers
            self.election_timer = None
            self.heartbeat_timer = None
            self.reset_election_timer()

            # Start the main thread
            self.running = True
            threading.Thread(target=self.apply_entries_thread).start()

        def reset_election_timer(self):
            if self.election_timer:
                self.election_timer.cancel()

            timeout = random.randint(
                self.election_timeout_range[0],
                self.election_timeout_range[1]
            )
            self.election_timer = threading.Timer(
                timeout / 1000.0, 
                self.start_election
            )
            self.election_timer.daemon = True
            self.election_timer.start()

        def start_heartbeat(self):
            if self.heartbeat_timer:
                self.heartbeat_timer.cancel()

            # Send heartbeats immediately
            self.send_append_entries()

            # Schedule next heartbeat
            self.heartbeat_timer = threading.Timer(
                50 / 1000.0,  # 50ms
                self.start_heartbeat
            )
            self.heartbeat_timer.daemon = True
            self.heartbeat_timer.start()

        def start_election(self):
            if not self.running:
                return

            self.state = NodeState.CANDIDATE
            self.current_term += 1
            self.voted_for = self.node_id
            votes_received = 1  # Vote for self

            print(f"Node {self.node_id} starting election for term {self.current_term}")

            # Reset election timer
            self.reset_election_timer()

            # Request votes from all other nodes
            for node in self.cluster_nodes:
                if node == self.node_id:
                    continue

                try:
                    last_log_index = len(self.log)
                    last_log_term = self.log[-1]['term'] if self.log else 0

                    response = requests.post(
                        f"http://{node}/request_vote",
                        json={
                            'term': self.current_term,
                            'candidate_id': self.node_id,
                            'last_log_index': last_log_index,
                            'last_log_term': last_log_term
                        },
                        timeout=0.1
                    )

                    result = response.json()

                    # If response contains higher term, revert to follower
                    if result['term'] > self.current_term:
                        self.current_term = result['term']
                        self.state = NodeState.FOLLOWER
                        self.voted_for = None
                        return

                    if result['vote_granted']:
                        votes_received += 1

                        # Check if majority achieved
                        if votes_received > len(self.cluster_nodes) / 2:
                            self.become_leader()
                            return

                except Exception as e:
                    print(f"Error requesting vote from {node}: {e}")

        def become_leader(self):
            if not self.running or self.state == NodeState.LEADER:
                return

            print(f"Node {self.node_id} becoming leader for term {self.current_term}")
            self.state = NodeState.LEADER

            # Initialize leader state
            self.next_index = {
                node: len(self.log) + 1 
                for node in self.cluster_nodes if node != self.node_id
            }
            self.match_index = {
                node: 0 
                for node in self.cluster_nodes if node != self.node_id
            }

            # Cancel election timer
            if self.election_timer:
                self.election_timer.cancel()

            # Start sending heartbeats
            self.start_heartbeat()

        def send_append_entries(self):
            if not self.running or self.state != NodeState.LEADER:
                return

            for node in self.cluster_nodes:
                if node == self.node_id:
                    continue

                try:
                    next_idx = self.next_index[node]
                    prev_log_index = next_idx - 1
                    prev_log_term = (
                        self.log[prev_log_index - 1]['term'] 
                        if prev_log_index > 0 and self.log 
                        else 0
                    )

                    # Get entries to send
                    entries = self.log[prev_log_index:] if prev_log_index < len(self.log) else []

                    response = requests.post(
                        f"http://{node}/append_entries",
                        json={
                            'term': self.current_term,
                            'leader_id': self.node_id,
                            'prev_log_index': prev_log_index,
                            'prev_log_term': prev_log_term,
                            'entries': entries,
                            'leader_commit': self.commit_index
                        },
                        timeout=0.1
                    )

                    result = response.json()

                    # If response contains higher term, revert to follower
                    if result['term'] > self.current_term:
                        self.current_term = result['term']
                        self.state = NodeState.FOLLOWER
                        self.voted_for = None

                        # Cancel heartbeat timer
                        if self.heartbeat_timer:
                            self.heartbeat_timer.cancel()

                        # Reset election timer
                        self.reset_election_timer()
                        return

                    if result['success']:
                        # Update indices
                        if entries:
                            self.match_index[node] = prev_log_index + len(entries)
                            self.next_index[node] = self.match_index[node] + 1

                        # Update commit index if needed
                        self.update_commit_index()
                    else:
                        # Decrement next_index and retry
                        self.next_index[node] = max(1, self.next_index[node] - 1)

                except Exception as e:
                    print(f"Error sending append entries to {node}: {e}")

        def update_commit_index(self):
            if self.state != NodeState.LEADER:
                return

            # Find the highest index that has been replicated to a majority of nodes
            for n in range(self.commit_index + 1, len(self.log) + 1):
                # Count nodes that have this entry
                count = 1  # Leader has it

                for node in self.match_index:
                    if self.match_index[node] >= n:
                        count += 1

                # Check if majority reached and entry is from current term
                if count > len(self.cluster_nodes) / 2 and (
                        n > 0 and self.log[n - 1]['term'] == self.current_term):
                    self.commit_index = n

        def append_log(self, data):
            if self.state != NodeState.LEADER:
                return False, "Not the leader"

            # Append to local log
            entry = {
                'term': self.current_term,
                'data': data
            }
            self.log.append(entry)

            # Update leader's match index for itself
            self.match_index[self.node_id] = len(self.log)

            # Try to replicate immediately
            self.send_append_entries()

            return True, "Log entry added"

        def apply_entries_thread(self):
            while self.running:
                # Apply committed entries that haven't been applied yet
                while self.last_applied < self.commit_index:
                    self.last_applied += 1
                    entry = self.log[self.last_applied - 1]

                    # Apply the command (in a real system, this would modify state)
                    print(f"Node {self.node_id} applying: {entry['data']}")

                time.sleep(0.01)  # Small sleep to prevent CPU hogging

        def request_vote_handler(self, term, candidate_id, last_log_index, last_log_term):
            if term < self.current_term:
                return {
                    'term': self.current_term,
                    'vote_granted': False
                }

            if term > self.current_term:
                self.current_term = term
                self.state = NodeState.FOLLOWER
                self.voted_for = None

            # Check if log is at least as up-to-date as ours
            our_last_index = len(self.log)
            our_last_term = self.log[-1]['term'] if self.log else 0

            log_ok = (
                last_log_term > our_last_term or 
                (last_log_term == our_last_term and last_log_index >= our_last_index)
            )

            if (self.voted_for is None or self.voted_for == candidate_id) and log_ok:
                # Grant vote
                self.voted_for = candidate_id
                self.reset_election_timer()

                return {
                    'term': self.current_term,
                    'vote_granted': True
                }
            else:
                return {
                    'term': self.current_term,
                    'vote_granted': False
                }

        def append_entries_handler(self, term, leader_id, prev_log_index, 
                                prev_log_term, entries, leader_commit):
            # Reply false if term < currentTerm
            if term < self.current_term:
                return {
                    'term': self.current_term,
                    'success': False
                }

            # If term is greater, update current term and convert to follower
            if term > self.current_term:
                self.current_term = term
                self.state = NodeState.FOLLOWER
                self.voted_for = None

            # Reset election timer since we've heard from the leader
            self.reset_election_timer()

            # If candidate or leader, step down
            if self.state != NodeState.FOLLOWER:
                self.state = NodeState.FOLLOWER

                if self.heartbeat_timer:
                    self.heartbeat_timer.cancel()

            # Check if our log has the prev_log_entry with matching term
            if prev_log_index > 0:
                if prev_log_index > len(self.log) or (
                        prev_log_index > 0 and 
                        self.log[prev_log_index - 1]['term'] != prev_log_term):
                    return {
                        'term': self.current_term,
                        'success': False
                    }

            # Process entries
            if entries:
                # Find conflicts: if an existing entry conflicts with a new one, 
                # delete it and all that follow
                for i, entry in enumerate(entries):
                    log_index = prev_log_index + i + 1

                    # If we have an entry at this index but terms don't match
                    if log_index <= len(self.log) and self.log[log_index - 1]['term'] != entry['term']:
                        # Delete this and all subsequent entries
                        self.log = self.log[:log_index - 1]
                        break

                # Append any new entries not already in the log
                for i, entry in enumerate(entries):
                    log_index = prev_log_index + i + 1

                    if log_index > len(self.log):
                        self.log.append(entry)

            # Update commit index
            if leader_commit > self.commit_index:
                self.commit_index = min(leader_commit, len(self.log))

            return {
                'term': self.current_term,
                'success': True
            }

        def shutdown(self):
            self.running = False

            if self.election_timer:
                self.election_timer.cancel()

            if self.heartbeat_timer:
                self.heartbeat_timer.cancel()
    ```


### Event Sourcing
- Event sourcing captures all state changes as a sequence of immutable events. 
- This pattern allows systems to rebuild state from event logs, enabling accurate recovery and providing a complete history of changes.
- Example:
    - In a financial application I developed, event sourcing allowed us to track every transaction and maintain a complete audit trail. 
    - When a database corruption occurred, we were able to rebuild the system's state from stored events with minimal disruption.

    ```py
    import uuid
    import json
    import datetime
    from collections import defaultdict

    class EventStore:
        def __init__(self, persistence_adapter=None):
            self.persistence_adapter = persistence_adapter
            self.events = defaultdict(list)
            self.event_handlers = defaultdict(list)

        def save_event(self, aggregate_id, event_type, event_data):
            """Save an event to the store"""
            event = {
                'id': str(uuid.uuid4()),
                'timestamp': datetime.datetime.now().isoformat(),
                'aggregate_id': aggregate_id,
                'type': event_type,
                'data': event_data,
                'sequence': len(self.events[aggregate_id])
            }

            # Add to in-memory store
            self.events[aggregate_id].append(event)

            # Persist if adapter available
            if self.persistence_adapter:
                self.persistence_adapter.save_event(event)

            # Trigger event handlers
            for handler in self.event_handlers[event_type]:
                handler(event)

            return event

        def get_events(self, aggregate_id):
            """Get all events for an aggregate"""
            if self.persistence_adapter:
                # Load from persistence if adapter available
                return self.persistence_adapter.get_events(aggregate_id)

            return self.events[aggregate_id]

        def register_handler(self, event_type, handler):
            """Register a handler for an event type"""
            self.event_handlers[event_type].append(handler)

    class Aggregate:
        def __init__(self, aggregate_id, event_store):
            self.id = aggregate_id
            self.event_store = event_store
            self.version = -1

            # Apply existing events to rebuild state
            self.replay_events()

        def replay_events(self):
            """Replay all events to rebuild aggregate state"""
            events = self.event_store.get_events(self.id)

            for event in events:
                self.apply_event(event, replay=True)
                self.version = event['sequence']

        def apply_event(self, event, replay=False):
            """Apply an event to the aggregate"""
            event_type = event['type']
            handler_method = f"apply_{event_type}"

            if hasattr(self, handler_method):
                getattr(self, handler_method)(event['data'], replay)

        def create_event(self, event_type, event_data):
            """Create and save a new event"""
            event = self.event_store.save_event(
                self.id, 
                event_type, 
                event_data
            )

            # Apply the event to update current state
            self.apply_event(event)
            self.version = event['sequence']

    # Example: SQLite persistence adapter

    class SQLiteEventStore:
        def __init__(self, db_path):
            import sqlite3
            self.conn = sqlite3.connect(db_path)
            self.create_tables()

        def create_tables(self):
            cursor = self.conn.cursor()
            cursor.execute('''
            CREATE TABLE IF NOT EXISTS events (
                id TEXT PRIMARY KEY,
                timestamp TEXT,
                aggregate_id TEXT,
                type TEXT,
                data TEXT,
                sequence INTEGER,
                UNIQUE(aggregate_id, sequence)
            )
            ''')
            self.conn.commit()

        def save_event(self, event):
            cursor = self.conn.cursor()
            cursor.execute(
                '''
                INSERT INTO events (id, timestamp, aggregate_id, type, data, sequence)
                VALUES (?, ?, ?, ?, ?, ?)
                ''',
                (
                    event['id'],
                    event['timestamp'],
                    event['aggregate_id'],
                    event['type'],
                    json.dumps(event['data']),
                    event['sequence']
                )
            )
            self.conn.commit()

        def get_events(self, aggregate_id):
            cursor = self.conn.cursor()
            cursor.execute(
                '''
                SELECT id, timestamp, aggregate_id, type, data, sequence
                FROM events
                WHERE aggregate_id = ?
                ORDER BY sequence
                ''',
                (aggregate_id,)
            )

            events = []
            for row in cursor.fetchall():
                events.append({
                    'id': row[0],
                    'timestamp': row[1],
                    'aggregate_id': row[2],
                    'type': row[3],
                    'data': json.loads(row[4]),
                    'sequence': row[5]
                })

            return events

    # Example: Bank Account Aggregate

    class BankAccount(Aggregate):
        def __init__(self, account_id, event_store):
            self.balance = 0
            self.status = "new"
            super().__init__(account_id, event_store)

        def open_account(self, customer_id, initial_deposit):
            if self.status != "new":
                raise ValueError("Account already opened")

            if initial_deposit < 100:
                raise ValueError("Initial deposit must be at least $100")

            self.create_event("account_opened", {
                "customer_id": customer_id,
                "initial_deposit":
    ```
