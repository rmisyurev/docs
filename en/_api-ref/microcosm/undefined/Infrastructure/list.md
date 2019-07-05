---
editable: false
---

# Method list

 

 
## HTTP request {#https-request}
```
GET undefined/microcosm/v1/infrastructures
```
 
## Query parameters {#query_params}
 
Parameter | Description
--- | ---
folderId | Required. The maximum string length in characters is 50.
pageSize | The maximum value is 1000.
pageToken | The maximum string length in characters is 100.
filter | The maximum string length in characters is 1000.
 
## Response {#responses}
**HTTP Code: 200 - OK**

```json 
{
  "infrastructures": [
    {
      "id": "string",
      "name": "string",
      "folderId": "string",
      "createdAt": "string",
      "status": "string",
      "labels": "object",
      "variables": "object",
      "infrastructureTemplateId": "string"
    }
  ],
  "nextPageToken": "string"
}
```

 
Field | Description
--- | ---
infrastructures[] | **object**<br>
infrastructures[].<br>id | **string**<br>
infrastructures[].<br>name | **string**<br>
infrastructures[].<br>folderId | **string**<br>
infrastructures[].<br>createdAt | **string** (date-time)<br><p>String in <a href="https://www.ietf.org/rfc/rfc3339.txt">RFC3339</a> text format.</p> 
infrastructures[].<br>status | **string**<br>
infrastructures[].<br>labels | **object**<br>
infrastructures[].<br>variables | **object**<br>
infrastructures[].<br>infrastructureTemplateId | **string**<br>
nextPageToken | **string**<br>