+++ 
  title = "Thoughts on MCP Code Mode "
  date = "2026-02-24T11:23:31.736Z"
  tags = [ "Cloudflare","MCP","APIs" ]
  draft = "false"
+++
Thoughs on Cloudflare's MCP Code Mode announcement https://blog.cloudflare.com/code-mode-mcp/

There is a balance among the responsibilities of the different parties involved in an MCP interaction.
* LLM
* Host/Agent
* Provider
* Protocol
* User/customer


It is true that currently, everyone is offloading costs to the user through tokens, and this causes a problem. However, I am not sure that shifting this completely to the provider is the right choice, at least for now.

In the Code Mode LLM user tool context, the context is minimized, but code generation, the middle layer still consumes tokens, and I am not seeing a comparison between the size of the API and the size of the generated code.
Cloudflare is in a position to bill for the compute of the generated code in their sandbox, further improving the roi from this approach.
  

