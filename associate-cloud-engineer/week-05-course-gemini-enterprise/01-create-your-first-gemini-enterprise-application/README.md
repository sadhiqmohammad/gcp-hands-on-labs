# Module 1: Create Your First Gemini Enterprise Application

Create a Gemini Enterprise app, connect it to Google Drive, Google Calendar and Cloud Storage, then use its pre-built agents and NotebookLM.

## Section 1.1 Create Your First Gemini Enterprise Application

Video: [Create Your First Gemini Enterprise Application](https://youtu.be/_Ol1uUa6QQ8) (6m 12s). Notes are from the transcript shown in Google Skills.

Key concepts:

- **What the demo builds:** a Gemini Enterprise application connected to several data stores (Google Drive, Google Calendar and Google Cloud Storage), then a tour of its pre-built agents and NotebookLM.
- **Create the app:**
  1. In the Google Cloud console, search for **Gemini Enterprise** and select the product.
  2. On the Gemini Enterprise homepage, select **Create your first app**.
  3. Give the app a name (the demo uses "Symbol Foods Marketing Team") and select **Create**.
- **Identity comes first:** before you can use the app and share it with your workforce, configure identity. Select **Setup identity**, choose **Google Identity** as the identity provider, then select **Confirm Workforce Identity**.
- **Connected data stores:** go to **Connected data stores > New data store** and pick a card for each source.
  - **Google Drive:** choose to sync all of your organization's shared drives, select **Continue**, name the data store (for example "Google Drive") and select **Create**.
  - **Google Calendar:** select the card, name the connector (for example "Google Calendar") and select **Create**.
  - **Cloud Storage:**
    - **Data type:** Documents, for unstructured files such as PDF, HTML and TXT.
    - **Synchronization frequency:** One time, because the files are ingested once.
    - **Browse** to pick the Cloud Storage bucket, choose **Select**, then **Continue**. Name the data store (for example "cloud storage") and select **Create**.
- **Share the app:** the app's **Overview** page shows a unique URL. You share the app with your workforce through that URL, and opening it takes you to the app.
- **App homepage:** it gives your workforce easy access to the most useful pre-built agents and tools.
- **Prompting:**
  - A prompt such as "Give me an update on our latest marketing data" searches both internal and external data sources and returns a context-aware response.
  - **Enhance your conversation** changes the search parameters. It also offers video generation with Veo, image generation with Nano Banana, and more.
  - Set to search **only internal sources**, a multimodal prompt ("Summarize the customer sentiment pie chart") gets an answer based on a pie chart image in your internal data.
- **Pre-built agents:**
  - **Idea Generation:** a team of agents that plan, generate and evaluate ideas on a topic. Gemini first produces a plan for you to review. If you're happy with it, select **Start session**, and after a few minutes the agents generate several ideas.
  - **Deep Research:** in-depth analysis and comprehensive reports on complex topics. Gemini drafts a research plan, which you review and can modify, then select **Start research**. After a few minutes you get a comprehensive report and an audio summary.
- **NotebookLM:**
  - Open it from the pinned section and select **Create new notebook**.
  - **Sources:** a mix of Google Docs, website URLs, YouTube videos, copied text and files uploaded from your hard drive.
  - **Grounded answers:** questions are answered from your sources. In the demo, "What audience did the spring cookie promotion target?" returns "Women aged 25 to 55 who are interested in baking and desserts in Oakland, California".
  - **Notes:** you can save responses as notes to come back to later.
  - **Studio tab:** turns source material into other formats, such as audio, videos, mind maps and various types of reports. The demo generates a five-minute video overview titled "Marketing by the Numbers".
