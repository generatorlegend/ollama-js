# Error Handling in Ollama JavaScript Library

This document outlines the error handling mechanisms in the Ollama JavaScript library, including different types of errors that can occur, how they are represented, and best practices for handling them in your applications.

## Types of Errors

The Ollama JavaScript library primarily uses two types of errors:

1. `ResponseError`: A custom error class for handling response errors from the Ollama API.
2. Standard JavaScript `Error`: Used for general error conditions.

### ResponseError

The `ResponseError` class extends the standard `Error` class and is specifically designed to handle errors returned by the Ollama API. It includes additional properties to provide more context about the error:

- `error`: A string describing the error message.
- `status_code`: A number representing the HTTP status code of the error response.

Here's how the `ResponseError` class is defined:

```javascript
class ResponseError extends Error {
  constructor(
    public error: string,
    public status_code: number,
  ) {
    super(error)
    this.name = 'ResponseError'

    if (Error.captureStackTrace) {
      Error.captureStackTrace(this, ResponseError)
    }
  }
}
```

## Error Handling Mechanisms

### checkOk Function

The library uses a `checkOk` function to validate API responses. This function:

1. Checks if the response is ok (status in the range 200-299).
2. If not ok, it attempts to parse the error message from the response body.
3. Throws a `ResponseError` with the parsed error message and status code.

Here's a simplified version of the `checkOk` function:

```javascript
const checkOk = async (response: Response): Promise<void> => {
  if (response.ok) {
    return
  }
  let message = `Error ${response.status}: ${response.statusText}`
  let errorData: ErrorResponse | null = null

  if (response.headers.get('content-type')?.includes('application/json')) {
    try {
      errorData = (await response.json()) as ErrorResponse
      message = errorData.error || message
    } catch (error) {
      console.log('Failed to parse error response as JSON')
    }
  } else {
    try {
      const textResponse = await response.text()
      message = textResponse || message
    } catch (error) {
      console.log('Failed to get text from error response')
    }
  }

  throw new ResponseError(message, response.status)
}
```

### Streaming Errors

For streaming responses, errors are handled within the `AbortableAsyncIterator` class. If an error message is encountered in the stream, it's thrown as a standard `Error`:

```javascript
async *[Symbol.asyncIterator]() {
  for await (const message of this.itr) {
    if ('error' in message) {
      throw new Error(message.error)
    }
    // ... rest of the iterator logic
  }
  throw new Error('Did not receive done or success response in stream.')
}
```

## Best Practices for Error Handling

When using the Ollama JavaScript library, consider the following best practices for error handling:

1. **Use try-catch blocks**: Wrap API calls in try-catch blocks to handle potential errors:

```javascript
try {
  const response = await ollama.generate({ /* ... */ })
  // Process the response
} catch (error) {
  if (error instanceof ResponseError) {
    console.error(`API Error: ${error.error} (Status: ${error.status_code})`)
  } else {
    console.error(`Unexpected error: ${error.message}`)
  }
}
```

2. **Check for specific error types**: Differentiate between `ResponseError` and other types of errors to provide more specific error handling:

```javascript
if (error instanceof ResponseError) {
  // Handle API-specific errors
} else if (error instanceof TypeError) {
  // Handle network errors or other type errors
} else {
  // Handle other unexpected errors
}
```

3. **Handle streaming errors**: When using streaming responses, be prepared to handle errors that may occur during the stream:

```javascript
const stream = await ollama.generate({ /* ... */, stream: true })
try {
  for await (const chunk of stream) {
    // Process each chunk
  }
} catch (error) {
  console.error(`Stream error: ${error.message}`)
}
```

4. **Graceful degradation**: Implement fallback mechanisms or graceful degradation strategies when errors occur, especially for non-critical features.

5. **Logging**: Log errors for debugging and monitoring purposes, but be cautious about logging sensitive information.

By following these practices and understanding the error handling mechanisms in the Ollama JavaScript library, you can create more robust and reliable applications that gracefully handle various error scenarios.