# url-shortner-api

`AWS Hosted Project`

This project contains the necessary infrastructure as code and lambda code to host a URL shortener API. Convert any long URL's to a short one. Easily connect this API to your front end application.

**AWS Services Used**:

* AWS Lambda: For the backend API.
* AWS DynamoDB: For storing link references.

# ⚙️ Installation

To deploy the application you must have [terraform cli](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli) and [aws cli](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) installed.

You must have a `aws profile` configured for terraform to use it and deploy it into your AWS account:

```
aws configure --profile <YOUR_PROFILE_NAME>
```

To begin, open the `provider.tf` file and under the aws provider change the profile used. Put your own profile name in `<YOUR_AWS_CONFIGURE_PROFILE>`. Then change the `<YOUR_AWS_REGION>` to `us-east-1`.

```
provider "aws" {
  region = "us-east-1"
  profile = "profile_name"
}
```

Next, go to `vars.tf` and change in `locals` the following values: `domain_name` and `domain_protocol`.

# 🚀 Deployment
Execute the following commands to deploy the infrastructure and lambdas:

```sh
npm install --prefix ./functions/get-short-url
terraform init
terraform apply
```

Once deployed, you should see in the outputs the URL's pointing to your lambdas.

# 🔗 API Usage

The API contains 3 different lambdas. Below we explain how to use each:

* `get_short_url`: Generates the `shortValue` from a long url and stores it in a DynamoDB table.
* `get_original_url`: Fetches the original URL by passing a `shortValue` generated by the first API call.
* `get_url_stats`: Fetches details about a URL without increasing the `viewsCount` property.

## Get Short URL

Method: `POST`

URL Example: (See Terraform output for `get_short_url_function_url` for actual URL)
```
https://<ID>.lambda-url.us-east-1.on.aws/
```

### Request Body:

```
{
    "url": "https://google.com",
    "alias": "custom-alias"
}
```

### Response Body:

```
{
    "lastViewedDate": "2023-02-06T00:15:05.319Z",
    "createdDate": "2023-02-06T00:15:05.319Z",
    "shortUrl": "https://<YOUR_DOMIAN_NAME>/oaIL23dz",
    "viewsCount": 0,
    "shortValue": "oaIL23dz",
    "originalUrl": "https://google.com"
}
```

### Properties

---
`url`

*Description:* A long url. Support https, http and non protocol URL's.

*Required:* `Yes`  

*Type:* `string`

---

`alias`

*Description:* A custom alias (name) for the URL. If this is used, a random ID will not be returned and instead a custom alias will be used.

*Required:* `No`  

*Type:* `string`

---

## Get Original URL

Method: `GET`

URL Example: (See Terraform output for `get_original_url_function_url` for actual URL)
```
https://<ID>.lambda-url.us-east-1.on.aws
```

### Request Query:

```
https://<ID>.lambda-url.us-east-1.on.aws?shortValue=<SHORT_VALUE>
```

### Response Body:

```
{
    "viewsCounter": "2",
    "lastViewedDate": "2023-02-06T00:47:44.471Z",
    "createdDate": "2023-02-06T00:44:01.242Z",
    "shortUrl": "https://<YOUR_DOMAIN_NAME>/lgatGICp",
    "shortValue": "lgatGICp",
    "originalUrl": "https://google.com"
}
```

### Query Parameters
---

`shortValue`

*Description:* A unique ID that will be used as the URL path. This value is returned when first creating a shortUrl.

*Required:* `Yes`  

*Type:* `string`

---


## Get URL Statistics
  
Method: `GET`

URL Example: (See Terraform output for `get_url_stats_function_url` for actual URL)
```
https://<ID>.lambda-url.us-east-1.on.aws
```

### Request Query:

```
https://<ID>.lambda-url.us-east-1.on.aws?shortValue=<SHORT_VALUE>
```

### Response Body:

```
{
    "viewsCounter": "2",
    "lastViewedDate": "2023-02-06T00:47:44.471Z",
    "createdDate": "2023-02-06T00:44:01.242Z",
    "shortUrl": "https://<YOUR_DOMAIN_NAME>/lgatGICp",
    "shortValue": "lgatGICp",
    "originalUrl": "https://google.com"
}
```

### Query Parameters
---

`shortValue`

*Description:* A unique ID that will be used as the URL path. This value is returned when first creating a shortUrl.

*Required:* `Yes`  

*Type:* `string`

---
