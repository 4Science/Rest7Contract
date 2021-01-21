# ORCID Queue Endpoints
[Back to the list of all defined endpoints](endpoints.md)

## Single ORCID Queue entry
**GET /api/cris/orcidqueues/<:id>**

Provide detailed information about a specific ORCID queue entry. The JSON response document is as follow
```json
{
  "id": 73,
  "ownerId": "87d24f06-0ca5-4abe-84f9-bfd36a8236d8",
  "entityId": "66e0d773-ddeb-4b6b-bcac-ced2ae67141c",
  "entityName": "My Publication",
  "entityType": "Publication",
  "type": "orcidqueue",
  "_links": {
    "self": {
      "href": "**/api/cris/orcidqueues/73"
    }
  }
}
```

Return codes:
* 200 OK - if the operation succeed
* 401 Unauthorized - if you are not authenticated
* 403 Forbidden - if you are not logged in with sufficient permissions
* 404 Not found - if an ORCID queue entry with the given id doesn't exist

The `ownerId` attribute represent the id of the researcher profile item that is associated with the the entity item with id `entityId` that should be sent to the ORCID register.

## Search ORCID Queue entries by owner id
**GET /api/cris/orcidqueues/search/findByOwner?ownerId=<:item-uuid>**

Provide detailed information about all the ORCID queue entries related to the owner with the given uuid. The JSON response document is as follow
```json
{
  "_embedded": {
    "orcidQueues": [
      {
        "id": 68,
        "ownerId": "87d24f06-0ca5-4abe-84f9-bfd36a8236d8",
        "entityId": "3eed1cf2-552d-491a-9a1f-ef941a0cafcd",
        "entityName": "ABC-54321",
        "entityType": "Project",
        "type": "orcidQueue",
        "_links": {
          "self": {
            "href": "**/api/cris/orcidqueues/68"
          }
        }
      },
      {
        "id": 73,
        "ownerId": "87d24f06-0ca5-4abe-84f9-bfd36a8236d8",
        "entityId": "66e0d773-ddeb-4b6b-bcac-ced2ae67141c",
        "entityName": "My Publication",
        "entityType": "Publication",
        "type": "orcidQueue",
        "_links": {
          "self": {
            "href": "**/api/cris/orcidqueues/73"
          }
        }
      }
    ]
  },
  "_links": {
    "self": {
      "href": "http://localhost:8080/server/api/cris/orcidQueues/search/findByOwner"
    }
  },
  "page": {
    "size": 20,
    "totalElements": 2,
    "totalPages": 1,
    "number": 0
  }
}
```

Return codes:
* 200 OK - if the operation succeed
* 401 Unauthorized - if you are not authenticated
* 403 Forbidden - if you are not logged in with sufficient permissions

## Delete an ORCID Queue entry
**DELETE /api/cris/orcidqueues/<:id>**

Delete a single ORCID queue entry by id. The provided response has no content.

Return codes:
* 204 No content - if the operation succeed
* 401 Unauthorized - if you are not authenticated
* 403 Forbidden - if you are not logged in with sufficient permissions