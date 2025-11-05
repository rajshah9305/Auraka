Aura OS - A-OK (Agentic OS Kernel) Prototype
This is a complete, runnable prototype of the "Aura" (Agentic Universal Runtime Architecture) concept.
It fully implements all 5 core layers of the architecture as a set of locally-runnable services.
Core Architecture
1. aura_kernel.py (The Kernel & Processor):
• This is the "OS" itself, built with langgraph.
• It contains the central AppState (Working Memory / "RAM").
• It runs the "Processor" (a single, tool-calling agent node) that drives the system.
• Crucially, it contains NO tool logic. It only has small, client-side tools that make HTTP calls.
2. aura_mcp_servers.py (The I/O Bus & Peripherals):
• This is a standalone FastAPI microservice that acts as the entire "I/O Bus."
• It hosts all the "peripherals" as API endpoints:
• /fs/ (File System): Reads and writes to a ./workspace/ directory on the host.
• /sandbox/ (Secure Core): Simulated by executing code in a subprocess.
• /memory/ (Long-Term Memory / "Hard Drive"): A complete RAG pipeline using FAISS (in-memory Vector DB) to add and query memory.
• /web/ (Web Peripheral): Uses duckduckgo-search to search the live internet.
3. run.sh (The Bootloader):
• A shell script to automatically install all dependencies, boot the aura_mcp_servers.py in the background, run the aura_kernel.py, and then clean up.
How it Works (The "Aura" Loop)
1. You run ./run.sh.
2. The aura_mcp_servers.py (I/O Bus) starts on http://127.0.0.1:8100.
3. The aura_kernel.py (Kernel) starts and is given a complex task.
4. The Kernel's Agent (Processor) decides it needs to search the web.
5. The Kernel's Tool Node calls the search_web() tool.
6. This tool makes an HTTP call: GET http://127.0.0.1:8100/web/search?query=....
7. The I/O Bus receives the call, runs the real duckduckgo_search, and returns the JSON result.
8. The Kernel receives the result and feeds it back to the Agent.
9. The Agent (Processor) now has information and decides to write a plan to a file.
10. The Kernel calls the write_file() tool.
11. This tool makes an HTTP call: POST http://127.0.0.1:8100/fs/write.
12. The I/O Bus receives the call and saves the content to ./workspace/plan.md.
13. ...This loop continues, fully decoupling the agent's brain (Kernel) from its "hands" (I/O Bus).
Dependencies (Handled by run.sh)
• langgraph
• langchain-openai
• fastapi
• uvicorn
• httpx (for the client)
• python-multipart (for fastapi)
• duckduckgo-search (for the web peripheral)
• faiss-cpu (for the vector memory)
• langchain (for embeddings & text splitting)
A-OK: The "Aura-OS Kernel"
The code provided here (A-OK) is a robust, functional prototype. It is not "production-ready" in the enterprise sense, as it lacks:
• True Security: The "Secure Core" is a subprocess, which is NOT SAFE for production. A real system would use Firecracker VMs (e.g., via e2b-sdk).
• Authentication: The MCP servers are unsecured (no API keys between services).
• Federation (Layer 6): This prototype does not (yet) implement the A2A protocol.
It is, however, a complete and powerful demonstration of your 5-layer architecture.
