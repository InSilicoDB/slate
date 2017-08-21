---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL
  - json: Response

<!-- toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a> -->

<!-- includes:
  - errors -->

search: true
---

# Introduction

Welcome to the GenePlaza Developer API! You can use our API to access genetic information of our users.

# Authentication

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl -X POST \
  https://developer.geneplaza.com/token \
  -H 'authorization: Basic <64encode_usercredentials>' \
  -H 'content-type: application/json' \
  -d '{
    "data": {
         "type": "token",
         "attributes": {
           "grant_type": "authorization_code",
           "code": <your_access_token>,
           "scope": "analysis"
         }
    }
}'
```

```json
{
    "data": {
        "type": "token",
        "id": "414364fcf3739e2649e90e5089c46c59434cfc99f",
        "attributes": {
            "accessToken": "414364fcf3739e2649e90e5089c46c59434cfc99f",
            "expiresIn": 3600,
            "tokenType": "Bearer",
            "scope": "analysis"
        }
    }
}
```

To be able to authenticate you will first need to have **credentials**. Contact us for this.
When an analysis is started a webhook of yours is called with the the `authorizationCode`. Use this `authorizationCode` to authenticate and get an `accessToken` back.

Once you have **credentials** you also need to register an application. You can have several applications registered per account. Each application need to have a two endpoints:

* one webhook for the computation of the customer's results, which is triggered by the developer api when a customer order it.
* one for sending the results of the user to your UX

### Parameters

Parameter | Default | Description
--------- | ------- | -----------
credentials | false | base64 encode of `username:password`
access_token | false | This you get from us with the webhook.

# Make an analysis

## Start of an analysis

> We call your webhook with following call:

```shell
curl -X POST \
  'https://<your_endpoint>' \
  -H 'content-type: application/json'
  -d '{
    "data": {
      "type": "analysis",
      "attributes": {
        "authorisationCode": <authorisationCode>,
        "analysisId": <analysisId>,
        "datasetId": <datasetId>
      }
    }
  }'
```
<aside class="warning">
Remember — We call your webhook with this call! You don't have to use this code,
but you need the attributes of the body to be able to use the API.
</aside>

### Data you need

Parameter | Description
--------- | -----------
authorisationCode | Is the code you need for the authentication
analysisId | The id of the analysis you want to create a report for, or you want to set the state to `ready` or `failed`. The state is now `running`.
datasetId | The dataset which you can query genotypes of

## Query genotypes

```shell
curl -X GET \
  'https://developer.geneplaza.com/datasets/<datasetId>/SNP?names=rs7221412%20rs17822931%20rs2395029%20rs3745274%20rs4363657%20rs9939609%20rs671%20rs1800012%20rs4988235&quality=0.80' \
  -H 'authorization: Bearer <accessToken>' \
  -H 'content-type: application/json'
```

```json
{
    "data": [
        {
            "type": "snp",
            "id": {},
            "links": {
                "self": "https://developer.geneplaza.com/datasets/18/SNP"
            },
            "attributes": {
                "name": "rs1800012",
                "chromosome": "17",
                "position": "48277749",
                "genotype": "CC",
                "probability": 1
            }
        },
        {
            "type": "snp",
            "id": {},
            "links": {
                "self": "https://developer.geneplaza.com/datasets/18/SNP"
            },
            "attributes": {
                "name": "rs4988235",
                "chromosome": "2",
                "position": "136608646",
                "genotype": "GA",
                "probability": 1
            }
        }
    ]
}
```

### HTTP Request

`GET https://developer.geneplaza.com/datasets/18/SNP?names=rs7221412%20rs17822931%20&quality=0.80`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
datasetId | `null` | The dataset of which you want to query genotypes
quality | 0.8 | quality or probability of the genotype, number between 0 and 1
names | `null` | list of space separated SNPs ids

### Headers Parameters

Parameter | Description
--------- | -----------
Authorization | `Bearer ` followed by your `accessToken`

## Save an analysis report

```shell
curl -X POST \
  'https://developer.geneplaza.com/analysis/<analysisId>/report-pages' \
  -H "authorization: Bearer <accessToken>" \
  -H "content-type: application/json" \
  -d '{
    "data": {
      "type": "page",
      "attributes": {
        "title": "<title>",
        "content": "<content>"
      }
    }
  }'
```

```json
```

Once you have compute results with the genetic data of the user, you can save the results of the analysis.

### Parameters

Parameter | Required | Description
--------- | ------- | -----------
analysisId | true | Analysis for which you save a report
title | true | Title of the report
content | true | Content of the report
accessToken | true | Your accessToken for this dataset

## Finish the analysis

```shell
curl -X PATCH \
  'https://developer.geneplaza.com/analysis/<analysisId>' \
  -H 'authorization: Bearer <accessToken>' \
  -H 'content-type: application/json' \
  -d '{
    "data": {
      "type":"analysis",
      "id": <analysisId>,
      "attributes": {
        "status": <analysisStatus>
      }
    }
  }'
```

```json
```

Once the analysis is done you can mark it as finished. When the anlysis is finished and in state `ready` it is accessible by the GenePlaza website for the customer. The customer is notified by email.

### Parameters

Parameter | Required | Description
--------- | ------- | -----------
analysisId | true | Analysis for which you want to set the state
status | true | `ready` or `error`
accessToken | true | Your accessToken for this dataset

# Accessing the report in the UI

## How it works

The content of the report is sent by javascript from the origin `https://www.geneplaza.com` with  `postMessage` to the your UI endpoint.

<aside class="info">
You could also save the content in your own DB and load it the way you like.
</aside>


```javascript
<targetWindow>.postMessage(
  JSON.stringify({
    preview: true|false,
    lang: "nl|en|fr",
    content: <content> //TODO,
    datasetId: <datasetId> //TODO
  }), <yourUIOrigin>);

```
