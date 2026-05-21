<div align="center">
  <h1>Threat Intelligence Feed Dashboard</h1>
  <p>Automating the collection, summarization, and prioritization of threat intelligence using AI.</p>
</div>

## ⏱ See the Project in 30 Seconds

Security analysts are frequently overwhelmed by the massive amounts of threat intelligence data coming from security blogs, CVE feeds, vulnerability databases, and other sources. Reviewing this data manually and accurately prioritizing threats takes up a lot of time and can lead to critical threats being overlooked.

This project solves this problem by automating the collection, summarization, and prioritization of threat intelligence using AI. This allows analysts to focus on more time sensitive security threats.

## Demo Video

> **[Insert Link to Demo Video Here]**
> *Our 2 to 3 minute demo covers the pipeline tour, robustness testing, system architecture, and final reflections.*

## 🏗️ Architecture Diagram

The system architecture outlines the flow of data through our four main components: Data Ingestion, AI Processing, Relevance Scoring, and the Output Dashboard.

![Architecture Diagram](docs/architecture.png)

## Component Directories

The project is broken down into four key components. You can explore the detailed READMEs for each component below:

* **[Component 1: Feed Collector](folder-components/component-1-feed-collector/README.md)**
  Collects threat intelligence data from public sources and normalizes it into Airtable.
* **[Component 2: AI Summarizer & IOC Extractor](folder-components/component-2-ai-summarizer-and-ioc-extractor/README.md)**
  Uses the Groq API and Flowise to summarize threat reports and extract indicators of compromise.
* **[Component 3: Relevance Scorer](folder-components/component-3-relevance-scorer/README.md)**
  Evaluates how relevant each threat is to an organization by comparing it to their specific technology stack.
* **[Component 4: Integration, Testing & Presentation](folder-components/component-4-integration-testing-and-presentation/README.md)**
  Integrates all components and presents the results in a user friendly Airtable dashboard.

## Dashboard Views

Here is a look at the final output dashboard where security analysts can review prioritized threats, AI summaries, and extracted IOCs.

<div align="center">
  <img src="https://github.com/jesusalvarezaguilar0/ai-capstone-threat-intelligence-feed-dashboard/blob/main/folder-components/component-4-integration-testing-and-presentation/image.png?raw=true" alt="Integration Testing and Presentation Dashboard" width="800">
</div>

## Team Member Profiles

| Name | Role / Component | GitHub Username |
|------|-----------------|-----------------|
| **Jesus Alvarez** | Component 1: Feed Collector | [@jesusalvarezaguilar0](https://github.com/jesusalvarezaguilar0) |
| **Harry Yachimba** | Component 2: AI Summarizer & IOC Extractor | [@HarryYachimba-ArmChairWarrior](https://github.com/HarryYachimba-ArmChairWarrior) |
| **Julian Silva-Erazo** | Component 3: Relevance Scorer | [@Julznyc12](https://github.com/Julznyc12) |
| **Safayet Safin** | Component 4: Integration, Testing & Presentation | [@ChaosDragon01](https://github.com/ChaosDragon01) |

##  How to Run

1. Configure the n8n workflow to ingest threat intelligence data from RSS feeds and security blogs.
2. Store normalized threat records in Airtable.
3. Run the Flowise AI workflow to summarize threat reports and extract IOCs using the Groq API.
4. Apply the relevance scoring component to prioritize threats.
5. View the results in the Airtable dashboard interface.
