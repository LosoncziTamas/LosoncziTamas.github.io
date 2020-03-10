---
layout:     post
title:      Notes on API design
date:       2020-01-22
author:     Tamás Losonczi
summary:    Notes on API design
categories: API design notes
tags:
 - REST
 - HATEOAS
 - software design
---

Below you can find some notes on API design I took a while ago. Unfortunately, I couldn't find the original (pretty awesome) presentation on which these notes based.

## REST (REpresentational Stateless Transfer)
- REST is a way to provide communication between systems on the internet.
- An architectural style which has six main beneficial features:
  - _Scalability_: the ability to adapt/grow and to interact with a large number of services.
  - _Generality_: generic specification of data transfer, that is independent of technology/programming language.
  - _Independence_: allows different parts of the API to be independent.
  - _Latency_: caching
  - _Security_: allows you to support security for example via specific HTTP headers
  - _Encapsulation_: allows you to encapsulate the details


## HATEOAS (Hypermedia as the engine of the application state)
- It means that when you are designing an API, you want to pretend that the customer of the API knows nothing about the API (data format). It's all about representing REST and state in documents and linking those documents to other resources. It's a further restriction on REST architecture.
- It's not a standard, it's an architectural style and different people interpret it differently. But there are some conventions.

## Resources
- Resources are data representations, not behavior or action. Use nouns for defining them.
- If your resources are "coarse-grained" rather than "fine-grained" (generic rather then specific) you can cover a lot of use-cases.
- Don't tightly couple resources with behavior. (You don't want to use it like RPC). Keep it simple.
- Fundamentally there are two types of resources:
   - Collection resource (collection of documents) ex.: `/applications`
   - Instance resource (single items/docs) ex.: `/application/asdas2`
- And we can build robust APIs based on these concepts.

## Behavior
- HTTP already supports behavior via its methods (PUT, GET, POST, DELETE, HEAD). These methods can be used to implement CRUD. It doesn't have to be a one-to-one correspondence. POST and PUT can be both used to implement both update and create.
- You can use PUT to create if the client already knows the resource ID, containing all the data for the request.
- You can use PUT for an update for a full replacement of existing resources.
- PUT is an idempotent operation (has no additional effect if it is called more than once with the same input parameters). Put cannot be used for a partial update.
- POST for creating is almost always done on a parent resource. (To create a child resource) The response is important in this case (created status and location)
- POST as update mostly done on instance resources. You can use it for a partial update. This is the only method that is not idempotent. It can be used to reduce the amount of traffic. 

## Media types
- A very important concept, media types allow linking to other resources. 
- It's structure is _format specification + parsing rules_
- When you are writing REST client always set accept-header (a comma-separated list, in the order of preference), so the server knows how to return data. 
- The server should always set the content-type header.
- The nice thing about media types, that you can even create your own. (ex.: `application/foo+json` so you can apply your custom parsing)


## General design guidelines
- Instead of thinking in base URLs, you should be thinking about media types, collections, and instances.
- Make it short (base URL ex: `api.foo.com`), treat browsers as clients (for debugging purposes).
- There are two ways for versioning:
  - embedding the version in the URL (not very flexible)
  - using the version in the media types (it's very flexible but can be an overkill)
- Use _UTC_ and _ISO 8061_ (ex.: `2007-03-01T13:00:00Z/2008-05-11T15:30:00Z`) for representing dates.
- Using href for resources (so every exposed resource knows it's fully qualified location) can be very important for linking to other documents.
- In POST methods when it feasible, return the data representation in the response body. Add override (`?_body=false`) for control.
- Hypermedia and linking are fundamental for scalability. You can grow your system via linking operations.
- For example, you have a resource that would like to represent a folder (which is not a primitive), in this case, you could have a href property which is referencing that object. This can be also applied to collections (not just instance resources).
- You can specify query parameters for partial representation (to get back only the fields you need).
- Pagination can be implemented by offset and limit query parameters, but if you want to follow HATEOAS, you could embed these into the response with references. (link to the first, to the next, to the last).
- Many to many relationships can be also achieved by using links.
- Errors should be as descriptive as possible. There are a limited number of HTTP error codes for both server and client errors. Custom code property can be very useful for providing as much info as possible.
- Error 401 means unauthenticated, 403 means unauthorized.

## Security
- If you can avoid sessions and session IDs. Authenticate every request if necessary, keep them stateless.
- Do not authenticate based on URLs. Secure based on the content.
- Use existing security protocols.
- Use API keys instead of password/username pairs. They have much more entropy (not just 12 characters or something). Password resets can mess up things. Don't use SHA or MD5 for password decryption (collisions, too fast), use BCrypt for example.
- IDs should be unique globally, don't rely on incremental IDs (at large scale they are very hard to manage). UUIDs are okay.

## Caching
- E-tags can give you a big performance boost.

## Maintenance
- If you want to move a resource from one location to other you can use HTTP redirect.
- Create abstraction layers when you are migrating.
- Use well-defined media types.
 
## API design:
- When you are developing an API keep in mind your audience (developers). Try to be pragmatic rather then ideologic. It should be easy to adapt to. If it's easy to adapt it will scalable. 
- Use two base URLs per resource. 
  - `/dogs`
  - `/dogs/1234`
- Don't use verbs in base URL.
- Use HTTP verbs to operate on collections and elements.
- Don't mix singular and plural nouns for resources.
- You shouldn't have cases where a URL is deeper than: `/resource/identifier/resource`
- Example for (dog-owner) association: `/owners/5678/dogs`
- For complex associations use HTTP question mark: `GET /dogs?color=red&state=running&location=park`
- Be more specific with errors, return as much info as possible, use the correct status code.
- Never release an API without a version. Make the version mandatory. ex: `v1/dogs`
- Use partial response (by using optional fields in a comma-delimited list) and pagination (limit and offset) to reduce unnecessary data. ex: `/joe.smith/friends?fields=id,name,picture` or `/dogs?limit=25&offset=50`
- For responses that don't return resource, it's okay to use verbs (ex.: algorithms). `/convert?from=EUR&to=CNY&amount=100`
- Support multiple formats, and make them specifiable either in the Accept header (true REST) or as a type parameter (`?type=json`).
- There are basically two models for search:
  - Global search `/search?q=fluffy+fur`
  - Scoped search `/owners/5678/dogs?q=fluffy+fur`

---