# Azure AI Foundry Connection Types Overview

This document provides an overview of the connection types available in Azure AI Foundry for integrating with AI model providers through agents. It covers APIM (API Management) and ModelGateway connections, their configurations, and guidance for implementation.

## Table of Contents

- [How Agents Service Uses Connections](#how-agents-service-uses-connections)
- [APIM (API Management) Connections](#apim-api-management-connections)
- [ModelGateway Connections](#modelgateway-connections)
- [Connection Type Comparison](#connection-type-comparison)
- [Advanced Configuration Options](#advanced-configuration-options)
- [Additional Resources](#additional-resources)

## How Agents Service Uses Connections

The Azure AI Foundry Agents service interacts with AI model providers through connections in two main ways:

### LLM Inference
- **Primary Usage**: The Agents service makes calls to chat completions endpoints for language model interactions
- **Authentication**: Uses credentials stored in the connection configuration
- **Request Flow**: Routes requests through the configured target URL with appropriate authentication headers

### Model Discovery
The Agents service needs to understand what models are available through each connection:

- **Dynamic Discovery**: The Agents service calls the get model endpoint to fetch details of the specific model name passed in the Agents request
  - Calls get model endpoint (e.g., `/deployments/{deploymentName}`) to get model details such as actual model name, version, and format (OpenAI, DeepSeek, etc.)

- **Static Configuration**: Models are predefined in the connection metadata
  - No runtime discovery calls needed
  - Reduces latency during Agents service operations
  - Useful when the model list is fixed and known in advance

## APIM (API Management) Connections

APIM connections are specialized connections designed for Azure API Management integration scenarios. They follow the connection patterns described above but include Azure API Management-specific defaults and conventions.

### Available Configurations

1. **Basic APIM Connection** - Used when you have exposed standard chat completion and get model (`/deployments/{deploymentName}`) endpoints through Azure API Management. The Agents service will call the get model endpoint to fetch details (model name, version, format) for the specific deployment name requested and use chat completions for LLM inference.

2. **APIM with Dynamic Discovery** - Used when you have custom endpoint paths (not the standard `/deployments/{deploymentName}` pattern) but still want runtime model discovery for specific deployment names.

3. **APIM with Static Models** - Used when you can provide a static list of deployments in the connection configuration itself. With this connection, you don't need to expose a get model endpoint on your gateway, and the Agents service will only use the chat completion endpoint exposed through Azure API Management. This can be used to reduce the latency of Agent runs.

### When to Use APIM Connections

- **Azure API Management Integration**: When routing AI models through Azure API Management gateways
- **Enterprise Scenarios**: Production environments requiring Azure API Management's enterprise features (rate limiting, monitoring, security policies)

## ModelGateway Connections  

ModelGateway connections provide a unified interface for connecting to various AI model providers through self-hosted gateways or third-party gateway solutions when Azure API Management cannot be used.

### Available Configurations

1. **Basic ModelGateway** - Used when you have exposed standard chat completion and get model endpoints on your self-hosted gateway or third-party gateway. The Agents service will call the get model endpoint (e.g., `/v1/models/{deploymentName}`) to fetch details (model name, version, format) for the specific deployment name requested.

2. **ModelGateway with Dynamic Discovery** - Used when you have custom endpoint paths but still want runtime model discovery for specific deployment names.

3. **ModelGateway with Static Models** - Used when you can provide a static list of deployments in the connection configuration itself. With this connection, you don't need to expose a get model endpoint on your gateway, and the Agents service will only use the chat completion endpoint exposed by your self-hosted or third-party gateway. This can be used to reduce latency of Agent runs.

### When to Use ModelGateway Connections

- **Self-Hosted Gateways**: When you have your own gateway infrastructure and cannot use Azure API Management
- **Third-Party Enterprise Gateways**: Integration with MuleSoft, Kong, or other non-Azure gateway solutions  
- **Azure API Management Alternatives**: When Azure API Management is not available or suitable for your environment
- **Multi-Provider Scenarios**: Connecting to OpenAI, Azure OpenAI, or other AI providers through custom gateway solutions
- **Direct Provider Access**: Direct connections to AI providers without any gateway layer
- **Flexible Authentication**: When custom authentication headers or formats are required
- **Mixed Provider Environments**: Managing multiple AI providers through a single connection type

## Connection Type Comparison

Choose APIM connections when working within Azure's ecosystem with Azure API Management, and ModelGateway connections when you have self-hosted gateways or cannot use Azure API Management for your environment.

| Aspect | APIM Connections | ModelGateway Connections |
|--------|------------------|------------------------|
| **Primary Use** | Azure API Management integration | Self-hosted and third-party gateway access |
| **Defaults** | Azure API Management-specific conventions | Generic provider patterns |
| **Enterprise Focus** | Azure-centric enterprise | Self-hosted and multi-provider enterprise |
| **Authentication** | Azure API Management subscription keys, Microsoft Entra ID | Flexible API key formats |
| **Discovery** | Azure API Management `/deployments` defaults | Configurable endpoints |

## Advanced Configuration Options

Both APIM and ModelGateway connections support additional configuration capabilities to customize their behavior:

### 1. Custom Headers
Configure static HTTP headers to be sent when interacting with the gateway's chat completion endpoint. Useful for:
- Additional gateway routing requirements
- Static metadata headers required by the gateway
- Custom headers needed for gateway processing

### 2. Inference API Version
Specify the API version for model inference calls (chat completions, embeddings, etc.):
- Controls the API contract used for LLM interactions
- Appended as query parameter to inference requests
- Allows targeting specific provider API versions

### 3. Deployment API Version  
Specify the API version for deployment management calls:
- Used only for model discovery endpoints
- Separate from inference API version for flexibility
- Controls the API contract for getting model details

### 4. Auth Config
Customize authentication header names when customers want to use different API key header names:
- Configure custom API key header names (e.g., `x-api-key` instead of default `Authorization`)
- Define header value templates (e.g., `Bearer {api_key}`, `Key {api_key}`)
- Support for gateways that require specific API key header formats
- Useful when gateways expect non-standard authentication header names

### 5. Deployment In Path
Support different chat completion request formats to accommodate various gateway patterns:
- **Azure OpenAI format**: `/deployments/{deploymentName}/chat/completions` (deploymentInPath: true)
- **Standard OpenAI format**: `/chat/completions` with model name in request body (deploymentInPath: false)
- Allows gateways to support either Azure OpenAI-like routing or standard OpenAI routing patterns

For detailed configuration examples and JSON schemas, refer to the specific connection type documentation:
- [APIM Connection Examples](./apim/APIM-Connection-Objects.md)
- [ModelGateway Connection Examples](./model-gateway/ModelGateway-Connection-Objects.md)