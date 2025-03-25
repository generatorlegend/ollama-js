# Getting Started with Ollama JavaScript Library

The Ollama JavaScript library provides an easy way to integrate your JavaScript project with [Ollama](https://github.com/jmorganca/ollama). This guide will help you get started with using the library in both Node.js and browser environments.

## Installation

To install the Ollama JavaScript library, use npm:

```bash
npm install ollama
```

## Basic Usage

### Node.js Environment

For Node.js applications, you can import and use the library as follows:

```javascript
import ollama from 'ollama'

async function main() {
  const response = await ollama.chat({
    model: 'llama2',
    messages: [{ role: 'user', content: 'Why is the sky blue?' }],
  })
  console.log(response.message.content)
}

main()
```

### Browser Environment

For browser applications, you need to import the browser-specific module:

```javascript
import ollama from 'ollama/browser'

async function main() {
  const response = await ollama.chat({
    model: 'llama2',
    messages: [{ role: 'user', content: 'Why is the sky blue?' }],
  })
  console.log(response.message.content)
}

main()
```

## Making API Calls

The Ollama JavaScript library provides several methods to interact with Ollama models. Here are some basic examples:

### Chat

To have a conversation with a model:

```javascript
const response = await ollama.chat({
  model: 'llama2',
  messages: [
    { role: 'user', content: 'What is the capital of France?' },
    { role: 'assistant', content: 'The capital of France is Paris.' },
    { role: 'user', content: 'And what is its population?' }
  ],
})
console.log(response.message.content)
```

### Generate

To generate text based on a prompt:

```javascript
const response = await ollama.generate({
  model: 'llama2',
  prompt: 'Write a short poem about coding',
})
console.log(response.response)
```

### Embeddings

To get vector embeddings for a given input:

```javascript
const response = await ollama.embeddings({
  model: 'llama2',
  prompt: 'The quick brown fox jumps over the lazy dog',
})
console.log(response.embeddings)
```

## Streaming Responses

You can enable response streaming by setting `stream: true` in your request. This returns an `AsyncGenerator` where each part is an object in the stream:

```javascript
const message = { role: 'user', content: 'Write a short story about a robot' }
const response = await ollama.chat({ model: 'llama2', messages: [message], stream: true })

for await (const part of response) {
  process.stdout.write(part.message.content)
}
```

## Custom Client

If you need to use a custom Ollama host or fetch implementation, you can create a custom client:

```javascript
import { Ollama } from 'ollama'

const ollama = new Ollama({ host: 'http://localhost:11434' })

const response = await ollama.chat({
  model: 'llama2',
  messages: [{ role: 'user', content: 'Hello, how are you?' }],
})
console.log(response.message.content)
```

This getting started guide covers the basics of using the Ollama JavaScript library. For more detailed information on available methods and options, please refer to the [API documentation](https://github.com/ollama/ollama-js#api).