---
id: l3i16ljz13qdvoorlk5r8q9
title: 17_LayeredSystem
desc: ''
updated: 1699966588355
created: 1699964777902
---
Rest architectural constraints.

Layered Architecture.

In this lecture you will learn about the layered system architecture, and by the end of this lecture,

you should be able to describe the benefits of layered architecture.

A typical three tier web application is an example of layered architecture.

There is a web tier, then there is an application server tier, and then there is the database tier.

Each of these layers have a unidirectional dependency on the layer next to it.

So for example, the web tier depends on the app tier, but the web tier is not aware of the database

tier.

Similarly, the app tier has a dependency on the database tier.

If we compare this architecture with the rest API architecture, we find many similarities.

Instead of the web tier.

We have some kind of a gateway between the rest client and the restful API server.

The Gateway is aware of only the restful API server and the rest API server is aware of only the database

layer.

Now let's say for a specific API, the load on the server has increased to a higher level and there

is a need to scale the API.

Now to do that you may add a load balancer which is a new layer between the gateway and the rest API

server and then increase the number of rest API servers to carry out horizontal scaling.

With this approach, we have added a new layer and we have updated an existing layer and we did all

of this without impacting the rest client because the rest client is dependent only on the gateway.

One important rule to keep in mind regarding the layered architecture is that a layer can only connect

with the layer that it is dependent on.

It cannot bypass its dependencies and reach out to other layers.

For example, the gateway cannot bypass the load balancer and reach out directly to an instance of the

rest API server.

Not technically this is feasible, but it is not the right practice as it will render the layered architecture

useless.

In this case, let's talk about the benefits of layered architecture.

The first one is that layering simplifies the architecture due to reduced dependencies.

The architecture may evolve with the changing needs, as you just saw, that you could add additional

layers and update the existing layers to meet the needs of your rest API.

The third one is that layer changes at most impacts only one other layer.

So if you are strictly following the layered architecture approach, it will be easier for you to manage

your architecture.

In this lecture you learned about the layered architecture constraint.

This constraint suggests that one should build the API using the layered architecture approach.

Each layer is dependent only on one layer and the dependency is unidirectional.

This approach simplifies the architecture and makes it easy to manage changes in the various layers.