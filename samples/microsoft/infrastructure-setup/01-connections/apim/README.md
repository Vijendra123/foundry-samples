# APIM Connection Examples

This folder contains Azure Bicep templates for creating APIM (API Management) connections to Azure AI Foundry projects.

## Prerequisites

1. **Azure CLI** installed and configured
2. **Existing APIM service** with APIs configured
3. **AI Foundry account and project** already created

## How to Deploy

### Basic APIM Connection
```bash
# 1. Edit parameters-basic.json with your resource IDs
# 2. Deploy using the parameters file
az deployment group create \
  --resource-group <your-resource-group> \
  --template-file connection-apim-basic.bicep \
  --parameters @parameters-basic.json
```

### Deployment API Version APIM Connection
```bash
# 1. Edit parameters-deployment-api.json with your resource IDs
# 2. Deploy using the parameters file
az deployment group create \
  --resource-group <your-resource-group> \
  --template-file connection-apim-deployment-api-version.bicep \
  --parameters @parameters-deployment-api.json
```

### Dynamic Discovery APIM Connection
```bash
# 1. Edit parameters-dynamic.json with your resource IDs
# 2. Deploy using the parameters file
az deployment group create \
  --resource-group <your-resource-group> \
  --template-file connection-apim-dynamic-discovery.bicep \
  --parameters @parameters-dynamic.json
```

### Static Models APIM Connection
```bash
# 1. Edit parameters-static.json with your resource IDs
# 2. Deploy using the parameters file
az deployment group create \
  --resource-group <your-resource-group> \
  --template-file connection-apim-static-models.bicep \
  --parameters @parameters-static.json
```

## Parameter Files

- `parameters-basic.json`: For basic APIM connections with minimal configuration
- `parameters-deployment-api.json`: For APIM connections with API versioning (includes inferenceAPIVersion and deploymentAPIVersion)
- `parameters-dynamic.json`: For APIM connections with dynamic model discovery (includes OpenAI endpoint configurations)
- `parameters-static.json`: For APIM connections with static model lists (includes customizable staticModels array)

Edit these files to update the resource IDs for your environment.