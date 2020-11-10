# Box Metadata Configuration

[Back to the list of all defined box types](boxes-types.md)

## Main Endpoint

**/api/layout/boxmetadataconfigurations**

Not Implemented

## Single Box Metadata Configuration

**/api/layout/boxmetadataconfigurations/<:id>**

Provides detailed information about the metadata included in the box

```json
{
  "id": 1,
  "rows": [
    {
      "fields": [
       {
           metadata: "dc.title",
           label: "Title",
           rendering: null,
           fieldType: "metadata",
           style: null,
           styleLabel: null,
           styleValue: null
       },
       {
           metadataGroup: {
                leading: "dc.contributor.author",
                elements: [
                    {
                        metadata: "dc.contributor.author",
                        label: "Name",
                        rendering: "crisref",
                        fieldType: "metadata",
                        style: null,
                        styleLabel: null,
                        styleValue: null
                    },
                    {
                        metadata: "oairecerif.author.affiliation",
                        label: "Affiliation",
                        rendering: "crisref",
                        fieldType: "metadata",
                        style: null,
                        styleLabel: null,
                        styleValue: null
                    }
                ]},
           label: "Authors",
           rendering: "table",
           fieldType: "metadataGroup",
           style: null,
           styleLabel: null,
           styleValue: null
       },
	  	{
	  		metadata: "dc.subject",
	  		label: "Subjects",
	  		rendering: "browselink",
	  		fieldType: "metadata",
	  		style: null,
	  		styleLabel: null,
	  		styleValue: null
	  	},
	  	{
	  		bitstream: {
	  			bundle: "ORIGINAL", 
	  			metadataField: "dc.type",
	  			metadataValue: "picture"
	  		},
	  		label: "Photo",
	  		rendering: "thumbnail",
	  		fieldType: "bitstream",
	  		style: null,
	  		styleLabel: null,
	  		styleValue: null
	  	}
  	}
  ]
}
```

It provides the configuration for a component that visualize a list of metadata according to specific rules

Attributes
* the *id* attribute has the same value that the id of the related box
* the *label* attribute is the i18n key for the field label to visualize
* the *rendering* attribute defines the component to use to visualize the field. Examples are browselink, longtext, identifier, date, etc. for metadata field and preview, thubmnail, etc. for bitstream field 
* the *style* attribute allows to set arbitrary css styles to the container of the generated html
* the *styleLabel* attribute allows to set arbitrary css styles to the label
* the *styleValue* attribute allows to set arbitrary css styles to the metadata value
* the *fieldType" is one of metadata, metadataGroup or bitstream a corresponding attribute will be present
* metadata: is the canonical name of the metadata to use (eg dc.contributor.author, dc.title, etc.)
* metadataGroup: is an object containing the leading metadata of the group and the ordered array of all the metadata in the group that must be shown together keeping the values in pair (i.e. the first value of the first metadata must be shown together with the first value of the second metadata, etc). The leading metadata define the multiplicity of the group, i.e. the there are three values for the leading metadata it is expected that the same number (three) of values is present for all the metadata in the group
* bitstream: is an object containing details to filter the bitstreams to use. It can be the name of the bundle to use and/or the value of specfic bitstream metadata

 
## Patch operations

