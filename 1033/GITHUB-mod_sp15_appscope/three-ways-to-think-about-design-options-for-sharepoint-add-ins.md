---
title: Three ways to think about design options for SharePoint Add-ins
ms.prod: SHAREPOINT
ms.assetid: 0942fdce-3227-496a-8873-399fc1dbb72c
---


# Three ways to think about design options for SharePoint Add-ins
Get an overview of the design and architecture options that are available with SharePoint Add-ins. You should first be familiar with the article  [SharePoint Add-ins](sharepoint-add-ins.md).
## Overview and the first decision
<a name="Overview"> </a>

This article looks at the architectural choices for SharePoint Add-ins in three different ways. First, you learn about the most important categories of design choices; second; you view add-in architecture in terms of application tiers; and, third, you see a set of factors you need to consider when making your design choices.



But the first decision to make is whether your SharePoint extension should be a SharePoint Add-in or a classic SharePoint farm solution or sandboxed solution. Some parts of the SharePoint object model, mainly connected with customizing SharePoint administration and security, are not accessible from clients. Only custom code running on the SharePoint server can access them, and custom server-side code is not allowed in a SharePoint Add-in. (A rich set of client object models and a REST/OData service make it possible for SharePoint Add-ins to do almost any end-user-oriented SharePoint extension.) For more information about deciding between classic solutions and add-ins, see  [SharePoint Add-ins compared with SharePoint solutions](http://msdn.microsoft.com/library/0e9efadb-aaf2-4c0d-afd5-d6cf25c4e7a8%28Office.15%29.aspx). Also helpful for making this decision is  [Choose the right API set in SharePoint 2013](http://msdn.microsoft.com/library/f36645da-77c5-47f1-a2ca-13d4b62b320d%28Office.15%29.aspx).




## Key elements in the design of SharePoint Add-ins
<a name="MajorCategoriesOfChoices"> </a>

There are three major categories of choices that need to be made when a SharePoint Add-in is designed. As always, application design involves trade-offs; choices you make in one category may limit your options in another. Not every possible combination of choices is feasible.




- **Hosting:** SharePoint Add-ins can be usefully divided into two major types based on how they are deployed and hosted.

  - **Provider-hosted** add-ins have their primary data storage and business logic deployed and hosted by you—the developer—outside of SharePoint in servers or a cloud account that you provide. You are responsible for enforcing isolation between the accounts of the various customers who purchase your add-in. Such add-ins can have SharePoint components too. These are hosted in the customer's SharePoint farm. This type of add-in provides you with the most flexibility in the other categories of design choices. It also enables you to use non-Microsoft platforms for the external data, logic, and web user interface (UI). (Within the category of provider-hosted add-ins, you also need to distinguish between add-ins whose remote components are within the same corporate firewall as the SharePoint farm and those whose remote components are outside of that firewall. The authorization systems for these two scenarios are different, which, in turn, makes a difference in which programming language you use to access the SharePoint data..)


  - **SharePoint-hosted** add-ins consist entirely of SharePoint 2013 components, such as lists, content types, workflows, and Web Parts. There are no external components. For more information about the kinds of SharePoint components that can be included in SharePoint Add-ins, see [Host webs, add-in webs, and SharePoint components in SharePoint 2013](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md).



    For more detailed information about the hosting options of SharePoint Add-ins, see  [Choose patterns for developing and hosting your SharePoint Add-in](choose-patterns-for-developing-and-hosting-your-sharepoint-add-in.md).


- **Connectivity:** SharePoint 2013 supports three kinds of secure create/read/update/delete (CRUD) access to data.

  - External web applications in an add-in use the OAuth protocol to access SharePoint data. For more information, see  [Authorization and authentication of SharePoint Add-ins](authorization-and-authentication-of-sharepoint-add-ins.md).


  - JavaScript can access data in a SharePoint add-in web and data on other websites within the same tenancy by using a special JavaScript library that enables secure cross-domain scripting. For more information, see  [Access SharePoint 2013 data from add-ins using the cross-domain library](access-sharepoint-2013-data-from-add-ins-using-the-cross-domain-library.md).


  - A SharePoint Add-in can also access external data through Business Connectivity Services (BCS) or a web service proxy. For more information, see  [Business Connectivity Services in SharePoint 2013](http://msdn.microsoft.com/library/64b7d032-4b83-4e9e-bc08-f0a161af5457%28Office.15%29.aspx) and [Query a remote service using the web proxy in SharePoint 2013](query-a-remote-service-using-the-web-proxy-in-sharepoint-2013.md).



    For more information on data storage and access in SharePoint Add-ins, see  [Data storage in SharePoint Add-ins](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md#Data),  [Secure data access and client object models for SharePoint Add-ins](secure-data-access-and-client-object-models-for-sharepoint-add-ins.md), and  [Work with external data in SharePoint 2013](work-with-external-data-in-sharepoint-2013.md).


- **UI:** There are three ways to surface a SharePoint Add-in in SharePoint: at a minimum, all add-ins are surfaced in a full web page. Optionally, an add-in can also be surfaced through an add-in part, and through a menu item or ribbon button. For more information, see [UX design for SharePoint Add-ins](ux-design-for-sharepoint-add-ins.md).

    > **NOTE**
    > SharePoint Add-ins can be installed by your customers to multiple site collections in a tenancy, or on a website-by-website basis. The former are called tenant-scoped add-ins. If you want your customers to have the tenant-scoped option, you may not include a custom ribbon button or an add-in part. For more information, see  [Tenancies and deployment scopes for SharePoint Add-ins](tenancies-and-deployment-scopes-for-sharepoint-add-ins.md). 

## Architectural tiers
<a name="Tiers"> </a>

Another way to think about your add-in architecture options is to think of the add-in as having three logical tiers: the UI, the business logic, and the data access. Each layer has multiple implementation options; again, choices made for one layer limit the options for others. The following tables describe some of the options, and their uses, for the remote components of an add-in and the SharePoint components.




**Remote components in provider-hosted add-ins: options for each tier**


|**Tier**|**Options**|**Good for**|
|:-----|:-----|:-----|
|UI  <br/> |ASP.NET pages in an ASP.NET forms or MVC application that is hosted in a Microsoft Azure web role.  <br/> |Leveraging the skills of an ASP.NET development staff.  <br/> |
||HTML 5 page with JavaScript.  <br/> |Rich user interface.  <br/> |
||PHP or other kind of web page hosted in a non-Microsoft cloud service.  <br/> |Integrating non-Microsoft applications into SharePoint.  <br/> |
||Silverlight in a Windows Phone app.  <br/> |Mobile access to SharePoint data and integration with geolocation data and push notifications.  <br/> |
|Business logic  <br/> |Client-side JavaScript.  <br/> |UI logic and light business logic.  <br/> Accessing SharePoint data through the JavaScript client object model.  <br/> |
||A Microsoft Azure worker role.  <br/> |Processor-intensive functionality.  <br/> Accessing SharePoint data through the .NET Framework client object model.  <br/> |
||A remote web service.  <br/> |Processor-intensive functionality.  <br/> Accessing SharePoint data through the .NET Framework client object model.  <br/> |
|Data  <br/> |SQL Azure.  <br/> |Full-featured relational data.  <br/> |
||Microsoft Azure table storage.  <br/> |Application settings and other metadata.  <br/> |
||Microsoft Azure blob storage.  <br/> |Storage of large files.  <br/> |
||A non-Microsoft cloud service.  <br/> |Leveraging existing data sources that are based on non-Microsoft platforms.  <br/> |
||A database on the developer's own server.  <br/> |Provider-hosting and developer control of tenancy isolation.  <br/> |
 

**SharePoint components: options for each tier**


|**Tier**|**Options**|**Good for**|
|:-----|:-----|:-----|
|UI  <br/> |Custom views of SharePoint 2013 lists and libraries on add-in web pages.  <br/> |Maximizing integration with SharePoint appearance and behavior.  <br/> |
||Silverlight application hosted in a Web Part (or within <object> tags) on an add-in web page.  <br/> |Leveraging existing Silverlight development experience.  <br/> Rich user interface.  <br/> |
|Business logic  <br/> |A SharePoint workflow.  <br/> |Implementing business processes.  <br/> |
||Client-side JavaScript supplemented with the SharePoint cross-domain library.  <br/> |Accessing SharePoint data in the add-in web.  <br/> Accessing data in other websites within the tenancy.  <br/> |
||A remote event handler.  <br/> |Handling CRUD events in SharePoint lists and libraries using externally hosted logic.  <br/> |
|Data  <br/> |SharePoint 2013 lists and libraries that are queried through Collaborative Application Markup Language (CAML), or LINQ, queries with one of the SharePoint client object models.  <br/> |Leveraging existing SharePoint and .NET Framework development experience.  <br/> |
||SharePoint 2013 lists and libraries that are queried through the SharePoint REST/OData web service.  <br/> |Accessing SharePoint data from non-Microsoft platforms.  <br/> Leveraging existing OData query experience.  <br/> |
||A BCS Model.  <br/> |Surfacing external data in SharePoint as a SharePoint list.  <br/> |
 

## Factors to consider when making your design decisions
<a name="DecisionFactors"> </a>

The SharePoint Add-in model enables so many possibilities for design that a simple decision tree is not possible. The following are some of the most important factors to consider when constructing the architecture of a SharePoint Add-in.




- Most importantly, of course, is the functionality you want to make available to customers—the use cases. For example, if your add-in includes processor-intensive functions, such as converting video files to another video format, that would be argument for creating a provider-hosted add-in in which the processing is done on one of your servers or a Microsoft Azure worker role.


- Because one kind of SharePoint Add-in, provider-hosted add-ins, requires you (or your customer) to host the non-SharePoint components and to enforce tenant isolation, you need to consider whether you have the hardware and IT staff to do this (or whether your targeted customers do).


- Which customers you are targeting is also a crucial consideration. If all your add-ins will be used in-house (that is, you have no external customers) or used by a single customer, provider-hosted add-ins are significantly easier to implement and maintain than when you have external customers or multiple customers will use the add-in. If you intend to sell the add-in publicly, you should also consider whether you will market it to businesses that have SharePoint Online accounts or those with their own SharePoint farms, or both.


- You should also consider your existing skills or the skills of your development staff. For example, if you are an experienced ASP.NET developer, that would be a point in favor of creating a remote web application and surfacing SharePoint list data on an ASP.NET page. On the other hand, if you are an experienced SharePoint developer, that would be a point in favor of using a custom SharePoint list and site page, with JavaScript to perform processing.



## Additional resources
<a name="AdditionalResources"> </a>


-  [Design SharePoint Add-ins](design-sharepoint-add-ins.md)


-  [Important aspects of the SharePoint Add-in architecture and development landscape](important-aspects-of-the-sharepoint-add-in-architecture-and-development-landscap.md)



