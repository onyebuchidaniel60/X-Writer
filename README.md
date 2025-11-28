#  AI-Powered Trending Topic Content Strategist (n8n Workflow)

This n8n workflow demonstrates a multi-agent automation pipeline designed to discover, research, synthesize, and publish trending content directly to X (formerly Twitter).

It moves beyond simple scheduling by integrating two distinct AI agents (using Google Gemini) to ensure the content is timely, grounded in real-time search data, and optimized for social media engagement.

##  Features

* **Two-Stage AI Pipeline:** Separates the analysis task (Topic Scorer) from the creative task (Content Strategist) for higher accuracy and better governance.

* **Real-Time Research:** Uses Google Search Grounding to ensure the content is based on the latest trending news and data.

* **X Post Optimization:** Generates content that adheres to strict character/word limits and includes relevant hashtags for maximum reach.

* **Scheduled Execution:** Designed to run automatically at defined intervals to maintain a consistent publishing cadence.

* **Analytics Integration:** Includes a step to fetch X metrics after posting, enabling performance tracking and continuous optimization of content strategy.

##  Workflow Architecture

The automation is structured as a two-part process within a single n8n canvas: 

### Phase 1: Topic Discovery & Scoring (Agent 1: Topic Scorer)

| Node | Function | Technology | 
| ----- | ----- | ----- | 
| **Data Source** | In a live environment, this node aggregates content from RSS Feeds, news APIs, or scraping results. | HTTP Request / RSS Feed / Custom | 
| **Topic Scorer Agent (Gemini)** | Analyzes the raw content block, identifies unique topics, and assigns a **Trend Score (1-10)** based on frequency and prominence. | **Gemini API** with **JSON Schema Output** | 
| **Logic Filter** | Sorts the scored topics and selects the single item with the highest `trend_score` (the most valuable topic). | Code Node / Item Lists Node | 

### Phase 2: Content Creation & Publishing (Agent 2: Content Strategist)

| Node | Function | Technology | 
| ----- | ----- | ----- | 
| **Prompt Preparation** | Prepares the final prompt, injecting the chosen topic and the strict word count limit. | Set Node | 
| **Content Strategist Agent (Gemini)** | **Mandatorily uses Google Search Grounding** to research the topic and synthesizes the findings into a compelling X post. **Strictly adheres to the output word limit.** | **Gemini API** with **Google Search Grounding** | 
| **X Posting** | Publishes the finalized content string to the connected X account. | X (Twitter) Node | 
| **Wait (3 Hours)** | Pauses the workflow to allow time for the post to gather initial metrics. | Wait Node | 
| **Analytics Fetch** | Fetches the `public_metrics` (likes, retweets, impressions) for the newly published post, ready for logging or reporting. | HTTP Request (X API v2) | 

## 🛠️ Setup Instructions

To run this workflow, you need an active n8n instance and the following credentials:

### 1. Gemini API Credentials

1. Create a **Google Gemini API Key** and enter it into a new **Google Gemini Chat Model** credential in n8n.

2. Ensure your chosen model (`gemini-2.5-flash-preview-09-2025` is recommended) is configured for both the Topic Scorer and Content Strategist nodes.

### 2. X (Twitter) API Credentials

1. Create an X Developer App and generate the required API keys and tokens.

2. Set up the **X OAuth2 API** credential in n8n for the publishing step. *Note: The `Fetch X metrics/X analytics` node currently uses an HTTP Request and requires a Bearer Token setup, which should be updated to your secure credentials.*

### 3. Workflow Configuration

Before running, ensure you configure the following nodes:

* **Data Source:** Update the initial node to point to your actual content source (e.g., RSS URL, database query, etc.).

* **X Posting Node:** Verify the message structure and ensure the **Tweet ID** variable is correctly mapped in the subsequent `Fetch X metrics/X analytics` node.

* **Content Strategist Agent:** Adjust the word count in the Prompt Preparation step (`{{ $json.wordLimit }}`) to match your desired post length.

## 🚀 Getting Started

1. Import the `X writer workflow.json` file into your n8n canvas.

2. Configure the **Google Gemini** and **X API** credentials.

3. Set the workflow to **Active** and use the **Cron node** (or another scheduling trigger) to define your desired publishing frequency (e.g., daily or every 3 hours).

4. Execute a test run to ensure both AI agents are correctly processing the data and publishing the content.
