#  Multi-Platform Social Media Automation with AI

An intelligent n8n workflow that transforms a single topic into platform-optimized content for LinkedIn, Twitter/X, and Instagram - complete with AI-generated infographics and automated publishing.

## What This Does

This automation system:
- ✅ Fetches content topics from Airtable
- ✅ Conducts real-time research using Perplexity AI
- ✅ Generates platform-specific content with Claude AI
- ✅ Creates custom infographics with DALL-E
- ✅ Routes content through Telegram approval workflow
- ✅ Publishes simultaneously to LinkedIn, Twitter/X, and Instagram
- ✅ Updates tracking database with post links
- ✅ Handles cleanup automatically

##  Platform-Specific Content Optimization

### LinkedIn
- **Style:** Professional thought leadership
- **Length:** 150-250 words
- **Format:** No emojis, no hashtags, natural paragraphs
- **Tone:** Conversational yet authoritative

### Twitter/X
- **Style:** Punchy and engaging
- **Length:** 200-280 characters
- **Format:** 2-3 emojis, 2-3 hashtags
- **Tone:** Bold and opinionated

### Instagram
- **Style:** Storytelling and inspirational
- **Length:** 150-200 words
- **Format:** 5-8 emojis, 10-15 hashtags
- **Tone:** Warm and relatable

##  Architecture

```
┌─────────────────┐
│  Schedule       │
│  Trigger        │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Airtable       │◄─── Content Queue
│  Fetch          │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Perplexity     │◄─── Real-time Research
│  AI Research    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Claude AI      │◄─── Platform-specific
│  Content Gen    │     Content Generation
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  DALL-E         │◄─── Infographic
│  Image Gen      │     Generation
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Google Drive   │◄─── Temporary Storage
│  Upload         │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Telegram       │◄─── Human Approval
│  Review         │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Multi-Platform │◄─── Parallel Publishing
│  Publishing     │     LinkedIn | Twitter | Instagram
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Airtable       │◄─── Status Update
│  Update         │     & Link Storage
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Google Drive   │◄─── Cleanup
│  Delete         │
└─────────────────┘
```

##  Prerequisites

### Required Services & API Access

1. **n8n Instance** (Self-hosted or Cloud)
   - Version 1.0 or higher recommended

2. **Airtable**
   - Personal Access Token
   - Base with content management table
   - Required fields: `Title`, `Link`, `Status`, `LinkedIn_Post_Link`, `Twitter_Post_Link`, `Instagram_Post_Link`

