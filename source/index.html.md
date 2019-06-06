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

To be able to authenticate you first need to have **credentials**. Contact us for this.
When an analysis is started a webhook of yours is called with the the `authorizationCode`. Use this `authorizationCode` to authenticate and get an `accessToken` back.

Once you have **credentials** you also need to register an application. You can have several applications registered per account. Each application need to have a two endpoints:

-   one webhook for the computation of the customer's results, which is triggered by the developer api when a customer order it.
-   one for sending the results of the user to your UX

## Authentication with an authorization_code

This is the way authenticate when you want to get access to a dataset genetic information.
The `authorization_code` is sent by us to your webhook registered in your application.

<aside class="info">
The access token you will get back, will give you access to the `analysis`, `snp` and `report pages` endpoints.
</aside>
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
           "code": <authorization_code>,
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

### Parameters

| Parameter                | Default | Description                            |
| ------------------------ | ------- | -------------------------------------- |
| 64encode_usercredentials | false   | base64 encode of `username:password`   |
| authorization_code       | false   | This you get from us with the webhook. |

## Authentication with credentials

This way of authentication is used for accessing the `application` endpoints.

<aside class="warning">
You also can use this type of authentication to access the `analysisTest` endpoint.
</aside>

```shell
curl -X POST \
  https://developer.geneplaza.com/token \
  -H 'authorization: Basic <64encode_usercredentials>' \
  -H 'content-type: application/json' \
  -d '{
	"data": {
         "type": "token",
         "attributes": {
           "grant_type": "client_credentials",
           "scope": "developer"
         }
    }
}'
```

### Parameters

| Parameter                | Default | Description                          |
| ------------------------ | ------- | ------------------------------------ |
| 64encode_usercredentials | false   | base64 encode of `username:password` |

# Application

## Create an application

```shell
curl -X POST \
  https://developer.geneplaza.com/applications \
  -H 'authorization: Bearer <access_token>' \
  -H 'content-type: application/json' \
  -d '{
	"data" : {
        "type":"application",
        "attributes":{
            "name" : "myApp",
            "description" : "some app",
            "webhook": "http://localhost:400/test",
            "webhookHeaders": {
            	"api-key": "secret"
            }
        }
	}
}
'
```

```json
{
    "data": {
        "type": "application",
        "id": 61,
        "links": {
            "self": "https://developer.geneplaza.com/applications/61"
        },
        "attributes": {
            "name": "myApp",
            "description": "some app",
            "featuredImage": null,
            "productImages": [],
            "cost": 0,
            "qualityRequirement": 0,
            "webhook": "http://localhost:400/test",
            "webhookHeaders": {
                "api-key": "secret"
            }
        }
    }
}
```

### Parameters

| Parameter      | Required | Description                                                                 |
| -------------- | -------- | --------------------------------------------------------------------------- |
| access_token   | true     | Your access_token after login                                               |
| name           | true     | name of your app                                                            |
| description    | true     | Description of your app                                                     |
| webhook        | true     | endpoint we call when analysis is requested by user                         |
| webhookHeaders | false    | optional if you want to add some headers to the call we do to your endpoint |

## Update an application

```shell
curl -X PATCH \
  https://developer.geneplaza.com/applications/<applicationId> \
  -H 'authorization: Bearer <access_token>' \
  -H 'content-type: application/json' \
  -d '{
	"data" : {
        "type":"application",
        "attributes":{
            "name" : "myApp",
            "description" : "some app",
            "webhook": "http://localhost:400/test",
            "webhookHeaders": {
            	"api-key": "secret"
            }
        }
	}
}
'
```

```json
{
    "data": {
        "type": "application",
        "id": 61,
        "links": {
            "self": "https://developer.geneplaza.com/applications/61"
        },
        "attributes": {
            "name": "myApp",
            "description": "some app",
            "featuredImage": null,
            "productImages": [],
            "cost": 0,
            "qualityRequirement": 0,
            "webhook": "http://localhost:400/test",
            "webhookHeaders": {
                "api-key": "secret"
            }
        }
    }
}
```

### Parameters

| Parameter      | Required | Description                                                                 |
| -------------- | -------- | --------------------------------------------------------------------------- |
| access_token   | true     | Your access_token after login                                               |
| applicationId  | true     | The id of your application                                                  |
| name           | false    | name of your app                                                            |
| description    | false    | Description of your app                                                     |
| webhook        | false    | endpoint we call when analysis is requested by user                         |
| webhookHeaders | false    | optional if you want to add some headers to the call we do to your endpoint |

## Get an application

```shell
curl -X GET \
  https://developer.geneplaza.com/applications/<applicationId> \
  -H 'authorization: Bearer <access_token>'
```

```json
{
    "data": {
        "type": "application",
        "id": 61,
        "links": {
            "self": "https://developer.geneplaza.com/applications/61"
        },
        "attributes": {
            "name": "myApp",
            "description": "some app",
            "featuredImage": null,
            "productImages": [],
            "cost": 0,
            "qualityRequirement": 0,
            "webhook": "http://localhost:400/test",
            "webhookHeaders": {
                "api-key": "secret"
            }
        }
    }
}
```

