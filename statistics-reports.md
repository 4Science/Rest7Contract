# Displaying usage statistics
[Back to the list of all defined endpoints](endpoints.md)

## Statistics for a DSpaceObject
**GET /api/statistics/usagereports**

As we don't have a use case to iterate over all the existing usage reports, the main endpoint is not implemented and a 405 error code is returned according to our [general error response codes](README.md#Error-codes).

## Single statistic
**GET /api/statistics/usagereports/<:id>**

This endpoint provides a specific statistic

An example JSON response document to `/api/statistics/usagereports/1911e8a4-6939-490c-b58b-a5d70f8d91fb_TopCountries`:
```json
{
    "id": "1911e8a4-6939-490c-b58b-a5d70f8d91fb_TopCountries",
    "type": "usagereport",
    "report-type": "TopCountries",
    "view-mode": "map",
    "points": [
        {
            "label": "United States",
            "type": "country",
            "id": "US",
            "values": {
                "views": 2
            }
        },
        {
            "label": "China",
            "type": "country",
            "id": "CN",
            "values": {
                "views": 1
            }
        }
    ],
    "_links" : {
      "self" : {
        "href" : "https://{dspace.url}/server/api/statistics/usagereports/1911e8a4-6939-490c-b58b-a5d70f8d91fb_TopCountries"
      }
    }
}
```

The ID is built from a concatenation of the UUID and the type of report for the given UUID

The type of point can be:
* country
* city
* bitstream
* item
* collection
* community
* date

The report-type specifies what data is returned, relevant e.g. to add a label to report. The current statistics support:
* TotalVisits
* TotalVisitsPerMonth
* TotalDownloads
* TopCountries
* TopCities

The view-mode specifies the visualization mode for the statistics
* chart.bar
* chart.pie
* chart.line
* map
* table 

Possible response status

- 200 OK - The specific statistics data was found, and the data has been properly returned.
* 401 Unauthorized - if you are not authenticated and the statistics are not available to the Anonymous user
* 403 Forbidden - if you are not logged in with sufficient permissions and the statistics are not available to the Anonymous user
- 404 Not Found - The specified ID was not found

## Search Statistics for a DSpaceObject
**GET /api/statistics/usagereports/search/object**

This endpoint provides a paginated list of statistics for a DSpaceObject. 

The DSpaceObject is given through the following parameters:
- `uri` The object to retrieve statistics for. The full URI of the rest resource must be specified, i.e. https://{dspace.url}/server/api/core/communities/{uuid}
- `startDate` If present represent the date from which the statistics are to be taken into account (date format YYYY-MM-DD)
- `endDate` If present represent the date until which the statistics are to be taken into account (date format YYYY-MM-DD)
- `category` If present filter returns only the reports in the requested category, see [Statistics Categories](statistics-categories.md). The parameter must match the `id` of the category

The usual parameters for paginated lists are supported as well:
- `page` The page number 
- `size` The number of reports in a page

An example JSON response document to `/api/statistics/usagereports/search/object?page=0&size=2&uri=https://{dspace.url}/server/api/core/site/6d65c6a2-3fe7-44dd-bacb-79271257c35d&category=mainReports`:

```json
{
    "_embedded": {
        "usagereports": [
            {
                "id": "6d65c6a2-3fe7-44dd-bacb-79271257c35d_TotalVisits",
                "type": "usagereport",
                "report-type": "TotalVisits",
                "view-mode": "chart.bar",
                "points": [
                    {
                        "label": "cad835c8-0cae-4769-a08a-857f0f814020",
                        "type": "item",
                        "id": "cad835c8-0cae-4769-a08a-857f0f814020",
                        "values": {
                            "views": 3313
                        }
                    },
                    {
                        "label": "6759d5e0-3915-4864-917c-1940bdb75fbd",
                        "type": "item",
                        "id": "6759d5e0-3915-4864-917c-1940bdb75fbd",
                        "values": {
                            "views": 3308
                        }
                    },
                    {
                        "label": "b0f6ce54-2ed8-4b67-a075-64794abb4e82",
                        "type": "item",
                        "id": "6759d5e0-3915-4864-917c-1940bdb75fbd",
                        "values": {
                            "views": 1800
                        }
                    }
                ],
                "_links" : {
                  "self" : {
                    "href" : "https://{dspace.url}/server/api/statistics/usagereports/6d65c6a2-3fe7-44dd-bacb-79271257c35d_TotalVisits"
                  }
                }
            }
        ]
    }
}
```

An example JSON response document to `/api/statistics/usagereports/search/object?uri=https://{dspace.url}/server/api/core/item/1911e8a4-6939-490c-b58b-a5d70f8d91fb`:

