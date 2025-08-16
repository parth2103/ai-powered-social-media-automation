# Make.com Blueprint Import Guide

## Overview

This guide walks you through importing the pre-built Make.com blueprint for the AI-powered social media automation system. The blueprint contains all necessary modules, connections, and logic flows to automate content generation and publishing.

## Prerequisites

- Active Make.com account (free tier sufficient for testing)
- Downloaded blueprint file: `automation/sheet-to-social-blueprint.json`
- Basic familiarity with Make.com interface

## Step 1: Accessing Make.com

### 1.1 Login to Make.com
1. Visit [make.com](https://make.com)
2. Click "Log in" or "Sign up" if you don't have an account
3. Complete the registration process if new user

### 1.2 Navigate to Scenarios
1. From the main dashboard, click "Scenarios" in the left sidebar
2. You'll see your scenarios list (empty if new account)

## Step 2: Blueprint Import Process

### 2.1 Create New Scenario
1. Click the "Create a new scenario" button
2. You'll be taken to the scenario editor

### 2.2 Import Blueprint
1. Look for the "Import Blueprint" option in the toolbar
2. Click "Import Blueprint"
3. A file upload dialog will appear

### 2.3 Upload Blueprint File
1. Click "Choose File" or drag and drop
2. Select `automation/sheet-to-social-blueprint.json` from your downloaded repository
3. Click "Upload" or "Import"
4. Wait for the import process to complete (usually 10-30 seconds)

### 2.4 Initial Import Verification
After import, you should see:
- 11 modules arranged in a workflow
- Router module with two distinct paths
- Red warning indicators on modules (expected - connections need configuration)

## Step 3: Understanding the Imported Workflow

### 3.1 Workflow Structure Overview
```
Google Sheets (Trigger) → Router → Two Paths:
                                  ├─ Content Generation Path
                                  └─ Social Publishing Path
```

### 3.2 Content Generation Path
```
Google Sheets → Router → OpenAI GPT-4 → JSON Parse → DALL-E 3 → Update Sheets
```

### 3.3 Social Publishing Path
```
Google Sheets → Router → Instagram Create → Instagram Publish
                      → Facebook Post
                      → LinkedIn Post
                      → Update Sheets
```

### 3.4 Module List
1. **Google Sheets - Search Rows** (Trigger)
2. **Router** (Logic controller)
3. **OpenAI - Create Chat Completion** (Content generation)
4. **JSON - Parse JSON** (Data processing)
5. **OpenAI - Create Image** (DALL-E image generation)
6. **Google Sheets - Update Row** (Content path completion)
7. **Instagram - Create Media Object** (Instagram preparation)
8. **Instagram - Publish Media Object** (Instagram posting)
9. **Facebook - Create Page Post** (Facebook posting)
10. **LinkedIn - Create Post** (LinkedIn posting)
11. **Google Sheets - Update Row** (Publishing path completion)

## Step 4: Module Configuration

### 4.1 Configure Google Sheets Trigger Module
1. Click on the Google Sheets module (Module #1)
2. Click "Add" next to Connection
3. Choose your Google authentication method:
   - **Recommended**: Service Account (for production)
   - **Alternative**: OAuth (for testing)
4. Configure module settings:
   ```
   Spreadsheet: [Select your automation spreadsheet]
   Include headers: Yes
   Sheet name: Sheet1
   Filter: Column F = "Generate Content" OR "Create Post"
   ```

### 4.2 Configure Router Module
The router module requires no configuration - it automatically routes based on the Control Column value from the Google Sheets trigger.

### 4.3 Configure OpenAI Modules
1. Click on the OpenAI GPT-4 module (Module #3)
2. Click "Add" next to Connection
3. Enter your OpenAI API key
4. Verify the prompt content matches the system prompt from `prompts/` directory
5. Repeat for DALL-E module (Module #5)

### 4.4 Configure Social Media Modules
For each platform (Instagram, Facebook, LinkedIn):
1. Click on the respective module
2. Click "Add" next to Connection
3. Complete the OAuth authorization flow
4. Select the appropriate account/page for posting

### 4.5 Configure Google Sheets Update Modules
1. Use the same connection as the trigger module
2. Verify row mapping uses `{{1.__ROW_NUMBER__}}`
3. Check that content mapping correctly references previous modules

## Step 5: Connection Setup Details

### 5.1 Google Sheets Connection
**Service Account Method** (Recommended):
1. Upload your service account JSON file
2. Test connection
3. Verify spreadsheet access

**OAuth Method** (Simpler setup):
1. Authorize with your Google account
2. Grant necessary permissions
3. Test connection

### 5.2 OpenAI Connection
1. Enter API key: `sk-...` (starts with sk-)
2. Click "Verify connection"
3. Ensure you have sufficient API credits

### 5.3 Instagram Connection
1. Follow OAuth flow to Facebook
2. Grant Instagram permissions
3. Select your Instagram Business account
4. Test with a sample media creation

### 5.4 Facebook Connection
1. Authorize with Facebook account
2. Select your Facebook Page
3. Grant posting permissions
4. Test connection

### 5.5 LinkedIn Connection
1. Complete LinkedIn OAuth
2. Select profile or company page
3. Grant sharing permissions
4. Verify connection

## Step 6: Blueprint Customization

### 6.1 Update Spreadsheet References
Replace placeholder spreadsheet ID with your actual ID:
1. Find modules with spreadsheet references
2. Update to your spreadsheet ID
3. Verify sheet name matches your setup

### 6.2 Customize Content Prompts
1. Click on OpenAI GPT-4 module
2. Review the system prompt
3. Modify if needed for your brand voice
4. Update DALL-E prompt for your visual style

### 6.3 Adjust Platform Settings
- **Instagram**: Verify account ID format
- **Facebook**: Ensure page ID is correct
- **LinkedIn**: Confirm person/company URN format

## Step 7: Testing the Imported Blueprint

### 7.1 Pre-Test Checklist
- [ ] All modules show green connection indicators
- [ ] Spreadsheet accessible and properly formatted
- [ ] OpenAI API key valid with sufficient credits
- [ ] Social media accounts properly connected
- [ ] Test data ready in spreadsheet

### 7.2 Test Content Generation
1. Add test idea to your spreadsheet:
   ```
   Column A: "Testing the imported automation workflow"
   Column F: "Generate Content"
   ```
2. Click "Run once" in Make.com
3. Monitor execution progress
4. Verify content appears in spreadsheet

### 7.3 Test Social Publishing
1. Change Control Column to "Create Post"
2. Run scenario again
3. Check posts appear on all platforms
4. Verify status updates in spreadsheet

### 7.4 Monitor Execution Logs
1. Click "Execution history" tab
2. Review each module's execution
3. Check for any errors or warnings
4. Verify data flow between modules

## Step 8: Troubleshooting Import Issues

### 8.1 Common Import Problems

#### "Blueprint file invalid"
- **Cause**: Corrupted or modified JSON file
- **Solution**: Re-download the blueprint from repository

#### "Module not recognized"
- **Cause**: Make.com app not available in your region
- **Solution**: Check app availability or use alternative modules

#### "Connection failed"
- **Cause**: Missing API credentials or permissions
- **Solution**: Verify all prerequisites are met

### 8.2 Module Configuration Issues

#### Red error indicators persist
- **Cause**: Incomplete connection setup
- **Solution**: Click each module and complete configuration

#### "Spreadsheet not found"
- **Cause**: Incorrect spreadsheet ID or permissions
- **Solution**: Verify ID and sharing settings

#### "API authentication failed"
- **Cause**: Invalid or expired credentials
- **Solution**: Regenerate API keys and reconnect

### 8.3 Execution Failures

#### "Trigger not firing"
- **Cause**: Filter conditions not met
- **Solution**: Verify Control Column values match filter

#### "Content generation timeout"
- **Cause**: OpenAI API slowness or rate limits
- **Solution**: Increase timeout or reduce prompt complexity

#### "Social posting failed"
- **Cause**: Platform-specific restrictions
- **Solution**: Check content compliance and account status

## Step 9: Production Deployment

### 9.1 Scenario Settings
Configure production settings:
```
Scenario Name: "AI Social Media Automation - Production"
Schedule: Every 15 minutes
Max errors: 3
Auto-commit: Yes
Data storage: 30 days
```

### 9.2 Error Handling
Set up error notifications:
1. Go to scenario settings
2. Enable "Email notifications"
3. Add your email for error alerts
4. Configure retry logic

### 9.3 Performance Monitoring
1. Review execution history daily
2. Monitor API usage and costs
3. Track success rates per platform
4. Optimize based on performance data

## Step 10: Blueprint Updates and Maintenance

### 10.1 Version Control
- Keep backups of working blueprint configurations
- Document any customizations made
- Export updated blueprints after modifications
- Track changes with version numbers

### 10.2 Regular Maintenance
- **Weekly**: Review execution logs and success rates
- **Monthly**: Update API credentials if needed
- **Quarterly**: Review and optimize prompt performance
- **Annually**: Audit all connected services and permissions

### 10.3 Blueprint Evolution
As you improve the automation:
1. Export modified blueprint
2. Update repository with new version
3. Document changes in release notes
4. Test thoroughly before production deployment

## Additional Resources

### Make.com Documentation
- [Make.com Academy](https://academy.make.com)
- [Module documentation](https://docs.make.com)
- [Community forum](https://community.make.com)

### Platform-Specific Guides
- Refer to `docs/instagram-api-configuration.md` for Instagram setup
- Check official API documentation for latest requirements
- Join developer communities for platform-specific help

This comprehensive import guide ensures successful deployment of the AI-powered social media automation blueprint in your Make.com environment.