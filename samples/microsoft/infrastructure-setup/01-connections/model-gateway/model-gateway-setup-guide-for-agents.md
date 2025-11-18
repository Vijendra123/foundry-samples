# Model Gateway Setup Guide for Foundry Agents

> **🎯 Gateway Readiness Guide**  
> This guide helps you prepare your self-hosted or third-party AI model gateway to work seamlessly with Foundry Agents using ModelGateway connections.

## 🏗️ Prerequisites: Gateway Requirements

Before connecting your gateway to Foundry Agents, ensure it meets the following requirements for ModelGateway connection compatibility.

### Gateway Types Supported

| Gateway Type | Description |
|--------------|-------------|
| **🏠 Self-Hosted** | Custom gateway solutions deployed in your infrastructure |
| **🌐 Third-Party Providers** | Enterprise gateway platforms |
| **☁️ Cloud Providers** | Direct AI service endpoints |
| **🔧 Custom Implementations** | Proprietary gateway solutions |

---

## 🚀 Gateway Readiness Requirements

### Requirement 1: 📡 Chat Completions API Endpoint

Your gateway **must** expose a chat completions endpoint that Foundry Agents can use for AI model interactions.

#### 🎯 Required Endpoint

Your gateway must provide an endpoint that accepts chat completion requests:

**Endpoint Pattern Options:**

**Option A: Direct Chat Completions** (Most Common)
```
POST {gateway-base-url}/chat/completions
```

**Option B: Deployment-Specific Routing**
```
POST {gateway-base-url}/deployments/{deployment-name}/chat/completions
```

#### 📋 API Compatibility

Your gateway must be compatible with the standard OpenAI Chat Completions API specification, including:

- **Request Format**: Support for standard OpenAI chat completions requests with messages, tools, and streaming parameters
- **Response Format**: Return responses in OpenAI chat completions format for both streaming and non-streaming modes
- **Function Calling**: Support for tool/function calling capabilities
- **Streaming**: Support for Server-Sent Events (SSE) streaming when requested

### Requirement 2: 🔍 Model Discovery Configuration

Choose one of the following model discovery approaches for your gateway:

#### Option A: 📋 Static Model Configuration

**✅ When to Use:**
- **🚀 Better Performance**: No additional API calls needed for model discovery
- **🔧 Simpler Setup**: No additional gateway endpoints required
- **💰 Cost Effective**: Reduces API calls to your gateway

**📝 Implementation:**
Configure the static model list directly in the ModelGateway connection metadata. No additional gateway setup required.

**Example Static Model List:**
```json
{
  "models": [
    {
      "name": "gpt-4-deployment",
      "properties": {
        "model": {
          "name": "gpt-4",
          "version": "0613",
          "format": "OpenAI"
        }
      }
    },
    {
      "name": "gpt-35-turbo-deployment", 
      "properties": {
        "model": {
          "name": "gpt-3.5-turbo",
          "version": "0613",
          "format": "OpenAI"
        }
      }
    }
  ]
}
```

#### Option B: 🌐 Dynamic Model Discovery via Gateway APIs

**📋 When to Use:**
- Models change frequently and need real-time discovery
- You have dynamic model provisioning
- Gateway manages model availability automatically

**🔧 Implementation Requirements:**
If you choose dynamic discovery, your gateway must expose **2 additional endpoints**:

##### 1. 📋 List Models Endpoint

**Endpoint:**
```
GET {gateway-base-url}/models
GET {gateway-base-url}/v1/models  
GET {gateway-base-url}/deployments
```

**Response Format Options:**

**Azure OpenAI Format:**
```json
{
  "value": [
    {
      "name": "gpt-4-deployment",
      "properties": {
        "model": {
          "format": "OpenAI",
          "name": "gpt-4",
          "version": "0613"
        }
      }
    },
    {
      "name": "gpt-35-turbo-deployment",
      "properties": {
        "model": {
          "format": "OpenAI", 
          "name": "gpt-3.5-turbo",
          "version": "0613"
        }
      }
    }
  ]
}
```

**OpenAI Format:**
```json
{
  "data": [
    {
      "id": "gpt-4",
      "object": "model",
      "created": 1687882411,
      "owned_by": "openai"
    },
    {
      "id": "gpt-3.5-turbo",
      "object": "model", 
      "created": 1677610602,
      "owned_by": "openai"
    }
  ]
}
```

##### 2. 🎯 Get Model Details Endpoint

**Endpoint:**
```
GET {gateway-base-url}/models/{deploymentName}
GET {gateway-base-url}/v1/models/{deploymentName}
GET {gateway-base-url}/deployments/{deploymentName}
```

> **⚠️ Important**: The placeholder must be exactly `{deploymentName}` (case-sensitive) for proper model discovery functionality.

**Response Format Options:**

**Azure OpenAI Format:**
```json
{
  "name": "gpt-4-deployment",
  "properties": {
    "model": {
      "format": "OpenAI",
      "name": "gpt-4", 
      "version": "0613"
    }
  }
}
```

**OpenAI Format:**
```json
{
  "id": "gpt-4",
  "object": "model",
  "created": 1687882411,
  "owned_by": "openai"
}
```

### Requirement 3: 🔐 Authentication Support

Your gateway must support one of the following authentication methods:

#### 🔑 API Key Authentication

By default, API keys are sent in the `api-key` header. This behavior can be customized using the `authConfig` object in your ModelGateway connection to specify different header names and formats.

**Default Format:**
```http
api-key: your-api-key-here
```

**Custom Header Formats (via authConfig):**
```http
Authorization: Bearer your-api-key-here
X-API-Key: your-api-key-here
X-Custom-Auth: Token your-api-key-here
```