### Adding fields
It is possible to add a fields
`curl -X PATCH '{dspace7-url}/api/layout/boxmetadataconfigurations/1'
-H "Authorization: Bearer ..." -H 'Content-Type: application/json'
--data '[{"op":"add","path":"/rows/0/<:fields>/0", "value":{"metadata":"orgunit.identifier.name",
          "label":"Department Name", "rendering":"browselink", "fieldType":"metadata", "style":null }}]'

that will transform

 ```json
{
  "id": 1,
  "rows": [
    {
	  "fields": [
	  	{
	  		metadata: "dc.contibutor.author",
	  		label: "Authors",
	  		rendering: "browselink",
	  		fieldType: "metadata",
	  		style: null
	  		styleLabel: null,
	  		styleValue: null
	  	},
	  	{
	  		bitstream: {
	  			bundle: "ORIGINAL", 
	  			metadataField: "dc.type",
	  			metadataValue: "picture"
	  		},
	  		label: "Authors",
	  		rendering: "thumbnail",
	  		fieldType: "bitstream",
	  		style: null
	  		styleLabel: null,
	  		styleValue: null
	  	}
  	}
  ],
  "type": "boxmetadataconfiguration"
}
```
in

```
{
  "id": 1,
  "rows": [
    {
	  "fields": [
	  	{
	  		metadata: "orgunit.identifier.name",
	  		label: "Department Name",
	  		rendering: "browselink",
	  		fieldType: "metadata",
	  		style: null,
	  		styleLabel: null,
	  		styleValue: null
	  	},
	  	{
	  		metadata: "dc.contibutor.author",
	  		label: "Authors",
	  		rendering: "browselink",
	  		fieldType: "metadata",
	  		style: null,
	  		styleLabel: null,
	  		styleValue: null
	  	},
	  	{
	  		bitstream: {
	  			bundle: "ORIGINAL", 
	  			metadataField: "dc.type",
	  			metadataValue: "picture"
	  		},
	  		label: "Authors",
	  		rendering: "thumbnail",
	  		fieldType: "bitstream",
	  		style: null,
	  		styleLabel: null,
	  		styleValue: null
	  	}
  	}
  ],
  "type": "boxmetadataconfiguration"
}
```

Please note that update the details of a specific field such as the label or changing the fieldType etc. is not supported at the moment. To add a field with fieldType bitstream or metadataGroup the full json representation of such field must be passed in the json patch add operation. It is not possible to modify the composition of a metadataGroup after that it has been added, if needed remove it and add again with the right composition. 

Return codes:
* 200 OK if the path operation succeed
* 401 Unauthorized - if you are not authenticated
* 403 Forbidden - if you are not logged in with sufficient permissions. Only system administrators can use the endpoint
* 404 Not found - if box doesn't exist (or was already deleted)
* 422 Unprocessable Entity - if the metadata doesn't exist (or the metadataField specified for a bitstream field or the leading or any metadata in the elements of metadataGroup field doesn't exist)

### Removing fields
It is possible to remove an metadata
curl --data '[{ "op": "remove", "path": "/rows/0/<:fields>/0"}]'
 -X PATCH ${dspace7-url}/api/layout/boxmetadataconfigurations/1
 
that will transform
 
 ```json
{
  "id": 1,
  "rows": [
    {
	  "fields": [
	  	{
	  		metadata: "dc.contibutor.author",
	  		label: "Authors",
	  		rendering: "browselink",
	  		fieldType: "metadata",
	  		style: null,
	  		styleLabel: null,
	  		styleValue: null
	  	},
	  	{
	  		bitstream: {
	  			bundle: "ORIGINAL", 
	  			metadataField: "dc.type",
	  			metadataValue: "picture"
	  		},
	  		label: "Authors",
	  		rendering: "thumbnail",
	  		fieldType: "bitstream",
	  		style: null,
	  		styleLabel: null,
	  		styleValue: null
	  	}
  	}
  ],
  "type": "boxmetadataconfiguration"
}
```
in

 ```json
{
  "id": 1,
  "rows": [
    {
	  "fields": [
	  	{
	  		bitstream: {
	  			bundle: "ORIGINAL", 
	  			metadataField: "dc.type",
	  			metadataValue: "picture"
	  		},
	  		label: "Authors",
	  		rendering: "thumbnail",
	  		fieldType: "bitstream",
	  		style: null,
	  		styleLabel: null,
	  		styleValue: null
	  	}
  	}
  ],
  "type": "boxmetadataconfiguration"
}
```

 Return codes:
 * 204: if the delete succeeded
 * 401  Unauthorized - if you are not authenticated
 * 403  Forbidden - if you are not logged in with sufficient permissions 
 * 422: if the specified metadata don't exist
    