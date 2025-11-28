# 🎨 LinkedIn Carousel Automation for Enterprise Storage News

An intelligent n8n workflow that automatically generates beautiful LinkedIn carousel posts from enterprise storage industry news. Perfect for B2B tech marketers and thought leaders who want to stay ahead of industry trends.

![Workflow Banner](https://img.shields.io/badge/n8n-Automation-red?style=for-the-badge&logo=n8n)
![LinkedIn](https://img.shields.io/badge/LinkedIn-Post_Automation-blue?style=for-the-badge&logo=linkedin)
![AI Powered](https://img.shields.io/badge/AI-Powered-green?style=for-the-badge&logo=openai)

---

## 🌟 What Does This Workflow Do?

This powerful automation:

1. **📰 Aggregates News** - Pulls articles from 10+ enterprise storage industry RSS feeds
2. **🔍 Smart Filtering** - Filters articles by date range and research subject
3. **🤖 AI Analysis** - Uses AI to identify pain points, trends, and key themes
4. **✨ Content Generation** - Creates engaging 5-slide LinkedIn carousel content
5. **🎨 Design Creation** - Generates professionally designed slides with your branding
6. **📤 Auto-Publishing** - Converts to PDF and posts directly to LinkedIn

All with just a form submission!

---

## 🚀 Features

- ✅ **10 Industry Sources** - StorageReview, Blocks & Files, Enterprise Storage Forum, and more
- ✅ **AI-Powered Analysis** - Automatic trend detection and pain point identification
- ✅ **Custom Branding** - Add your own logo to every slide
- ✅ **Multiple Design Styles** - Minimal, Bold, Data-driven, Modern, and Dark themes
- ✅ **Flexible Lookback** - Choose 1-5 day article range
- ✅ **Targeted Messaging** - Define your messaging focus and research subject
- ✅ **One-Click Publishing** - Automatically posts to LinkedIn

---

## 🔄 Workflow Architecture

### Visual Flow

```
Form Submission
      ↓
[Parallel RSS Fetching (10 sources)]
      ↓
   Merge All
      ↓
  Aggregate
      ↓
Filter by Date & Subject
      ↓
Combine Articles
      ↓
AI Analysis (Pain Points)
      ↓
Prepare Carousel Data
      ↓
AI Content Strategy
      ↓
Split into Slides
      ↓
Generate HTML (5 slides)
      ↓
Convert to Images
      ↓
Aggregate Images
      ↓
Convert to PDF
      ↓
Upload to LinkedIn
      ↓
Post to LinkedIn
```

---

## 📦 Node-by-Node Breakdown

### 1️⃣ **Input & Trigger Layer**

#### `On form submission` (Form Trigger)
**Type**: n8n Form Trigger  
**Purpose**: Creates a web form that initiates the workflow

**What it does:**
- Presents a user-friendly form with 3 fields:
  - Research Subject (text input)
  - Lookback Period in Days (dropdown: 1-5)
  - Messaging Focus (text input)
- Generates a unique webhook URL
- Captures form data and passes it to all RSS feed nodes

**Why it's important:** This is your workflow's entry point. Anyone with the form URL can generate carousels without accessing n8n directly.

---

### 2️⃣ **Data Collection Layer** (10 Parallel Nodes)

#### `RSS feed` through `RSS feed9` (HTTP Request Nodes)
**Type**: HTTP Request  
**Purpose**: Fetch articles from enterprise storage news sources

**Sources:**
1. **StorageReview** - `https://www.storagereview.com/feed`
2. **Blocks & Files** - `https://blocksandfiles.com/feed/`
3. **Enterprise Storage Forum** - `https://www.enterprisestorageforum.com/feed/`
4. **The Register** - `https://www.theregister.com/software/headlines.atom`
5. **InfoStor** - `https://www.infostor.com/feed/`
6. **Computer Weekly** - `https://www.computerweekly.com/rss/Storage.xml`
7. **SiliconANGLE** - `https://siliconangle.com/category/data-storage/feed/`
8. **Data Center Knowledge** - `https://www.datacenterknowledge.com/rss.xml`
9. **Storage Newsletter** - `https://www.storagenewsletter.com/feed/`
10. **ZDNet** - `https://www.zdnet.com/topic/storage/rss.xml`

**What each node does:**
- Makes HTTP GET request to RSS feed
- Retrieves XML/Atom formatted articles
- Timeout set to 10 seconds per feed
- Runs in parallel for speed

**Why parallel execution:** All 10 feeds are fetched simultaneously, reducing total execution time from ~100 seconds to ~10 seconds.

---

### 3️⃣ **Data Consolidation Layer**

#### `Merge` Node
**Type**: Merge  
**Purpose**: Combines all RSS feed outputs into a single data stream

**What it does:**
- Takes 10 separate inputs (one from each RSS feed)
- Merges them into a unified dataset
- Preserves all article data from each source
- Configured for `numberInputs: 10`

**Output:** Single stream containing 100-500 articles depending on feed activity

---

#### `Aggregate` Node
**Type**: Aggregate  
**Purpose**: Prepares merged data for processing

**What it does:**
- Aggregates all items into a single data structure
- Creates a `data` array containing all RSS XML responses
- Makes it easier for the next node to loop through feeds

**Why it's needed:** Converts multiple items into one item with an array, simplifying the filtering logic.

---

### 4️⃣ **Filtering & Processing Layer**

#### `Artical by Date` (Code Node)
**Type**: JavaScript Code  
**Purpose**: Filters articles by date range and research subject

**What it does:**
- Extracts form inputs (lookback days, research subject)
- Calculates cutoff date (today minus lookback days)
- Parses XML from each aggregated feed
- Extracts: title, link, description, publication date
- Filters articles that:
  - Are newer than cutoff date
  - Contain research subject keywords in title or description
- Sorts by date (newest first)
- Limits to top 50 matching articles

**Example filtering:**
```javascript
Research Subject: "NVMe"
Lookback: 2 days
Cutoff Date: Nov 26, 2025

Article 1: "New NVMe SSD Technology" (Nov 27) ✅ MATCHES
Article 2: "Cloud Storage Trends" (Nov 27) ❌ No NVMe mention
Article 3: "NVMe Performance Study" (Nov 25) ❌ Too old
```

**Output:** 0-50 articles matching criteria, or error message if none found

---

#### `Combine Articals` (Code Node)
**Type**: JavaScript Code  
**Purpose**: Formats filtered articles into readable text

**What it does:**
- Takes all filtered articles
- Formats each as:
  ```
  Article 1:
  Title: [title]
  Description: [description]
  Link: [url]
  Date: [pubDate]
  ---
  ```
- Combines into single text string
- Attaches messaging focus and research subject
- Counts total articles

**Output:** Single item with formatted article text ready for AI analysis

---

### 5️⃣ **AI Analysis Layer**

#### `Summarize articles & identify pain points` (OpenAI Node)
**Type**: AI Model (LLaMA via OpenRouter)  
**Purpose**: Analyzes articles to extract business insights

**What it does:**
- Sends all article text to AI model (meta-llama/llama-3-8b-instruct)
- Uses system prompt: "Expert at analyzing enterprise storage industry news"
- Asks AI to identify:
  - Most painful client issues/challenges
  - Key industry trends
  - Common themes
  - 2-3 sentence summary
- Requests structured JSON output

**Example output:**
```json
{
  "painPoints": [
    {"issue": "High cloud storage costs", "frequency": "mentioned in 12 articles"},
    {"issue": "Data migration complexity", "frequency": "mentioned in 8 articles"}
  ],
  "keyTrends": ["NVMe adoption", "AI-driven storage", "Multi-cloud strategies"],
  "commonThemes": ["Cost optimization", "Performance", "Security"],
  "summary": "Enterprise storage market is shifting toward NVMe-based solutions..."
}
```

**Why LLaMA:** Cost-effective, fast, and excellent at structured data extraction

---

### 6️⃣ **Content Strategy Layer**

#### `Prepare Carousel Data` (Code Node)
**Type**: JavaScript Code  
**Purpose**: Structures data for carousel content generation

**What it does:**
- Extracts AI analysis results
- Gets form inputs (research subject, messaging focus)
- Selects top 5 articles as references
- Packages everything into clean JSON structure

**Output:** Single item containing all necessary data for carousel creation

---

#### `Create Carousel Content Strategy` (OpenAI Node)
**Type**: AI Model (LLaMA via OpenRouter)  
**Purpose**: Generates complete 5-slide carousel content

**What it does:**
- Takes pain points, trends, and articles
- Uses system prompt: "Expert LinkedIn marketing strategist for B2B tech"
- Creates 5-slide structure:
  - **Slide 1:** Hook (biggest pain point)
  - **Slide 2:** Problem amplification
  - **Slide 3:** Key insight/trend
  - **Slide 4:** Solution direction
  - **Slide 5:** Call-to-action
- For each slide generates:
  - Headline (max 60 characters)
  - Body text (2-3 bullets or 1-2 paragraphs)
  - Visual description
  - Design style (minimal/bold/data-driven)
- Creates carousel concept, ICP, and post caption

**Example output:**
```json
{
  "carouselConcept": "NVMe: The Hidden Cost Killer",
  "icp": "IT Directors at mid-market enterprises",
  "postCaption": "Are you paying 40% more for storage than you should?...",
  "slides": [
    {
      "slideNumber": 1,
      "headline": "Your Storage Budget is Bleeding",
      "bodyText": "• 40% of enterprises overspend on storage\n• Legacy systems cost 3x more to maintain",
      "visualDescription": "Bold statistics with warning imagery",
      "designStyle": "bold"
    }
  ]
}
```

---

### 7️⃣ **Content Distribution Layer**

#### `Split Into Individual Slides` (Code Node)
**Type**: JavaScript Code  
**Purpose**: Separates carousel data into individual slide items

**What it does:**
- Parses AI response (handles JSON or markdown-wrapped JSON)
- Extracts carousel metadata
- Creates 5 separate items (one per slide)
- Each item contains:
  - Slide-specific content
  - Global carousel data (concept, ICP, caption)
  - Total slide count

**Why it's needed:** Each slide needs to be processed individually for HTML generation

**Output:** 5 items flowing through the workflow (one per slide)

---

### 8️⃣ **Visual Design Layer**

#### `Generate HTML for Slide1` (Code Node)
**Type**: JavaScript Code  
**Purpose**: Creates beautiful HTML slide design for each slide

**What it does:**
- Takes slide data (headline, body text, design style)
- Selects color scheme based on design style:
  - **Minimal:** Purple gradient
  - **Bold:** LinkedIn blue
  - **Data-driven:** Navy with cyan accents
  - **Modern:** Purple-pink gradient
  - **Dark:** Dark with green accents
- Generates complete HTML with:
  - 1080x1080px dimensions (LinkedIn optimized)
  - Gradient background
  - Slide number badge
  - CTA badge on slide 5
  - Formatted headline and body text
  - Your logo in bottom-right corner
  - Glassmorphism effects
  - Custom fonts and shadows

**Logo integration:**
```javascript
const logoUrl = 'https://i.ibb.co/WmG4cRC/image-modified.png';
```
**Important:** Replace this URL with your own logo!

**CSS Features:**
- Responsive text sizing
- Backdrop blur effects
- Smooth shadows
- Auto bullet formatting for multi-line text

**Output:** Complete HTML document for each slide

---

#### `Split Out2` Node
**Type**: Split Out  
**Purpose**: Extracts HTML field for processing

**What it does:**
- Takes the HTML string from previous node
- Splits it out as separate field
- Prepares for image conversion API

**Why it's needed:** The image conversion API expects HTML as a specific field format

---

#### `Convert HTML to Image` (HTTP Request Node)
**Type**: HTTP Request to htmlcsstoimage.com API  
**Purpose**: Converts HTML slides to PNG images

**What it does:**
- Sends HTML to HTML to Image API (HCTI)
- Configures image settings:
  - Viewport: 1080x1080px
  - Device scale: 2x (for retina quality)
  - Google Fonts: Roboto
- Receives back PNG image URL

**API credentials:** Uses HTTP Basic Auth with your HCTI API key

**Output:** Image URL for each slide (5 total)

---

### 9️⃣ **Image Consolidation Layer**

#### `Aggregate2` Node
**Type**: Aggregate  
**Purpose**: Collects all 5 slide images together

**What it does:**
- Waits for all 5 image URLs to be generated
- Aggregates them into a single item
- Creates a `data` array with all image URLs in order

**Output:** Single item containing array of 5 image URLs

---

### 🔟 **PDF Conversion Layer**

#### `Image to PDF` (HTTP Request Node)
**Type**: HTTP Request to PDF.co API  
**Purpose**: Combines 5 images into a single PDF carousel

**What it does:**
- Takes all 5 image URLs from aggregate
- Sends to PDF.co API
- Combines images in order into multi-page PDF
- Each slide becomes one PDF page

**API format:**
```json
{
  "url": "image1.png,image2.png,image3.png,image4.png,image5.png"
}
```

**Output:** PDF URL hosted by PDF.co

---

#### `download pdf` (HTTP Request Node)
**Type**: HTTP Request  
**Purpose**: Downloads the PDF file as binary data

**What it does:**
- Fetches PDF from PDF.co URL
- Downloads as binary file
- Stores in n8n as `corousel.pdf`
- Prepares for LinkedIn upload

**Why it's needed:** LinkedIn requires the actual PDF file, not just a URL

---

### 1️⃣1️⃣ **LinkedIn Publishing Layer**

#### `LinkedIn user URN` (HTTP Request Node)
**Type**: HTTP Request to LinkedIn API  
**Purpose**: Gets your LinkedIn profile identifier

**What it does:**
- Calls LinkedIn `/v2/userinfo` endpoint
- Retrieves your user URN (unique identifier)
- Uses OAuth2 authentication

**Output:** Your LinkedIn profile ID (e.g., `urn:li:person:ABC123`)

**Why it's needed:** LinkedIn API requires this ID to post on your behalf

---

#### `Get Upload URL` (HTTP Request Node)
**Type**: HTTP Request to LinkedIn API  
**Purpose**: Requests permission to upload a document

**What it does:**
- Calls LinkedIn `/rest/documents?action=initializeUpload`
- Provides your user URN as owner
- LinkedIn responds with:
  - Temporary upload URL
  - Document ID for later reference

**How it works:**
1. You: "I want to upload a document"
2. LinkedIn: "OK, upload to this temporary URL, here's your document ID"

**Output:** Upload URL and document reference

---

#### `Upload PDF` (HTTP Request Node)
**Type**: HTTP Request (PUT method)  
**Purpose**: Uploads your PDF carousel to LinkedIn's servers

**What it does:**
- Uses the temporary upload URL from previous step
- Uploads the `corousel.pdf` binary file
- Sets Content-Type as `application/pdf`
- Uses OAuth2 authentication

**Technical details:**
- Method: PUT (not POST!)
- Body: Binary PDF data
- LinkedIn stores the PDF temporarily

---

#### `Post to LinkedIn` (HTTP Request Node)
**Type**: HTTP Request to LinkedIn API  
**Purpose**: Publishes your carousel post

**What it does:**
- Creates a LinkedIn post with:
  - Author: Your user URN
  - Commentary: Post caption (currently "Hello", should be dynamic)
  - Visibility: PUBLIC
  - Content: References uploaded PDF document
  - Distribution: MAIN_FEED
- Publishes immediately (lifecycleState: PUBLISHED)

**Post structure:**
```json
{
  "author": "urn:li:person:YOUR_ID",
  "commentary": "Your post caption here",
  "visibility": "PUBLIC",
  "content": {
    "media": {
      "title": "Carousel Title",
      "id": "urn:li:document:DOC_ID"
    }
  }
}
```

**Output:** Successfully published LinkedIn post with carousel

---

## 📊 Data Flow Summary

```
USER INPUT (Form)
  ↓
COLLECT: 10 RSS feeds → ~200 articles
  ↓
FILTER: By date & keywords → 50 articles
  ↓
ANALYZE: AI extracts pain points → JSON insights
  ↓
CREATE: AI generates carousel → 5 slides
  ↓
DESIGN: HTML templates → 5 HTML documents
  ↓
CONVERT: HTML → 5 PNG images
  ↓
COMBINE: Images → 1 PDF document
  ↓
UPLOAD: PDF → LinkedIn servers
  ↓
PUBLISH: LinkedIn post → PUBLIC
```

---

## 📋 Prerequisites

Before setting up this workflow, you'll need:

### Required Services & Accounts

1. **n8n** - Self-hosted or cloud instance ([n8n.io](https://n8n.io))
2. **OpenRouter Account** - For AI processing ([openrouter.ai](https://openrouter.ai))
3. **HTML to Image API** - For slide generation ([htmlcsstoimage.com](https://htmlcsstoimage.com))
4. **PDF.co Account** - For PDF conversion ([pdf.co](https://pdf.co))
5. **LinkedIn Developer Account** - For posting ([LinkedIn API](https://developer.linkedin.com))

### Required Credentials

You'll need to set up these credentials in n8n:

- `Open router n8n setup` - OpenRouter API credentials
- `html to image` - HTML to Image API credentials
- `LinkedIn account` - LinkedIn OAuth2 credentials

---

## 🛠️ Installation

### Step 1: Import the Workflow

1. Download the `workflow.json` file from this repository
2. Open your n8n instance
3. Go to **Workflows** → **Import from File**
4. Select the downloaded JSON file

### Step 2: Configure Your Logo

1. Upload your logo to an image hosting service (e.g., [ImgBB](https://imgbb.com))
2. Open the **Generate HTML for Slide1** node
3. Replace the `logoUrl` variable with your logo URL:

```javascript
const logoUrl = 'YOUR_LOGO_URL_HERE';
```

### Step 3: Set Up Credentials

Configure each credential in n8n:

#### OpenRouter API
- Go to **Settings** → **Credentials** → **Add Credential**
- Select **OpenAI API**
- Enter your OpenRouter API key
- Name it: `Open router n8n setup`

#### HTML to Image API
- Select **HTTP Basic Auth**
- Enter your API User ID and Key
- Name it: `Azeema html to image`

#### LinkedIn OAuth2
- Select **LinkedIn OAuth2 API**
- Follow LinkedIn's OAuth setup guide
- Name it: `LinkedIn account 2`

#### PDF.co API
- Update the API key in the **Image to PDF** node header
- Replace the placeholder key with your PDF.co API key

### Step 4: Test the Workflow

1. Click on the **On form submission** node
2. Copy the webhook URL
3. Open the URL in your browser
4. Fill in the test form:
   - **Research Subject**: e.g., "cloud storage"
   - **Lookback Period**: 2 days
   - **Messaging Focus**: e.g., "cost optimization"
5. Click **Generate Carousel**

---

## 📖 How to Use

### Fill the Form

Access your workflow's form URL and provide:

1. **Research Subject** - Topic to search for (e.g., "NVMe", "data backup", "hybrid cloud")
2. **Lookback Period** - How many days back to search (1-5 days)
3. **Messaging Focus** - Your content angle (e.g., "security", "ROI", "innovation")

### Workflow Steps

The automation will:

1. ✅ Fetch articles from 10 RSS feeds
2. ✅ Filter by date and research subject
3. ✅ Aggregate up to 50 relevant articles
4. ✅ Analyze with AI to extract pain points and trends
5. ✅ Generate 5-slide carousel strategy
6. ✅ Create branded visual slides
7. ✅ Convert to PDF
8. ✅ Post directly to your LinkedIn profile

### Output

You'll get:
- 5 professionally designed carousel slides
- Engaging headline and body copy for each slide
- A strategic LinkedIn post caption
- Automatic posting to LinkedIn

---

## 🎨 Customization

### Design Styles

The workflow supports 5 design themes:
- **Minimal** - Clean purple gradient
- **Bold** - LinkedIn blue with strong contrast
- **Data-driven** - Navy blue with cyan accents
- **Modern** - Purple-pink gradient
- **Dark** - Sleek dark theme with green accents

### Modify RSS Sources

To add or change news sources, edit the RSS feed nodes:

1. Duplicate an existing RSS feed node
2. Update the URL to your preferred source
3. Connect it to the **Merge** node
4. Update the `numberInputs` parameter in the Merge node

### Adjust Slide Count

Default is 5 slides. To change:

1. Edit the **Create Carousel Content Strategy** node
2. Modify the prompt to request different slide count
3. Ensure the **Split Into Individual Slides** node handles the new count

---

## 📁 Repository Structure

```
linkedin-carousel-automation/
├── README.md                 # This file
├── workflow.json            # n8n workflow file
├── .gitignore               # Git ignore file
├── pdf                      # License file
└── screenshort/             # n8n workflow and LinkedIn post
    ├── sreenshort_1.png
    └── sreenshort_2.png
```

---

## 🐛 Troubleshooting

### Common Issues

**"No articles found matching criteria"**
- Increase the lookback period
- Use broader search terms
- Check if RSS feeds are accessible

**"HTML to Image conversion failed"**
- Verify your HTML to Image API credentials
- Check API quota/limits
- Ensure HTML is valid

**"LinkedIn posting failed"**
- Refresh LinkedIn OAuth token
- Verify LinkedIn API permissions
- Check PDF size (LinkedIn has size limits)

**"Invalid JSON from AI"**
- AI occasionally returns malformed JSON
- The workflow has JSON extraction fallbacks
- Try running again if it fails

---

## 🚀 Future Enhancements

Potential improvements:
- [ ] Support for Instagram carousel format
- [ ] Multi-language content generation
- [ ] A/B testing for different messaging angles
- [ ] Analytics tracking for engagement metrics
- [ ] Scheduled automated posting
- [ ] Custom color scheme configuration via form

---

## 📊 Performance Tips

- **API Costs**: This workflow uses AI and conversion APIs. Monitor your usage
- **Rate Limits**: RSS feeds are called once per execution. PDF.co has daily limits
- **Execution Time**: Full workflow takes 2-3 minutes to complete
- **Best Practices**: Run during off-peak hours for better API response times

---

## 📜 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

## 📞 Contact & Support

**Need Help?** Have questions or suggestions?

- 📧 **Email**: [your.email@example.com](mailto:uzmakhatun0205@gmail.com)
- 💼 **LinkedIn**: [LinkedIn Profile](https://www.linkedin.com/in/uzma-khatun-88b990334/)
- 💼 **GitHub**: [GitHub Profile](https://github.com/UzmaKhatun)
- 💼 **Portfolio**: [Portfolio ](https://portfolio-uzmakhatun.netlify.app/)

---

## 🙏 Acknowledgments

- **n8n** - For the amazing workflow automation platform
- **OpenRouter** - For AI model access
- **RSS Feed Sources** - All the enterprise storage news publications

---

## ⭐ Show Your Support

If this workflow helped you, please:
- ⭐ Star this repository
- 🐦 Share on Twitter/LinkedIn
- 🔗 Link back to this repo in your content
- ☕ [Buy me a coffee](https://buymeacoffee.com/) (optional)

---

**Made with ❤️ by Uzma Khatun**

*Last Updated: November 2025*
