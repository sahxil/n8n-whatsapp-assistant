# WhatsApp Google Drive Assistant 🤖

An intelligent n8n workflow that transforms WhatsApp into a powerful Google Drive management interface, enabling file operations and AI-powered document summarization through simple text commands.

## Features ✨

- 📱 **WhatsApp Integration**: Command-driven interface via Twilio
- ☁️ **Google Drive Operations**: List, delete, move, and summarize files
- 🤖 **AI Summarization**: Powered by Google Gemini 1.5 Flash
- 🔒 **Security**: OAuth2 authentication and confirmation-based deletion
- 📊 **Audit Trail**: Complete operation logging in Google Sheets
- 🛡️ **Error Handling**: User-friendly WhatsApp error messages

## Commands 📱

```
LIST /FolderName - List all files in the specified folder
DELETE /path/to/file.pdf - Delete a file (requires confirmation)
MOVE /source/file.pdf /DestinationFolder - Move file to new location
SUMMARY /FolderName - AI summary of all documents in folder
CONFIRM 123456 - Confirm deletion with generated ID
```

## Architecture 🏗️

```
WhatsApp → Twilio Webhook → n8n Workflow → Google Drive API
                                ↓
            Google Gemini AI ← File Processing
                                ↓
            Response → Twilio → WhatsApp
```

## Prerequisites 📋

- n8n instance (Docker recommended)
- Twilio Account with WhatsApp Sandbox
- Google Cloud Project with Drive API enabled
- Google Gemini API key
- Google Sheets for audit logging

## Setup Instructions 🚀

### 1. Twilio WhatsApp Setup

