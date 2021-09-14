# Boxes Endpoints
[Back to the list of all defined endpoints](endpoints.md)

## Main Endpoint

**/api/layout/sections**   

Provide access to all the configured CRIS layout sections. The full JSON response document is as follow
```json
{
  "_embedded": {
    "sections": [
      {
        "id": "publications",
        "componentRows": [
          [
            {
              "browseNames": [
                "itemdept",
                "author",
                "title",
                "type",
                "dateissued",
                "subject"
              ],
              "componentType": "browse",
              "style": "col-md-4"
            }
          ],
          [
            {
              "discoveryConfigurationName": "publication",
              "sortField": "dateaccessioned_dt",
              "order": "desc",
              "componentType": "top",
              "style": "col-md-6"
            }
          ]
        ],
        "type": "section",
        "_links": {
          "self": {
            "href": "http://localhost:8080/server/api/layout/sections/publications"
          }
        }
      },
      {
        "id": "researcherprofiles",
        "componentRows": [
          [
            {
              "browseNames": [
                "rpname",
                "rpdept"
              ],
              "componentType": "browse",
              "style": "col-md-4"
            }
          ]
        ],
        "type": "section",
        "_links": {
          "self": {
            "href": "http://localhost:8080/server/api/layout/sections/researcherprofiles"
          }
        }
      }, 
      {
        "id": "site",
        "componentRows": [
          [
            {
              "discoveryConfigurationName": "site",
              "style": "col-md-12",
              "searchType": "basic",
              "initialStatements": 3,
              "displayTitle": false,
              "componentType": "search"
            }
          ],
          [
            {
              "style": "col-md-12 py-4",
              "counterSettingsList": [
                {
                  "discoveryConfigurationName": "researchoutputs",
                  "icon": "fas fa-book fa-3x",
                  "entityName": "publications",
                  "link": "/explore/researchoutputs"
                },
                {
                  "discoveryConfigurationName": "project_funding",
                  "icon": "fas fa-project-diagram fa-3x",
                  "entityName": "project_funding",
                  "link": "/explore/fundings_and_projects"
                },
                {
                  "discoveryConfigurationName": "person",
                  "icon": "fas fa-graduation-cap fa-3x",
                  "entityName": "rprofiles",
                  "link": "/explore/researcherprofiles"
                }
              ],
              "componentType": "counters"
            }
          ],
          [
            {
              "discoveryConfigurationName": "researchoutputs",
              "sortField": "metric.view",
              "order": "desc",
              "style": "col-md-4",
              "titleKey": null,
              "componentType": "top"
            },
            {
              "discoveryConfigurationName": "researchoutputs",
              "sortField": "metric.download",
              "order": "desc",
              "style": "col-md-4",
              "titleKey": null,
              "componentType": "top"
            },
            {
              "discoveryConfigurationName": "researchoutputs",
              "sortField": "metric.scopus.citation",
              "order": "desc",
              "style": "col-md-4",
              "titleKey": null,
              "componentType": "top"
            }
          ],
          [  
            {
              "style": "col-md-12 py-5 w-50 center",
              "content": "https://www.images/test.png",
              "contentType": "image",
              "componentType": "text-row"
            },
            {
              "style": "col-md-12 h2 d-flex justify-content-center py-3",
              "content": "section.title",
              "contentType": "text-key",
              "componentType": "text-row"
            },
            {
              "style": "col-md-12 d-flex justify-content-center py-2",
              "content": "section.description",
              "contentType": "text-key",
              "componentType": "text-row"
            }
          ]
        ],
        "type": "section",
        "_links": {
          "self": {
            "href": "https://dspacecris7.4science.cloud/server/api/layout/sections/site"
          }
        }
      }
    ]
  }
    ]
  },
  "_links": {
    "self": {
      "href": "http://localhost:8080/server/api/layout/sections"
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
## Single Section
**/api/layout/sections/<:id>**

Provide detailed information about a specific CRIS layout section. The JSON response document is as follow
```json
{
  "id": "publications",
  "componentRows": [
    [
      {
        "browseNames": [
          "itemdept",
          "author",
          "title",
          "type",
          "dateissued",
          "subject"
        ],
        "componentType": "browse",
        "style": "col-md-4"
      },
      {
        "discoveryConfigurationName": "publication",
        "componentType": "search",
        "style": "col-md-8"
      }
    ],
    [
      {
        "discoveryConfigurationName": "publication",
        "sortField": "dateaccessioned_dt",
        "order": "desc",
        "componentType": "top",
        "style": "col-md-6"
      },
      {
        "discoveryConfigurationName": "publication",
        "componentType": "facet",
        "style": "col-md-6"
      }
    ]
  ],
  "type": "section"
}
```
Return codes:
* 200 OK - if the operation succeed
* 404 Not found - if a section with the given id doesn't exist

The `componentRows` attribute represent the list of components that compose the section, splitted by rows. There are 4 different types of components, distinguishable by their `componentType` attribute which can have one of the following values: [ `browse`,`top`,`facet`,`search`,`text-row`, ,`counters` ]. The attributes of each component may vary according to the type of the component itself. In addition to the `componentType` attribute used to indicate the type of the component, each component type has the following attributes:
* `browse`: has only one attribute named `browseNames` representing a list of names
* `top`: has the `discoveryConfigurationName` indicating the discovery configuration name and two attributes named `sortField` and `order` indicating by which field and in what order to sort the search results
* `facet`: has the `discoveryConfigurationName` indicating the discovery configuration name,  and the optional `facetsPerRow` defining how many facet box UI will display.
* `search`: has the `discoveryConfigurationName` indicating the discovery configuration name. searchType with value `basic` or `advanced` (default) defining whether displayed box should consist in a single input box or many containing multiple statements that can be combined with AND, OR, NOT keywords.
* `text-row`: has the `order` property, defining the order on which the content should appear among other text row elements in the same list of cris layout elements,  `content` property, defining the static content to be displayed, while `contentType` defines the type of content, its value can be `image`, meaning that content is an image url, `text-raw` meaning that content is a static text to be displayed and `text-key` meaning actual text to be displayed should be rendered using UI’s I18n logic
* `counters` : has `counterSettingsList` property, each element representing a counter to be displayed by this infographic and having following properties: `discoveryConfigurationName`, the discovery configuration to be used to count by query elements; `icon`, a font awesome reference to icon to be displayed (i.e. `fas fa-book fa-3x`), label text key to be displayed together with the icon, `link`(optional) link to be followed when icon is clicked.