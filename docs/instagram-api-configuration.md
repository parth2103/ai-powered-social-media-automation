# Instagram Business API Configuration Guide

## Overview

Setting up Instagram Business API is often the most complex part of this automation. This guide provides step-by-step instructions to navigate common pitfalls and successfully configure Instagram posting automation.

## Prerequisites

### Required Accounts
- **Facebook Developer Account**: Primary requirement for Instagram API access
- **Facebook Page**: Must be a business page, not personal profile
- **Instagram Business Account**: Must be converted from personal to business
- **Instagram-Facebook Connection**: Instagram account must be linked to Facebook Page

### Important Notes
- Personal Instagram accounts cannot use the Instagram Business API
- Instagram Creator accounts have limited API access
- The Instagram account must be connected to a Facebook Page you admin

## Step 1: Instagram Account Setup

### 1.1 Convert to Business Account
1. Open Instagram mobile app
2. Go to Settings → Account
3. Tap "Switch to Professional Account"
4. Select "Business"
5. Connect to your Facebook Page
6. Fill in business information (contact details, category, etc.)

### 1.2 Verify Business Account Status
1. Go to Instagram settings
2. Check that "Account Type" shows "Business"
3. Verify that your Facebook Page is connected
4. Note your Instagram Business Account ID (found in Facebook Page settings)

## Step 2: Facebook Developer App Creation