### Parameters

| Parameter     | Required | Description                   |
| ------------- | -------- | ----------------------------- |
| access_token  | true     | Your access_token after login |
| applicationId | true     | The id of your application    |

## Get all your applications

```shell
curl -X GET \
  https://developer.geneplaza.com/applications \
  -H 'authorization: Bearer <access_token>'
```

```json
{
    "data": [
        {
            "type": "application",
            "id": 52,
            "links": {
                "self": "https://developer.geneplaza.com/applications/52"
            },
            "attributes": {
                "name": "Abacavir-response",
                "description": "''",
                "featuredImage": "''",
                "productImages": [],
                "cost": 0,
                "qualityRequirement": 0,
                "webhook": "http://localhost:6001/analysis?humanTraitIds=24&format=jsonapi",
                "webhookHeaders": []
            }
        },
        {
            "type": "application",
            "id": 59,
            "links": {
                "self": "https://developer.geneplaza.com/applications/59"
            },
            "attributes": {
                "name": "myApp",
                "description": "some app",
                "featuredImage": null,
                "productImages": [],
                "cost": 0,
                "qualityRequirement": 0,
                "webhook": "http://localhost:400/test",
                "webhookHeaders": {
                    "api-key": "secret"
                }
            }
        }
    ]
}
```

### Parameters

| Parameter    | Required | Description                   |
| ------------ | -------- | ----------------------------- |
| access_token | true     | Your access_token after login |

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

| Parameter         | Description                                                                                                                              |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| authorisationCode | Is the code you need for the authentication                                                                                              |
| analysisId        | The id of the analysis you want to create a report for, or you want to set the state to `ready` or `failed`. The state is now `running`. |
| datasetId         | The dataset which you can query genotypes of                                                                                             |

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

| Parameter | Default | Description                                                    |
| --------- | ------- | -------------------------------------------------------------- |
| datasetId | `null`  | The dataset of which you want to query genotypes               |
| quality   | 0.8     | quality or probability of the genotype, number between 0 and 1 |
| names     | `null`  | list of space separated SNPs ids                               |

### Headers Parameters

| Parameter     | Description                             |
| ------------- | --------------------------------------- |
| Authorization | `Bearer` followed by your `accessToken` |

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

Once you have computed results with the genetic data of the user, you can save the results of the analysis.

### Parameters

| Parameter   | Required | Description                          |
| ----------- | -------- | ------------------------------------ |
| analysisId  | true     | Analysis for which you save a report |
| title       | true     | Title of the report                  |
| content     | true     | Content of the report                |
| accessToken | true     | Your accessToken for this dataset    |

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

Once the analysis is done you can mark it as finished. When the analysis is finished and in state `ready` it is accessible by the GenePlaza website for the customer. The customer is notified by email.

### Parameters

| Parameter   | Required | Description                                  |
| ----------- | -------- | -------------------------------------------- |
| analysisId  | true     | Analysis for which you want to set the state |
| status      | true     | `ready` or `error`                           |
| accessToken | true     | Your accessToken for this dataset            |

## Get an analysis

```shell
curl -X GET \
  'https://developer.geneplaza.com/analysis/<analysisId>' \
  -H 'authorization: Bearer <accessToken>'
```

```json
{
    "data": {
        "type": "analysis",
        "id": <analysisId>,
        "links": {
            "self": "https://developer.geneplaza.com/analysis/<analysisId>"
        },
        "attributes": {
            "applicationId": <int>,
            "datasetId": <int>,
            "status": "ready",
            "notes": ""
        }
    }
}
```

### Parameters

| Parameter   | Required | Description                       |
| ----------- | -------- | --------------------------------- |
| analysisId  | true     | Id of the analysis                |
| accessToken | true     | Your accessToken for this dataset |

# Accessing the report in the UI

## How it works

The content of the report is sent by javascript from the origin `https://www.geneplaza.com` with  `postMessage` to the your UI endpoint. Your UI endpoint is opened in a iframe in the GenePlaza website.

<aside class="info">
You could also save the content in your own DB and load it the way you like.
</aside>

<aside class="info">
To the URL of your endpoint we append a query parameter `analysisId`, and open this URL in a iframe.
</aside>

```javascript
<targetWindow>.postMessage(
  JSON.stringify({
    preview: true|false,
    lang: "nl|en|fr",
    reportPage: <reportPage>,
    analysisId: <analysisId>
  }), <yourUIOrigin>);
```

### Parameters

| Parameter  | Description                                             |
| ---------- | ------------------------------------------------------- |
| analysisId | Id of the analysis                                      |
| reportPage | The content saved by your application for this analysis |

# Testing your application

Only a certain role can start analysises. To be able to test your application there is an API endpoint
made that allows application developers to start analysises and get authorization_code for certain
datasets.

## Login to get an accessToken

```shell
curl -X POST \
  https://developer.geneplaza.com/token \
  -H 'authorization: Basic <64encode_usercredentials>' \
  -H 'content-type: application/json' \
  -d '{
	"data": {
         "type": "token",
         "attributes": {
           "grant_type": "client_credentials",
           "scope": "developer"
         }
    }
}'
```

