# Module Configuration Guide

## Module-by-Module Setup Instructions

### 1. Google Sheets Search Module (Trigger)

**Purpose**: Monitors spreadsheet for new content ideas and workflow triggers

**Configuration Settings**:
```json
{
  "spreadsheet": "YOUR_SPREADSHEET_ID",
  "includesHeaders": true,
  "sheetName": "Sheet1",
  "filter": {
    "condition": "OR",
    "rules": [
      {
        "field": "{{6}}", // Column F (Control Column)
        "operator": "equal",
        "value": "Generate Content"
      },
      {
        "field": "{{6}}", // Column F (Control Column)
        "operator": "equal",
        "value": "Create Post"
      }
    ]
  }
}
```

**Required Permissions**:
- Google Sheets API access
- Read access to target spreadsheet
- Service account email added to sheet sharing

**Testing Checklist**:
- [ ] Module connects to correct spreadsheet
- [ ] Filters work for both trigger values
- [ ] Headers are properly detected
- [ ] Row data maps correctly to subsequent modules

---

### 2. Basic Router Module

**Purpose**: Routes workflow based on Control Column value

**Route 1: Generate Content**
- **Filter Condition**: Control Column = "Generate Content"
- **Flow**: GPT-4 → JSON Parse → DALL-E → Sheet Update

**Route 2: Create Post**
- **Filter Condition**: Control Column = "Create Post"  
- **Flow**: Instagram → Facebook → LinkedIn → Sheet Update

**Configuration Notes**:
- No parameters required for router module
- Filter conditions are case-sensitive
- Routes process independently and can run in parallel

---

### 3. OpenAI GPT-4 Module (Content Generation)

**Purpose**: Generates platform-optimized social media content

**Configuration Settings**:
```json
{
  "model": "gpt-4-1106-preview",
  "messages": [
    {
      "role": "system",
      "content": "[SYSTEM_PROMPT_FROM_PROMPTS_DIRECTORY]"
    },
    {
      "role": "user", 
      "content": "{{1.`A (My Idea)`}}"
    }
  ],
  "response_format": {
    "type": "json_object"
  },
  "max_tokens": 1500,
  "temperature": 0.7
}
```

**Required Setup**:
- OpenAI API key with GPT-4 access
- Billing account with sufficient credits
- JSON response format enabled

**Expected Response Format**:
```json
{
  "Facebook_Post_Content": "...",
  "Instagram_Post_Content": "...",
  "LinkedIn_Post_Content": "..."
}
```

**Troubleshooting**:
- If response isn't JSON: Check prompt includes JSON format instruction
- If content quality is poor: Adjust temperature (0.5-0.9 range)
- If hitting rate limits: Implement delay between requests

---

### 4. JSON Parse Module

**Purpose**: Extracts platform-specific content from GPT-4 response

**Configuration Settings**:
```json
{
  "data": "{{3.choices[].message.content}}"
}
```

**Validation Steps**:
- Verify GPT-4 module returns valid JSON
- Check that all three platform keys exist in response
- Ensure content fields are properly mapped to next modules

---

### 5. DALL-E 3 Module (Image Generation)

**Purpose**: Creates visual content for social media posts

**Configuration Settings**:
```json
{
  "prompt": "[IMAGE_PROMPT_FROM_PROMPTS_DIRECTORY] Based on: {{1.`A (My Idea)`}}",
  "model": "dall-e-3",
  "size": "1024x1024",
  "quality": "standard",
  "style": "vivid"
}
```

**Optimization Notes**:
- Use "standard" quality for cost efficiency
- "vivid" style works better for social media
- 1024x1024 optimal for all platforms
- Prompt engineering crucial for consistent brand aesthetic

**Cost Considerations**:
- Standard quality: $0.040 per image
- HD quality: $0.080 per image
- Monitor usage if processing high volumes

---

### 6. Google Sheets Update Row (Content Generation)

**Purpose**: Saves generated content back to spreadsheet

**Configuration Settings**:
```json
{
  "spreadsheet": "YOUR_SPREADSHEET_ID",
  "sheetName": "Sheet1", 
  "row": "{{1.`__ROW_NUMBER__`}}",
  "values": {
    "B": "{{4.Facebook_Post_Content}}",
    "C": "{{4.Instagram_Post_Content}}", 
    "D": "{{4.LinkedIn_Post_Content}}",
    "E": "{{5.data[].url}}",
    "F": "Content Generated",
    "G": "{{formatDate(now; \"YYYY-MM-DD HH:mm:ss\")}}"
  }
}
```

