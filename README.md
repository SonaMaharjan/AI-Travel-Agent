# AI-Travel-Agent
A travel agent built on Google's ADK to generate summary of the best flight options.

## Introduction 
### 1. Problem Statement
Finding the best flight involves sifting through massive amounts of data - a process that is both time-consuming and often leads to confusion. The core issue with the current flight booking websites is the lack of intelligent curation, as travellers need actionable recommendations, not just raw information.

### 2. Solution: The Intelligent Travel Agent
The solution is a travel agent built on the Google Agent Development Kit (ADK). This agent acts as an advisor that transforms a user's natural request (e.g., "Find me flight options from Kuala Lumpur to Singapore leaving on 15 December 2025 and returning on 30 December 2025 for 5 people") into a curated flight summary.

The use of agents is essential because the task requires orchestrated, multi-step execution:

* **Understanding Intent**: The agent first converts the user's plain language into precise travel parameters (like IATA airport codes and dates).
* **Tool Control**: It then systematically calls external tools: first to construct the specific search URL on kayak.com and then to manage the web scraping session using Browserbase.
* **Synthesis**: Finally, the agent applies its reasoning to the raw scraped data, analysing price vs. duration trade-offs to present the top three most strategic flight options.

### 3. Core Value Proposition
This agent provides a summary of the best flight options, eliminating the need for manual searching and filtering.

## Architecture
### 1. Architecture: Root Agent (Sequential)
The solution employs a Root Agent (`root_agent`) that directs the entire workflow. This Root Agent is equipped with four sub-agents:

* `parameter_extraction_agent`: This sub-agent's role is to extract the relevant parameters (origin_code, destination_code, departure_date, return_date, adults, cabin_class) from the user query.
* `kayak_url_agent`: This sub-agent's role is to create a Kayak search URL using the extracted parameters from parameter_extraction_agent. The function for creating the URL is create_kayak_flights_url.
browserbase_scrape_agent: This sub-agent's role is to scrape relevant data from the generated Kayak URL. This agent is connected to the Browserbase MCP server, utilising browserbase_session_create, browserbase_stagehand_navigate, browserbase_stagehand_act, browserbase_stagehand_extract, browserbase_session_close.
* `summarizer_agent`: This agent's role is to summarize the scraped results.

### 2. Key Technical Concepts Demonstrated
This project showcases the following core ADK features:

* **Tool Use and Function Calling**: The agent selects and calls google_search tool to find the IATA code for the relevant cities in the user input.
* **Structured Output**: The agent is explicitly told to extract data using a defined Schema (FLIGHT_DATA_SCHEMA). This forces the scraping tool to return clean, predictable data, which is vital for the agent's final analytical summary.
* ***Constrained LLM Behaviour***: The sub-agents operate under specific instructions written into their instruction set. This guarantees the agent follows the correct sequence of steps.