3. **Claude AI (Anthropic)**
   - API Key from [Anthropic Console](https://console.anthropic.com/)
   - Recommended model: `claude-sonnet-4-20250514`

4. **Perplexity AI**
   - API Key from [Perplexity Settings](https://www.perplexity.ai/settings/api)
   - Model: `sonar`

5. **OpenAI**
   - API Key from [OpenAI Platform](https://platform.openai.com/)
   - Access to DALL-E 3 model

6. **Google Drive**
   - OAuth2 credentials
   - Folder ID for temporary image storage

7. **Telegram**
   - Bot Token from [@BotFather](https://t.me/botfather)
   - Your Chat ID

8. **LinkedIn**
   - Developer App from [LinkedIn Developers](https://www.linkedin.com/developers/)
   - OAuth2 credentials
   - Required permissions: `w_member_social`, `r_basicprofile`
   - Your LinkedIn Person URN

9. **Twitter/X**
   - Developer Portal access: [X Developer Portal](https://developer.twitter.com/)
   - App with OAuth 2.0 enabled
   - Required permissions: `tweet.read`, `tweet.write`, `users.read`

10. **Instagram**
    - Meta Developer account: [Meta for Developers](https://developers.facebook.com/)
    - Instagram Business Account connected to Facebook Page
    - Access Token with `instagram_basic`, `instagram_content_publish` permissions
    - Instagram Business Account ID

##  Installation

### 1. Import Workflow to n8n

1. Download `linkedin_automation_workflow.json`
2. In n8n, go to **Workflows** → **Import from File**
3. Select the JSON file

### 2. Configure Credentials

Set up the following credentials in n8n:

#### Airtable
- **Credential Type:** Airtable Token API
- **Access Token:** Your Airtable Personal Access Token

#### Anthropic (Claude AI)
- **Credential Type:** Anthropic API
- **API Key:** Your Anthropic API Key

#### Perplexity AI
- **Credential Type:** HTTP Header Auth
- **Header Name:** `Authorization`
- **Header Value:** `Bearer YOUR_PERPLEXITY_API_KEY`

#### OpenAI
- **Credential Type:** OpenAI API
- **API Key:** Your OpenAI API Key

#### Google Drive
- **Credential Type:** Google Drive OAuth2 API
- Follow OAuth2 setup wizard

#### Telegram
- **Credential Type:** Telegram API
- **Access Token:** Your Bot Token
- **Base URL:** (leave default)

#### LinkedIn
- **Credential Type:** LinkedIn OAuth2 API
- Follow OAuth2 setup wizard
- **Client ID & Secret:** From your LinkedIn Developer App

#### Twitter/X
- **Credential Type:** Twitter OAuth2 API
- Follow OAuth2 setup wizard
- **Client ID & Secret:** From X Developer Portal

#### Instagram
- **Credential Type:** Instagram API
- **Access Token:** Your Instagram Graph API Token
- **Account ID:** Your Instagram Business Account ID

### 3. Update Workflow Configuration

Replace the following placeholders in the workflow:

```javascript
YOUR_AIRTABLE_BASE_ID          → Your Airtable Base ID
YOUR_TABLE_ID                  → Your Airtable Table ID
YOUR_GOOGLE_DRIVE_FOLDER_ID    → Google Drive Folder ID
YOUR_TELEGRAM_CHAT_ID          → Your Telegram Chat ID
YOUR_LINKEDIN_PERSON_ID        → Your LinkedIn Person URN
```

### 4. Set Up Airtable Structure

Create an Airtable base with the following fields:

| Field Name | Type | Options |
|------------|------|---------|
| Title | Single line text | - |
| Link | URL | - |
| Status | Single select | Waiting, Generated, Posted |
| LinkedIn_Post_Link | URL | - |
| Twitter_Post_Link | URL | - |
| Instagram_Post_Link | URL | - |
| Posted_Date | Date | Include time |

### 5. Configure Schedule

The workflow runs at:
- 10:00 AM
- 3:00 PM  
- 8:00 PM

Modify the cron expression in the **Schedule Trigger** node to adjust timing:
```
0 10,15,20 * * *
```

##  Usage

### Basic Workflow

1. **Add Content to Airtable**
   - Create a new record with `Title` and optional `Link`
   - Set `Status` to `Waiting`

2. **Automated Processing**
   - Workflow triggers at scheduled time
   - Researches topic via Perplexity AI
   - Generates platform-specific content with Claude AI
   - Creates infographic with DALL-E
   - Uploads to Google Drive

3. **Review & Approve**
   - Receive Telegram message with all content
   - Review LinkedIn, Twitter, and Instagram posts
   - Review generated infographic
   - Approve or reject

4. **Automatic Publishing**
   - Posts publish to all three platforms
   - Airtable updates with post links
   - Status changes to "Posted"
   - Temporary files cleaned up

### Manual Trigger

You can also trigger the workflow manually:
1. Open workflow in n8n
2. Click **Execute Workflow**
3. Process runs immediately

##  Customization

### Modify Content Style

Edit the prompt in the **Generate Platform Content** node to adjust:
- Tone and voice
- Content structure
- Word count limits
- Emoji usage
- Hashtag strategy

### Change AI Models

Update model selections in:
- **Claude AI Model** node: Change to `claude-opus-4` for higher quality
- **Perplexity Search** node: Adjust research depth
- **Generate Image** node: Modify DALL-E settings for different image styles

### Adjust Image Specifications

In the **Generate Image** node:
- **Size:** Change from `1024x1792` (portrait) to `1792x1024` (landscape) or `1024x1024` (square)
- **Quality:** Switch between `standard` and `hd`

### Platform Selection

To publish to only specific platforms:
- Disable connections to unwanted platform nodes
- Remove unused nodes from workflow

##  Monitoring

### Check Execution History
- n8n Dashboard → Executions
- Filter by status (Success, Error, Running)
- Review execution logs

### Track Published Content
- Airtable base shows all posted content
- Direct links to published posts
- Publication timestamps

### Error Handling
The workflow includes error handling for:
- API rate limits
- Failed image generation
- Publishing failures
- Approval timeouts

##  Security Best Practices

1. **Never commit credentials** to version control
2. **Use environment variables** for sensitive data
3. **Rotate API keys** regularly
4. **Limit API permissions** to minimum required
5. **Enable 2FA** on all service accounts
6. **Monitor API usage** to detect unauthorized access

##  Cost Considerations

Approximate costs per execution:

| Service | Cost per Run | Notes |
|---------|-------------|-------|
| Claude AI | ~$0.03 | Based on Sonnet 4 pricing |
| Perplexity AI | ~$0.01 | Sonar model |
| OpenAI DALL-E | ~$0.04 | HD quality image |
| Airtable | Free tier | Up to 1,200 records/base |
| Google Drive | Free tier | 15GB storage |
| Other APIs | Free tier | Within rate limits |

**Monthly estimate (3x/day):** ~$7.20

## Troubleshooting

### Common Issues

**Workflow doesn't trigger**
- Check Schedule Trigger is enabled
- Verify cron expression is correct
- Check n8n service is running

**Content not generating**
- Verify Claude AI API key is valid
- Check API rate limits
- Review prompt formatting

**Image generation fails**
- Confirm OpenAI API key has DALL-E access
- Check prompt length (max 4000 characters)
- Verify billing is active

**Publishing fails**
- Check platform API credentials
- Verify account permissions
- Review platform-specific rate limits

**Telegram approval not working**
- Confirm bot token is correct
- Check chat ID is accurate
- Verify bot has permission to send messages

##  Contributing

Improvements and suggestions welcome! Feel free to:
- Fork this workflow
- Submit pull requests
- Open issues for bugs or feature requests
- Share your customizations

##  License

This workflow is provided as-is under the MIT License. You're free to modify and distribute it.

##  Acknowledgments

Built with:
- [n8n](https://n8n.io/) - Workflow automation
- [Anthropic Claude](https://www.anthropic.com/) - AI content generation
- [Perplexity AI](https://www.perplexity.ai/) - Real-time research
- [OpenAI DALL-E](https://openai.com/dall-e) - Image generation

## 📧 Support

For questions or issues:
- Check the [n8n Community](https://community.n8n.io/)
- Review platform-specific API documentation
- Open an issue in this repository

---

**Note:** This workflow requires active API subscriptions and proper credentials for all services. Costs may vary based on usage patterns and API pricing changes.
