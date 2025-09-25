# Codex MCP Wrapper

Async Python utilities to manage `codex mcp serve`, launch detached Codex agents,
and supervise their MCP event streams.

```python
import asyncio
from codex_mcp import CodexMCPClient

async def main():
    client = CodexMCPClient()
    await client.start()

    session = await client.start_detached_codex("Summarise the latest logs")

    async def watcher():
        while True:
            event = await session.next_event()
            print("event", event.get("msg", {}).get("type"))
            if session.done():
                break

    watcher_task = asyncio.create_task(watcher())
    result = await session.wait_result()
    print("final result", result)

    watcher_task.cancel()
    await client.close()

asyncio.run(main())
```
