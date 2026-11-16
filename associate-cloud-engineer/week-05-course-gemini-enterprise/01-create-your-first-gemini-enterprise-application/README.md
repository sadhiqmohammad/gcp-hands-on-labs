# Module 1: Create Your First Gemini Enterprise Application

Create a Gemini Enterprise app, connect it to Google Drive, Google Calendar and Cloud Storage, then use its pre-built agents and NotebookLM.

## Section 1.1 Create Your First Gemini Enterprise Application

Video: [Create Your First Gemini Enterprise Application](https://youtu.be/_Ol1uUa6QQ8) (6m 12s). Notes are from the transcript shown in Google Skills. Screenshots are frames from the course video.

Key concepts:

- **What the demo builds:** a Gemini Enterprise application connected to several data stores (Google Drive, Google Calendar and Google Cloud Storage), then a tour of its pre-built agents and NotebookLM.
- **Identity comes first:** before you can use the app and share it with your workforce, configure identity (Google Identity in the demo).
- **Data stores** connect your sources to the app. Cloud Storage uses the **Documents** data type for unstructured files (PDF, HTML, TXT) and **One time** sync when files are ingested once.
- **Share the app** with your workforce through the unique URL on its **Overview** page.
- **Prompts** can search internal and external sources, only internal sources, or include images (multimodal).
- **Pre-built agents:** Idea Generation (a team of agents that plan, generate and evaluate ideas) and Deep Research (in-depth reports with an audio summary).
- **NotebookLM** answers only from the sources you add, saves answers as notes, and its **Studio** turns sources into audio, videos, mind maps and reports.

### Step 1. Create the app

In the Google Cloud console, search for **Gemini Enterprise** and select the product.

![Console search results for Gemini Enterprise](images/01-search-gemini-enterprise.jpg)

On the Gemini Enterprise homepage, select **Create your first app**.

![Welcome to Gemini Enterprise page with Create your first app](images/02-create-your-first-app.jpg)

Give the app a name (the demo uses "Cymbal Foods - Marketing Team") and select **Create**.

![Create page with the app display name and Create button](images/03-name-the-app.jpg)

### Step 2. Set up identity

Before you can use the app and share it with your workforce, configure your identity settings. Select **Set up identity**.

![App dashboard with the Set up identity link](images/04-setup-identity.jpg)

Choose **Google Identity** as your identity provider, then select **Confirm Workforce Identity**.

![Choose an identity provider page with Use Google Identity selected](images/05-choose-google-identity.jpg)

### Step 3. Connect Google Drive

Select **Connected data stores**, then **New data store**.

![Empty Connected data stores page](images/06-connected-data-stores.jpg)

Locate the **Google Drive** card and select it.

![Select a data source page with workspace source cards](images/07-select-google-drive.jpg)

Choose to sync **All** of your organization's shared drives, then select **Continue**.

![Choose drives you want to sync, with All selected](images/08-drive-sync-all.jpg)

Name the data store (for example "Google Drive") and select **Create**.

![Data connector name set to Google Drive](images/09-drive-name-create.jpg)

### Step 4. Connect Google Calendar

Locate and select the **Google Calendar** card.

![Google Calendar card on the Select a data source page](images/10-select-google-calendar.jpg)

Name the connector (for example "Google Calendar") and select **Create**.

![Data connector name set to Google Calendar](images/11-calendar-name-create.jpg)

Both Google Drive and Google Calendar are now connected.

![Connected data stores listing Google Calendar and Google Drive](images/12-drive-and-calendar-connected.jpg)

### Step 5. Connect Cloud Storage

Locate and select the **Cloud Storage** card.

![Cloud Storage card under Cloud sources](images/13-select-cloud-storage.jpg)

For the data type, select **Documents**, because you are importing unstructured documents such as PDF, HTML and TXT.

![Unstructured Data Import with Documents selected](images/14-cloud-storage-documents.jpg)

For synchronization frequency, select **One time**, because the files are ingested once.

![Synchronization frequency set to One time](images/15-cloud-storage-one-time.jpg)

Select **Browse**, find the Cloud Storage bucket, and choose **Select**. Then select **Continue**.

![Select folder dialog with the Cloud Storage bucket highlighted](images/16-cloud-storage-select-bucket.jpg)

Name the data store (for example "Cloud Storage") and select **Create**.

![Data store name set to Cloud Storage](images/17-cloud-storage-name-create.jpg)

Three data stores are now connected.

![Connected data stores listing Cloud Storage, Google Calendar and Google Drive](images/18-three-data-stores.jpg)

### Step 6. Share and open the app

Go back to the **Overview** page. You share the app with your workforce using its unique URL. Select the URL to open the app.

![Overview page with the Gemini Enterprise webapp URL](images/19-overview-app-url.jpg)

The app homepage gives your workforce easy access to the most useful pre-built agents and tools.

![Gemini Enterprise app homepage with pinned agents](images/20-app-homepage.jpg)

### Step 7. Prompt across your data

Enter "Give me an update on our latest marketing data" and select submit.

![Prompt box with the marketing data prompt](images/21-prompt-marketing-data.jpg)

Gemini searches both internal and external data sources and returns a context-aware response.

![Response with overall marketing performance metrics](images/22-marketing-data-response.jpg)

### Step 8. Search only internal sources with a multimodal prompt

Start a new chat and select **Enhance your conversation**. From here you can also generate videos with Veo, generate images with Nano Banana, and more. Set it to search only internal sources.

![Enhance your conversation menu with Search internal sources](images/23-enhance-conversation.jpg)

Enter "Summarize the customer sentiment pie chart" and select submit.

![Prompt with the Internal sources chip](images/24-internal-sources-prompt.jpg)

Gemini searches your internal sources and responds based on the pie chart image.

![Response summarising perceived and actual customer sentiment](images/25-pie-chart-response.jpg)

### Step 9. Idea Generation agent

Go to **Idea Generation**, a team of agents that plan, generate and evaluate ideas on your topic. Enter "Help me brainstorm a new marketing campaign for Gen-Z customers based on our latest products" and select submit.

![Create a multi-agent ideation session page with the Gen-Z prompt](images/26-idea-generation-prompt.jpg)

Gemini generates a campaign plan for your review. Scroll to the end of the plan and, if you're happy with it, select **Start session**.

![End of the campaign plan with the Start session button](images/27-idea-generation-start-session.jpg)

After a few minutes the team of agents generates multiple ideas for your review.

![Ranked ideas list with the idea overview](images/28-idea-generation-ideas.jpg)

### Step 10. Deep Research agent

Go to **Deep Research**, which performs in-depth analysis and generates comprehensive reports on complex topics. Enter "Research the latest marketing tips and tricks that food companies are using and generate a list of actionable steps our company can take to improve our current marketing strategy" and select submit.

![Deep Research page with the food marketing prompt](images/29-deep-research-prompt.jpg)

Gemini drafts a research plan. Review it, modify it if needed, then select **Start research**.

![End of the research plan with the Start research button](images/30-deep-research-start.jpg)

After a few minutes you get a comprehensive report and an audio summary.

![Research report with the research audio summary player](images/31-deep-research-report-audio.jpg)

### Step 11. NotebookLM

From the pinned section, select NotebookLM (shown as **Gemini Notebook** in the app), then select **Create new notebook**.

![Welcome to Gemini Notebook with Create new notebook](images/32-notebooklm-create-notebook.jpg)

Sources can be a mix of Google Docs, website URLs, YouTube videos and copied text. The demo uploads files from the hard drive: choose the files and select **Open**.

![Add sources dialog with upload, Google Drive, Link and Paste text options](images/33-notebooklm-add-sources.jpg)

Ask "What audience did the spring cookie promotion target?". NotebookLM answers from the sources: "Women aged 25 to 55 who are interested in baking and desserts in Oakland, California".

![Grounded answer about the Spring Cookie Promotion audience](images/34-notebooklm-grounded-answer.jpg)

Save useful responses as notes so you can access them later.

![Save to note button under the response](images/35-notebooklm-save-to-note.jpg)

Go to the **Studio** tab, where you transform source material into formats such as audio, videos, mind maps and various reports.

![Studio tab with Audio Overview, Video Overview, Mind Map and Reports](images/36-notebooklm-studio.jpg)

Generate a video overview. After a few minutes, a five-minute video presentation titled "Marketing by the Numbers" is ready.

![Video Overview titled Marketing by the Numbers](images/37-notebooklm-video-overview.jpg)
