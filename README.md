# n8n Image-to-Video Generation Workflow

## 📌 Repository Description

This repository contains a **complete n8n workflow** that automates the **end-to-end process of generating a cinematic product video from images**.

The workflow starts from a **Webhook trigger**, reads product and image data from **Airtable**, uses **AI models** to analyze images and generate structured prompts, creates **start and end frame images**, and finally generates a **video** using an external video-generation API. All intermediate results are logged back to Airtable, making the process **resumable and traceable**.

No credentials or secrets are included in this repository.

---

## 🎯 What This Workflow Does (High-Level)

**Image → AI Analysis → Prompts → Images → Video**

This workflow automates what is normally a manual creative pipeline:

1. Analyze reference images
2. Generate cinematic prompts
3. Create visual frames
4. Convert frames into a video
5. Store results for review

---

## 🧠 End-to-End Workflow Steps (Image → Video)

### **Step 1: Webhook Trigger (Entry Point)**

* The workflow is triggered via a **Webhook**
* A `recordId` is passed as a query parameter
* This `recordId` is used throughout the workflow to fetch and update the correct Airtable record

---

### **Step 2: Fetch Product Data from Airtable**

* Uses the **Airtable – Search Records** node
* Fetches:

  * Product name
  * Reference images (Image 1 & Image 2)
  * Creative direction (if already provided)

This ensures the workflow always works with the latest data.

---

### **Step 3: Route Execution Based on Record State (Switch Node)**

The **Switch** node decides what to do next:

* **No prompts exist** → Generate AI prompts
* **Prompts exist but no images** → Generate start & end images
* **Images exist** → Generate video

✅ This makes the workflow **idempotent and resumable** (safe to re-run).

---

### **Step 4: Analyze Reference Images (AI Vision Analysis)**

* Uses **Google Gemini (Vision)**
* Analyzes Image 1 and Image 2
* Extracts visual, stylistic, and product-related details

Purpose:

> Understand what is in the images before generating cinematic prompts.

---

### **Step 5: Generate Structured Cinematic Prompts (AI Agent)**

* Uses an **AI Agent with a Structured Output Parser**
* Produces **clean JSON output** with:

  * `start_image_prompt`
  * `end_image_prompt`
  * `transition_prompt`
  * `creative_direction`

These prompts are optimized for cinematic 3D product videography.

---

### **Step 6: Log Prompts Back to Airtable**

* The generated prompts are written back to Airtable
* Ensures prompts are stored, reviewable, and reusable

This step creates a **clear audit trail**.

---

### **Step 7: Generate Start & End Frame Images**

* Uses an **HTTP Request** node to call the image-generation API
* Inputs:

  * Generated prompts
  * Reference images
* Outputs:

  * Start frame image
  * End frame image

The workflow waits for the generation to complete and retrieves the final image URLs.

---

### **Step 8: Convert Image URLs to Airtable Attachments**

* Airtable requires images as **array-based attachments**
* A dedicated **HTTP Request** node converts generated image URLs into the correct Airtable format

This ensures compatibility with Airtable’s attachment fields.

---

### **Step 9: Generate Video from Images**

* Uses the **Video Generation API**
* Inputs:

  * Transition prompt
  * Start frame image
  * Aspect ratio (16:9)
* Output:

  * A cinematic product video

The workflow includes:

* Wait nodes
* Status checks
* Retry-safe execution

---

### **Step 10: Store Final Video in Airtable**

* Once video generation succeeds:

  * The video URL is saved to Airtable
* The record is updated to reflect completion

This allows teams to view or download the final video directly from Airtable.

---

## 🧩 Key Design Principles

* **State-based routing** (no duplicate work)
* **AI-structured outputs** (clean, reusable data)
* **Resumable execution**
* **No hardcoded secrets**

---

## 🚀 How to Use This Workflow

1. Import the `.json` file into n8n
2. Configure credentials for:

   * Airtable
   * AI models
   * Image & video generation APIs
3. Trigger the workflow via webhook with a valid `recordId`
4. Monitor execution and outputs in Airtable

---

## 🔐 Security Notes

* No credentials are stored in this repository
* Use n8n credentials or environment variables
* Never commit API keys to GitHub


## 📝 Final Notes

This workflow demonstrates how **AI + automation** can fully replace manual creative pipelines, turning raw images into polished product videos with minimal human intervention.
