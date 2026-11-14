# Guide to Writing REST APIs

REST is an architectural style, not an exact standard. Its author,
[Roy Fielding](https://en.wikipedia.org/wiki/Roy_Fielding), is unhappy with how everyone has implemented his idea in
practice. In his view, even the mere presence of Swagger UI is already a sign that you are not following REST. Why?
Because in his original idea, true REST is HATEOAS and nothing else. Several authors have tried to bridge this gap
between expectations and reality by introducing the concept of
[REST maturity levels](https://martinfowler.com/articles/richardsonMaturityModel.html).

We have accepted the position of the forces of evil: we do not implement HATEOAS, and instead rely on the code-first
FastAPI/Litestar approach. We write "handlers" and get Swagger UI for them.

Still, we want to follow the core principles of REST architecture, which we have formulated as follows. We recommend
that you follow these rules too:

- "Handler," "endpoint," and similar terms are our jargon for an API address. In other words, `/rest/example/books/1/`
  is a handler.

- The interaction format is JSON.

- The protocol is HTTP/2 or HTTP/3.

- Every address represents a resource or an entity. What is a "resource" or an "entity"? Fielding suggests treating any
  entity as a resource---not necessarily one stored in a database. It can also be the result of a computation, an
  abstract concept, and so on. We interpret this more narrowly: for us, it is usually some structure with a fixed set of
  fields. For example, you can think of it as a database table, a Pydantic model, or both. This is a rough description,
  but it is sufficient.

  - This leads to the rule that URLs should use nouns. If a verb is present, it is most likely not REST.
  - Verbs, i.e. actions, are represented by HTTP method names (`POST`, `PUT`, `PATCH`, `DELETE`, `GET`).
  - Usually (but not always), each entity has five simple actions. We call this CRUD:
    - `GET /rest/example/books/` --- a list of entities.
    - `GET /rest/example/books/1/` --- a single entity.
    - `POST /rest/example/books/` --- create an entity.
    - `PUT /rest/example/books/1/` --- update an entity.
    - `DELETE /rest/example/books/` --- delete an entity.
  - Actions may be omitted. In other words, having just one handler, `GET /rest/example/books/`, is perfectly fine.

- Versioning makes everything more complicated, so do not introduce it unless you have a clear understanding of why you
  need it. Often, guidelines impose it on us, or we simply want to follow the latest trend. But supporting different
  versions is difficult and burdensome, and testing them is even harder. Versioning is needed when you have an unknown
  or broad set of clients (a very common example is mobile apps). When building REST APIs within a team, versioning is
  usually unnecessary.

  Also, from a philosophical perspective, a version is not part of an entity. A book is just a book, not BookVersion1 or
  BookVersion2. Therefore, we most often version REST in the way that is most logical from the perspective of HTTP
  semantics: by placing the version in the `Accept` header. Headers themselves are request metadata, and a version is
  metadata too. The [fast-version package](https://github.com/community-of-python/fast-version) can help with this.

- Backward compatibility is important: adding new fields implies that they must not be required and must have default
  values.

- Headers with the `X-` prefix (`X-Request-Id`, `X-User-Id`, and so on) are prohibited. As far back as June 2012,
  [RFC 6648](https://www.rfc-editor.org/rfc/rfc6648) (BCP 178) deprecated this prefix for new parameters in all
  application protocols, including HTTP. The reason is simple: a non-standard header may eventually become standard, but
  its prefix cannot then be removed, and you end up having to support both names. Name your headers without a prefix,
  for example, `Request-Id`. If a header has already been standardized, use its standard name.

- URL construction conventions:

  - The `/api/` prefix is not very clear and does not leave much room for maneuver, so we use the following
    prefixes/layouts:
    - `/rest/[service-name]/[entity-name]/` for REST.
    - `/rpc/[service-name]/[verb-based-action]/` for things that do not fit into REST. We call these URLs RPC (remote
      procedure call); their names usually contain verbs. For example: `/rpc/users/reorder-templates-tree/`.
    - Nesting is prohibited. Do not do this: `/rest/[service-name]/[something]/[something]` or
      `/rpc/[service-name]/[something]/[something]/`. The URL structure is:
      `/[protocol-type]/[service-name]/[something]/`.
  - We try to include trailing slashes.
  - Hyphens are used as separators in all names because they are less likely to be confused with, for example, Python
    entities. We rely on underscores in many places, so the more clearly your names differ, the easier it is to
    distinguish query parameters and URLs from other strings. Also, you will not be able to create variables with the
    same name, since they will always differ by one character.
  - We try to name entities using either the singular or plural form. I prefer the plural, but you can choose whichever
    you like. However, remember that the choice must be consistent across the team. Nothing is worse than seeing
    `/rest/some-service/books/` and `/rest/some-service/user/` and having to wonder why one is plural and the other is
    singular.

- Despite some recommendations to the contrary, nested REST does not seem convenient, maintainable, clear, or simple.
  For example, the URL `/rest/service/books/123/readers/12/books/2/` is confusing: most importantly, it is unclear which
  entity we are referring to, and figuring out how to construct such URLs---and, above all, what should be nested inside
  what---is an extremely tangled subject. What if we also introduce `/rest/service/readers/12/books/2/`? How would it
  differ from the URL above? Some people will say, "That's obvious," but they are in the minority; everyone else will
  stop and think. Therefore, I consider this an architectural smell.

  Instead of nested REST, you can use query parameters to further filter the results. For example:
  `GET /rest/example/books/?user-id=5`. This is a fairly semantic approach, and it lets you build quite complex filters
  without worrying about what comes before what.

- Prefix names:

  - `/rest/*` --- the basic REST API (admin panel, frontend, or just an API).
  - `/rpc/*` --- the basic RPC API for the same consumers.
  - `/rest-internal/*`, `/rpc-internal/*` --- internal handlers used between microservices.
  - `/rest-public/*`, `/rpc-public/*` --- external handlers for consumers (these can be specialized into
    `rest-public-webm` and `rest-public-mobile`).
  - `/rest-admin/*`, `/rpc-admin/*` --- the admin panel (if your application is not itself an admin panel, it makes
    sense to create these namespaces).
