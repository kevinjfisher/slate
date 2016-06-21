---
title: Checkbox Web Services

language_tabs:
  - csharp : C#
  - vb : Visual Basic

toc_footers:
  - <a target = "_blank" href="https://www.checkbox.com/support">Submit a support ticket</a>
  - <a target = "_blank" href="https://www.checkbox.com/plans-and-pricing/free-trial/">Sign up for a free trial of Checkbox</a>
  - <a target = "_blank" href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>


search: true
---

# Introduction

Welcome to the Checkbox Web Services documentation page. Here you will find some basic documentation for some of our most used API methods as well as some shot code snippets which demonstrate how to utilize the method in actual code.

For ease of use this documentation should always reflect the most recent version of our Checkbox Online platform, which is currently at Checkbox 6 - 2016 Q2. If you are currently on an older version of Checkbox some or all of the documentation or examples here may not reflect your installation.

If you have any issues with the documentation or need any further assistance please feel free to submit a support ticket here : [Checkbox Support](https://www.checkbox.com/support)

You can also find some code samples using the Checkbox API here : [Web Service Samples](https://github.com/PrezzaTechnologies/Developer-Samples)

<aside class="warning"> This documentation page is currently in development and does not contain documentation for all of the features of the Checkbox API</aside>

# Getting Started

## API Information

Checkbox 6 Web Services are built with the Windows Communication Foundation (WCF). We recommend building your application using the latest version of the .NET Framework (version 4.6 at the time of this document’s release) in order to take advantage of improvements in the WCF stack.

Each of our Web Services have a Web Services Description Language (WSDL), which informs the client how to communicate with the service. This means that the services may be accessed using a number of different clients (environments and languages, i.e. Java). It is up to you, the developer, to reference the Web Services appropriately. In the documentation on this website all services should have the endpoint to utilize referenced at the bottom of the section.

## Checking if the API is Live

The first thing you will want to do is ensure that your Web Services are installed correctly and can be accessed by your application. The easiest way to do that is to type a service’s URL into a web browser.

Let’s take a look at the User Management Service by visiting the following URL:

http://[CHECKBOX URL]/Services/UserManagementService.svc

<aside class="notice">
You must replace <code>[yourdomain]</code> with the domain of your Checkbox installation.
</aside>


## Adding a Service Reference

>Consuming the client

```csharp
var proxy = new UserManagementServiceClient();
var result = proxy.GetUsersInRole(authenticationToken,role,pageNumber,
    pageSize,sortField,sortAscending,filterField,filterValue);

// Handle errors
if (!result.CallSuccess)
{
    Console.WriteLine(result.FailureMessage);
    return null;
}

return result.ResultData.ResultPage;
```
```vb
Dim proxy = New UserManagementServiceClient()
Dim result = proxy.GetUsersInRole(authenticationToken, role, pageNumber, pageSize, sortField, sortAscending, _
	filterField, filterValue)

' Handle errors
If Not result.CallSuccess Then
	Console.WriteLine(result.FailureMessage)
	Return Nothing
End If

Return result.ResultData.ResultPage
```

Note: we are using C# and Visual Studio 2010 for this example, but you may use whatever environment or language you would like.


1. Create a new project or open an existing one
2. Right click the “References” folder
3. Find and select “Add Service Reference” (for .NET Framework 3.0 and up) or “Add Web Reference” (for .NET Framework 2.0 and 1.1).
4. Enter the address for the service you want to use
5. Click “Go”
6. Select the contract you want to use
7. Enter a name for the proxy class that will be created
8. Click “OK”



# Authentication

The Checkbox API requires an authentication token (AuthToken) in order to make API calls. In order to obtain an AuthToken you can access the AuthenticationService.Login method using your username and password. AuthTokens are on a rolling 20 minute expiration timer. This means that your token will last for 20 minutes after the last API call was made utilizing it.

AuthTokens inherit the permissions of the user that they were obtained for. This means that you will be able to access all data using this token that you would if you were logged into the application as them.


`Authentication Endpoint: https://[yourdomain]/services/authenticationservice.svc`


<aside class="notice">
You must replace <code>[yourdomain]</code> with the domain of your Checkbox installation.
</aside>

## Login


The login method will take the username and password supplied, and if valid, return an authtoken. This authtoken will expire after 20 minutes of not being utilized, or if a new authtoken is requested for the user.

The authtoken assumes all permissions of the user that was authenticated in order to retrieve it.
> Example Request

```csharp
class Test
{
    static void Main()
    {
        AuthenticationServiceClient client = new AuthenticationServiceClient();

        authToken = client.Login("admin", "password").ResultData;

        // Always close the client.
        client.Close();
    }
}

```
```vb
Class Test
    Shared Sub Main()
        Dim client As AuthenticationServiceClient = New AuthenticationServiceClient()

        Dim authToken as string = client.Login("admin", "password").ResultData

        'Always close the client
        client.Close()
    End Sub
End Class
```


> The above request returns XML structured like this

```xml
<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
   <s:Body>
      <LoginResponse xmlns="http://tempuri.org/">
         <LoginResult xmlns:a="http://schemas.datacontract.org/2004/07/Checkbox.Wcf.Services.Proxies"
         xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
            <a:CallSuccess>true</a:CallSuccess>
            <a:FailureExceptionType i:nil="true"/>
            <a:FailureMessage i:nil="true"/>
            <a:ResultData>d6ffaabe-e9b1-4922-a18f-a9dc1e3a6342</a:ResultData>
         </LoginResult>
      </LoginResponse>
   </s:Body>
</s:Envelope>

```

### Input Parameters

Parameter | Type | Description
--------- | ------- | -----------
username | string | Username to obtain the authToken on behalf of
password | string | Password used to authenticate the username supplied

# Responses

The following service allows you to get response data from for your surveys in different formats, and with different filtering options.

In order to utilize this service you will need an AuthToken from the authentication service, and you will need access to the responses within the Checkbox application.

`Response Data Endpoint: https://[yourdomain]/services/responsedataservice.svc`

<aside class="notice">
You must replace <code>[yourdomain]</code> with the domain of your Checkbox installation.
</aside>

## ExportResponsesTabular

The ExportReponsesTabular method will return filtered or unfiltered response data in the format of an IENumerable SimpleNameValueCollection format which is easy to write to CSV files or insert into databases. The Name will always be the question or property, and the value will always be the answer or value or the field.

> Example Request

```csharp
class Test
{
    static void Main()
    {
        ResponseDataServiceClient client = new ResponseDataServiceClient();

        // Use the 'client' variable to call operations on the service.
        var responseData = client.ExportResponsesTabular("e2e8c988-996a", 1001, 0, 0, "UniqueIdentifier", "admin", null,
        true, 1, "2016-01-01", "2017-01-01",false, false, false, false,false, false, false,false, false, false);

        // Always close the client.
        client.Close();
    }
}

```

```vb
Class Test
	Private Shared Sub Main()
		Dim client As New ResponseDataServiceClient()

		' Use the 'client' variable to call operations on the service.
		Dim responseData = client.ExportResponsesTabular("e2e8c988-996a", 1001, 0, 0, "UniqueIdentifier", "admin", _
			Nothing, True, 1, "2016-01-01", "2017-01-01", False, _
			False, False, False, False, False, False, _
			False, False, False)

		' Always close the client.
		client.Close()
	End Sub
End Class
```

> The above request returns JSON structured like this

```json
{  
   "d":{  
      "__type":"ServiceOperationResultOfPagedListResultOfTabularResponseExportDataVUqw3SxCVUqw3SxC:#Checkbox.Wcf.Services.Proxies",
      "CallSuccess":true,
      "FailureExceptionType":null,
      "FailureMessage":null,
      "ResultData":{  
         "__type":"PagedListResultOfTabularResponseExportDataVUqw3SxC:#Checkbox.Wcf.Services.Proxies",
         "ResultPage":{  
            "__type":"TabularResponseExportData:#Checkbox.Wcf.Services.Proxies",
            "Respondents":null,
            "Responses":[  
               {  
                  "__type":"SimpleNameValueCollection:#Checkbox.Wcf.Services.Proxies",
                  "NameValueList":[  
                     {  
                        "__type":"SimpleNameValue:#Checkbox.Wcf.Services.Proxies",
                        "Name":"Id",
                        "Value":"4393"
                     },
                     {  
                        "__type":"SimpleNameValue:#Checkbox.Wcf.Services.Proxies",
                        "Name":"What is your favorite color?",
                        "Value":"Blue"
                     }
                  ]
               },
               {  
                  "__type":"SimpleNameValueCollection:#Checkbox.Wcf.Services.Proxies",
                  "NameValueList":[  
                     {  
                        "__type":"SimpleNameValue:#Checkbox.Wcf.Services.Proxies",
                        "Name":"Id",
                        "Value":"4394"
                     },
                     {  
                        "__type":"SimpleNameValue:#Checkbox.Wcf.Services.Proxies",
                        "Name":"What is your favorite color?",
                        "Value":"Green"
                     }
                  ]
               }
            ]
         },
         "TotalItemCount":2
      }
   }
}
```



> The above request returns XML structured like this

```xml
<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
   <s:Body>
      <ExportResponsesTabularResponse xmlns="http://tempuri.org/">
         <ExportResponsesTabularResult xmlns:a="http://schemas.datacontract.org/2004/07/Checkbox.Wcf.Services.Proxies" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
            <a:CallSuccess>true</a:CallSuccess>
            <a:FailureExceptionType i:nil="true"/>
            <a:FailureMessage i:nil="true"/>
            <a:ResultData>
               <a:ResultPage>
                  <a:Respondents i:nil="true"/>
                  <a:Responses>
                     <a:SimpleNameValueCollection>
                        <a:NameValueList>
                           <a:SimpleNameValue>
                              <a:Name>Id</a:Name>
                              <a:Value>4393</a:Value>
                           </a:SimpleNameValue>
                           <a:SimpleNameValue>
                              <a:Name>What is your favorite color?</a:Name>
                              <a:Value>Blue</a:Value>
                           </a:SimpleNameValue>
                        </a:NameValueList>
                     </a:SimpleNameValueCollection>
                     <a:SimpleNameValueCollection>
                        <a:NameValueList>
                           <a:SimpleNameValue>
                              <a:Name>Id</a:Name>
                              <a:Value>4394</a:Value>
                           </a:SimpleNameValue>
                           <a:SimpleNameValue>
                              <a:Name>What is your favorite color?</a:Name>
                              <a:Value>Green</a:Value>
                           </a:SimpleNameValue>
                        </a:NameValueList>
                     </a:SimpleNameValueCollection>
                  </a:Responses>
               </a:ResultPage>
               <a:TotalItemCount>2</a:TotalItemCount>
            </a:ResultData>
         </ExportResponsesTabularResult>
      </ExportResponsesTabularResponse>
   </s:Body>
</s:Envelope>
```

### Input Parameters

Parameter | Type | Description
--------- | ------- | -----------
authToken | string | Authentication token to validate permission over the data requested
surveyId | int | Survey Identifier this is either a 4 or 5 digit number
pageNumber | int | Page number to pull from responses supplying 0 will pull all pages at once
resultsPerpage | int | Number of results to pull into a single page of responses
filterField | string | Field to filter response data on. Valid values are : UniqueIdentifier, Invitee, ResponseID, Guid, RespondentGuid, StartDate
filterValue | string | Value to filter the FilterField on
sortField | string | Field to sort the data returned on. Valid values are : UniqueIdentifier, Invitee, ResponseID, Guid, RespondentGuid, StartDate
sortAscending | bool | True will sort the data ascending, false will sort the data descending
period | int | Value should be equal to 1 (Field is used internally)
dtStart | DateTime | DateTime to be used as the response Start date
dtEnd | DateTime | DateTime to be used as the response End Date
DetailedReponseInfo | bool | True will pull detailed response information which includes response meta data
DetailedUserInfo | bool | True will pull detailed user information such as the entire user profile of the respondents
IncludeOpenEndedResults | bool | True will pull open ended responses. False will not include any single line or multi line questions / answers
IncludeAliases | bool | True will replace Questions / Answers with alias values. If no alias value is present for a question it will pull the full question / answer text
IncludeHiddenItems | bool | True will pull hidden item data collected from the "Hidden Item Page" of the survey
IncludeIncompleteResponses | bool | True will pull all incomplete responses. Incomplete responses cannot be filtered on by EndDate. This means that if this is set to true, all responses will be pulled regardless of start date.
StripHTMLTagsFromAnswers | bool | True will remove all styling and HTML code from response answers
StripHTMLTagsFromQuestions | bool | True will remove all styling and HTML code from survey questions
MergeAnswersForSelectMany | bool | True will return checkbox item answers in the format of A,B,C where A B C are all options for the question. False will return checkbox item answers in seperate key-value pairs.
includeScoreData | bool | True will return detailed scoring information such as total score, score per page, and possible score per page

# Surveys

The following service allows you to manage and retrieve data about your surveys.

In order to utilize this service you will need an AuthToken form the authentication service, and you will need access to the surveys you are trying to pull from Checkbox.

`Survey Management Endpoint: https://[yourdomain]/services/surveymanagementservice.svc`

<aside class="notice">
You must replace <code>[yourdomain]</code> with the domain of your Checkbox installation.
</aside>

## ListAvailableSurveys

The ListAVailableSurveys method can be used to pull a list of all surveys that the user has access to. This method will pull any survey the user has access to, even if they just have access to complete a response for the survey. They may not have edit or management access to the surveys that are returned.

> Example Request

```csharp
class Test
{
    static void Main()
    {
        SurveyManagementService client = new SurveyManagementService();

        // Use the 'client' variable to call operations on the service.
        var surveylist = ListAvailableSurveys("e2e8c988-996a", 0, 100, null, false, null, null);

        // Always close the client.
        client.Close();
    }
}

```
```vb
Class Test
	Private Shared Sub Main()
		Dim client As New SurveyManagementService()

		' Use the 'client' variable to call operations on the service.
		Dim surveylist = ListAvailableSurveys("e2e8c988-996a", 0, 100, Nothing, False, Nothing, _
			Nothing)

		' Always close the client.
		client.Close()
	End Sub
End Class
```
> The above request returns JSON structured like this

```json
{  
   "d":{  
      "__type":"ServiceOperationResultOfPagedListResultOfArrayOfSurveyListItemVUqw3SxCVUqw3SxC:#Checkbox.Wcf.Services.Proxies",
      "CallSuccess":true,
      "FailureExceptionType":null,
      "FailureMessage":null,
      "IsAuthenticated":true,
      "ResultData":{  
         "__type":"PagedListResultOfArrayOfSurveyListItemVUqw3SxC:#Checkbox.Wcf.Services.Proxies",
         "ResultPage":[  
            {  
               "__type":"SurveyListItem:#Checkbox.Wcf.Services.Proxies",
               "ChildItems":null,
               "ChildrenCount":0,
               "CompletedResponseCount":-1,
               "Creator":"admin",
               "ID":1348,
               "IncompleteResponseCount":0,
               "IsActive":false,
               "LastModified":"\/Date(1375886208740-0400)\/",
               "Name":"My first survey",
               "SurveyGuid":"0837109e-9edb-433d-a62a-7794a0ed850f",
               "TestResponseCount":0,
               "Type":"ResponseTemplate"
            },
            {  
               "__type":"SurveyListItem:#Checkbox.Wcf.Services.Proxies",
               "ChildItems":null,
               "ChildrenCount":0,
               "CompletedResponseCount":-1,
               "Creator":"admin",
               "ID":1309,
               "IncompleteResponseCount":0,
               "IsActive":false,
               "LastModified":"\/Date(1371126861453-0400)\/",
               "Name":"My second survey",
               "SurveyGuid":"0e08a4f5-a898-4f55-a069-fc1d92e4c48f",
               "TestResponseCount":0,
               "Type":"ResponseTemplate"
            }
         ],
         "TotalItemCount":554
      }
   }
}
```
> The above request returns XML structured like this

```xml
<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
   <s:Body>
      <ListAvailableSurveysResponse xmlns="http://tempuri.org/">
         <ListAvailableSurveysResult xmlns:a="http://schemas.datacontract.org/2004/07/Checkbox.Wcf.Services.Proxies" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
            <a:CallSuccess>true</a:CallSuccess>
            <a:FailureExceptionType i:nil="true"/>
            <a:FailureMessage i:nil="true"/>
            <a:IsAuthenticated>false</a:IsAuthenticated>
            <a:ResultData>
               <a:ResultPage>
                  <a:SurveyListItem>
                     <a:ChildItems i:nil="true"/>
                     <a:ChildrenCount>0</a:ChildrenCount>
                     <a:CompletedResponseCount>-1</a:CompletedResponseCount>
                     <a:Creator>admin</a:Creator>
                     <a:ID>1348</a:ID>
                     <a:IncompleteResponseCount>0</a:IncompleteResponseCount>
                     <a:IsActive>false</a:IsActive>
                     <a:LastModified>2013-08-07T10:36:48.74</a:LastModified>
                     <a:Name>My first survey</a:Name>
                     <a:SurveyGuid>0837109e-9edb-433d-a62a-7794a0ed850f</a:SurveyGuid>
                     <a:TestResponseCount>0</a:TestResponseCount>
                     <a:Type>ResponseTemplate</a:Type>
                  </a:SurveyListItem>
                  <a:SurveyListItem>
                     <a:ChildItems i:nil="true"/>
                     <a:ChildrenCount>0</a:ChildrenCount>
                     <a:CompletedResponseCount>-1</a:CompletedResponseCount>
                     <a:Creator>admin</a:Creator>
                     <a:ID>1309</a:ID>
                     <a:IncompleteResponseCount>0</a:IncompleteResponseCount>
                     <a:IsActive>false</a:IsActive>
                     <a:LastModified>2013-06-13T08:34:21.453</a:LastModified>
                     <a:Name>My second survey</a:Name>
                     <a:SurveyGuid>0e08a4f5-a898-4f55-a069-fc1d92e4c48f</a:SurveyGuid>
                     <a:TestResponseCount>0</a:TestResponseCount>
                     <a:Type>ResponseTemplate</a:Type>
                  </a:SurveyListItem>
               </a:ResultPage>
               <a:TotalItemCount>554</a:TotalItemCount>
            </a:ResultData>
         </ListAvailableSurveysResult>
      </ListAvailableSurveysResponse>
   </s:Body>
</s:Envelope>
```
### Input Parameters

Parameter | Type | Description
--------- | ------- | -----------
authToken | string | Authentication token to validate permission over the data requested
pageNumber | int | Page number to pull from responses supplying 0 will pull all pages at once
pageSize | int | Number of results to pull into a single page of responses
sortField | string | Field to sort the data returned on
sortAscending | bool | True will sort the data ascending, false will sort the data descending
filterField | string | Field to filter response data on
filterValue | string | Value to filter the FilterField on

## ListSurveysAndFolders

Coming Soon !

## GetSurveyMetaData

Coming Soon !

## GetSurveyInfoByName

Coming Soon !

## GetSurveyInfoByGuid

Coming Soon !

## CopySurvey

Coming Soon !

## AddFavoriteSurvey

Coming Soon !

# Users

Coming Soon !
