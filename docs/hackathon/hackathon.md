# Table of Contents
- [1. Introduction](#1-introduction)
- [2. Purpose of the Document](#2-purpose-of-the-document)
- [3. Technical Requirements](#3-technical-requirements)
  - [3.1 Database Requirements](#31-database-requirements)
    - [Relational Structure](#relational-structure)
  - [3.2 API Requirements](#32-api-requirements)
    - [Data Import and Export](#data-import-and-export)
  - [3.3 AI Integration](#33-ai-integration)
- [4. Practical Use Cases and Methods](#4-practical-use-cases-and-methods)
  - [4.1 Data Collection](#41-data-collection)
    - [4.1.1 Basic Recommendations](#411-basic-recommendations)
    - [4.1.2 Web Scraping](#412-web-scraping)
    - [4.1.3 API](#413-api)
  - [4.2 Data Processing](#42-data-processing)
    - [4.2.1 Source-Based Tagging of Collected Data](#421-source-based-tagging-of-collected-data)
    - [4.2.2 Keyword Extraction](#422-keyword-extraction)
    - [4.2.3 Auto-Tagging](#423-auto-tagging)
    - [4.2.4 Data Cleaning](#424-data-cleaning)
  - [4.3 AI Usage](#43-ai-usage)
    - [4.3.1 Connection of Existing Entities in the Database](#431-connection-of-existing-entities-in-the-database)
    - [4.3.2 Content Generation](#432-content-generation)
    - [4.3.3 Content Tracking](#433-content-tracking)
    - [4.3.4 Content Validation & Correction](#434-content-validation--correction)
    - [4.3.5 Techniques to Consider](#435-techniques-to-consider)
- [5. Additional Notes & Considerations](#5-additional-notes--considerations)
- [6. Conclusion](#6-conclusion)

# 1. Introduction
This hackathon centers on building a comprehensive data ecosystem, and ends with submitting high-quality data into a knowledge graph. The primary aim is to experiment with techniques that streamline data collection, processing, organizing, and storage, ultimately preparing and exporting data in the GRC-20 format. By combining relational database design, AI functionalities, and efficient data handling, participants can discover new methods to gather, organize and improve their datasets.

# 2. Purpose of the Document
The purpose of this document is to provide recommended basic strategies and best practices for:
- Setup relational databases that store and manage data.
- Collecting data from various sources (scraping, APIs) and normalizing it into a structured format.
- Integrating AI-driven functionalities for data processing.

# 3. Technical Requirements
## 3.1 Database Requirements
### Relational Structure
- Use a relational schema. Define tables, columns, and constraints (e.g., primary/foreign keys).
- Keep relationships and property values intact when importing or exporting.
- Consider that you will have to transform incoming data (CSV, JSON, etc.) to match the relational schema.

## 3.2 API Requirements
### Data Import and Export
- Allow selective or full exports of data, particularly when preparing for GRC-20 submission.
- Ensure that every relevant field in your database can be mapped and converted into the required GRC-20 fields.
- More information about GRC-20 will be provided later.

## 3.3 AI Integration
- Make sure your solution integrates AI tools.

# 4. Practical Use Cases and Methods
## 4.1 Data Collection
### 4.1.1 Basic Recommendations
- Set the right strategies for identifying the most reliable sources.
- Decide appropriate data gathering approach for each source:
  - **Single time**: Perform a one-off extraction for static or rarely changing sources.
  - **Interval**: Schedule automated data collection/updates (daily, weekly, or monthly) for frequently updated sources.
  - **Change-trigger**: Initiate automated extraction whenever the data in the source changes.

### 4.1.2 Web Scraping
Web scraping is the automated process of extracting data from websites. It uses specialized tools or scripts to crawl pages, parse their HTML, and collect structured information.

### 4.1.3 API
Many public data sources (e.g., Wikidata) offer APIs for more reliable and structured data retrieval. Leverage these APIs to automate data collection without dealing with changing HTML structures.

## 4.2 Data Processing
### 4.2.1 Source-Based Tagging of Collected Data
- Save any tags or labels that come with the original source.
- Assign metadata or tags that identify the origin of each data point (e.g., source name, URL, timestamps, authors).

### 4.2.2 Keyword Extraction
- Identify useful keywords to aid in categorization and linking of data.
- Maintain synonym lists (e.g., BTC, Bitcoin) to unify references to the same entity.

### 4.2.3 Auto-Tagging
- Apply predefined relationships between tags to automatically add higher-level or related tags.

### 4.2.4 Data Cleaning
- Use automated scripts and/or AI-based tools to remove duplicates and validate data.
- Compare data from multiple sources to flag inconsistencies.
- Implement manual review processes for flagged data.

## 4.3 AI Usage
### 4.3.1 Connection of Existing Entities in the Database
- Automatically link related entities via relational properties (e.g., claims linked to supporting quotes).

### 4.3.2 Content Generation
- Produce new content using AI based on filtered datasets (e.g., by specific keywords).

### 4.3.3 Content Tracking
- Track AI-generated outputs for both AI and human reviews.

### 4.3.4 Content Validation & Correction
- Detect incorrect, incomplete, or duplicate data through AI-based validators.
- Build a review process for human verification of AI suggestions.

### 4.3.5 Techniques to Consider
- [**Structured Output**](https://platform.openai.com/docs/guides/structured-outputs)
- [**Retrieval-Augmented Generation**](https://en.wikipedia.org/wiki/Retrieval-augmented_generation)

# 5. Additional Notes & Considerations
- Consider integrating or developing a UI with filtering, batch editing, natural language search (via LLM) for non-technical users.
- Useful tools:
  - [Baserow](https://baserow.io)
  - [Teable](https://github.com/teableio/teable)
  - [Directus](https://directus.io/)
  - [NocoDB](https://github.com/nocodb/nocodb)
- Maintain logs of data transformations (scraping events, AI processing steps).
- Every entity should have the following fields:
  - **Name, Description, Cover, and Types**
  - Cover image: Ideally 2384×640 px.

# 6. Conclusion
These are some of the methods we’ve found helpful so far. Our goal is to build on these approaches and discover even better ones. This document will continue to evolve throughout the Hackathon.
