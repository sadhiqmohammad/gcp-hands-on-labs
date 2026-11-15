# Module 2: Assessment

The course assessment: 8 questions, passing score 80%. Passed with 100%.

## Section 2.1 Assessment

The correct answer is marked ✅. Each explanation uses the feedback the assessment shows for the correct answer and the demo in Section 1.1, and says so where the course doesn't cover an option.

### Question 1

A company's Google Cloud administrator is performing the initial one-time configuration for a Gemini Enterprise application and needs to ensure the organization's workforce can securely authenticate and access their internal data. Which task should they perform first?

- Enable the Gemini Code Assist API
- ✅ **Choose an identity provider**
- Define a data synchronization frequency
- Connect the organization's data stores

**Why it's correct:** the administrator must first configure the identity settings so Gemini can authenticate users and ensure secure access to data and resources. In the demo, **Setup identity** (choose Google Identity, then **Confirm Workforce Identity**) comes before you can use or share the app (Section 1.1).

**Why the others are wrong:**

- *Gemini Code Assist API:* the demo never enables it, and it isn't part of the Gemini Enterprise app setup in this course.
- *Synchronization frequency:* that is a setting inside one data store (the Cloud Storage connector), chosen after identity is set up.
- *Connect data stores:* the demo connects Drive, Calendar and Cloud Storage only after identity is configured.

### Question 2

You ask Gemini Notebook a question about a product that is not mentioned in any of the sources you uploaded to the current notebook. How will the tool respond?

- It will automatically browse the web to fill in the missing information
- ✅ **It will generate an answer based only on the content of the uploaded documents**
- It will search your connected Google Drive to find the answer
- It will ask you to add your Google Calendar as a source

**Why it's correct:** Gemini Notebook is strictly grounded in the sources you provide, so its answers stay accurate and can cite those sources. In the demo, NotebookLM answers questions from the files uploaded to that notebook (Section 1.1).

**Why the others are wrong:**

- *Browse the web:* a notebook doesn't go beyond its sources. Searching many websites is what Deep Research does.
- *Search connected Google Drive:* connected data stores feed the app's general search, not a notebook. A notebook only uses the sources added to it.
- *Ask to add Google Calendar:* Calendar is an app data store, not a notebook source. Notebook sources are Google Docs, website URLs, YouTube videos, copied text and uploaded files.

### Question 3

A marketing team lead needs to quickly understand the primary findings of a customer sentiment pie chart saved as a PNG file. How can they use Gemini Enterprise to achieve this?

- Transcribe the chart into a spreadsheet and connect it as a data store
- Use the Google Calendar data store to find the meeting where the chart was shown
- Request a Deep Research report on pie chart design trends
- ✅ **Ask the AI assistant to "Summarize the customer sentiment pie chart."**

**Why it's correct:** Gemini Enterprise supports multimodal prompts, so it can interpret and summarize image content. The demo uses this exact prompt with search set to internal sources only, and Gemini answers from the pie chart image (Section 1.1).

**Why the others are wrong:**

- *Transcribe into a spreadsheet:* unnecessary manual work. Gemini reads the image directly.
- *Calendar data store:* finding the meeting doesn't summarize the chart's findings.
- *Deep Research on design trends:* that researches pie chart design in general, not what this chart says.

### Question 4

A company stores its weekly sales reports in a designated Cloud Storage bucket. To ensure their Gemini Enterprise application always reflects the latest data, which synchronization frequency should they use?

- One time
- Streaming
- Multiple ingestion types
- ✅ **Periodic**

**Why it's correct:** periodic ingestion is for data sources that are updated on a recurring schedule (for example hourly, daily or weekly). It keeps the data store synchronized with the source files automatically.

**Why the others are wrong:**

- *One time:* the files are ingested once and never refreshed. The demo picks One time only because its files are ingested once (Section 1.1), so new weekly reports would be missed.
- *Streaming* and *Multiple ingestion types:* the course doesn't cover these. For files added on a weekly schedule, the course's answer is Periodic.

### Question 5

Cymbal Foods needs a comprehensive report on global food marketing trends by analyzing hundreds of external websites. Which feature should they use?

- Search internal sources enhancement
- Gemini Notebook grounded questions
- Google Calendar data sync
- ✅ **Deep Research agent**

**Why it's correct:** the Deep Research agent browses hundreds of websites and generates detailed reports based on its findings. In the demo it researches food-company marketing tips and produces a comprehensive report and an audio summary (Section 1.1).

**Why the others are wrong:**

- *Search internal sources:* this limits search to internal data, the opposite of analyzing external websites.
- *Gemini Notebook:* grounded only in the sources you upload, so not suited to researching hundreds of sites.
- *Google Calendar sync:* connects calendar data and does no research.

### Question 6

An administrator is concerned that connecting a shared Google Drive to the Gemini Enterprise application will allow everyone in the company to read private, unshared documents. How does Gemini Enterprise handle file permissions?

- It requires the IT department to manually re-assign permissions for every file
- ✅ **Users can only access documents already shared with them via existing permissions**
- It automatically grants read access to all employees for indexed files
- All connected files become public to any user with the application URL

**Why it's correct:** the application respects existing Drive permissions, so users only see content they already have rights to view. The demo video doesn't cover permissions. This comes from the assessment's feedback.

**Why the others are wrong:** each one assumes the app replaces Drive permissions, either by making IT re-assign them by hand or by opening files to all employees or to anyone with the URL. Since the app keeps existing permissions, none of these happen.

### Question 7

A retail company has detailed product descriptions in PDF files and also has a corresponding set of structured data (product ID, price, stock level) for each PDF. To ensure that users can filter search results by price or stock level, which data import option should they choose?

- Tabular/E-commerce
- Documents
- Media/Catalog Assets
- ✅ **Documents with Metadata (RAG)**

**Why it's correct:** this option is designed for unstructured documents paired with structured metadata. The system indexes the document content and makes the metadata available for filtering and other advanced search functions.

**Why the others are wrong:**

- *Documents:* for unstructured files such as PDF, HTML and TXT on their own (Section 1.1). It has no structured fields to filter on, such as price or stock level.
- *Tabular/E-commerce* and *Media/Catalog Assets:* the course doesn't cover these. Neither is the option for PDFs paired with metadata, which is what this question describes.

### Question 8

A researcher has a small set of specific text files and wants to create a video overview that summarizes only those sources. Which tool is best suited for this task?

- General Gemini Enterprise Search
- ✅ **Gemini Notebook**
- Idea Generation Agent
- Deep Research Agent

**Why it's correct:** Gemini Notebook is designed for focused research on uploaded sources and includes a Video Overview feature. In the demo, the Studio tab generates a five-minute video overview from the uploaded files (Section 1.1).

**Why the others are wrong:**

- *General search:* searches internal and external sources and answers in chat. It isn't limited to your files and doesn't make video overviews.
- *Idea Generation:* a team of agents that plans, generates and evaluates ideas, not summaries of given sources.
- *Deep Research:* researches many websites and produces a report with an audio summary, not a video overview of only your files.