```json
{
    "_embedded": {
        "usagereports": [
            {
                "id": "1911e8a4-6939-490c-b58b-a5d70f8d91fb_TotalVisits",
                "type": "usagereport",
                "report-type": "TotalVisits",
                "view-mode": "chart.bar",
                "points": [
                    {
                        "label": "1911e8a4-6939-490c-b58b-a5d70f8d91fb",
                        "type": "item",
                        "id": "1911e8a4-6939-490c-b58b-a5d70f8d91fb",
                        "values": {
                            "views": 3
                        }
                    }
                ],
                "_links" : {
                  "self" : {
                    "href" : "https://{dspace.url}/server/api/statistics/usagereports/1911e8a4-6939-490c-b58b-a5d70f8d91fb_TotalVisits"
                  }
                }
            },
            {
                "id": "0aa1fe0c-e173-4a36-a526-5c157dedfc07_TotalVisitsPerMonth",
                "points": [
                  {
                    "id": "September 2020",
                    "label": "September 2020",
                    "values": {
                      "views": 0
                    },
                    "type": "date"
                  },
                  {
                    "id": "October 2020",
                    "label": "October 2020",
                    "values": {
                      "views": 0
                    },
                    "type": "date"
                  },
                  {
                    "id": "November 2020",
                    "label": "November 2020",
                    "values": {
                      "views": 0
                    },
                    "type": "date"
                  },
                  {
                    "id": "December 2020",
                    "label": "December 2020",
                    "values": {
                      "views": 0
                    },
                    "type": "date"
                  },
                  {
                    "id": "January 2021",
                    "label": "January 2021",
                    "values": {
                      "views": 0
                    },
                    "type": "date"
                  },
                  {
                    "id": "February 2021",
                    "label": "February 2021",
                    "values": {
                      "views": 67
                    },
                    "type": "date"
                  },
                  {
                    "id": "March 2021",
                    "label": "March 2021",
                    "values": {
                      "views": 234
                    },
                    "type": "date"
                  }
                ],
                "type": "usagereport",
                "report-type": "TotalVisitsPerMonth",
                "view-mode": "chart.line",
                "_links": {
                  "self": {
                    "href": "https://dspacecris7.4science.cloud/server/api/statistics/usagereports/0aa1fe0c-e173-4a36-a526-5c157dedfc07_TotalVisitsPerMonth"
                "points": [
                    {
                        "label": "1911e8a4-6939-490c-b58b-a5d70f8d91fb",
                        "type": "date",
                        "id": "1911e8a4-6939-490c-b58b-a5d70f8d91fb",
                        "values": {
                            "2020-03": 0,
                            "2020-04": 0,
                            "2020-05": 3
                        }
                    }
                ],
                "_links" : {
                  "self" : {
                    "href" : "https://{dspace.url}/server/api/statistics/usagereports/1911e8a4-6939-490c-b58b-a5d70f8d91fb_TotalVisits"
                  }
                }
            },
            {
                "id": "1911e8a4-6939-490c-b58b-a5d70f8d91fb_TotalDownloads",
                "type": "usagereport",
                "report-type": "TotalDownloads",
                "view-mode": "chart.bar",
                "points": [
                    {
                        "label": "8d33bdfb-e7ba-43e6-a93a-f445b7e8a1e2",
                        "type": "bitstream",
                        "id": "8d33bdfb-e7ba-43e6-a93a-f445b7e8a1e2",
                        "values": {
                            "downloads": 8
                        }
                    }
                ],
                "_links" : {
                  "self" : {
                    "href" : "https://{dspace.url}/server/api/statistics/usagereports/1911e8a4-6939-490c-b58b-a5d70f8d91fb_TotalVisits"
                  }
                }
            },
            {
                "id": "1911e8a4-6939-490c-b58b-a5d70f8d91fb_TopCountries",
                "type": "usagereport",
                "report-type": "TopCountries",
                "view-mode": "map",
                "points": [
                    {
                        "label": "United States",
                        "type": "country",
                        "id": "US",
                        "values": {
                            "views": 2
                        }
                    },
                    {
                        "label": "China",
                        "type": "country",
                        "id": "CN",
                        "values": {
                            "views": 1
                        }
                    }
                ],
                "_links" : {
                  "self" : {
                    "href" : "https://{dspace.url}/server/api/statistics/usagereports/1911e8a4-6939-490c-b58b-a5d70f8d91fb_TotalVisits"
                  }
                }
            }
        ]
    }
}
```

Possible response status:
* 200 OK - The DSpaceObject was found, and the data has been properly returned.
* 400 Bad Request - The uri parameter format is incorrect
* 401 Unauthorized - if you are not authenticated and the statistics are not available to the Anonymous user
* 403 Forbidden - if you are not logged in with sufficient permissions and the statistics are not available to the Anonymous user
* 422 Unprocessable Entity - if the parameters are inconsistent, the uri is not resolved to a DSpaceObject the end date is earlier than the start date