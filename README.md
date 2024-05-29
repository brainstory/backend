# Brainstory Backend

Notes on how to develop your backend instance.

## Guidelines

We've exported the OpenAPI specs for the business logic and the authentication. This by itself is probably not enough to build a complete replacement backend - but we hope it may give a good starting point. 

For a "minimum-viable-brainstory", the 3 main endpoints required would be the `/stt`, `/response` and `/streaming-response` ones. While the first two are understandable, the latter requires some explanation.

The `/streaming-response` endpoint was used for the summarization call at the end of a session. The `socket_target.url` should yield a websocket protocol URL, streaming the LLM response back to the client (thus avoiding potential service timeouts), which results were then saved by the client calling the proper API to do so.  

The format for the streaming messages in the websocket packets is the following

```json
{
    "type": "",
    "content": "",
    "timestamp": ""
}
```

where `type` can be `status`, `chunk` or `cumulative`; `content` is the message content and `timestamp` is a timestamp value (the actual values don't matter, but it's potentially helpful to identify issues or sort out-of-order packets). The `status` messages have a user-readable `status` as their contents; the `chunk` messages have a streamed LLM response chunk and finally a `cumulative` message is sent when the LLM finished responding, and we send the entire output in one. 