# Troubleshooting Guide

## Quick Reference

### Emergency Checklist
If your automation suddenly stops working:
1. Check Make.com scenario status (paused/active)
2. Verify Google Sheets accessibility 
3. Test OpenAI API key validity
4. Check social media platform status pages
5. Review recent execution logs for error patterns

## Google Sheets Issues

### Issue: "Spreadsheet not found" Error
**Symptoms:** Make.com can't access the Google Spreadsheet
**Common Causes:**
- Incorrect Spreadsheet ID in Make.com configuration
- Service account lacks permission to access spreadsheet
- Spreadsheet moved to Trash or deleted
- Changed sharing permissions

**Solutions:**
1. **Verify Spreadsheet ID:**
   ```
   URL: https://docs.google.com/spreadsheets/d/[SPREADSHEET_ID]/edit
   Compare ID in Make.com with actual URL
   ```

2. **Check Service Account Permissions:**
   - Go to spreadsheet Share settings
   - Verify service account email is listed
   - Ensure permission is "Editor" not "Viewer"
   - Re-add service account if missing

3. **Test Connection:**
   ```bash
   # Test Google Sheets API access
   curl -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
   "https://sheets.googleapis.com/v4/spreadsheets/YOUR_SPREADSHEET_ID"
   ```

### Issue: "Permission denied" Error
**Symptoms:** API calls fail with 403 forbidden error
**Solutions:**
1. Regenerate service account credentials
2. Download new JSON key file
3. Update Make.com connection with new credentials
4. Verify Google Sheets API is enabled in Google Cloud Console

### Issue: Data Not Updating in Spreadsheet
**Symptoms:** Make.com runs successfully but sheet data unchanged
**Solutions:**
1. Check row number mapping in Update Row module
2. Verify column letters match your sheet structure
3. Test with manual data entry in Make.com
4. Clear cache and refresh spreadsheet

## OpenAI API Issues

### Issue: "Rate limit exceeded" Error
**Symptoms:** Content generation fails with rate limit message
**Causes:**
- Exceeded tokens per minute limit
- Exceeded requests per minute limit
- Insufficient API credits

**Solutions:**
1. **Check Current Usage:**
   ```bash
   curl https://api.openai.com/v1/usage \
     -H "Authorization: Bearer YOUR_API_KEY"
   ```

2. **Implement Rate Limiting:**
   - Add delays between requests in Make.com
   - Reduce scenario execution frequency
   - Process fewer ideas per execution

3. **Optimize Token Usage:**
   - Shorten system prompts
   - Reduce max_tokens parameter
   - Use more efficient models (GPT-3.5 vs GPT-4)

### Issue: "Invalid API key" Error
**Symptoms:** Authentication fails with 401 unauthorized
**Solutions:**
1. Verify API key format (starts with `sk-`)
2. Check key hasn't expired or been revoked
3. Regenerate API key in OpenAI dashboard
4. Update key in Make.com connection settings

### Issue: Poor Content Quality
**Symptoms:** Generated content is generic, off-brand, or low quality
**Solutions:**
1. **Refine System Prompt:**
   - Add more specific brand voice guidelines
   - Include examples of desired output
   - Specify content structure requirements

2. **Adjust Parameters:**
   - Lower temperature (0.5-0.7) for more consistent output
   - Increase max_tokens if content is being cut off
   - Experiment with different models

3. **Improve Input Quality:**
   - Provide more detailed content ideas
   - Include context and target audience
   - Add specific angles or perspectives

### Issue: JSON Parsing Failures
**Symptoms:** Content generation succeeds but JSON parsing fails
**Solutions:**
1. **Verify Response Format:**
   - Check OpenAI module has "JSON Object" response format enabled
   - Review system prompt includes JSON format instruction
   - Test with simpler prompts to isolate issue

2. **Handle Malformed JSON:**
   - Add error handling in JSON Parse module
   - Implement fallback content generation
   - Use regex to clean JSON before parsing

## Social Media API Issues

### Instagram Issues

#### Issue: "URL blocked" Error
**Symptoms:** OAuth redirect fails during Instagram setup
**Solutions:**
1. Check Facebook app OAuth settings
2. Verify redirect URL exactly matches: `https://www.make.com/oauth/cb/instagram`
3. Remove any trailing slashes or extra parameters
4. Wait 15 minutes after changes for propagation

#### Issue: "Client OAuth Login" Error
**Symptoms:** Cannot complete Instagram authorization
**Solutions:**
1. Enable "Client OAuth Login" in Facebook app settings
2. Enable "Web OAuth Login" 
3. Add valid redirect URIs in OAuth settings
4. Ensure app is not in development mode restrictions

#### Issue: "Media object creation failed"
**Symptoms:** Error when creating Instagram media
**Common Causes:**
- Image URL not publicly accessible
- Image format not supported
- Image dimensions too small
- Content violates Instagram guidelines

**Solutions:**
1. **Test Image URL:**
   ```bash
   curl -I "YOUR_IMAGE_URL"
   # Should return 200 OK with proper content-type
   ```

2. **Verify Image Requirements:**
   - Format: JPG, PNG, or GIF
   - Minimum dimensions: 320x320 pixels
   - Maximum file size: 30MB
   - Publicly accessible URL

3. **Check Content Guidelines:**
   - Avoid copyrighted material
   - Ensure appropriate content rating
   - Follow Instagram community standards

#### Issue: "Publishing failed"
**Symptoms:** Media object created but publishing fails
**Solutions:**
1. Check account hasn't exceeded daily posting limits (25 posts/day)
2. Verify caption length under 2,200 characters
3. Ensure no duplicate content restrictions
4. Check account isn't temporarily restricted

### Facebook Issues

