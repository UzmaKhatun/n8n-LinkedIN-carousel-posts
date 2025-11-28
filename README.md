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
- `LinkedIn account 2` - LinkedIn OAuth2 credentials

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

## 🔒 Security & Credentials

### ⚠️ Important Security Notice

**The workflow JSON file contains credential references but NOT the actual secrets.** You must set up your own credentials in n8n.

### Credential IDs in the Workflow

These IDs reference credentials but don't expose secrets:
- `eCHfetHriLLotBZl` - OpenRouter API reference
- `aCKCvratzBMcKqiV` - HTML to Image API reference
- `f4HTdzcJ6FruRpK0` - LinkedIn OAuth2 reference

**Before uploading to GitHub:**
- ✅ The provided JSON is safe to upload
- ✅ No API keys or secrets are exposed
- ❌ DO NOT commit any `.env` files with actual credentials

---

## 📁 Repository Structure

```
linkedin-carousel-automation/
├── README.md                 # This file
├── workflow.json            # n8n workflow file
├── .gitignore               # Git ignore file
├── LICENSE                  # License file
└── assets/                  # Optional: Screenshots and examples
    ├── workflow-preview.png
    └── sample-carousel.png
```

### Additional Files to Include

1. **`.gitignore`** - Add this to prevent credential leaks:
```
.env
*.env
credentials.json
config/credentials/
```

2. **`LICENSE`** - Choose a license (MIT recommended for open source)

3. **`assets/`** - Add screenshots of:
   - Workflow diagram
   - Sample carousel output
   - Form interface

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

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request. For major changes:

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📞 Contact & Support

**Need Help?** Have questions or suggestions?

- 📧 **Email**: [your.email@example.com](mailto:your.email@example.com)
- 💼 **LinkedIn**: [Your LinkedIn Profile](https://linkedin.com/in/yourprofile)
- 🐛 **Issues**: [GitHub Issues](https://github.com/yourusername/repo/issues)
- 💬 **Discussions**: [GitHub Discussions](https://github.com/yourusername/repo/discussions)

---

## 🙏 Acknowledgments

- **n8n** - For the amazing workflow automation platform
- **OpenRouter** - For AI model access
- **RSS Feed Sources** - All the enterprise storage news publications
- **Community** - Thanks to everyone who contributed ideas and feedback

---

## ⭐ Show Your Support

If this workflow helped you, please:
- ⭐ Star this repository
- 🐦 Share on Twitter/LinkedIn
- 🔗 Link back to this repo in your content
- ☕ [Buy me a coffee](https://buymeacoffee.com/yourhandle) (optional)

---

**Made with ❤️ by [Your Name]**

*Last Updated: November 2025*
