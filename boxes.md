# Boxes Endpoints
[Back to the list of all defined endpoints](endpoints.md)

## Main Endpoint

**/api/layout/boxes**   

Not implemented

## New Box
**POST /api/layout/boxes**

To create a new Box perform as POST with the follow JSON:

```json
{
  "shortname": "primary",
  "header": "Primary Information",
  "entityType": "Person",
  "collapsed": false,
  "minor": false,
  "style": "col-md-6",
  "clear": true,
  "security": 0,
  "boxType": "METADATA",
  "type": "box"
}
```
This endpoint is reserved to system administrators, its returns the created box.

Return codes:
* 200 OK - if the operation succeed
* 401 Unauthorized - if you are not authenticated
* 403 Forbidden - if you are not logged in with sufficient permissions
* 422 UNPROCESSABLE ENTITY - if the json body is unprocessable for CrisLayoutBoxRest entity

## Delete tab

**DELETE /api/layout/boxes/<:id>**

Delete a Box. This endpoint is reserved to system administrators.

Return codes:
* 204 No content - if the operation succeed
* 401 Unauthorized - if you are not authenticated
* 403 Forbidden - if you are not logged in with sufficient permissions
* 404 Not found - if the box doesn't exist (or was already deleted)

## Single Box

**GET /api/layout/boxes/<:id>**

Provide detailed information about a specific box. The JSON response document is as follow

```json
{
  "id": 1,
  "shortname": "primary",
  "header": "Primary Information",
  "entityType": "Person",
  "collapsed": false,
  "minor": false,
  "style": "col-md-6",
  "clear": true,
  "security": 0,
  "boxType": "RELATION",
  "type": "box"
}
```

Attributes
* the *header* attribute is the label or the i18n key to use to present the section to the user
* the *security* attribute is a constant where 0 mean public, 1 mean administrators, 2 mean owner only, 3 owner & administrators, 4 custom metadata
* the *minor* attribute is used to flag box that should be ignored in the determination of the tab visualization
* the *boxType* attribute is used to choice the appropriate component. It could be metadata, search, bibliometrics
* the *clear* attribute is true if the box fills the entire row, false otherwise. 

Exposed links:
* configuration: link to a configuration entity with more information specific for the box type. Configuration details depend on the [specific boxType](boxes-types.md)
* securityMetadata: link to the metadatafields that defined the security

Return codes:
* 200 OK - if the operation succeed
* 401 Unauthorized - if you are not authenticated. Please note that this also apply to resource policy related to the Anonymous group
* 403 Forbidden - if you are not logged in with sufficient permissions. Only system administrators, users with ADMIN right on the target resource, users mentioned in the policy (eperson or member of the group) can access the resourcepolicy
* 404 Not found - if the resourcepolicy doesn't exist (or was already deleted)

## Linked entities

### SecurityMetadata

#### Retrieve SecurityMetadata of the box

**GET /api/layout/boxes/<:id>/securitymetadata**

It returns the metadatafields that defined the security of box

**GET /api/layout/boxes/<:id>/securitymetadata/<:id>**

_Unsupported._ If you want detailed information about a single metadatafield in the box, use the `/api/core/metadatafields/<:metadatafield.id>` endpoint.

**POST /api/layout/boxes/<:id>/securitymetadata**

A POST request will result in adding the metadatafield to the list of metadata used to evaluate access permission to the box.

The metadata (also more than one) MUST be included in the body using the `text/uri-list` content type
 
Return codes:
 * 204 No Content - if the update succeeded (including the case of no-op if the mapping was already as requested)
 * 401 Unauthorized - if you are not authenticated
 * 403 Forbidden - if you are not logged in as administrator 
 * 404 Not found - if the box doesn't exist (or was already deleted)
 * 422 Unprocessable Entity - if the specified metadata is not found

**PUT /api/layout/boxes/<:id>/securitymetadata**

_Unsupported._ You may replace or update the security metadata using DELETE requests and/or POST requests.

**DELETE /api/layout/boxes/<:id>/securitymetadata**

_Unsupported._ At this time, we do not support removing all security metadata in a single request. Please use `DELETE /api/layout/boxes/<:id>/securitymetadata/<:metadatafield.id>` to remove metadatafield one by one.

**DELETE /api/layout/boxes/<:id>/securitymetadata/<:metadatafield.id>**

A DELETE request will result in removing the metadatafield from the current list.

Return codes:
 * 204 No Content if the delete succeeded (including the case of no-op if the metadata was not mapped) 
 * 401 Unauthorized - if you are not authenticated
 * 403 Forbidden - if you are not logged in as administrator
 * 404 Not found - if the box doesn't exist (or was already deleted)
 * 422 Unprocessable Entity - if the specified uri cannot resolve to a metadatafield (if the uri is valid but the metadatafield is not found, 204 is expected)


### Configuration

#### Retrieve configuration of the box

**GET /api/layout/boxes/<:id>/configuration**

It returns configuration entity with more information specific for the [box type](boxes-types.md)

## Search methods

### findByItem

**GET /api/layout/boxes/search/findByItem?uuid=<:item-uuid>&tab=<:id>**

It returns the boxes that are available for the specified item in the requested tab. The boxes are sorted by priority ascending. This are filtered based on the permission of the current user and available data in the items (empty boxes are not included).

### findByEntityType

**GET /api/layout/boxes/search/findByEntityType?type=<:string>**

It returns the boxes that are available for the items of the specified type. This endpoint is reserved to system administrators