**Important Notes**:
- Uses same connection as trigger module
- Row number automatically detected from trigger
- Timestamp uses Make.com's formatDate function
- Status update prevents re-triggering

---

### 7. Instagram Create Media Object

**Purpose**: Prepares Instagram post for publishing

**Configuration Settings**:
```json
{
  "account": "YOUR_INSTAGRAM_BUSINESS_ACCOUNT_ID",
  "image_url": "{{1.`E (Image link)`}}",
  "caption": "{{1.`C (Instagram Post Content)`}}"
}
```

**Prerequisites**:
- Instagram Business Account
- Facebook Page connected to Instagram
- Instagram Basic Display API access
- Business verification completed

**Common Issues**:
- Image URL must be publicly accessible
- Caption length limit: 2,200 characters
- Account ID format: Numeric string (not username)

---

### 8. Instagram Publish Media Object

**Purpose**: Publishes prepared Instagram content

**Configuration Settings**:
```json
{
  "account": "YOUR_INSTAGRAM_BUSINESS_ACCOUNT_ID",
  "creation_id": "{{7.id}}"
}
```

**Publishing Notes**:
- Requires creation ID from previous module
- 2-step process prevents accidental publishing
- Can add scheduling if needed (Instagram API supports this)

---

### 9. Facebook Create Page Post

**Purpose**: Posts content to Facebook Page

**Configuration Settings**:
```json
{
  "page": "YOUR_FACEBOOK_PAGE_ID",
  "message": "{{1.`B (Facebook Post Content)`}}",
  "picture": "{{1.`E (Image link)`}}"
}
```

**Setup Requirements**:
- Facebook Page (not personal profile)
- Page access token with publish permissions
- Business verification if required by audience size
- Image URL accessible from Facebook servers

**Advanced Options**:
- Scheduled publishing available
- Link preview customization
- Audience targeting options

---

### 10. LinkedIn Create Post

**Purpose**: Publishes professional content to LinkedIn

**Configuration Settings**:
```json
{
  "person": "YOUR_LINKEDIN_PERSON_ID",
  "text": "{{1.`D (LinkedIn Post Content)`}}",
  "media": [
    {
      "url": "{{1.`E (Image link)`}}",
      "altText": "Social media content image"
    }
  ]
}
```

**LinkedIn API Notes**:
- Person ID format: "urn:li:person:XXXXXXXX"
- Character limit: 3,000 for posts
- Image requirements: Min 552x552, max 7680x4320
- Alt text required for accessibility

**Permission Scopes Required**:
- `w_member_social` - Write posts
- `r_liteprofile` - Read profile info

---

### 11. Google Sheets Final Update

**Purpose**: Marks content as successfully published

**Configuration Settings**:
```json
{
  "spreadsheet": "YOUR_SPREADSHEET_ID",
  "sheetName": "Sheet1",
  "row": "{{1.`__ROW_NUMBER__`}}",
  "values": {
    "F": "Posted",
    "G": "{{formatDate(now; \"YYYY-MM-DD HH:mm:ss\")}}"
  }
}
```

**Status Tracking**:
- "Posted" indicates successful completion
- Timestamp shows publication time
- Prevents re-processing of completed items

## Global Configuration Best Practices

### Error Handling
```json
{
  "maxErrors": 3,
  "autoCommit": true,
  "autoCommitTriggerLast": true,
  "sequential": false
}
```

### Connection Management
- Use consistent connection names across modules
- Test connections individually before running full scenario
- Monitor API rate limits and quotas
- Implement retry logic for transient failures

### Security Considerations
- Store API keys securely in Make.com vault
- Use service accounts for Google services
- Rotate API keys regularly
- Monitor unusual API usage patterns

### Performance Optimization
- Disable sequential processing for parallel operations
- Use webhook triggers for real-time processing
- Implement conditional logic to skip unnecessary modules
- Cache frequently accessed data when possible

### Monitoring and Maintenance
- Set up execution notifications for failures
- Review logs weekly for performance issues
- Monitor API usage against quotas
- Update prompts based on content quality feedback