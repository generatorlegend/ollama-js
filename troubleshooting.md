# Troubleshooting

This guide covers common issues you might encounter when using the Ollama JavaScript library and provides potential solutions and debugging tips.

## Connection Issues

### Unable to Connect to Ollama Server

If you're experiencing connection issues, try the following:

1. Ensure the Ollama server is running on your machine or the specified host.
2. Check if the host and port are correct in your configuration:

```javascript
import { Ollama } from 'ollama'

const ollama = new Ollama({ host: 'http://127.0.0.1:11434' })
```

3. Verify that there are no firewall or network restrictions blocking the connection.

## API Errors

### ResponseError: Error 404: Not Found

This error typically occurs when the requested model is not available on the Ollama server.

1. Check if you've spelled the model name correctly in your request.
2. Ensure the model is downloaded and available on your Ollama server.
3. Use the `ollama.list()` method to see available models:

```javascript
const models = await ollama.list()
console.log(models)
```

### ResponseError: Error 400: Bad Request

This error can occur for various reasons related to invalid input. Common causes include:

1. Missing required parameters in your request.
2. Incorrect data types for parameters.
3. Exceeding maximum token limits for the model.

Review your request object and ensure all required fields are present and correctly formatted.

## Streaming Issues

### Stream Not Yielding Results

If you're using streaming but not receiving results:

1. Ensure you've set `stream: true` in your request object.
2. Check that you're correctly iterating over the AsyncGenerator:

```javascript
const response = await ollama.chat({ model: 'llama2', messages: [message], stream: true })
for await (const part of response) {
  console.log(part.message.content)
}
```

3. Verify that your `for await...of` loop is within an asynchronous function.

## Performance Issues

### Slow Response Times

If you're experiencing slow response times:

1. Check your network connection and latency to the Ollama server.
2. Consider using a smaller or more efficient model if available.
3. Adjust the `keep_alive` parameter to keep the model loaded for subsequent requests:

```javascript
const response = await ollama.chat({
  model: 'llama2',
  messages: [message],
  keep_alive: '5m'
})
```

## Debugging Tips

1. Enable verbose logging in your application to capture more details about the requests and responses.

2. Use the `show` method to inspect model details:

```javascript
const modelInfo = await ollama.show({ model: 'llama2' })
console.log(modelInfo)
```

3. For browser environments, use the browser's developer tools to inspect network requests and responses.

4. When reporting issues, include:
   - The version of the Ollama JavaScript library you're using
   - The model and parameters used in your request
   - Any error messages or unexpected behavior observed

## Still Having Issues?

If you're still encountering problems after trying these troubleshooting steps, consider:

1. Checking the [Ollama GitHub repository](https://github.com/jmorganca/ollama) for known issues or to report a new one.
2. Joining the Ollama community forums or chat channels for additional support.
3. Reviewing the [Ollama REST API documentation](https://github.com/jmorganca/ollama/blob/main/docs/api.md) to ensure your usage aligns with the expected behavior.

Remember to keep your Ollama server and JavaScript library up to date, as new versions may include bug fixes and performance improvements.