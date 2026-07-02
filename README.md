System  Title: Weather-Aware Agentic Travel Swarm via     Air bnb Model Context Protocol (MCP)

System Overview

Developed and deployed a localized, high-throughput multi-agent orchestration framework capable of dynamically querying environmental, temporal, and real-time lodging data. By serving a 30-billion parameter foundation model locally on an enterprise GPU workstation, the system ensures data privacy and eliminates cloud API rate limits. The final system utilizes a cyclic Reasoning and Acting (ReAct) loop to dynamically anchor relative dates, pull weather forecasts, and retrieve active Airbnb accommodations to recommend optimized, weather-aware travel itineraries.
Technical Stack & Architecture
Core Infrastructure & Compute Layer

    Hardware Accelerator: AMD Instinct™ MI300X GPU (leveraging enterprise-grade VRAM for local, low-latency foundation model hosting).

    Operating System Environment: Linux Ubuntu Pro container environment running Python 3.12.

    Inference Serving Engine: vLLM Engine (an open-source, high-throughput LLM serving engine using PagedAttention to host the model locally via an OpenAI-compatible API on port 8000).

Software Frameworks & Orchestration

    Agentic Framework: PydanticAI (v1.65.0) (a production-grade development framework used to manage state graphs, system prompts, tool injections, and execution loops).

    Inter-Process Communication Contract: Model Context Protocol (MCP) (an open standard architecture utilized via asynchronous MCPServerStdio pipes to link LLM reasoning boundaries with isolated command-line background processes).

    Runtime Environment: Node.js v20 (with npx execution layer to dynamically spin up standard protocol servers without heavy static overhead).

Foundation Model

    Primary Intelligence Layer: Qwen3-30B-A3B (configured with a Hermes tool-call parser and auto-tool choice parameters to automatically determine when and how to call external servers).

Connected Datasets & Core Tools

The architecture acts as a dynamic router rather than relying on static vector indices. The agent evaluates natural language prompts by executing steps across three distinct tools:
Integration / Target	Protocol Connection	Dataset & Operational Scope
Temporal Data	

mcp-server-time

(Python module)
	Translates relative human expressions (e.g., "next Sunday") into deterministic, formatted ISO temporal strings based on specific time zones.
Accommodation Dataset	

@openbnb/mcp-server-airbnb

(Live npx tool execution)
	

Live Airbnb Data Layer: Connects the agent directly to properties in specific geographic locations. The model programmatically handles multiple API tools over the pipe:

• airbnb_search(params): Queries location, stay windows, and guests.

• airbnb_listing_details(params): Pulls real-time property metadata and constraints.
Environmental Telemetry	

@modelcontextprotocol/server-weather

(Live npx tool execution)
	Retrieves live geographical meteorological telemetry, atmospheric warnings, and multi-day forecasted climate variables for specific target cities.
Multi-Server Swarm Orchestration Loop
Plaintext

       [ User Prompt: "Find a place in Vancouver for 3 nights next week..." ]
                                        │
                                        ▼
                     ┌──────────────────────────────────────┐
                     │         PydanticAI Platform          │
                     │    (Qwen3-30B ReAct Execution)       │
                     └──────────────────┬───────────────────┘
                                        │
        ┌───────────────────────────────┼───────────────────────────────┐
        ▼ (JSON-RPC Stdout/Stdin)       ▼ (JSON-RPC Stdout/Stdin)       ▼ (JSON-RPC Stdout/Stdin)
┌───────────────┐               ┌──────────────────┐            ┌───────────────────────┐
│  Time Server  │               │  Weather Server  │            │     Airbnb Server     │
│ (mcp-server)  │               │ (server-weather) │            │  (mcp-server-airbnb)  │
└───────┬───────┘               └────────┬─────────┘            └───────────┬───────────┘
        │                                │                                  │
        └───────────────────────────────►┼◄─────────────────────────────────┘
                                         │
                                         ▼
                 [ Synthesized Weather-Aware Lodging Recommendation ]

Key Highlight

The core strength of this system is that the agent didn't just guess or scrape static lists; it executed sequential multi-tool synthesis. When asked for a stay, it checked the Time Server to map the date, hit the Weather Server to see if the forecast looked good for a trip, and simultaneously pulled active properties using the Airbnb Server to give a perfectly tailored recommendation.
