---
title: "Composite API"
sort: 2
---

# Using Composite Resources

[Source Link](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/using_composite_resources.htm)

The examples in this section use composite resources to improve your application’s performance by minimizing the number of round-trips between the client and server.

- **Execute Dependent Requests in a Single API Call**

    The following example uses the Composite resource to execute several dependent requests all in a single call. First, it creates an account and retrieves its information. Next it uses the account data and the Composite resource’s reference ID functionality to create a contact and populate its fields based on the account data. Then it retrieves specific information about the account’s owner by using query parameters in the request string. Finally, if the metadata has been modified since a certain date, it retrieves account metadata. The composite.json file contains the composite request and subrequest data.

- **Update an Account, Create a Contact, and Link Them with a Junction Object**

    The following example uses the Composite resource to update some fields on an account, create a contact, and link the two records with a junction object called AccountContactJunction. All these requests are executed in a single call. The composite.json file contains the composite request and subrequest data.

- **Update a Record and Get Its Field Values in a Single Request**

    Use the Composite Batch resource to execute multiple requests in a single API call.

- **Upsert an Account and Create a Contact**

    The following example uses the Composite resource to upsert an account and create a contact that is linked to the account. All these requests are executed in a single call. The composite.json file contains the composite request and subrequest data.

- **Create Nested Records**

    Use the sObject Tree resource to create nested records that share a root record type. For example, in a single request, you can create an account along with its child contacts, and a second account along with its child accounts and contacts. Once the request is processed, the records are created and parents and children are automatically linked by ID. In the request data, you supply the record hierarchies, required and optional field values, each record’s type, and a reference ID for each record, and then use the POST method of the resource. The response body will contain the IDs of the created records if the request is successful. Otherwise, the response contains only the reference ID of the record that caused the error and the error information.

- **Create Multiple Records**

    While the resource can be used to create nested records, you can also create multiple, unrelated records of the same type. In a single request, you can create up to two hundred records. In the request data, you supply the required and optional field values for each record, each record’s type, and a reference ID for each record, and then use the POST method of the resource. The response body will contain the IDs of the created records if the request is successful. Otherwise, the response contains only the reference ID of the record that caused the error and the error information.

- **Using Composite Graphs**

    Composite graphs provide an enhanced way to perform composite requests, which execute a series of REST API requests in a single call.

- **Using a Composite Graph**

    This example shows how to use a composite graph. It also demonstrates how one request can use more than one composite graph.

- **allOrNone Parameters in Composite and Collections Requests**

    If a Composite request uses sObject Collections, there are two or more allOrNone parameters that can interact, one on the Composite request and one on each sObject Collections subrequest.