### 2.1 Create Facebook App
1. Visit [Facebook Developers](https://developers.facebook.com)
2. Click "My Apps" → "Create App"
3. Select "Business" app type
4. Fill in app details:
   ```
   App Name: "Your Social Media Automation"
   App Contact Email: your-email@domain.com
   App Purpose: "Automate Instagram content posting"
   ```

### 2.2 Add Instagram Basic Display Product
1. In your app dashboard, click "Add Product"
2. Find "Instagram Basic Display" and click "Set Up"
3. This creates the basic foundation for Instagram API access

### 2.3 Generate App ID and Secret
1. Go to Settings → Basic in your Facebook app
2. Note your App ID and App Secret
3. Add platform if prompted:
   - Platform: Website
   - Site URL: `https://www.make.com/`

## Step 3: Instagram API Configuration

### 3.1 Instagram Basic Display Setup
1. Go to Products → Instagram Basic Display → Basic Display
2. Click "Create New App"
3. Fill in Instagram App details:
   ```
   Display Name: "Social Media Automation"
   Namespace: "your-namespace" (lowercase, no spaces)
   ```

### 3.2 Configure OAuth Settings
1. In Instagram Basic Display settings, add redirect URIs:
   ```
   https://www.make.com/oauth/cb/instagram
   https://localhost/oauth/cb/instagram
   ```
2. Add deauthorize callback URL:
   ```
   https://www.make.com/oauth/deauth/instagram
   ```
3. Add data deletion request URL:
   ```
   https://www.make.com/oauth/delete/instagram
   ```

### 3.3 Add Instagram Test Users
1. Go to Roles → Roles in your Facebook app
2. Click "Add Instagram Testers"
3. Enter your Instagram username
4. Send invitation
5. Accept invitation in your Instagram app:
   - Go to Settings → Privacy → Apps and Websites
   - Find pending invitations and accept

## Step 4: Facebook Page Integration

### 4.1 Connect Instagram to Facebook Page
1. Go to your Facebook Page settings
2. Navigate to "Instagram" in the left sidebar
3. Click "Connect Account"
4. Log in to your Instagram Business account
5. Verify the connection is successful

### 4.2 Verify Page Permissions
Required permissions for your Facebook Page:
- `pages_manage_posts`: Allows posting content
- `pages_read_engagement`: Allows reading post metrics
- `instagram_basic`: Basic Instagram access
- `instagram_content_publish`: Required for posting

### 4.3 Get Page Access Token
1. Go to [Facebook Graph API Explorer](https://developers.facebook.com/tools/explorer/)
2. Select your app
3. Generate User Access Token with required permissions
4. Exchange for long-lived page access token:
   ```bash
   curl -X GET "https://graph.facebook.com/v18.0/me/accounts?access_token=YOUR_USER_TOKEN"
   ```

## Step 5: Make.com Integration

### 5.1 Instagram Connection Setup
1. In Make.com, go to your scenario
2. Click on Instagram module
3. Click "Add" next to Connection
4. Choose "Instagram for Business"
5. Click "Continue"

### 5.2 Authorization Flow
1. You'll be redirected to Facebook OAuth
2. Log in with your Facebook account
3. Grant permissions to your app
4. Select your Facebook Page
5. Authorize Instagram access
6. You'll be redirected back to Make.com

### 5.3 Account Selection
1. Select your Instagram Business Account from dropdown
2. Test the connection
3. Verify that your account details appear correctly

## Step 6: Testing and Validation

### 6.1 Test Media Upload
1. Create a simple test in Make.com:
   ```json
   {
     "image_url": "https://via.placeholder.com/1080x1080.png",
     "caption": "Test post from automation system #test"
   }
   ```
2. Run the Instagram Create Media Object module
3. Verify you receive a creation ID

### 6.2 Test Publishing
1. Use the creation ID from previous step
2. Run Instagram Publish Media Object module
3. Check your Instagram account for the posted content
4. Verify the post appears correctly

### 6.3 Validate Permissions
Test that your setup has all required permissions:
```bash
# Test basic access
curl -X GET "https://graph.facebook.com/v18.0/YOUR_INSTAGRAM_ACCOUNT_ID?fields=id,username&access_token=YOUR_TOKEN"

# Test media creation capability
curl -X POST "https://graph.facebook.com/v18.0/YOUR_INSTAGRAM_ACCOUNT_ID/media" \
  -F "image_url=https://via.placeholder.com/1080x1080.png" \
  -F "caption=Test post" \
  -F "access_token=YOUR_TOKEN"
```

## Common Issues and Solutions

### Issue 1: "URL blocked" Error
**Symptoms**: OAuth redirect fails with "URL blocked" message
**Cause**: Redirect URI not properly configured in Facebook app
**Solution**:
1. Go to Facebook app settings
2. Check "Valid OAuth Redirect URIs"
3. Ensure exact match: `https://www.make.com/oauth/cb/instagram`
4. No trailing slashes or extra parameters

### Issue 2: "App not approved for Instagram Basic Display"
**Symptoms**: Authorization fails during OAuth flow
**Cause**: Instagram Basic Display product not properly set up
**Solution**:
1. Go to App Review in Facebook Developer Console
2. Request Instagram Basic Display permissions
3. Provide app purpose and usage details
4. Wait for approval (usually 24-48 hours)

### Issue 3: "Instagram account not eligible"
**Symptoms**: Account doesn't appear in Make.com dropdown
**Cause**: Account is not a Business account or not connected to Facebook Page
**Solution**:
1. Verify Instagram account type in app settings
2. Ensure account is connected to Facebook Page
3. Check that you're admin of the connected Facebook Page
4. Wait 24 hours after conversion for API recognition

### Issue 4: "Media object creation failed"
**Symptoms**: Error when creating Instagram media
**Cause**: Image URL not accessible or wrong format
**Solution**:
1. Ensure image URL is publicly accessible
2. Verify image format (JPG, PNG)
3. Check image dimensions (minimum 320x320)
4. Test URL directly in browser

### Issue 5: "Publishing failed" 
**Symptoms**: Media object created but publishing fails
**Cause**: Various Instagram-specific restrictions
**Solution**:
1. Check if content violates Instagram community guidelines
2. Verify account hasn't exceeded posting limits
3. Ensure caption length is under 2,200 characters
4. Check for duplicate content restrictions

### Issue 6: "Token expired" Error
**Symptoms**: Authentication fails after initial setup
**Cause**: Access token has expired
**Solution**:
1. Regenerate long-lived access token
2. Update token in Make.com connection settings
3. Set up automatic token refresh if possible
4. Monitor token expiration dates

## Security and Compliance

### Data Privacy
- Instagram API requires adherence to Facebook Platform Policy
- Users must consent to data usage for business purposes
- Implement data retention and deletion policies
- Regular audit of API access and usage

### Rate Limiting
Instagram API has strict rate limits:
- **Content Publishing**: 25 posts per day per account
- **API Calls**: 200 calls per hour per user
- **Bulk Operations**: Limited to prevent spam

### Best Practices
1. **Content Quality**: Ensure all automated content meets Instagram's quality standards
2. **User Experience**: Don't over-automate; maintain authentic engagement
3. **Monitoring**: Regular review of posted content and engagement metrics
4. **Backup Plans**: Manual posting procedures for automation failures

## Advanced Configuration

### Webhook Integration
For real-time posting without polling:
1. Set up webhook endpoint in Facebook app
2. Subscribe to Instagram media events
3. Configure Make.com webhook trigger
4. Process events for immediate posting

### Multi-Account Management
For managing multiple Instagram accounts:
1. Create separate Facebook apps for each account
2. Use different Make.com scenarios
3. Implement centralized content management
4. Monitor cross-account posting schedules

### Content Scheduling
For advanced scheduling beyond basic automation:
1. Use Instagram's built-in scheduling features
2. Implement queue management in Google Sheets
3. Add time-based triggers in Make.com
4. Consider timezone handling for global audiences

## Performance Optimization

### Reduce API Calls
- Batch operations where possible
- Cache frequently accessed data
- Use efficient polling intervals
- Implement smart retry logic

### Content Optimization
- Pre-process images to optimal dimensions
- Validate content before API calls
- Use consistent hashtag strategies
- Monitor content performance metrics

### Error Recovery
- Implement comprehensive error logging
- Set up automated retry mechanisms
- Create manual intervention workflows
- Monitor success rates and failure patterns

This comprehensive guide should help you successfully configure Instagram Business API for your social media automation system. Remember that Instagram's API and policies change frequently, so always check the official Facebook Developer documentation for the latest requirements.