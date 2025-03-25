# Ollama JavaScript API Reference

This document provides a comprehensive reference for the Ollama JavaScript library, covering both Node.js and browser usage.

## Table of Contents

1. [Installation](#installation)
2. [Initialization](#initialization)
3. [Methods](#methods)
   - [generate](#generate)
   - [chat](#chat)
   - [create](#create)
   - [pull](#pull)
   - [push](#push)
   - [delete](#delete)
   - [copy](#copy)
   - [list](#list)
   - [show](#show)
   - [embed](#embed)
   - [embeddings](#embeddings)
   - [ps](#ps)
   - [abort](#abort)
4. [Types](#types)

## Installation

To install the Ollama JavaScript library, use npm:

```bash
npm install ollama
```

## Initialization

To use the Ollama library, import it and create an instance:

```javascript
import ollama from 'ollama'

// Or for CommonJS
// const ollama = require('ollama').default
```

You can also create a custom instance with specific configuration:

```javascript
import { Ollama } from 'ollama'

const customOllama = new Ollama({
  host: 'http://localhost:11434',
  headers: { 'Custom-Header': 'value' }
})
```

## Methods

### generate

Generates a response from a text prompt.

```typescript
generate(request: GenerateRequest): Promise<GenerateResponse | AbortableAsyncIterator<GenerateResponse>>
```

#### Parameters

- `request`: `GenerateRequest` object with the following properties:
  - `model`: string (required) - The name of the model to use.
  - `prompt`: string (required) - The prompt to generate from.
  - `system`: string (optional) - System prompt to use.
  - `template`: string (optional) - The prompt template to use.
  - `context`: number[] (optional) - The context to use for generation.
  - `options`: Partial<Options> (optional) - Additional options for generation.
  - `stream`: boolean (optional) - Whether to stream the response.
  - `raw`: boolean (optional) - Whether to return raw output.
  - `format`: string | object (optional) - The format to use for the response.
  - `images`: Uint8Array[] | string[] (optional) - Images to include in the generation.
  - `keep_alive`: string | number (optional) - Duration to keep the model alive.

#### Returns

- If `stream: true`, returns a `Promise<AbortableAsyncIterator<GenerateResponse>>`.
- Otherwise, returns a `Promise<GenerateResponse>`.

#### Example

```javascript
const response = await ollama.generate({
  model: 'llama2',
  prompt: 'Why is the sky blue?'
})

console.log(response.response)
```

### chat

Chats with the model using a series of messages.

```typescript
chat(request: ChatRequest): Promise<ChatResponse | AbortableAsyncIterator<ChatResponse>>
```

#### Parameters

- `request`: `ChatRequest` object with the following properties:
  - `model`: string (required) - The name of the model to use.
  - `messages`: Message[] (optional) - The conversation history.
  - `stream`: boolean (optional) - Whether to stream the response.
  - `format`: string | object (optional) - The format to use for the response.
  - `options`: Partial<Options> (optional) - Additional options for chat.
  - `keep_alive`: string | number (optional) - Duration to keep the model alive.
  - `tools`: Tool[] (optional) - Tools available for the model to use.

#### Returns

- If `stream: true`, returns a `Promise<AbortableAsyncIterator<ChatResponse>>`.
- Otherwise, returns a `Promise<ChatResponse>`.

#### Example

```javascript
const response = await ollama.chat({
  model: 'llama2',
  messages: [
    { role: 'user', content: 'Hello, how are you?' }
  ]
})

console.log(response.message.content)
```

### create

Creates a new model.

```typescript
create(request: CreateRequest): Promise<ProgressResponse | AbortableAsyncIterator<ProgressResponse>>
```

#### Parameters

- `request`: `CreateRequest` object with the following properties:
  - `model`: string (required) - The name of the model to create.
  - `from`: string (optional) - Base model to create from.
  - `stream`: boolean (optional) - Whether to stream the response.
  - `quantize`: string (optional) - Quantization level.
  - `template`: string (optional) - The prompt template to use.
  - `system`: string (optional) - System prompt to use.
  - `parameters`: Record<string, unknown> (optional) - Additional parameters.
  - `messages`: Message[] (optional) - Initial conversation history.
  - `adapters`: Record<string, string> (optional) - Adapters to use.

#### Returns

- If `stream: true`, returns a `Promise<AbortableAsyncIterator<ProgressResponse>>`.
- Otherwise, returns a `Promise<ProgressResponse>`.

#### Example

```javascript
const response = await ollama.create({
  model: 'my-custom-model',
  from: 'llama2'
})

console.log(response.status)
```

### pull

Pulls a model from the Ollama registry.

```typescript
pull(request: PullRequest): Promise<ProgressResponse | AbortableAsyncIterator<ProgressResponse>>
```

#### Parameters

- `request`: `PullRequest` object with the following properties:
  - `model`: string (required) - The name of the model to pull.
  - `insecure`: boolean (optional) - Whether to allow insecure connections.
  - `stream`: boolean (optional) - Whether to stream the response.

#### Returns

- If `stream: true`, returns a `Promise<AbortableAsyncIterator<ProgressResponse>>`.
- Otherwise, returns a `Promise<ProgressResponse>`.

#### Example

```javascript
const response = await ollama.pull({
  model: 'llama2'
})

console.log(response.status)
```

### push

Pushes a model to the Ollama registry.

```typescript
push(request: PushRequest): Promise<ProgressResponse | AbortableAsyncIterator<ProgressResponse>>
```

#### Parameters

- `request`: `PushRequest` object with the following properties:
  - `model`: string (required) - The name of the model to push.
  - `insecure`: boolean (optional) - Whether to allow insecure connections.
  - `stream`: boolean (optional) - Whether to stream the response.

#### Returns

- If `stream: true`, returns a `Promise<AbortableAsyncIterator<ProgressResponse>>`.
- Otherwise, returns a `Promise<ProgressResponse>`.

#### Example

```javascript
const response = await ollama.push({
  model: 'my-custom-model'
})

console.log(response.status)
```

### delete

Deletes a model from the server.

```typescript
delete(request: DeleteRequest): Promise<StatusResponse>
```

#### Parameters

- `request`: `DeleteRequest` object with the following property:
  - `model`: string (required) - The name of the model to delete.

#### Returns

- Returns a `Promise<StatusResponse>`.

#### Example

```javascript
const response = await ollama.delete({
  model: 'my-custom-model'
})

console.log(response.status)
```

### copy

Copies a model from one name to another.

```typescript
copy(request: CopyRequest): Promise<StatusResponse>
```

#### Parameters

- `request`: `CopyRequest` object with the following properties:
  - `source`: string (required) - The name of the model to copy from.
  - `destination`: string (required) - The new name for the copied model.

#### Returns

- Returns a `Promise<StatusResponse>`.

#### Example

```javascript
const response = await ollama.copy({
  source: 'llama2',
  destination: 'my-llama2-copy'
})

console.log(response.status)
```

### list

Lists the models on the server.

```typescript
list(): Promise<ListResponse>
```

#### Returns

- Returns a `Promise<ListResponse>`.

#### Example

```javascript
const response = await ollama.list()

console.log(response.models)
```

### show

Shows the metadata of a model.

```typescript
show(request: ShowRequest): Promise<ShowResponse>
```

#### Parameters

- `request`: `ShowRequest` object with the following properties:
  - `model`: string (required) - The name of the model to show.
  - `system`: string (optional) - System prompt to use.
  - `template`: string (optional) - The prompt template to use.
  - `options`: Partial<Options> (optional) - Additional options.

#### Returns

- Returns a `Promise<ShowResponse>`.

#### Example

```javascript
const response = await ollama.show({
  model: 'llama2'
})

console.log(response.modelfile)
```

### embed

Embeds text input into vectors.

```typescript
embed(request: EmbedRequest): Promise<EmbedResponse>
```

#### Parameters

- `request`: `EmbedRequest` object with the following properties:
  - `model`: string (required) - The name of the model to use.
  - `input`: string | string[] (required) - The text to embed.
  - `truncate`: boolean (optional) - Whether to truncate the input.
  - `options`: Partial<Options> (optional) - Additional options.
  - `keep_alive`: string | number (optional) - Duration to keep the model alive.

#### Returns

- Returns a `Promise<EmbedResponse>`.

#### Example

```javascript
const response = await ollama.embed({
  model: 'llama2',
  input: 'Hello, world!'
})

console.log(response.embeddings)
```

### embeddings

Embeds a text prompt into a vector.

```typescript
embeddings(request: EmbeddingsRequest): Promise<EmbeddingsResponse>
```

#### Parameters

- `request`: `EmbeddingsRequest` object with the following properties:
  - `model`: string (required) - The name of the model to use.
  - `prompt`: string (required) - The text to embed.
  - `options`: Partial<Options> (optional) - Additional options.
  - `keep_alive`: string | number (optional) - Duration to keep the model alive.

#### Returns

- Returns a `Promise<EmbeddingsResponse>`.

#### Example

```javascript
const response = await ollama.embeddings({
  model: 'llama2',
  prompt: 'Hello, world!'
})

console.log(response.embedding)
```

### ps

Lists the running models on the server.

```typescript
ps(): Promise<ListResponse>
```

#### Returns

- Returns a `Promise<ListResponse>`.

#### Example

```javascript
const response = await ollama.ps()

console.log(response.models)
```

### abort

Aborts any ongoing streamed requests to Ollama.

```typescript
abort(): void
```

#### Example

```javascript
ollama.abort()
```

## Types

For detailed type information, please refer to the `interfaces.ts` file in the source code. Key types include:

- `GenerateRequest`
- `ChatRequest`
- `CreateRequest`
- `PullRequest`
- `PushRequest`
- `DeleteRequest`
- `CopyRequest`
- `ShowRequest`
- `EmbedRequest`
- `EmbeddingsRequest`
- `GenerateResponse`
- `ChatResponse`
- `ProgressResponse`
- `ListResponse`
- `ShowResponse`
- `EmbedResponse`
- `EmbeddingsResponse`
- `StatusResponse`

These types define the structure of request and response objects used in the API methods.