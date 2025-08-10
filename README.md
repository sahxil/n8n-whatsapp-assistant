# WhatsApp-Driven Google Drive Assistant (n8n Workflow)

## Overview

An intelligent n8n workflow that transforms WhatsApp into a powerful Google Drive management interface, enabling file operations and AI-powered document summarization through simple text commands.

## Features ✨

- **📱 WhatsApp Integration**: Command-driven interface via Twilio
- **☁️ Google Drive Operations**: List, delete, move, and summarize files
- **🤖 AI Summarization**: Powered by Google Gemini 1.5 Flash
- **🔒 Security**: OAuth2 authentication and confirmation-based deletion
- **📊 Audit Trail**: Complete operation logging in Google Sheets
- **🛡️ Error Handling**: User-friendly WhatsApp error messages

## Command Syntax

```
LIST /FolderName          - List all files in the specified folder
DELETE /path/to/file.pdf  - Delete a file (requires confirmation)
MOVE /source/file.pdf /DestinationFolder - Move file to new location
SUMMARY /FolderName       - AI summary of all documents in folder
CONFIRM 123456            - Confirm deletion with generated ID
```

## Architecture

```
WhatsApp → Twilio Webhook → n8n Workflow → Google Drive API
                                      ↓
                          Google Gemini AI ← File Processing
                                      ↓
                          Response → Twilio → WhatsApp
```

## Prerequisites

- n8n instance (Docker recommended)
- Twilio Account with WhatsApp Sandbox
- Google Cloud Project with Drive API enabled
- Google Gemini API key
- Google Sheets for audit logging

## Setup Instructions

### 1. Twilio WhatsApp Sandbox Setup

1. Create a [Twilio account](https://www.twilio.com/try-twilio)
2. Navigate to Console → Develop → Messaging → Try it out → Send a WhatsApp message
3. Follow the sandbox setup instructions
4. Note your:
   - Account SID
   - Auth Token
   - Sandbox WhatsApp number

### 2. Google Cloud Setup

1. Create a new project in [Google Cloud Console](https://console.cloud.google.com/)
2. Enable the following APIs:
   - Google Drive API
   - Google Sheets API
3. Create OAuth2 credentials:
   - Go to APIs & Credentials → Create Credentials → OAuth 2.0 Client IDs
   - Add your n8n domain to authorized origins
   - Download the credentials JSON

### 3. Google Gemini API Setup

1. Get API key from [Google AI Studio](https://aistudio.google.com/)
2. Enable Gemini API in your Google Cloud project

### 4. n8n Docker Deployment

```bash
# Clone this repository
git clone [your-repo-url]
cd whatsapp-drive-assistant

# Copy and configure environment
cp .env.sample .env
# Edit .env with your credentials

# Start n8n with Docker Compose
docker-compose up -d

# Access n8n at http://localhost:5678
```

### 5. Workflow Import

1. Access your n8n instance
2. Go to Workflows → Import from File
3. Upload the `workflow.json` file
4. Configure credentials for:
   - Twilio API
   - Google Drive OAuth2
   - Google Sheets OAuth2
   - HTTP Header Auth (for Gemini API)

### 6. Webhook Configuration

1. Get the webhook URL from the Webhook node
2. In Twilio Console → WhatsApp Sandbox Settings
3. Set the webhook URL for incoming messages

## Environment Variables (.env)

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

## Security Features 🔒

- **OAuth2 Authentication**: Secure Google Drive access
- **Confirmation Required**: Delete operations require explicit confirmation
- **Audit Logging**: All operations logged with timestamps
- **Scoped Permissions**: Limited to user's own Google Drive
- **Input Validation**: Command parsing with error handling

## Error Handling Strategy

- **Invalid Commands**: Clear syntax help via WhatsApp
- **File Not Found**: User-friendly error messages
- **API Failures**: Graceful degradation with retry logic
- **Rate Limits**: Informative messages about service limits
- **Network Issues**: Timeout handling and user notification

## Workflow Node Groups

1. **📨 Input Processing**: Webhook → Command Parser → Router
2. **📁 LIST Operations**: Folder Search → File Listing → Response
3. **🗑️ DELETE Operations**: File Search → Confirmation → Deletion
4. **📦 MOVE Operations**: Source/Destination Resolution → File Transfer
5. **🤖 SUMMARY Operations**: File Download → AI Processing → Response
6. **📊 AUDIT Logging**: Google Sheets Integration

## AI Summary Strategy

- **Multi-format Support**: PDF, DOCX, TXT, Images, Google Docs
- **Intelligent Prompting**: Context-aware prompts based on file type
- **Fallback Handling**: Multiple AI providers for reliability
- **Content Extraction**: Robust binary data processing

## Extensibility 🔧

The workflow is designed for easy extension:

- **New Commands**: Add cases to the main router switch
- **Additional AI Providers**: Swap Gemini for OpenAI/Claude
- **Enhanced Security**: Add user authentication layers
- **Multi-user Support**: Tenant isolation capabilities
- **Advanced Operations**: Batch processing, file sharing

## Demo Video

[🎥 Watch the complete demo](demo-video-link.txt)

## Troubleshooting

### Common Issues:

1. **Twilio Rate Limits**: Free accounts have daily message limits
2. **Binary Data Issues**: Ensure proper n8n binary data mode
3. **OAuth Expiration**: Re-authenticate Google credentials periodically
4. **Webhook Failures**: Verify public accessibility of n8n instance

### Debug Mode:

Enable verbose logging in n8n for troubleshooting:

```bash
docker-compose -f docker-compose.debug.yml up
```

## License

MIT License - see LICENSE file for details

## Contributing

This is an internship project, but suggestions are welcome via issues.

## Acknowledgments

- **n8n Community**: Workflow automation platform
- **Google AI**: Gemini API for document summarization
- **Twilio**: WhatsApp Business API integration
- Built with ❤️ for efficient document management

---

_Internship Project - August 2025_
