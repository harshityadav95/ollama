# Accessing Ollama via REST API

## Introduction
Ollama is an open-source tool for running large language models (LLMs) locally on your machine. It provides a REST API that allows you to interact with models programmatically, enabling integration into applications, scripts, or other tools. The API is lightweight and runs on a local server, typically accessible at `http://localhost:11434`.

This documentation covers how to set up Ollama, access its REST API, and use key endpoints for generating text, chatting, and managing models. All examples assume a basic understanding of HTTP requests (e.g., using tools like `curl`, Postman, or programming libraries).

## Prerequisites
- **Ollama Installation**: Download and install Ollama from the official website (https://ollama.ai). Follow the platform-specific instructions for macOS, Windows, or Linux.
- **Model Setup**: Pull a model using the Ollama CLI. For example, to pull the Gemma 3 1B model:
  ```
  ollama pull gemma3:1b
  ```
  Available models can be listed with `ollama list`.
- **Server Start**: Ollama runs a local server automatically when you start it. If needed, ensure it's running on port 11434 (default).
- **Tools for Testing**: Use `curl` for command-line requests or libraries like `requests` in Python for programmatic access.

## API Overview
- **Base URL**: `http://localhost:11434`
- **Content-Type**: Most requests use `application/json`.
- **Authentication**: None required (local access only).
- **Common Endpoints**:
  - `/api/generate`: Generate text from a prompt.
  - `/api/chat`: Engage in conversational chat.
  - `/api/tags`: List available models.
  - `/api/pull`: Pull a model (alternative to CLI).
  - `/api/delete`: Delete a model.

Responses are typically in JSON format. Errors return HTTP status codes (e.g., 400 for bad requests).

## Key Endpoints and Usage

### 1. Generate Text (`/api/generate`)
This endpoint generates text based on a prompt using a specified model.

**Request**:
- Method: `POST`
- Body (JSON):
  ```json
  {
    "model": "gemma3:1b",
    "prompt": "Explain quantum computing in simple terms.",
    "stream": false
  }
  ```
  - `model`: Name of the model (e.g., "gemma3:1b").
  - `prompt`: The input text.
  - `stream`: Set to `true` for streaming responses (default: `false`).

**Response** (non-streaming):
```json
{
  "model": "gemma3:1b",
  "created_at": "2023-10-01T12:00:00Z",
  "response": "Quantum computing uses quantum bits or qubits...",
  "done": true
}
```

**Example with curl** (runnable command):
```bash
curl -X POST http://localhost:11434/api/generate -H "Content-Type: application/json" -d '{"model": "gemma3:1b", "prompt": "Hello, world!", "stream": false}'
```

**Python Example**:
```python
import requests

response = requests.post("http://localhost:11434/api/generate", json={
    "model": "gemma3:1b",
    "prompt": "What is AI?",
    "stream": false
})
print(response.json()["response"])
```

### 2. Chat (`/api/chat`)
For conversational interactions, maintaining context across messages.

**Request**:
- Method: `POST`
- Body (JSON):
  ```json
  {
    "model": "gemma3:1b",
    "messages": [
      {"role": "user", "content": "Hello!"}
    ],
    "stream": false
  }
  ```
  - `messages`: Array of message objects with `role` ("user", "assistant") and `content`.

**Response**:
Similar to `/api/generate`, but includes conversation history.

**Example** (runnable curl command):
```bash
curl -X POST http://localhost:11434/api/chat -H "Content-Type: application/json" -d '{"model": "gemma3:1b", "messages": [{"role": "user", "content": "Tell me a joke."}], "stream": false}'
```

### 3. List Models (`/api/tags`)
Retrieve a list of installed models.

**Request**:
- Method: `GET`

**Response**:
```json
{
  "models": [
    {
      "name": "gemma3:1b",
      "size": "3.8GB",
      "modified_at": "2023-10-01T12:00:00Z"
    }
  ]
}
```

**Example** (runnable curl command):
```bash
curl http://localhost:11434/api/tags
```

### 4. Pull a Model (`/api/pull`)
Download and install a model.

**Request**:
- Method: `POST`
- Body: `{"name": "gemma3:1b"}`

**Response**: Streaming JSON updates on progress.

**Example** (runnable curl command):
```bash
curl -X POST http://localhost:11434/api/pull -H "Content-Type: application/json" -d '{"name": "gemma3:1b"}'
```

### 5. Delete a Model (`/api/delete`)
Remove a model.

**Request**:
- Method: `DELETE`
- Body: `{"name": "gemma3:1b"}`

**Example** (runnable curl command):
```bash
curl -X DELETE http://localhost:11434/api/delete -H "Content-Type: application/json" -d '{"name": "gemma3:1b"}'
```

## Advanced Options
- **Streaming**: Set `"stream": true` for real-time responses. Handle as a stream of JSON objects.
- **Parameters**: Customize generation with options like `temperature`, `top_p`, etc., in the request body (e.g., `{"options": {"temperature": 0.7}}`).
- **Context Management**: For chat, include previous messages in the `messages` array to maintain context.

## Troubleshooting
- **Server Not Running**: Ensure Ollama is started (`ollama serve` if needed).
- **Model Not Found**: Pull the model first.
- **Port Issues**: Check if port 11434 is available.
- **Errors**: Common codes: 404 (model not found), 500 (server error). Check logs with `ollama logs`.
- **Performance**: Local models can be resource-intensive; monitor CPU/GPU usage.

## Best Practices
- Use streaming for long responses to avoid timeouts.
- Cache responses if integrating into apps.
- Test with small prompts first.
- Refer to the official Ollama docs (https://github.com/jmorganca/ollama) for updates.

For more details or examples, visit the Ollama repository.
