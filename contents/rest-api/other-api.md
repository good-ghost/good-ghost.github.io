---
title: "Org 관련 REST API"
sort: 1
---

# Org 관련 REST API

## List Available REST API Versions

Use the Versions resource to list summary information about each REST API version currently available, including the version, label, and a link to each version's root. You don’t need authentication to retrieve the list of versions.

**GET** `instanceUrl` + `/services/data/`

````result
``` json
[
    {
        "label" : "Spring '11",
        "url" : "/services/data/v21.0",
        "version" : "21.0"
    },
    {
        "label" : "Summer '11",
        "url" : "/services/data/v22.0",
        "version" : "22.0"
    },
    {
        "label" : "Winter '12",
        "url" : "/services/data/v23.0",
        "version" : "23.0"
    }
    ...
]
```
````

## List Org Limits

- Use the Limits resource to list your org limits.
- Max is the limit for the org.

Remaining is the number of calls or events left for the org.

**GET** `instanceUrl` + `/services/data/v60.0/limits/`

*add header* `X-PrettyPrint:1`

````result
``` json
{
    "ActiveScratchOrgs": {
        "Max": 3,
        "Remaining": 3
    },
    "AnalyticsExternalDataSizeMB": {
        "Max": 40960,
        "Remaining": 40960
    },
    "ConcurrentAsyncGetReportInstances": {
        "Max": 200,
        "Remaining": 200
    },
    ...
}
```
````

## List Available REST Resources

Use the Resources by Version resource to list the resources available for the specified API version. This provides the name and URI of each additional resource.

**GET** `instanceUrl` + `/services/data/v60.0/`

````result
``` json
{
   "tooling" : "/services/data/v60.0/tooling",
   "metadata" : "/services/data/v60.0/metadata",
   "eclair" : "/services/data/v60.0/eclair",
   "folders" : "/services/data/v60.0/folders",
   "prechatForms" : "/services/data/v60.0/prechatForms",
   "contact-tracing" : "/services/data/v60.0/contact-tracing",
   ...
}
```
````

## Get a List of Objects

Use the Describe Global resource to list the objects available in your org and available to the logged-in user. This resource also returns the org encoding, as well as maximum batch size permitted in queries.

**GET** `instanceUrl` + `/services/data/v60.0/sobjects/`

````result
``` json
{ 
  "encoding" : "UTF-8", 
  "maxBatchSize" : 200, 
  "sobjects" : [ { 
    "activateable" : false, 
    "custom" : false,
    "customSetting" : false, 
    "createable" : true, 
    "deletable" : true, 
    "deprecatedAndHidden" : false, 
    "feedEnabled" : true, 
    "keyPrefix" : "001", 
    "label" : "Account", 
    "labelPlural" : "Accounts", 
    "layoutable" : true, 
    "mergeable" : true, 
    "mruEnabled" : true, 
    "name" : "Account", 
    "queryable" : true, 
    "replicateable" : true, 
    "retrieveable" : true, 
    "searchable" : true, 
    "triggerable" : true, 
    "undeletable" : true,
    "updateable" : true, 
    "urls" : { 
      "sobject" : "/services/data/v60.0/sobjects/Account", 
      "describe" : "/services/data/v60.0/sobjects/Account/describe", 
      "rowTemplate" : "/services/data/v60.0/sobjects/Account/{ID}" 
    },
  }, 
  ...
  ]
}
```
````

## Get Metadata for an Object

Use the sObject Basic Information resource to get metadata for an object.

**GET** `instanceUrl` + `/services/data/v60.0/sobjects/Account/`

````result
``` json
{
  "objectDescribe" :
  {
    "name" : "Account",
    "updateable" : true,
    "label" : "Account",
    "keyPrefix" : "001",

    ...

    "replicateable" : true,
    "retrieveable" : true,
    "undeletable" : true,
    "triggerable" : true
  },
  "recentItems" :
  [
    {
      "attributes" :
      {
        "type" : "Account",
        "url" : "/services/data/v60.0/sobjects/Account/001D000000INjVeIAL"
      },
      "Id" : "001D000000INjVeIAL",
      "Name" : "asdasdasd"
    },

    ...

  ]
}
```
````

## Get Field and Other Metadata for an Object

Use the sObject Describe resource to retrieve all the metadata for an object, including information about each field, URLs, and child relationships.

**GET** `instanceUrl` + `/services/data/v60.0/sobjects/Account/describe/`

````result
``` json
{
  "name" : "Account",
  "fields" :
  [
    {
      "length" : 18,
      "name" : "Id",
      "type" : "id",
      "defaultValue" : {    "value" : null  },
      "updateable" : false,
      "label" : "Account ID",
      ...
    },

    ...

  ],


  "updateable" : true,
  "label" : "Account",
  "keyPrefix" : "001",
  "custom" : false,

  ...

  "urls" :
  {
    "uiEditTemplate" : "https://MyDomainName.my.salesforce.com/{ID}/e",
    "sobject" : "/services/data/v60.0/sobjects/Account",
    "uiDetailTemplate" : "https://MyDomainName.my.salesforce.com/{ID}",
    ...
  },

  "childRelationships" :
  [
    {
      "field" : "ParentId",
      "deprecatedAndHidden" : false,
      ...
    }, 

    ....

  ],

  "createable" : true,
  "customSetting" : false,
  ...
}
```
````