- Create a [Twilio account](https://www.twilio.com/try-twilio)
- Navigate to Console → Develop → Messaging → Try it out → Send a WhatsApp message
- Follow the sandbox setup instructions
- Note your:
  - Account SID
  - Auth Token
  - Sandbox WhatsApp number

### 2. Google Cloud Setup

- Create a new project in [Google Cloud Console](https://console.cloud.google.com/)
- Enable the following APIs:
  - Google Drive API
  - Google Sheets API
- Create OAuth2 credentials:
  - Go to APIs & Credentials → Create Credentials → OAuth 2.0 Client IDs
  - Add your n8n domain to authorized origins
  - Download the credentials JSON

### 3. Google Gemini API

- Get API key from [Google AI Studio](https://aistudio.google.com/)
- Enable Gemini API in your Google Cloud project

### 4. Installation

```bash
# Clone this repository
git clone https://github.com/sahxil/n8n-whatsapp-assistant
cd n8n-whatsapp-assistant

# Copy and configure environment
cp .env.sample .env
# Edit .env with your credentials

# Start n8n with Docker Compose
docker-compose up -d

# Access n8n at http://localhost:5678
```

### 5. Workflow Import

- Access your n8n instance
- Go to Workflows → Import from File
- Upload the `workflow.json` file
- Configure credentials for:
  - Twilio API
  - Google Drive OAuth2
  - Google Sheets OAuth2
  - HTTP Header Auth (for Gemini API)

### 6. Webhook Configuration

- Get the webhook URL from the Webhook node
- In Twilio Console → WhatsApp Sandbox Settings
- Set the webhook URL for incoming messages

## Environment Variables 🔧

```env
# Twilio Configuration
TWILIO_ACCOUNT_SID=your_account_sid
TWILIO_AUTH_TOKEN=your_auth_token
TWILIO_WHATSAPP_NUMBER=+14155238886

# Google Configuration
GOOGLE_CLIENT_ID=your_oauth_client_id
GOOGLE_CLIENT_SECRET=your_oauth_client_secret

# Gemini AI Configuration
GEMINI_API_KEY=your_gemini_api_key

# n8n Configuration
N8N_HOST=0.0.0.0
N8N_PORT=5678
N8N_PROTOCOL=http
WEBHOOK_URL=https://your-domain.com
```

## Security Features 🛡️

- **OAuth2 Authentication**: Secure Google Drive access
- **Confirmation Required**: Delete operations require explicit confirmation
- **Audit Logging**: All operations logged with timestamps
- **Scoped Permissions**: Limited to user's own Google Drive
- **Input Validation**: Command parsing with error handling

## Error Handling 🚨

- **Invalid Commands**: Clear syntax help via WhatsApp
- **File Not Found**: User-friendly error messages
- **API Failures**: Graceful degradation with retry logic
- **Rate Limits**: Informative messages about service limits
- **Network Issues**: Timeout handling and user notification

## Workflow Architecture 🔄

- **📨 Input Processing**: Webhook → Command Parser → Router
- **📁 LIST Operations**: Folder Search → File Listing → Response
- **🗑️ DELETE Operations**: File Search → Confirmation → Deletion
- **📦 MOVE Operations**: Source/Destination Resolution → File Transfer
- **🤖 SUMMARY Operations**: File Download → AI Processing → Response
- **📊 AUDIT Logging**: Google Sheets Integration

## AI Integration 🤖

- **Multi-format Support**: PDF, DOCX, TXT, Images, Google Docs
- **Intelligent Prompting**: Context-aware prompts based on file type
- **Fallback Handling**: Error handling for API failures
- **Content Extraction**: Robust binary data processing

## Extensibility 🔧

The workflow is designed for easy extension:

- **New Commands**: Add cases to the main router switch
- **Additional AI Providers**: Swap Gemini for OpenAI/Claude
- **Enhanced Security**: Add user authentication layers
- **Multi-user Support**: Tenant isolation capabilities
- **Advanced Operations**: Batch processing, file sharing

## Known Limitations ⚠️

### Current Limitations

- **Single User**: Designed for single Google Drive account access
- **File Size Limits**: Large files may exceed processing timeouts
- **API Rate Limits**:
  - Twilio Sandbox: Limited daily message quota
  - Google Drive API: 1000 requests/100 seconds/user
  - Gemini API: Rate limits vary by tier
- **File Type Support**: AI summarization works best with text-based documents
- **Binary Data Processing**: Complex files may cause processing failures
- **No Concurrent Operations**: Sequential processing only
- **Memory Constraints**: Large file downloads may cause memory issues

### Security Considerations

- **Webhook Exposure**: Webhook endpoint should be secured in production
- **Token Management**: OAuth tokens require periodic refresh
- **Audit Trail**: Deletion confirmations stored indefinitely
- **Error Logging**: Sensitive information may appear in n8n logs

### User Experience Limitations

- **Command Syntax**: Strict syntax requirements (case-sensitive)
- **No Undo Functionality**: Deleted files cannot be recovered via workflow
- **Limited Feedback**: No progress indicators for long operations
- **Error Context**: Some error messages could provide more specific guidance
- **No Batch Operations**: One command per message

### Technical Constraints

- **Network Dependency**: Requires stable internet for all operations
- **Single Point of Failure**: n8n instance downtime affects entire system
- **Storage Requirements**: Temporary file processing requires disk space
- **Docker Dependency**: Containerized deployment preferred
- **Webhook Reliability**: Relies on Twilio webhook delivery

### Planned Improvements (Out of Scope)

- Multi-user support with authentication
- Batch operations for multiple files
- Natural language command parsing
- File preview capabilities
- Advanced search and filtering
- Integration with additional cloud storage providers

## Troubleshooting 🔧

### Common Issues

- **Twilio Rate Limits**: Free accounts have daily message limits
- **Binary Data Issues**: Ensure proper n8n binary data mode
- **OAuth Expiration**: Re-authenticate Google credentials periodically
- **Webhook Failures**: Verify public accessibility of n8n instance

### Debug Mode

Enable verbose logging in n8n for troubleshooting:

```bash
docker-compose -f docker-compose.debug.yml up
```

## License 📄

MIT License - see LICENSE file for details

## Contributing 🤝

This is an internship project, but suggestions are welcome via issues.

## Acknowledgments 🙏

- **n8n Community**: Workflow automation platform
- **Google AI**: Gemini API for document summarization
- **Twilio**: WhatsApp Business API integration

---

**Built with ❤️ for efficient document management**

_Internship Project - August 2025_
