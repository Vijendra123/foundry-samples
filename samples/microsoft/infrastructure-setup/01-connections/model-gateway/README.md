# ModelGateway Connection Examples

This folder contains Azure Bicep templates for creating ModelGateway connections to Azure AI Foundry projects.

## ⚠️ Important Notice

**ModelGateway connections are currently not supported in Azure AI Foundry.** These templates are provided as examples for future use when ModelGateway support becomes available.

## Prerequisites

1. **Azure CLI** installed and configured
2. **AI Foundry account and project** already created

## How to Deploy (When Supported)

### Basic ModelGateway Connection
```bash
# 1. Edit parameters-basic.json with your resource IDs
# 2. Deploy using the parameters file (API key will be prompted)
az deployment group create \
  --resource-group <your-resource-group> \
  --template-file connection-modelgateway-basic.bicep \
  --parameters @parameters-basic.json
```

### Dynamic Discovery ModelGateway Connection
```bash
# 1. Edit parameters-dynamic.json with your resource IDs
# 2. Deploy using the parameters file (API key will be prompted)
az deployment group create \
  --resource-group <your-resource-group> \
  --template-file connection-modelgateway-dynamic.bicep \
  --parameters @parameters-dynamic.json
```

### Static Models ModelGateway Connection
```bash
# 1. Edit parameters-static.json with your resource IDs
# 2. Deploy using the parameters file (API key will be prompted)
az deployment group create \
  --resource-group <your-resource-group> \
  --template-file connection-modelgateway-static.bicep \
  --parameters @parameters-static.json
```

## Parameter Files

- `parameters-basic.json`: For basic ModelGateway connections
- `parameters-dynamic.json`: For dynamic discovery connections
- `parameters-static.json`: For static model list connections

Edit these files to update the resource IDs and target URLs for your environment. API keys will be prompted securely during deployment.