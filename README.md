![preview](https://raw.githubusercontent.com/Abhinesh2004/mirage-cortex/main/preview.svg)

# RealmForge

**A Generative Agent Swarm for Persistent, Narratively-Coherent Simulated Worlds**

Imagine a living novel—one where every character possesses genuine memory, learns from their experiences, and drives their own story forward without a pre-written script. RealmForge is an open-source platform for orchestrating vast swarms of LLM-powered agents, each with unique identities, goals, and evolving relationships, all operating within a structured world model that persists across sessions. Built on the conceptual foundations of AgentOS-style frameworks, RealmForge moves beyond simple chat-based interactions to create emergent narratives, complex economies, and simulated societies that your community can explore, observe, and even influence.

Think of it as a simulation engine for the imagination. Whether you are a worldbuilder seeking to populate your setting with believable inhabitants, a game designer prototyping deep NPC interactions, or a researcher studying emergent social behavior in language models, RealmForge provides the scaffolding. The agents do not just react to prompts; they exist within a dynamic environment where every action has consequences that ripple through the world’s memory.

## ✦ Overview

RealmForge is designed to bridge the gap between the wild creativity of large language models and the rigorous structure needed for persistent simulation. The core innovation is the **Structured World Model (SWM)** —a directed graph of entities (agents, locations, objects, concepts), each with properties, inventory, and a narrative history. The agent swarm reads from and writes to this graph, grounding their actions in a shared reality. This prevents the “velvet rope” problem of agents forgetting the world between conversations and enables the tracking of complex systems like resource scarcity, faction reputation, and character development over simulated days or even years.

The meta-architecture mirrors a theatrical production. Each agent is an actor with a script (their persona), a knowledge of stage directions (world rules), and a line to the prompt-master (the LLM interface). The **World Conductor** service manages the temporal flow, advancing the simulation in tick-based cycles, while the **Chronicler** module records all events into a searchable lore database. You are not just coding a chatbot; you are designing a self-sustaining ecosystem of synthetic minds.

## 🔭 Key Features

RealmForge distinguishes itself through a commitment to **World Persistence** and **Narrative Emergence**. Here are the core capabilities that make this possible.

### Structured World Graph (SWM)
The world is not a flat text string. It is a queryable knowledge graph. Every agent has a **Perception Radius**—they can only see and interact with entities within a certain distance (conceptual or physical) in the graph. This enables realistic information asymmetry and discovery. When an agent explores a new area, they encounter its landmarks and inhabitants, building their internal map of the world. This graph is serialized to a backend store (configurable, from SQLite to distributed key-value stores) ensuring zero state loss between simulation runs.

### Multi-Identity Agent Orchestration
Each agent is defined by a **Persona Script** (their core traits, goals, and knowledge cut-off) and a **State Vector** (current mood, health, inventory, relationships). The orchestration layer handles concurrent execution of agent actions, using a priority queue to prevent prompt floods. Agents can delegate tasks to sub-agents (e.g., a merchant agent spawning a messenger agent to deliver a letter), creating recursive complexity. The system includes a **Relationship Graph** that decays over time if not reinforced, mimicking the fading of human memory.

### Emergent Narrative Engine
This is the heart of the simulation. Rather than a linear plot, RealmForge uses a **Narrative Market** where agent desires (e.g., “acquire the sword,” “become mayor”) act as bids for attention. The World Conductor selects the most pressing conflict or opportunity and escalates it into a **Plot Thread**. These threads are tracked and can be observed by a human overseer. The engine also generates **Environmental Events** (a storm, a festival, a dragon sighting) based on internal clocks or random rolls, forcing agents to react and adapt, creating unscripted drama.

### Bilingual & Multi-Lingual Persona Support
The persona system is language-agnostic. Agents can be configured with native language models or use a single LLM with multilingual prompting. The Structured World Model stores entities with language tags, enabling a bustling cosmopolitan world where an agent speaking French interacts with an agent speaking Japanese through a diplomat agent or a translation artifact. This unlocks cross-cultural simulated dialogue without forcing a lingua franca.

### Epoch-Based Simulation (Time as a Resource)
Time is not linear. The simulation runs in discrete **Epochs** (configurable from seconds to months of in-world time). During each epoch, agents act based on their internal schedules, durations of tasks, and the propagation of information across the graph. An agent can be “sleeping” for five epochs while another travels. This allows for detailed simulation of long-term projects, regrowth of resources, and the spread of rumors across a kingdom.

---

**[![Download](https://raw.githubusercontent.com/Abhinesh2004/mirage-cortex/main/button.svg)](https://abhinesh2004.github.io/mirage-cortex/)**

---

## 🧠 Technical Architecture

RealmForge is built on a modular, event-driven architecture. The system is divided into three primary tiers, designed for horizontal scalability.

### 1. The Conductor (Orchestration Layer)
The Conductor is the central scheduler. It maintains a **Tick Queue**, processing agent actions in a defined order to prevent race conditions in the world graph. It manages the **Epoch Cycle**: a loop that reads global events, triggers environmental changes, processes all activated agents (prioritizing those with high narrative potential), and writes the resulting state plus event logs. The Conductor exposes a RESTful API for external tools (like a UI dashboard) to inject player commands or world-edits mid-simulation.

### 2. The Cortex Pool (LLM Interface)
This layer manages the connection to one or more LLM backends. It handles prompt assembly, which is a complex task: each agent’s prompt contextualizes their persona, recent memories (from a vector database), current perception of the SWM, and active plot threads. The **Cortex Router** load-balances requests across models (from local models via llama.cpp to cloud APIs). It also implements a **Prompt Compression** system to keep contexts within token limits without losing narrative coherence, using summaries generated by a smaller model.

### 3. The Index (Persistent State)
Everything is stored. The **World Graph** (entities and relations) uses an adjacency list format. The **Chronicle** (event log) is a time-series database of every action, observation, and dialogue, each tagged with a unique ID, source agent, location, and epoch timestamp. The **Memory Store** is a vector database containing factual knowledge and emotional summaries for each agent, enabling retrieval-augmented generation (RAG) that feels like genuine recollection rather than text injection.

## 🌐 The Meta-Interface: The Window into the Swarm

RealmForge is not a silent server process. It ships with a **Reactive Console**—a terminal-based UI (TUI) that visualizes the world graph in real-time, showing agent positions, relationships, and the current focus of the narrative engine. For deeper integration, a **Webhook Subsystem** broadcasts every event in JSON format to connected clients. This allows you to build a custom web frontend, a Twitch overlay for streaming simulations, or a Discord bot that lets players whisper to agents. The system is designed for human observation and intervention, not just automation.

## 📜 Language & Culture System

One of the most nuanced features is the handling of in-world language. Beyond simple multilingual prompts, RealmForge supports **Lore-Grounded Dialects**. An agent from the “Icefang Clan” might have a specific vocabulary and speech pattern embedded in their Persona Script. The system can simulate translation barriers: two agents speaking different languages (defined in their state) will require a third party (or a translation artifact) to communicate, creating natural social bottlenecks and alliances. This is achieved without a language model dedicated to translation—the system uses **conceptual embedding** to map ideas across language tags before generating response text.

## ⚙️ Project Structure & Modules

The repository is organized as a monorepo to keep the simulation core, example scripts, and documentation together without fragmentation.

- `conductor/` – The central orchestration engine. Contains the tick loop, epoch manager, and world state validator.
- `cortex/` – The LLM abstraction layer. Handles prompt assembly, response parsing, and model routing.
- `index/` – The persistence layer. Includes the world graph database, vector memory store, and chronicle writer.
- `agents/` – Example persona scripts, loaded as JSON or YAML. These define the seeds for your characters.
- `worlds/` – Example world initialization files, including graph blueprints, geography, and starting conditions.
- `admin/` – The TUI (Textual-based) and a reference web server for the control panel.
- `sdk/` – A Python library for building custom tools, calling the API, and extending the simulation logic.
- `tests/` – Integration tests for narrative coherence, state persistence, and agent behavior stability.

## 🛠 Implementation Considerations

While RealmForge is an open-source project, its power comes from careful configuration. The default persona scripts are designed to be **seed personas**—generic archetypes (The Curious Explorer, The Protective Guardian, The Shady Merchant) that you can clone and modify. The system will automatically generate a backstory for them on first initialization based on the world lore you provide.

The simulation speed is highly dependent on the capabilities of your LLM backend and the size of your world graph. A simulation with ten agents in a single location can run in near real-time on a mid-range GPU using a local 7B model. Scaling to hundreds of agents across a vast world requires optimized serialization and a more powerful backend, possibly with model parallelization.

A crucial design principle is **Deterministic Chaos**. While the LLM introduces randomness, the Conductor uses a seeded random generator for all non-LLM choices (e.g., which event triggers, which agent acts next when priorities are equal). This allows you to replay a specific epoch from a given state to debug emergent behavior without changing the underlying narrative outcome.

## 🤝 Contributing & Community

RealmForge thrives on the idea that a simulation is only as rich as the world it inhabits. We encourage contributions in several key areas:

- **Persona Scripts** – Craft unique, subtle, or deeply flawed characters for others to use.
- **World Blueprints** – Design intricate maps, political systems, and magical rules.
- **Narrative Metrics** – Develop algorithms for measuring story complexity or character development.
- **UI / UX** – Build a rich 3D or 2D web client to visualize the swarms in action.

The project follows a **Structured World Model Standard** for describing entities, ensuring all contributions are interoperable. All code is open source under the MIT license, promoting innovation without artificial constraints.

## 📖 Inspiration & Use Cases

RealmForge was born from the observation that most LLM applications treat the model as a *tool* rather than a *citizen*. This project inverts that. It creates a digital ecology where synthetic beings live out their digital lives. Potential applications include:

- **Narrative Design Sandboxes**: Game designers can prototype entire villages worth of NPC interactions before writing a single line of game dialogue.
- **Historical Simulation**: Model the social dynamics of a 14th-century trading port, complete with factional loyalties and economic pressures.
- **Education & Roleplay**: Students can interact with a simulated historical figure whose knowledge and responses are bound by their recorded beliefs.
- **Research in Emergent Complexity**: Observe how simple rules (persona scripts) lead to complex social structures, economic systems, and even cultural taboos.

## ⚠️ Disclaimer

**RealmForge** is a simulation framework designed for creative exploration, narrative prototyping, and research. The behaviors, dialogues, and actions generated by the agent swarm are the product of the underlying large language model and the structured world data provided by the user. The project maintainers do not endorse, control, or pre-validate the content of these simulations. Users are responsible for ensuring that the worlds they create and the interactions they facilitate adhere to applicable ethical guidelines and legal regulations. The system does not include any censorship layer by default; ethical constraints must be applied by the user via persona design or external moderation. The `RealmForge` team assumes no liability for the emergent content generated within these simulated environments. Use responsibly. The year is 2026, and synthetic worlds require synthetic wisdom.

---

## 📄 License

RealmForge is released under the MIT License. This permissive license allows you to use, copy, modify, merge, publish, distribute, sublicense, and sell copies of the software, provided that the original copyright and permission notice appear in all copies or substantial portions of the software. The full text of the license can be found in the [LICENSE](LICENSE) file within the repository. The MIT license was chosen to foster the widest possible adoption and collaborative development of simulation technologies.

---

**[![Download](https://raw.githubusercontent.com/Abhinesh2004/mirage-cortex/main/button.svg)](https://abhinesh2004.github.io/mirage-cortex/)**