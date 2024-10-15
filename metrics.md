# Metrics Endpoint
[Back to the list of all defined endpoints](endpoints.md)

## Single Metric
**/api/cris/metrics/<:id>**

Provide detailed information about a specific metrics. The JSON response document is as follow
```json
{
    "id":1,
    "metricType":"view",
    "metricCount":2312.0,
    "acquisitionDate": "Nov 21, 2020",
    "startDate":null,
    "endDate":null,
    "last":true,
    "remark": {"identifier":"2-s2.0-00349160000",
               "link":"https://www.scopus.com/inward/citedby.uri?partnerID\u000dhzOxMe3b\u0026scp\u003d67349162500\utt6origin\u003dinward",
               "pmid":"10000000",
               "doi":"10.0000/j.gene.2030.04.000"
               },
    "deltaPeriod1":8,
    "deltaPeriod2":31,
    "rank":50,
    "type":"metric",
    "uniqueType": "cris.metric",
    "_links":{
        "self":{
            "href":"http://localhost/api/cris/metrics/1"
        }
    }
}
```
Return codes:
* 200 OK - if the operation succeed
* 401 Unauthorized - if you are not authenticated.
* 403 Forbidden - if you are not logged in with sufficient permissions. Only users with ADMIN right and users tha has READ policy on item related to the metric
* 404 Not found - if the metric doesn't exist (or was already deleted)