#### Issue: "Page not found" Error
**Symptoms:** Cannot post to Facebook Page
**Solutions:**
1. Verify Page ID is correct (numeric format)
2. Check you have admin access to the Page
3. Ensure Page is published (not unpublished)
4. Verify Page access token has correct permissions

#### Issue: "Insufficient permissions" Error
**Symptoms:** Authentication succeeds but posting fails
**Solutions:**
1. Request `pages_manage_posts` permission
2. Regenerate Page access token
3. Check Page settings allow API posting
4. Verify app has required Facebook app review approvals

### LinkedIn Issues

#### Issue: "Token expired" Error
**Symptoms:** LinkedIn posting fails with authentication error
**Solutions:**
1. LinkedIn access tokens expire every 60 days
2. Regenerate access token in LinkedIn Developer Console
3. Update Make.com connection with new token
4. Set up token refresh reminders

#### Issue: "Content rejected" Error
**Symptoms:** Post creation fails due to content restrictions
**Solutions:**
1. Check content length limits (3,000 characters)
2. Ensure appropriate business content
3. Avoid promotional language that violates LinkedIn policies
4. Include proper image alt text for accessibility

## Make.com Workflow Issues

### Issue: Scenario Not Triggering
**Symptoms:** Automation doesn't start despite meeting trigger conditions
**Solutions:**
1. **Check Scenario Status:**
   - Verify scenario is "Active" not "Paused"
   - Review scheduling settings
   - Check execution quota hasn't been exceeded

2. **Verify Trigger Conditions:**
   - Test Google Sheets filter conditions manually
   - Ensure Control Column values exactly match filter
   - Check for hidden characters or spaces

3. **Review Execution History:**
   - Look for failed executions with error details
   - Check if trigger module is receiving data
   - Verify data format matches expected structure

### Issue: Module Connection Failures
**Symptoms:** Red error indicators on modules
**Solutions:**
1. **Test Individual Connections:**
   - Click each module and test connection
   - Regenerate API keys if needed
   - Update OAuth tokens for social platforms

2. **Check Data Mapping:**
   - Verify data fields are correctly mapped between modules
   - Test with sample data to isolate mapping issues
   - Check for null or empty values breaking workflow

### Issue: Execution Timeouts
**Symptoms:** Scenario fails with timeout errors
**Solutions:**
1. **Optimize Module Performance:**
   - Reduce OpenAI prompt length
   - Lower image generation quality
   - Process fewer items per execution

2. **Increase Timeouts:**
   - Adjust module timeout settings
   - Split complex operations into multiple scenarios
   - Use webhook triggers for better performance

## Performance Issues

### Issue: Slow Execution Times
**Symptoms:** Scenario takes longer than expected to complete
**Causes:**
- OpenAI API response delays
- Large image file processing
- Network latency to social media APIs

**Solutions:**
1. **Optimize API Calls:**
   - Use smaller image sizes for faster processing
   - Reduce OpenAI token counts
   - Implement parallel processing where possible

2. **Monitor Execution:**
   - Track execution times by module
   - Identify bottleneck modules
   - Consider alternative approaches for slow operations

### Issue: High API Costs
**Symptoms:** Unexpected high bills from OpenAI or other services
**Solutions:**
1. **Monitor Usage:**
   - Set up billing alerts in OpenAI dashboard
   - Track token usage per execution
   - Review cost per generated post

2. **Optimize Costs:**
   - Use GPT-3.5 instead of GPT-4 for simpler content
   - Reduce DALL-E image generation frequency
   - Batch process multiple ideas when possible

## Error Recovery Strategies

### Automated Recovery
1. **Retry Logic:**
   - Configure maximum retry attempts (recommended: 3)
   - Use exponential backoff for rate-limited APIs
   - Set appropriate retry delays

2. **Fallback Mechanisms:**
   - Alternative content generation prompts
   - Backup image sources
   - Manual intervention workflows

### Manual Recovery
1. **Error Monitoring:**
   - Set up email notifications for failures
   - Review execution logs daily
   - Track error patterns and frequencies

2. **Recovery Procedures:**
   - Document standard recovery steps
   - Create manual backup workflows
   - Maintain emergency contact information for API support

## Monitoring and Prevention

### Proactive Monitoring
1. **Daily Checks:**
   - Review execution success rates
   - Monitor API usage and quotas
   - Check social media posting results

2. **Weekly Analysis:**
   - Analyze error patterns and trends
   - Review content quality and engagement
   - Update prompts based on performance

3. **Monthly Maintenance:**
   - Rotate API keys for security
   - Update OAuth tokens before expiration
   - Review and optimize workflow performance

### Prevention Best Practices
1. **API Management:**
   - Monitor rate limits proactively
   - Set usage alerts before limits
   - Maintain backup API keys

2. **Content Quality:**
   - Regular prompt optimization
   - A/B testing of content variations
   - Feedback incorporation from engagement metrics

3. **System Reliability:**
   - Regular backup of configurations
   - Documentation of all customizations
   - Version control for prompt changes

## Getting Additional Help

### Internal Resources
- Review `docs/setup-guide.md` for configuration details
- Check `examples/workflow-execution-logs.md` for normal execution patterns
- Consult `automation/module-configuration.md` for specific module settings

### External Support
- **Make.com**: Community forum and support documentation
- **OpenAI**: API documentation and developer forum
- **Social Media Platforms**: Official developer documentation and support
- **GitHub Issues**: Report bugs or request features in this repository

### Community Resources
- Join automation and no-code communities
- Participate in Make.com user groups
- Follow social media API developer blogs
- Subscribe to relevant newsletters for API updates

Remember: Many issues are temporary and resolve themselves within 24 hours. If you encounter persistent problems, document the error details and steps attempted before seeking support.