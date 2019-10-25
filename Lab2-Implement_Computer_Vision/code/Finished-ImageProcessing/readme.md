注意！如果要使用已完成的解决方案，必须使用 TestCLI 下的`settings.json`中的密钥填写以下内容

```json
{
  "CognitiveServicesKeys": {
    "Vision": "VisionKeyHere"
  },
  "AzureStorage": {
    "ConnectionString": "ConnectionStringHere",
    "BlobContainer": "images"
  },
  "CosmosDB": {
    "EndpointURI": "CosmosURIHere",
    "Key": "CosmosKeyHere",
    "DatabaseName": "images",
    “CollectionName”: "metadata"
  }
}
```