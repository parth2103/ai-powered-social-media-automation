# Complete Setup Guide

## Prerequisites

Before starting, ensure you have access to:
- Make.com account (Free tier sufficient for testing)
- OpenAI API access with billing enabled
- Google account for Sheets integration
- Facebook Developer account
- Instagram Business account linked to Facebook Page
- LinkedIn account (Personal or Company Page)

## Step 1: Google Sheets Setup

### 1.1 Create Spreadsheet
1. Go to [Google Sheets](https://sheets.google.com)
2. Create a new blank spreadsheet
3. Name it "AI Social Media Automation"

### 1.2 Set Up Column Structure
Copy the exact header structure from our template:

| Column | Header | Purpose |
|--------|--------|---------|
| A | My Idea | Input your content ideas here |
| B | Facebook Post Content | Auto-generated Facebook content |
| C | Instagram Post Content | Auto-generated Instagram content |
| D | LinkedIn Post Content | Auto-generated LinkedIn content |
| E | Image Link | Auto-generated DALL-E image URL |
| F | Control Column | Workflow control ("Generate Content" or "Create Post") |
| G | Status/Timestamp | Execution status and timestamp |

### 1.3 Configure Sharing
1. Click "Share" button in top-right corner
2. Set to "Anyone with the link can view"
3. Copy the Spreadsheet ID from the URL:
   ```
   https://docs.google.com/spreadsheets/d/[SPREADSHEET_ID]/edit
   ```
4. Save this ID - you'll need it for Make.com configuration

## Step 2: OpenAI API Setup

### 2.1 Create OpenAI Account
1. Visit [OpenAI Platform](https://platform.openai.com)
2. Sign up or log in to your account
3. Navigate to API section

### 2.2 Generate API Key
1. Go to "API keys" in your dashboard
2. Click "Create new secret key"
3. Name it "Social Media Automation"
4. Copy and securely save the API key
5. **Important**: This key is only shown once

### 2.3 Set Up Billing
1. Go to "Billing" section
2. Add payment method
3. Set usage limits to control costs:
   - Recommended: $10/month for testing
   - Increase based on usage patterns

### 2.4 Test API Access
Verify your setup with a simple test:
```bash
curl https://api.openai.com/v1/models \
  -H "Authorization: Bearer YOUR_API_KEY"
```

## Step 3: Social Media API Configuration

### 3.1 Facebook & Instagram Setup

#### Create Facebook App
1. Go to [Facebook Developers](https://developers.facebook.com)
2. Click "My Apps" → "Create App"
3. Select "Business" as app type
4. Fill in app details:
   - App Name: "Social Media Automation"
   - Contact Email: Your email
   - App Purpose: "Automate social media posting"

#### Configure Instagram Basic Display
1. In your Facebook app, click "Add Product"
2. Find "Instagram Basic Display" and click "Set Up"
3. Go to Instagram Basic Display → Basic Display
4. Create new Instagram App ID
5. Add Instagram Test Users:
   - Go to Roles → Roles
   - Add your Instagram account as a test user

#### Set Up Permissions
Required permissions for Instagram:
- `instagram_basic`
- `pages_show_list`
- `pages_read_engagement`

Required permissions for Facebook:
- `pages_manage_posts`
- `pages_read_engagement`

### 3.2 LinkedIn API Setup

#### Create LinkedIn App
1. Go to [LinkedIn Developers](https://developer.linkedin.com)
2. Click "Create app"
3. Fill in application details:
   - App name: "Social Media Automation"
   - LinkedIn Page: Associate with your business page
   - Privacy policy URL: Required (can use generic business URL)
   - App logo: Upload any professional logo

#### Configure Products
Request access to:
- "Share on LinkedIn" product
- "Sign In with LinkedIn" product

#### OAuth Settings
1. Go to "Auth" tab
2. Add authorized redirect URLs:
   ```
   https://www.make.com/oauth/cb/linkedin
   ```

### 3.3 Google Sheets API Setup

#### Enable Google Sheets API
1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. Create new project or select existing one
3. Enable Google Sheets API:
   - Go to "APIs & Services" → "Library"
   - Search for "Google Sheets API"
   - Click "Enable"

#### Create Service Account
1. Go to "APIs & Services" → "Credentials"
2. Click "Create Credentials" → "Service Account"
3. Fill in service account details:
   - Name: "Social Media Automation"
   - Description: "Service account for Make.com integration"
4. Download the JSON credentials file
5. Copy the service account email address

#### Share Spreadsheet with Service Account
1. Open your Google Spreadsheet
2. Click "Share"
3. Paste the service account email
4. Set permission to "Editor"
5. Uncheck "Notify people"
6. Click "Share"

## Step 4: Make.com Configuration

### 4.1 Import Blueprint
1. Log in to [Make.com](https://make.com)
2. Go to "Scenarios"
3. Click "Create a new scenario"
4. Click "Import Blueprint"
5. Upload the `automation/sheet-to-social-blueprint.json` file
6. Click "Save"

### 4.2 Configure Connections

#### Google Sheets Connection
1. Click on the Google Sheets module
2. Click "Add" next to Connection
3. Choose "Service Account" authentication
4. Upload your service account JSON file
5. Test the connection

#### OpenAI Connection
1. Click on OpenAI modules
2. Click "Add" next to Connection
3. Enter your OpenAI API key
4. Test the connection

#### Instagram Connection
1. Click on Instagram modules
2. Click "Add" next to Connection
3. Follow OAuth flow to authorize your Instagram Business account
4. Select your Instagram Business account

#### Facebook Connection
1. Click on Facebook modules
2. Click "Add" next to Connection
3. Authorize with your Facebook account
4. Select your Facebook Page

#### LinkedIn Connection
1. Click on LinkedIn modules
2. Click "Add" next to Connection
3. Complete OAuth authorization
4. Select your LinkedIn profile or company page

### 4.3 Update Module Settings

#### Google Sheets Search Module
- **Spreadsheet**: Select your spreadsheet
- **Sheet Name**: "Sheet1" (or your sheet name)
- **Search Method**: "Contains"
- **Filter**: Column F (Control Column) contains "Generate Content" OR "Create Post"

#### OpenAI GPT-4 Module
- **Model**: gpt-4-1106-preview
- **Messages**: 
  - System: [Content from prompts/content-generation-system-prompt.txt]
  - User: {{1.`A (My Idea)`}}
- **Response Format**: JSON Object
- **Max Tokens**: 1500
- **Temperature**: 0.7

#### DALL-E 3 Module
- **Prompt**: [Content from prompts/image-generation-prompt.txt] + {{1.`A (My Idea)`}}
- **Size**: 1024x1024
- **Quality**: Standard
- **Style**: Vivid

#### Update Row Modules
- **Spreadsheet**: Same as search module
- **Row Number**: {{1.__ROW_NUMBER__}}
- **Values**: Map generated content to appropriate columns

### 4.4 Configure Routing Logic
Router module automatically routes based on Control Column value:
- "Generate Content" → Content generation flow
- "Create Post" → Social media publishing flow

## Step 5: Testing

### 5.1 Test Content Generation
1. Add a test idea to Row 2 of your spreadsheet:
   ```
   Column A: "Testing AI automation workflow"
   Column F: "Generate Content"
   ```
2. In Make.com, click "Run once"
3. Verify that:
   - Content appears in columns B, C, D
   - Image URL appears in column E
   - Status updates to "Content Generated"

### 5.2 Test Social Publishing
1. Change the Control Column (F) to "Create Post"
2. Run the scenario again
3. Verify posts appear on:
   - Your Instagram Business account
   - Your Facebook Page
   - Your LinkedIn profile/page
4. Check that status updates to "Posted"

### 5.3 Troubleshooting Common Issues

#### "Spreadsheet not found"
- Verify spreadsheet ID in module settings
- Ensure service account has access to the spreadsheet
- Check that the spreadsheet is not in Trash

#### "Rate limit exceeded" (OpenAI)
- Check your API usage limits
- Reduce frequency of test runs
- Consider upgrading your OpenAI plan

#### "Instagram posting failed"
- Ensure Instagram account is a Business account
- Verify Facebook Page is properly connected to Instagram
- Check that image URLs are publicly accessible

#### "LinkedIn authorization failed"
- Verify your LinkedIn app has required permissions
- Check that redirect URLs match exactly
- Ensure your LinkedIn app is not in development mode restrictions

## Step 6: Production Setup

### 6.1 Scheduling
1. In Make.com scenario settings, set up scheduling:
   - **Recommended**: Every 15 minutes during business hours
   - **Alternative**: Webhook trigger for real-time processing

### 6.2 Error Handling
Configure error handling:
- **Max errors**: 3 attempts
- **Error notifications**: Enable email alerts
- **Incomplete executions**: Store for manual review

### 6.3 Monitoring
Set up monitoring:
- **Execution history**: Review daily
- **Cost tracking**: Monitor OpenAI usage
- **Performance metrics**: Track success rates

### 6.4 Scaling Considerations
For high-volume usage:
- **Rate limiting**: Respect API limits across all platforms
- **Cost optimization**: Monitor token usage and optimize prompts
- **Content quality**: Regular review of generated content
- **Backup plans**: Alternative workflows for API failures

## Security Best Practices

### API Key Management
- Store API keys securely in Make.com vault
- Rotate keys regularly (quarterly recommended)
- Monitor API usage for unusual patterns
- Use environment-specific keys for testing vs. production

### Access Control
- Limit Google Sheets access to necessary accounts only
- Use service accounts instead of personal accounts where possible
- Regular audit of connected applications and permissions
- Implement IP restrictions where supported by APIs

### Data Privacy
- Review all social media platform privacy policies
- Ensure compliance with relevant data protection regulations
- Monitor what data is being processed and stored
- Implement data retention policies

## Cost Optimization

### OpenAI Usage
- **Prompt optimization**: Reduce unnecessary tokens in prompts
- **Model selection**: Consider GPT-3.5 for simpler content generation
- **Batch processing**: Process multiple ideas in single API calls where possible
- **Content caching**: Avoid regenerating similar content

### Make.com Operations
- **Scenario optimization**: Combine operations where possible
- **Conditional logic**: Skip unnecessary modules based on conditions
- **Data transfer**: Minimize data passed between modules
- **Execution frequency**: Balance automation speed with cost

This setup guide provides a complete foundation for implementing the AI-powered social media automation system. Follow each step carefully, and refer to the troubleshooting section if you encounter any issues.