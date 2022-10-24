# Web Events

Web Events, also known as _Webhooks_, are an increasingly common pattern in Web applications. Establishing standards for using them helps [create more Web](https://www.pandastrike.com/posts/20151019-create-more-web/).

## Terminology

The term _Webhooks_ is confusing: it originally referred to a URL that encapsulated an HTTP request. However, there was no term for the client making the request. In attempt to clarify this, some people began using the qualifiers _incoming_ and _outgoing_. Unfortunately, the term _outgoing Webhook_ is semantically oxymoronic and thus confusing.

Our attempt to address this is to ditch the term Webhook altogether, since it’s already overloaded. Instead, we refer to the client (requestor) as generating a _Web event_. The server side—the thing that was originally called a Webhook—is a _Web event handler_, or, more simply, a Web handler.

### Translating From Webhooks to Events

| Incoming Webhook    | Outgoing Webhook |
| ------------------- | ---------------- |
| Web (Event) Handler | Web Event        |

> **Note** Another candidate for terminology would be publish-and-subscribe. The W3C even has [a draft recommendation](https://www.w3.org/TR/websub/) for publish-and-subcribe. There are some differences between that recommendation and the proposal here: further study is required to determine whether it might be worth using that terminology.

## Web Handlers (Incoming Webhook)

Web handlers are an alias to a description of an HTTP request, including any required credentials. If the authorization scheme is well-known, the Events Service may negotiate to obtain an ephemeral credential for performance reasons.

Handlers may be deleted by simply deleting the alias entry.

## Web Events

Web Events rely on response policies. Any resource method may support Events by adding a corresponding policy. For a given set of response conditions (success, cache hit, client error, server error, and so on), the policy would find matching Event specifications and call the corresponding URL. Since we can perform this asynchronously there are neglible performance implications.

Event policies may allow events to be generated from a variety of potential requests by making use of pinned bindings. Each such binding in a specification is applied—by removing any other bindings—in the context of the original request to generate an encoded request object. The encoded value is used to look up the target URLs for the event. For example, to generate events for all updates to any entry in a given collection, we would specify the database and collection bindings, effectively making the entry binding a wildcard.

## Transforms

We introduce transforms as part of the Events service, to map one data type to another. We could thus support basic workflows entirely within the Events service. We could start out with relative simple transforms, like renaming a field, and introduce more complex features, like mapping across an array, in time. We could eventually integrate Web Queries into the transformation, making it possible to enrich a resource using other resources, and then transform them.

## Authorization

Authorization for the Events service, as with other services, is done via Rune. We can include these Runes alongside those we forge upon authentication. When creating Web Handlers, two Runes may be necessary: one for the Events Service and one for the Handler. Of course, our Web app can handle this for you, and for most creators, this will be sufficient. However, for developers, this will be less convenient than most Webhook implementations, which are coupled to an underlying service, and thus require only one credential.

## Brokers

In theory, brokers could seamlessly and unintrusivel add Handlers, Events, and Transforms to any API. API providers would get Web Event support for free, creating myriad possibilities for interactions between services.

## Implications For No Code

The events service, particularly with addition of transforms, could form the basis for an integrative layer for workflows. We could provide a high-level specification, describing a workflow, that is transformed into a series of requests to the Events service (similar to the way CloudFormation maches an infrastructure description to the deployed infrastructure). We could build workflow presets on top of this for common patterns, like notifications.