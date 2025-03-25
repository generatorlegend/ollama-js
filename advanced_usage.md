# Advanced Usage of Ollama JavaScript Library

This guide covers advanced usage of the Ollama JavaScript library, including working with embeddings, using tools, and handling multimodal inputs. These features allow you to leverage the full power of Ollama in your applications.

## Table of Contents

1. [Working with Embeddings](#working-with-embeddings)
2. [Using Tools](#using-tools)
3. [Handling Multimodal Inputs](#handling-multimodal-inputs)

## Working with Embeddings

Ollama provides functionality to generate embeddings for text inputs. Embeddings are vector representations of text that can be used for various natural language processing tasks, such as semantic search or text classification.

### Generating Embeddings

To generate embeddings for a text input, use the `embed` method:

```javascript
import ollama from 'ollama'

async function generateEmbedding() {
  const response = await ollama.embed({
    model: 'llama2',
    prompt: 'Hello, world!'
  })

  console.log(response.embedding)
}

generateEmbedding()
```

The `embed` method returns an `EmbedResponse` object containing the embedding vector.

### Batch Embeddings

For processing multiple texts at once, use the `embeddings` method:

```javascript
import ollama from 'ollama'

async function generateBatchEmbeddings() {
  const response = await ollama.embeddings({
    model: 'llama2',
    prompt: ['Hello, world!', 'How are you?', 'Embeddings are useful']
  })

  console.log(response.embeddings)
}

generateBatchEmbeddings()
```

The `embeddings` method returns an `EmbeddingsResponse` object containing an array of embedding vectors.

## Using Tools

Ollama supports the use of tools, which are predefined functions that the model can call to perform specific tasks. This feature enables more complex interactions and allows the model to access external data or perform calculations.

### Defining Tools

Tools are defined as objects with a specific structure. Here's an example of defining two mathematical tools:

```javascript
const addTwoNumbersTool = {
  type: 'function',
  function: {
    name: 'addTwoNumbers',
    description: 'Add two numbers together',
    parameters: {
      type: 'object',
      required: ['a', 'b'],
      properties: {
        a: { type: 'number', description: 'The first number' },
        b: { type: 'number', description: 'The second number' }
      }
    }
  }
}

const subtractTwoNumbersTool = {
  type: 'function',
  function: {
    name: 'subtractTwoNumbers',
    description: 'Subtract two numbers',
    parameters: {
      type: 'object',
      required: ['a', 'b'],
      properties: {
        a: { type: 'number', description: 'The first number' },
        b: { type: 'number', description: 'The second number' }
      }
    }
  }
}
```

### Implementing Tool Functions

For each tool, you need to implement the corresponding function:

```javascript
function addTwoNumbers(args: { a: number, b: number }): number {
  return args.a + args.b
}

function subtractTwoNumbers(args: { a: number, b: number }): number {
  return args.a - args.b
}

const availableFunctions = {
  addTwoNumbers: addTwoNumbers,
  subtractTwoNumbers: subtractTwoNumbers
}
```

### Using Tools in Chat

To use tools in a chat context, include them in the `chat` method call:

```javascript
async function chatWithTools() {
  const messages = [{ role: 'user', content: 'What is three minus one?' }]
  
  const response = await ollama.chat({
    model: 'llama2',
    messages: messages,
    tools: [addTwoNumbersTool, subtractTwoNumbersTool]
  })

  if (response.message.tool_calls) {
    for (const tool of response.message.tool_calls) {
      const functionToCall = availableFunctions[tool.function.name]
      if (functionToCall) {
        const output = functionToCall(tool.function.arguments)
        console.log('Function output:', output)

        // Add the function response to messages for the model to use
        messages.push(response.message)
        messages.push({
          role: 'tool',
          content: output.toString(),
        })
      }
    }

    // Get final response from model with function outputs
    const finalResponse = await ollama.chat({
      model: 'llama2',
      messages: messages
    })
    console.log('Final response:', finalResponse.message.content)
  }
}

chatWithTools()
```

This example demonstrates how to use tools in a conversation, process the model's tool calls, and provide the results back to the model for a final response.

## Handling Multimodal Inputs

Ollama supports multimodal inputs, allowing you to work with both text and images in your prompts. This is particularly useful for tasks that require visual understanding alongside language processing.

### Using Images in Prompts

To include an image in your prompt, you can pass the image file path to the `generate` method:

```javascript
import ollama from 'ollama'

async function generateWithImage() {
  const imagePath = './path/to/your/image.jpg'
  const response = await ollama.generate({
    model: 'llava',
    prompt: 'Describe this image:',
    images: [imagePath],
    stream: true,
  })

  for await (const part of response) {
    process.stdout.write(part.response)
  }
}

generateWithImage()
```

In this example, we're using the 'llava' model, which is designed to handle both text and image inputs. The `images` array can contain multiple image file paths if needed.

### Streaming Responses

As shown in the example above, you can set `stream: true` to receive the response in chunks. This is useful for displaying partial results as they become available, especially for longer responses.

### Using Images in Chat

You can also use images in a chat context:

```javascript
async function chatWithImage() {
  const imagePath = './path/to/your/image.jpg'
  const messages = [
    { role: 'user', content: 'What do you see in this image?', images: [imagePath] }
  ]

  const response = await ollama.chat({
    model: 'llava',
    messages: messages,
    stream: true,
  })

  for await (const part of response) {
    process.stdout.write(part.response)
  }
}

chatWithImage()
```

This approach allows for more interactive conversations that involve visual elements.

By leveraging these advanced features - embeddings, tools, and multimodal inputs - you can create more sophisticated and powerful applications with the Ollama JavaScript library. These capabilities enable a wide range of use cases, from complex decision-making systems to visual question-answering applications.