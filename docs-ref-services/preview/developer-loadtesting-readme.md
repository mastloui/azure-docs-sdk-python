---
title: Azure Developer LoadTesting client library for Python
keywords: Azure, python, SDK, API, azure-developer-loadtesting, loadtestservice
author: lmazuel
ms.author: lmazuel
ms.date: 08/02/2022
ms.topic: reference
ms.devlang: python
ms.service: loadtestservice
---

# Azure Developer LoadTesting client library for Python - version 1.0.0b1 

Azure Developer LoadTesting provides client library in python to the user by which they can interact natively with Load Test Service.The service is for performing the load test to optimize application performance, scalability or capacity. The user can get the client-side and server-side metrices to see the details reported by the test engine and information about your Azure application components.


## Getting started

### Installating the package

```bash
python -m pip install azure-developer-loadtesting
```

#### Prequisites

- Python 3.7 or later is required to use this package.
- You need an [Azure subscription][azure_sub] to use this package.
- An existing Azure Developer LoadTesting instance.
#### Create with an Azure Active Directory Credential
To use an [Azure Active Directory (AAD) token credential][authenticate_with_token],
provide an instance of the desired credential type obtained from the
[azure-identity][azure_identity_credentials] library.

To authenticate with AAD, you must first [pip][pip] install [`azure-identity`][azure_identity_pip]

After setup, you can choose which type of [credential][azure_identity_credentials] from azure.identity to use.
As an example, [DefaultAzureCredential][default_azure_credential] can be used to authenticate the client:

Set the values of the client ID, tenant ID, and client secret of the AAD application as environment variables:
`AZURE_CLIENT_ID`, `AZURE_TENANT_ID`, `AZURE_CLIENT_SECRET`

Use the returned token credential to authenticate the client:

```python
from azure.developer.loadtesting import LoadTestingClient
from azure.identity import DefaultAzureCredential
client = LoadTestingClient(endpoint='<endpoint>', credential=DefaultAzureCredential())
```

## Examples

### Creating a load test 
```python
from azure.developer.loadtesting import LoadTestingClient
from azure.identity import DefaultAzureCredential
from azure.core.exceptions import HttpResponseError

TEST_ID = "some-test-id"  
DISPLAY_NAME = "new_namespace-new-namespace"  
SUBSCRIPTION_ID = os.environ["SUBSCRIPTION_ID"]  

client = LoadTestingClient(endpoint='<endpoint>', credential=DefaultAzureCredential())

try:
    result = client.load_test_administration.create_or_update_test(
        TEST_ID,
        {
            "resourceId": f"/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/yashika-rg/providers/Microsoft.LoadTestService/loadtests/loadtestsdk",
            "description": "",
            "displayName": DISPLAY_NAME,
            "loadTestConfig": {
                "engineSize": "m",
                "engineInstances": 1,
                "splitAllCSVs": False,
            },
            "secrets": {},
            "environmentVariables": {},
            "passFailCriteria": {"passFailMetrics": {}},
            "keyvaultReferenceIdentityType": "SystemAssigned",
            "keyvaultReferenceIdentityId": None,
        },
    )
    print(result)
except HttpResponseError as e:
     print('service responds error: {}'.format(e.response.json()))

```

### Uploading .jmx file to a Test
```python
from azure.developer.loadtesting import LoadTestingClient
from azure.identity import DefaultAzureCredential
from azure.core.exceptions import HttpResponseError

TEST_ID = "some-test-id"  
FILE_ID = "some-file-id"  

client = LoadTestingClient(endpoint='<endpoint>', credential=DefaultAzureCredential())

try:

    result = client.load_test_administration.upload_test_file(TEST_ID, FILE_ID, open("sample.jmx", "rb"))
    print(result)
except HttpResponseError as e:
    print("Failed to send JSON message: {}".format(e.response.json()))
```

### Running a Test
```python
from azure.developer.loadtesting import LoadTestingClient
from azure.identity import DefaultAzureCredential
from azure.core.exceptions import HttpResponseError

TEST_ID = "some-test-id"  
TEST_RUN_ID = "some-testrun-id" 
DISPLAY_NAME = "new_namespace-new-namespace"  

client = LoadTestingClient(endpoint='<endpoint>', credential=DefaultAzureCredential())

try:
    result = client.load_test_runs.create_and_update_test(
        TEST_RUN_ID,
        {
            "testId": TEST_ID,
            "displayName": DISPLAY_NAME,
            "requestSamplers": [],
            "errors": [],
            "percentiles": ["90"],
            "groupByInterval": "5s",
        },
    )
    print(result)
except HttpResponseError as e:
    print("Failed to send JSON message: {}".format(e.response.json()))
```
## Key concepts
The following components make up the Azure Load Testing Service. The Azure Load Test client library for Python allows you to interact with each of these components through the use of a dedicated client object.

### Load testing resource
The Load testing resource is the top-level resource for your load-testing activities. This resource provides a centralized place to view and manage load tests, test results, and related artifacts. A load testing resource contains zero or more load tests.

### Test
A test specifies the test script, and configuration settings for running a load test. You can create one or more tests in an Azure Load Testing resource.

### Test Engine
A test engine is computing infrastructure that runs the Apache JMeter test script. You can scale out your load test by configuring the number of test engines. The test script runs in parallel across the specified number of test engines.

### Test Run
A test run represents one execution of a load test. It collects the logs associated with running the Apache JMeter script, the load test YAML configuration, the list of app components to monitor, and the results of the test.

### App Component
When you run a load test for an Azure-hosted application, you can monitor resource metrics for the different Azure application components (server-side metrics). While the load test runs, and after completion of the test, you can monitor and analyze the resource metrics in the Azure Load Testing dashboard.

### Metrics
During a load test, Azure Load Testing collects metrics about the test execution. There are two types of metrics:

1. Client-side metrics give you details reported by the test engine. These metrics include the number of virtual users, the request response time, the number of failed requests, or the number of requests per second. 

2. Server-side metrics are available for Azure-hosted applications and provide information about your Azure application components. Metrics can be for the number of database reads, the type of HTTP responses, or container resource consumption.

## Troubleshooting
More about it is coming soon...

## Next steps

More examples are coming soon...

## Contributing

This project welcomes contributions and suggestions. Most contributions require
you to agree to a Contributor License Agreement (CLA) declaring that you have
the right to, and actually do, grant us the rights to use your contribution.
For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether
you need to provide a CLA and decorate the PR appropriately (e.g., label,
comment). Simply follow the instructions provided by the bot. You will only
need to do this once across all repos using our CLA.

This project has adopted the
[Microsoft Open Source Code of Conduct][code_of_conduct]. For more information,
see the Code of Conduct FAQ or contact opencode@microsoft.com with any
additional questions or comments.

<!-- LINKS -->
[code_of_conduct]: https://opensource.microsoft.com/codeofconduct/
[authenticate_with_token]: /azure/cognitive-services/authentication?tabs=powershell#authenticate-with-an-authentication-token
[azure_identity_credentials]: https://github.com/Azure/azure-sdk-for-python/tree/azure-developer-loadtesting_1.0.0b1/sdk/identity/azure-identity#credentials
[azure_identity_pip]: https://pypi.org/project/azure-identity/
[default_azure_credential]: https://github.com/Azure/azure-sdk-for-python/tree/azure-developer-loadtesting_1.0.0b1/sdk/identity/azure-identity#defaultazurecredential
[pip]: https://pypi.org/project/pip/
[azure_sub]: https://azure.microsoft.com/free/