**OpenAI Compatible:**
```http
Authorization: Bearer sk-your-openai-key-here
```

#### 🛡️ OAuth 2.0 Client Credentials (Coming Soon)

Support for OAuth 2.0 client credentials flow will be available in the upcoming release.

### Requirement 4: 🌐 Network Accessibility

#### 📡 Public Internet Access

- Gateway should be accessible via HTTPS on public internet

OR 

#### 🔒 Private Network Access (Coming Soon)

- Use Foundry Agents BYO VNet feature for private network scenarios
- Ensure gateway is reachable within the virtual network
- Configure appropriate network security groups and firewall rules

---

> **💡 Configuration Tip**: For detailed connection parameter configuration and examples, see the [ModelGateway Connection Objects](./ModelGateway-Connection-Objects.md) documentation.

---

## 📋 Gathering Connection Details

Once your gateway passes all tests, collect the following information for creating your ModelGateway connection:

### 🎯 1. Target URL

**Extract Base URL:** Take everything before the endpoint paths:

**Examples:**
- If chat completions endpoint is: `https://my-gateway.company.com/api/v1/chat/completions`
- Target URL would be: `https://my-gateway.company.com/api/v1`

- If chat completions endpoint is: `https://api.openai.com/v1/chat/completions`  
- Target URL would be: `https://api.openai.com/v1`

### 🔧 2. API Version (If Required)

**Check API Version Parameter:** 
- Look for `api-version` query parameter requirements in your gateway
- Note the value if required for chat completions calls
- Common examples: `2024-02-01`, `2025-03-01`, etc.

### 🛤️ 3. Deployment Routing Method

Determine how your gateway handles model/deployment routing:

**Path-based Routing (`"deploymentInPath": "true"`):**
```
POST /deployments/{deployment-name}/chat/completions
```

**Parameter-based Routing (`"deploymentInPath": "false"`):**
```
POST /chat/completions
Body: {"model": "deployment-name"}
```

### 🔍 4. Model Discovery Configuration

**For Static Discovery:**
- List all available models/deployments
- Include model names, versions, and formats
- Document deployment names for connection configuration

**For Dynamic Discovery:**
- Document the list models endpoint path
- Document the get model endpoint path with `{deploymentName}` placeholder
- Specify the response format (OpenAI or AzureOpenAI)

### 🔐 5. Authentication Details

**For API Key Authentication:**
- Note the header name (e.g., `Authorization`, `X-API-Key`)
- Note the format (e.g., `Bearer {api_key}`, `{api_key}`, `Token {api_key}`)

### 🏷️ 6. Custom Headers (Optional)

> **⚠️ Security Warning**: Do not use custom headers for authentication credentials or secrets. Use the proper authentication configuration instead.

Document any custom headers required by your gateway:
- Rate limiting headers
- Routing policy headers  
- Client identification headers
- Environment specification headers

---

## 📝 Example Gateway Configurations

### 🌐 OpenAI Direct Connection

```json
{
  "target": "https://api.openai.com/v1",
  "authConfig": {
    "type": "api_key",
    "name": "Authorization", 
    "format": "Bearer {api_key}"
  },
  "deploymentInPath": "false",
  "modelDiscovery": {
    "listModelsEndpoint": "/models",
    "getModelEndpoint": "/models/{deploymentName}",
    "deploymentProvider": "OpenAI"
  }
}
```

### 🏢 Enterprise Gateway with Custom Auth

```json
{
  "target": "https://enterprise-gateway.company.com/ai/v2",
  "authConfig": {
    "type": "api_key",
    "name": "X-API-Key",
    "format": "Token {api_key}"
  },
  "deploymentInPath": "true", 
  "inferenceAPIVersion": "2024-02-01",
  "customHeaders": {
    "X-Environment": "production",
    "X-Client-App": "foundry-agents"
  }
}
```

### 🔧 Self-Hosted Gateway with Static Models

```json
{
  "target": "https://my-gateway.internal.com/llm",
  "authConfig": {
    "type": "api_key", 
    "name": "Authorization",
    "format": "Bearer {api_key}"
  },
  "deploymentInPath": "false",
  "models": [
    {
      "name": "local-gpt-4",
      "properties": {
        "model": {
          "name": "gpt-4",
          "version": "local-fine-tuned",
          "format": "OpenAI"
        }
      }
    }
  ]
}
```

---

## 🔗 Next Steps

Once your gateway meets all requirements and you've gathered the connection details:

1. **📋 Create ModelGateway Connection**: Use the [ModelGateway Connection Setup Guide](./README.md) to create your connection
2. **🎯 Configure Connection Metadata**: Use the collected details to configure your connection properly  
3. **🧪 Test with Foundry Agents**: Deploy a test agent to verify the connection works correctly
4. **📊 Monitor Performance**: Monitor your gateway for performance and reliability

### 📚 Related Resources

| Resource | Description | Link |
|----------|-------------|------|
| **📋 ModelGateway Connection Setup** | Step-by-step instructions for creating ModelGateway connections | [Setup Guide](./README.md) |
| **📖 ModelGateway Connection Schema** | Detailed JSON schema and configuration options | [Connection Objects](./ModelGateway-Connection-Objects.md) |
| **🤖 Agent Development Samples** | Sample agents for testing your connection | [Azure AI Projects Samples](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/ai/azure-ai-projects/samples/agents) |
| **🔧 Integration Guide** | Overview of gateway integration with Foundry | [Integration Guide](../apim-and-modelgateway-integration-guide.md) |

> **💡 Pro Tip**: Start with static model configuration for initial testing, then move to dynamic discovery if needed. Static configuration provides better performance and simpler troubleshooting.