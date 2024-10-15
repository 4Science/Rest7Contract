# WorkspaceItem data of detect duplicate sectionType
[Back to the definition of the workspaceitems endpoint](workspaceitems.md)

The section data represent the data about duplicates detected during the submission

```json
{
  "matches" : {
	"9db079fc-359f-4aa4-ad4f-2dd2f0afaac6" : {
	  "submitterDecision" : null,
	  "workflowDecision" : null,
	  "adminDecision" : null,
	  "submitterNote" : null,
	  "workflowNote" : null,
	  "matchObject" : {
		"id" : "9db079fc-359f-4aa4-ad4f-2dd2f0afaac6",
		"uuid" : "9db079fc-359f-4aa4-ad4f-2dd2f0afaac6",
		"name" : "Sample submission",
		"handle" : "123456789/28381",
		...
	  }
	},
   ...
  }
}
```

## Patch operations
The PATCH method expects a JSON body according to the [JSON Patch specification RFC6902](https://tools.ietf.org/html/rfc6902)

Each successful Patch operation will return a HTTP 200 CODE with the new workspaceitem as body 

### Add

To submit decision about found duplicates, the client must send a JSON Patch ADD operation to the *submitterDecision* path as follow:

`curl --data '[ { "op": "add", "path": "/sections/detect-duplicate/matches/<:object-uuid>/submitterDecision", "value": {"value": "<operation>", "note": <note> }}]' -X PATCH ${dspace7-url}/api/submission/workspaceitems/<:id>`

where supported values are:
- \<operation> set to "verify" and \<note> set to a string, such as "check the title for duplicate" if we have to signal a note;
- \<operation> set to "reject" and \<note> set to null if we have to ignore the duplication.
- \<object-uuid> is the UUID of the found duplicate.

For example, if an item exists with the following metadata
```json
{
 "id": "9db079fc-359f-4aa4-ad4f-2dd2f0afaac6",
 "uuid": "9db079fc-359f-4aa4-ad4f-2dd2f0afaac6",
 "name": "Sample submission",
 "handle": "123456789/28381",
 "metadata": [
   {
	 "key": "dc.contributor.author",
	 "value": "Francesco, Cadili",
	 "language": null,
	 "authority": "rp05896",
	 "confidence": 600
   },
   {
	 "key": "dc.contributor.editor",
	 "value": "Matteo, Perelli",
	 "language": null,
	 "authority": "rp05897",
	 "confidence": 600
   },
   ...
   {
	   "key": "dc.title",
	   "value": "Sample submission",
	   "language": null,
	   "authority": null,
	   "confidence": -1
	 }
   ],
   "inArchive": true,
   "discoverable": true,
   "withdrawn": false,
   "lastModified": "2020-01-31T10:53:37.227+0000",
   "type": "item",
   "uniqueType": "core.item"
}	
```

and we are submitting a workspace item:
```json
{
  "id": 43366,
  ...,
  "_embedded": {
    ...,
    "item": {
      "id": "8a4d8ed1-9ad0-413f-8122-710231bea28e",
      "uuid": "8a4d8ed1-9ad0-413f-8122-710231bea28e",
      "name": "sample submission",
      "handle": null,
      "metadata": [
        {
          "key": "dc.title",
          "value": "sample submission",
          "language": null,
          "authority": null,
          "confidence": -1
        }
      ],
      "inArchive": false,
      "discoverable": true,
      "withdrawn": false,
      "lastModified": "2020-01-31T11:58:03.355+0000",
      "type": "item",
      "_links": {
        "bitstreams": {
          "href": "http://localhost:8082/spring-rest/api/core/items/8a4d8ed1-9ad0-413f-8122-710231bea28e/bitstreams"
        },
        "owningCollection": {
          "href": "http://localhost:8082/spring-rest/api/core/items/8a4d8ed1-9ad0-413f-8122-710231bea28e/owningCollection"
        },
        "templateItemOf": {
          "href": "http://localhost:8082/spring-rest/api/core/items/8a4d8ed1-9ad0-413f-8122-710231bea28e/templateItemOf"
        },
        "self": {
          "href": "http://localhost:8082/spring-rest/api/core/items/8a4d8ed1-9ad0-413f-8122-710231bea28e"
        }
      },
      "_embedded": {
        "owningCollection": null,
        "templateItemOf": null,
        "bitstreams": null
      }
    }
}
```

When submitting the workspace item 43366, for instance, its title, using the following patch (that is called under the hood during Dspace submission process)

`curl --data 'curl --data '[ { "op": "add", "path": "/sections/basicinformation/dc.title", "value": {"value": "sample submission", "language": null, "authority": null, "display": "sample submission", "confidence": -1, "place": 0,  "otherInformation": null}}]' -X PATCH ${dspace7-url}/api/submission/workspaceitems/43366`

The following duplicates are identified, listed inside detect-duplicate section:
```json
{
  "id": 43366,
  ...
  "sections": {
    ...,
    "detect-duplicate": {
      "matches": {
        "9db079fc-359f-4aa4-ad4f-2dd2f0afaac6": {
		      "submitterDecision" : null,
          "workflowDecision" : null,
          "adminDecision" : null,
          "submitterNote" : null,
          "workflowNote" : null,
          "matchObject": {
            "id": "9db079fc-359f-4aa4-ad4f-2dd2f0afaac6",
            "uuid": "9db079fc-359f-4aa4-ad4f-2dd2f0afaac6",
            "name": "Sample submission",
            "handle": "123456789/28381",
            "metadata": [
              {
                "key": "dc.contributor.author",
                "value": "Francesco, Cadili",
                "language": null,
                "authority": "rp05896",
                "confidence": 600
              },
              {
                "key": "dc.contributor.editor",
                "value": "Matteo, Perelli",
                "language": null,
                "authority": "rp05897",
                "confidence": 600
              },
              ...,
              {
                "key": "dc.title",
                "value": "Sample submission",
                "language": null,
                "authority": null,
                "confidence": -1
              },
              ...
            ],
            "inArchive": true,
            "discoverable": true,
            "withdrawn": false,
            "lastModified": "2020-01-31T10:53:37.227+0000",
            "type": "item",
            "uniqueType": "submission.workspaceitem"
          }
        }
      }
    },
    ...
  }
}
```
In section detect-duplicate is listed the item (9db079fc-359f-4aa4-ad4f-2dd2f0afaac6) since the dc.title is the same as our submitting workspace item. 
Now we can state that the found duplicate is to be further analyzed adding a comment for the administrator or ignored. 

To report a note we use the following request:

`curl --data 'curl --data '[ { "op": "add", "path": "/sections/detect-duplicate/matches/9db079fc-359f-4aa4-ad4f-2dd2f0afaac6/submitterDecision", "value": {"value": "verify", "note": "check the title for duplicate"}}]' -X PATCH ${dspace7-url}/api/submission/workspaceitems/43366`

will result in
```json
{
  "id": 43366,
  ...
  "sections": {
    ...,
    "detect-duplicate": {
      "matches": {
        "9db079fc-359f-4aa4-ad4f-2dd2f0afaac6": {
		      "submitterDecision" : "verify",
          "workflowDecision" : null,
          "adminDecision" : null,
          "submitterNote" : "check the title for duplicate",
          "workflowNote" : null,
          "matchObject": {
            "id": "9db079fc-359f-4aa4-ad4f-2dd2f0afaac6",
            "uuid": "9db079fc-359f-4aa4-ad4f-2dd2f0afaac6",
            "name": "Sample submission",
            "handle": "123456789/28381",
            "metadata": [
              {
                "key": "dc.contributor.author",
                "value": "Francesco, Cadili",
                "language": null,
                "authority": "rp05896",
                "confidence": 600
              },
              {
                "key": "dc.contributor.editor",
                "value": "Matteo, Perelli",
                "language": null,
                "authority": "rp05897",
                "confidence": 600
              },
              ...,
              {
                "key": "dc.title",
                "value": "Sample submission",
                "language": null,
                "authority": null,
                "confidence": -1
              },
              ...
            ],
            "inArchive": true,
            "discoverable": true,
            "withdrawn": false,
            "lastModified": "2020-01-31T10:53:37.227+0000",
            "type": "item",
            "uniqueType": "core.item"
          }
        }
      }
    },
    ...
  }
}
```
Where the result of the path operation is underlined by the submitterDecision field set to "verify" and the submitterNote set to "check the title for duplicate".

if instead, we wanted to ignore the duplicate we would have run the following command:

`curl --data 'curl --data '[ { "op": "add", "path": "/sections/detect-duplicate/matches/9db079fc-359f-4aa4-ad4f-2dd2f0afaac6/submitterDecision", "value": {"value": "reject", "note": null}}]' -X PATCH ${dspace7-url}/api/submission/workspaceitems/43366`

and we would have achieved the following result:
```json
{
  "id": 43366,
  ...
  "sections": {
    ...,
    "detect-duplicate": {
      "matches": {
        "9db079fc-359f-4aa4-ad4f-2dd2f0afaac6": {
		      "submitterDecision" : "reject",
          "workflowDecision" : null,
          "adminDecision" : null,
          "submitterNote" : null,
          "workflowNote" : null,
          "matchObject": {
            "id": "9db079fc-359f-4aa4-ad4f-2dd2f0afaac6",
            "uuid": "9db079fc-359f-4aa4-ad4f-2dd2f0afaac6",
            "name": "Sample submission",
            "handle": "123456789/28381",
            "metadata": [
              {
                "key": "dc.contributor.author",
                "value": "Francesco, Cadili",
                "language": null,
                "authority": "rp05896",
                "confidence": 600
              },
              {
                "key": "dc.contributor.editor",
                "value": "Matteo, Perelli",
                "language": null,
                "authority": "rp05897",
                "confidence": 600
              },
              ...,
              {
                "key": "dc.title",
                "value": "Sample submission",
                "language": null,
                "authority": null,
                "confidence": -1
              },
              ...
            ],
            "inArchive": true,
            "discoverable": true,
            "withdrawn": false,
            "lastModified": "2020-01-31T10:53:37.227+0000",
            "type": "item",
            "uniqueType": "core.item"
          }
        }
      }
    }
    ...
  }
}
```
Where the result of the path operation is underlined by the submitterDecision field set to "reject".

### Replace
The operation is not implemented. The replace operation will be performed with a sequence of Add and Remove operations.

### Remove
To be implemented.

### Move, Test & copy
No plan to implement the move, test and copy operations at the current stage