```json
{
    "data": {
        "type": "token",
        "id": <accessToken>,
        "attributes": {
            "accessToken": <accessToken>,
            "expiresIn": 3600,
            "tokenType": "Bearer",
            "scope": "developer"
        }
    }
}
```

### Parameters

| Parameter                | Default | Description                          |
| ------------------------ | ------- | ------------------------------------ |
| 64encode_usercredentials | false   | base64 encode of `username:password` |

## Start a test analysis

```shell
curl -X POST \
  'https://developer.geneplaza.com/analysistest' \
  -H 'authorization: Bearer <accessToken>' \
  -H 'content-type: application/json' \
  -d ' {
        "data" : {
          "type":"analysis",
          "attributes":{
            "applicationId" : <your_application_id>,
            "datasetId" : 18
          }
       }
  }'
```

```json
{
    "data": {
        "type": "analysis",
        "id": <analysisId>,
        "links": {
            "self": "https://developer.geneplaza.com/analysis/9056"
        },
        "attributes": {
            "applicationId": <applicationId>,
            "datasetId": 18,
            "status": "running",
            "notes": "",
            "authorizationCode": "someAuthorizationCode"
        }
    }
}
```
<aside class="warning">
With the `authorizationCode` returned from the analysis creation, you can access the `analysis`, `snp` and `report pages` endpoints.
</aside>

### Parameters

| Parameter     | Required | Description                                                                                              |
| ------------- | -------- | -------------------------------------------------------------------------------------------------------- |
| datasetId     | true     | Id of the dataset you which to have access to the genetic data. For testing are `18` and `227` available |
| accessToken   | true     | Your accessToken, got by logging in.                                                                     |
| applicationId | true     | The id of your application                                                                               |

# Create a dataset

## Upload data

```shell
curl -F ‘data=@path/to/local/genotype/file.[tex|zip]’ \
  -H 'authorization: Bearer <accessToken>' \
  'https://developer.geneplaza.com/upload'
```

```json
{
    "data": {
        "type": "dataset",
        "id": <datasetId>,
        "links": {
            "self": "https://developer.geneplaza.com/dataset/<datasetId>"
        },
        "attributes": {
            "datasetId": 18
        }
    }
}
```

Upload a 23andme, AncestryDNA, MyHeritage file and a dataset will be created.

# Traits

## List traits

```shell
curl -X GET -H 'authorization: Bearer <accessToken>' 'https://developer.geneplaza.com/traits'
```

```json
{
    "data": [
      {
        "type": "trait",
        "id": <traitid>,
        "attributes": {
          "name": "todo",
          "variants": ["rs23432", "rs23432"]
        }
      },
      {
        "type": "trait",
        "id": <traitid>,
        "attributes": {
          "name": "todo",
          "variants": ["rs23432", "rs23432"]
        }
      }
    ]
}
```

List all traits available.

## Get trait for dataset

```shell
curl -X GET -H 'authorization: Bearer <accessToken>' 'https://developer.geneplaza.com/dataset/<datasetId>/trait/<traitId>'
```

```json
{
    "data": {
      "type": "trait-result",
      "id": <trait-result>,
      "attributes": {
          "status": "running",
      }
    }
}
```
```json
OR when ready

{
    "data": {
      "type": "trait-result",
      "id": <trait-result>,
      "attributes": {
          "status": "ready",
          "type": "binary | quantitative",
          "effectSizeDistributions": {
              "EUR": [[x1,y1], [x2,y2], ...],
              "EAS": [[x1,y1], [x2,y2], ...],
              "AFR": [[x1,y1], [x2,y2], ...],
              "AMR": [[x1,y1], [x2,y2], ...],
              "SAS": [[x1,y1], [x2,y2], ...]
          },
          "zScore": 0.5,
          "effectSize": 23.2343,
          "ethnicity": "EUR",
          "genotypeScores": {
            "rs78503206": {
              "genotype": "CC",
              "effectSize": 0.052336227229741565
            },
            "rs6554267": {
              "genotype": "TT",
              "effectSize": -0.0324724484302187
            },
            "rs7227945": {
              "genotype": "TT",
              "effectSize": -0.035994294104095395
            },
            "rs72921001": {
              "genotype": "AA",
              "effectSize": -0.27263571801638176
            },
            "rs75378433": {
              "genotype": "CC",
              "effectSize": 0.05236703124421473
            },
            "rs13412810": {
              "genotype": "GG",
              "effectSize": 0.07638294299624267
            }
          }
        }
      }
    }
}
```

This will generate and get the results of a trait for a dataset.

### Parameters

| Parameter     | Required | Description                                                                                              |
| ------------- | -------- | -------------------------------------------------------------------------------------------------------- |
| datasetId     | true     | Id of the dataset you which to have access to the genetic data.|
| accessToken   | true     | Your accessToken                                                                     |
| traitId | true     | The id of the trait you want results for                                                                               |
