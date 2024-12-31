# Displaying statistics categories
[Back to the list of all defined endpoints](endpoints.md)

## Statistics for a DSpaceObject
**GET /api/statistics/categories**

As we don't have a use case to iterate over all the existing categories, the main endpoint is not implemented and a 405 error code is returned according to our [general error response codes](README.md#Error-codes).

## Single statistic
**GET /api/statistics/categories/<:id>**

This endpoint provides a specific statistic category

An example JSON response document to `/api/statistics/categories/mainReports`:
```json
{
    "id": "mainReports",
    "type": "category",
    "uniqueType": "statistics.category",
    "category-type": "mainReports",
    "_links" : {
     "self" : {
       "href" : "https://{dspace.url}/server/api/statistics/categories/mainReports"
     }
    }
}
```

The category-type often will match the id, it is expected to be used for internationalization

Possible response status

* 200 OK - The specific statistics category has been found, and the category has been properly returned.
- 404 Not Found - The specified ID has been not found

## Search Statistics Categories with reports for a DSpaceObject
**GET /api/statistics/categories/search/object**

This endpoint provides a paginated list of statistics categories that have not empty reports for a DSpaceObject. 

The DSpaceObject is given through the following parameters:
- `uri` The object to retrieve statistics for. The full URI of the rest resource must be specified, i.e. https://{dspace.url}/server/api/core/community/{uuid}
- `startDate` If present represent the date from which the statistics are to be taken into account (date format YYYY-MM-DD)
- `endDate` If present represent the date until which the statistics are to be taken into account (date format YYYY-MM-DD)

The usual parameters for paginated lists are supported as well:
- `page` The page number 
- `size` The number of reports in a page

An example JSON response document to `/api/statistics/categories/search/object?page=0&size=2&uri=https://{dspace.url}/server/api/core/site/6d65c6a2-3fe7-44dd-bacb-79271257c35d`:

```json
{
    "_embedded": {
        "categories": [
            {
                "id": "site-mainReports",
                "type": "category",
                "uniqueType": "statistics.category",
                "category-type": "mainReports",
                "_links" : {
                  "self" : {
                    "href" : "https://{dspace.url}/server/api/statistics/categories/site-mainReports"
                  }
                }
            }
        ]
    }
}
```

An example JSON response document to `/api/statistics/categories/search/object?uri=https://{dspace.url}/server/api/core/item/1911e8a4-6939-490c-b58b-a5d70f8d91fb` assuming multiple categories have reports:

```json
{
    "_embedded": {
        "categories": [
            {
                "id": "item-mainReports",
                "type": "category",
                "uniqueType": "statistics.category",
                "category-type": "mainReports",
                "_links" : {
                  "self" : {
                    "href" : "https://{dspace.url}/server/api/statistics/categories/item-mainReports"
                  }
                }
            },
            {
                "id": "item-relatedPublications",
                "type": "category",
                "uniqueType": "statistics.category",
                "category-type": "relatedPublications",
                "_links" : {
                  "self" : {
                    "href" : "https://{dspace.url}/server/api/statistics/categories/item-relatedPublications"
                  }
                }
            },
            {
                "id": "item-relatedProjects",
                "type": "category",
                "uniqueType": "statistics.category",
                "category-type": "relatedProjects",
                "_links" : {
                  "self" : {
                    "href" : "https://{dspace.url}/server/api/statistics/categories/item-relatedProjects"
                  }
                }
            }
        ]
    }
}
```

Possible response status:
* 200 OK - The DSpaceObject has been found, and the categories, eventually an empty page, have been properly returned.
* 400 Bad Request - The uri parameter format is missing
* 422 Unprocessable Entity - if the parameters are inconsistent, the uri is not resolved to a DSpaceObject the end date is earlier than the start date