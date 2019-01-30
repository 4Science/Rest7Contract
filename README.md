# Rest7Contract

Repository to discuss the new REST API contract for DSpace 7. The code to implement this contract is being developed on the DSpace [`master` branch](https://github.com/DSpace/DSpace/tree/master/dspace-spring-rest)

***
:warning: **As the DSpace 7 REST API is under active development, this REST Contract is expected to change rapidly.** REST Contract contributors will be rapidly adding ideas (via Pull Requests) to this repository in order to enable the detailed discussions necessary to finalize the REST Contract. Once the REST Contract stabilizes, we will remove this warning and more actively publicize it.

**If you would like to get involved in our DSpace 7 development effort, we welcome new contributors.**
  * DSpace 7 Meetings and subteams can be found on the [DSpace 7 UI Working Group](https://wiki.duraspace.org/display/DSPACE/DSpace+7+UI+Working+Group) wiki page.
  * DSpace 7 REST API development work (corresponding to this contract) is occurring on the DSpace [`master` branch](https://github.com/DSpace/DSpace/tree/master/dspace-spring-rest)
  * DSpace 7 Angular UI work is occurring at https://github.com/DSpace/dspace-angular
***

## Community resources

* [REST Code Branch](https://github.com/DSpace/DSpace/tree/master/dspace-spring-rest)
* [REST Coding Tips](https://wiki.duraspace.org/display/DSPACE/DSpace+7+REST%3A+Coding+DSpace+Objects)
* [Community Slack Channel](https://dspace-org.slack.com/messages/C3T5FTLNP)
* [Historical Documentation Behind this Project](https://wiki.duraspace.org/display/DSPACE/DSpace+7+UI+Working+Group)
* [DSpace 7 UI and REST Working Group Meeting Notes](https://wiki.duraspace.org/display/DSPACE/DSpace+7+UI+Working+Group)

## Detailed Documentation pages
* [Endpoints](endpoints.md)
* [Endpoint Search Options and Relationships](search-rels.md)
* [Endpoint Projections](projections.md)

## Use of the HTTP Verbs and HTTP Response CODE

_Please note that within this section, all terms used are meant to reference RESTful terminology and/or terminology borrowed from [Spring Data REST](https://docs.spring.io/spring-data/rest/docs/current/reference/html/#repository-resources)._ 
 - `resource` - anything that can be identified via a URI. It's a representation of an underlying object. e.g. `/items/123-456-789` is a resource that represents a single DSpace Item. For additional examples see https://restful-api-design.readthedocs.io/en/latest/resources.html
 - `object` - we use this term to mean the actual object *behind* the resource. In other words, a resource is a JSON representation of some object (often a DSpaceObject) stored in the database. This is also sometimes called an "entity".
 - `collection` - a group of resources e.g. `/items` is a collection of all DSpace Item resources. This is also sometimes called a "collection resource" (e.g. in Spring Data REST)
     - Somewhat confusingly, Spring Data REST likes to use the phrase "item resources" for individual resources within a collection. Below, we've just called them "resources".
 - `association` - a link or relationship between two resources. This is also sometimes called an "association resource" (e.g. in Spring Data REST).
 
### On collection of resources endpoints

This type of endpoint interacts with a group (or collection) of resources (objects). For example: `/api/core/items` references *all* Items in the system. 

- `POST`:
Adds a new resource to the group (or collection). This creates a new object. The data for the new object _should be included_ in the request body. Related or additional information (such as required associations to other objects) may be passed as querystring parameters in the request, _if it is required to create the object._

- `GET`:
Returns the first page of the resources in the group (or collection). See [Pagination](#pagination) section. 

### On single resource endpoints

This type of endpoint interacts with a single resource (object). For example: `/api/core/items/123-456-789` references a single Item.

- `GET`:
Returns a single resource.

- `HEAD`:
Returns whether the target resource is available.

- `PUT`:
Replaces the state of the target resource with the supplied request body. This updates the object (via full replacement). The updated information _should be included_ in the request body. Related or additional information (such as required associations to other objects) may be passed as querystring parameters in the request, _if it is necessary to update the object._

- `PATCH`:
Similar to PUT but partially updating the resources state. We adhere to the [JSON Patch specification RFC6902](https://tools.ietf.org/html/rfc6902) see the [General rules for the Patch operation](patch.md) for more details.

- `DELETE`:
Deletes the target resource and object.

### On sub-path of a single resource endpoint (associations)

This type of endpoint interacts with (one or more) resources that are *associated with* a single resource. For that reason, it is sometimes called an "association" endpoint. For example: `/api/core/items/123-456-789/mappedCollections` references all Collections that are mapped to a single Item.

- `GET`:
Returns the state of the association (for the current resource)

- `PUT`:
Binds (or links) the resource(s) pointed to by the given URI(s) to the current resource. This replaces any existing links with the URIs passed in the request. Resource URIs to link must be sent in the body of the request using `Content-Type:text/uri-list`. [See example from Spring Data REST Relationships](https://www.baeldung.com/spring-data-rest-relationships). Return `400 Bad Request` if multiple URIs were given for a *-to-one-association
   ```
   # Example curl command to replace Item-to-Collection mappings with the two listed
   # Notice the two Collection URIs are separated by a newline (\n)
   curl -i -X PUT "http://localhost:8080/rest/api/core/items/<:uuid>/mappedCollections" 
        -H "Content-Type:text/uri-list" 
        -d "http://localhost:8080/rest/api/core/collections/1c11f3f1-ba1f-4f36-908a-3f1ea9a557eb \n http://localhost:8080/rest/api/core/collections/5ad50035-ca22-4a4d-84ca-d5132f34f588"
   ```

- `POST`:
Only supported for collection associations (i.e. associations allowing for multiple resources). Adds/Links a new resource (via its URI) to the association. Resource URIs to link must sent in the body of the request using `Content-Type:text/uri-list`. [See example from Spring Data REST Relationships](https://www.baeldung.com/spring-data-rest-relationships)
   ```
   # Example curl command to add a *new* Collection mapping for an Item
   curl -i -X POST "http://localhost:8080/rest/api/core/items/<:uuid>/mappedCollections" 
        -H "Content-Type:text/uri-list" 
        -d "http://localhost:8080/rest/api/core/collections/5ad50035-ca22-4a4d-84ca-d5132f34f588"
   ```

- `DELETE`:
Unbinds (unlinks) the association. Return `405 Method Not Allowed` if the association is required (and cannot be removed)

### Error codes
400 Bad Request - if multiple URIs were given for a to-one-association

401 Unauthorized (Unauthenticated) - if the request requires a logged-in user

403 Forbidden - if the requester doesn't have enough privilege to execute the request

404 Not Found - if the requested entity or collection doesn't exist

405 Method Not Allowed - if the methods is not implemented or a DELETE method is called on a non-optional association

422 Unprocessable Entity - if the request is well-formed, but is invalid based on the given data. For example, if you attempt to create a resource under a non-existent parent resource, or attempt to update a read-only (non-editable) field.

## Pagination
Each endpoints that expose a collection of resources, including sub-paths for embedded or linked collections (aka list of items of a collection, etc.), MUST implement the pagination with the following common behavior

### Request parameters
- **page**: 0 based integer value that specify the requested page in the result set [default 0]. Negative values must be rejected with a 400 Error code.
- **size**: the dimension of the result set window to show. It must be a positive value. Negative or zero values must be rejected with a 400 Error code. The default value as well as maximum values are configurable by the system administrator. Different Maximum values apply for anonymous users, logged-in users and administrators. Size over the maximum value are automatically reset to the maximum allowed value, no error is thrown.
- **sort**: the criteria to use. Ordering is specified appending a comma and the keyword asc or desc to the criteria name (i.e. title,asc). Unknown sort criteria and/or ordering keyword produce an error response with Http Code 400

### Response
The HAL document always includes a page object with the following attributes
- **size**: the dimension of the result set window returned (can be different than the requested value due to imposed limit, see the request parameters section)
- **totalElements**: the total size of the result set
- **totalPages**: the number of available page of result using the current window size
- **number**: the index (0-based) of the returned page
An example

	"page": {
    	"size": 5,
    	"totalElements": 14,
    	"totalPages": 3,
    	"number": 0
  	}

and, when applicable, the following links
- **self**: a parameterized link to the requested collection page
- **next**: the link to the next page of resources in the collection, if any, keeping the same option for size and sorting
- **previous**: the link to the previous page of resources in the collection, if any, keeping the same option for size and sorting
- **first**: the link to the first page of resources in the collection, keeping the same option for size and sorting
- **last**: the link to the last page of resources in the collection, keeping the same option for size and sorting

An example

	"_links": {
    	"first": {
      	"href": "http://localhost:8080/dspace-spring-rest/api/core/bitstreams?page=0&size=5"
    	},
    	"self": {
      	"href": "http://localhost:8080/dspace-spring-rest/api/core/bitstreams"
    	},
    	"next": {
      	"href": "http://localhost:8080/dspace-spring-rest/api/core/bitstreams?page=1&size=5"
    	},
    	"last": {
	      "href": "http://localhost:8080/dspace-spring-rest/api/core/bitstreams?page=2&size=5"
	    }
  	}

### Out of bound pages
In the case that the request parameters lead to a page outside the result set an empty page should be returned with the links needed to go to the first and last page of the result set if the results set is not empty and the total number of resources in the collection

### Error Code
400 Bad Request. If an unknown sort criteria is requested or a not valid ordering keyword is specified

## Design Principles
In the creation of the REST API, we've tried to follow a few specific design principles listed below

### On the Naming of Endpoints
Names should be descriptive but reasonably short. Use nouns instead of verbs. Form compounds by concatenating words, all lower case, without punctuation.  For example: `metadatafields`, not `metadata-fields` or `MetadataFields`.

### HATEOAS & HAL
The REST API supports the [HATEOAS paradigm](https://restfulapi.net/hateoas/) and adopt the [HAL format](http://stateless.co/hal_specification.html) to express links and embedded resources. Links are always expected to be **absolute** to make easier the implementation of "follow link" methods on the REST client side.

Because the API responds using the HAL Format, we distribute it with an [embedded HAL Browser provided by Spring](https://docs.spring.io/spring-data/rest/docs/current/reference/html/#_the_hal_browser).

### Statelessness
The REST API is considered [stateless](https://restfulapi.net/statelessness/), meaning the client is responsible for keeping state information and sending it to the server when it is needed. Because the server stores no state (or session) information internally, it will return a "token" (which contains any state information) to the client. The client must then return that token in *each subsequent request*, if the client wants that state information preserved.

#### JSON Web Tokens
[JSON Web Tokens (JWT)](https://jwt.io/) are used to store state (and authentication) information between requests. This is the format of token the REST API returns to the client. The client should return the JWT to the server in subsequent requests.

### ALPS - Application Level Profile Semantics
**While not yet implemented**, we expect future support for the ALPS metadata (<http://alps.io/>), so a profile link MUST exists from the root of API.
A profile link as defined in [RFC 6906](<https://tools.ietf.org/html/rfc6906>), is a place to include application level details. See the ALPS draft spec (http://tools.ietf.org/html/draft-amundsen-richardson-foster-alps-00)

### Spring Technology alignment
While it's an implementation detail, the new REST API uses many Spring REST (Java) libraries, including:

* [Spring Boot](https://spring.io/projects/spring-boot) - Provides base Spring webapp functionality
* [Spring MVC](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/web.html#mvc) - Provides Spring web framework
* [Spring REST](https://spring.io/understanding/REST) - Provides base REST tools/libraries for building REST APIs on Spring MVC
* [Spring HATEOAS](https://spring.io/projects/spring-hateoas) - Provides tools to create REST APIs following HATEOAS principles, and returning HAL Format responses.
* [Spring Data REST](https://spring.io/projects/spring-data-rest) (_alignment_) - We do **NOT** directly use Spring Data REST at this time (because of incompatibilities with our data layer). However, we have chosen to _align our implementation with Spring Data REST_ where possible.
* [Spring Data REST Hal Browser](https://docs.spring.io/spring-data/rest/docs/current/reference/html/#_the_hal_browser) - Provides the out-of-the-box HAL Browser you see when accessing our REST API
* [Spring REST Docs](https://spring.io/projects/spring-restdocs) - Provides tools to help document REST APIs in Spring MVC

Each of these libraries were chosen based on design principles above, and based on the fact that much of our underlying Java APIs use or align with Spring technologies.

## ETags & conditional headers
The ETag header (<http://tools.ietf.org/html/rfc7232#section-2.3>) provides a way to tag resources. This can prevent clients from overriding each other while also making it possible to reduce unnecessary calls. It is expected that all the returned document have an ETag

The ETag value can be used with in GET request with the *If-None-Match* conditional header. If the header MATCHES the ETag, the API will conclude nothing has changed, and instead of sending a copy of the resource, an HTTP 304 Not Modified status code is returned.

The ETag value can be also used with DELETE, POST, PUT and PATCH with the *If-Match* conditional header to avoid to perform action on changed resources (concurrency issues, optimistic lock approach).

Finally, when possible the If-Modified-Since header in GET request should be respected. If the resource has been not modified since the value of the Header the API should return an
HTTP 304 Not Modified status code. Resources that support the *If-Modified-Since* header *MUST* return the Last-Modified Header in the GET response, such header *MUST BE NOT* returned by resources not able to manage the If-Modified-Since header.  

## Endpoints
At the root of the api a HAL document MUST list all the primary endpoints allowing full discovery of the current version of the API.

[Documentation of the defined endpoints](endpoints.md)

## API Versioning
... here we will describe our strategy to provide access overtime to a specific version of the REST API...
### Deprecated endpoints & methods
... how we want to let know the client about deprecated endpoints & methods?
### Experimentals endpoints & methods
... do we want/need to introduce endpoints keeping the right to change behavior and other aspects without face with the self-imposed guarantee about backward compatibilty and versioning?
