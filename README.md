# AI Email Management System

## Overview
This project implements an automated email management system using CrewAI and LangGraph, featuring intelligent AI agents that handle email filtering, summarization, and response generation. The system provides continuous email monitoring and automated responses while maintaining natural communication styles.

## Features
- Automated email monitoring and processing
- Three specialized AI agents for different email tasks
- Gmail API integration
- Natural language response generation
- Customizable email filtering rules
- Real-time email summarization
- Continuous monitoring system

## Architecture
```
Email Management System
├── Agents
│   ├── Senior Email Analyst
│   ├── Email Action Specialist
│   └── Email Response Writer
├── Services
│   ├── Gmail API Integration
│   ├── Email Monitoring Service
│   └── Response Generation Engine
└── Utils
    ├── Email Parser
    └── Style Analyzer
```

## Prerequisites
- Python 3.8+
- Gmail Account
- Google Cloud Console Account
- OpenAI API Key
- CrewAI and LangGraph installations

## Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/email-ai-agents.git
cd email-ai-agents

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Linux/Mac
# or
.\venv\Scripts\activate  # Windows

# Install dependencies
pip install -r requirements.txt
```

## Configuration

### 1. Environment Setup
Create a `.env` file:
```env
OPENAI_API_KEY=your_openai_api_key
GMAIL_API_KEY=your_gmail_api_key
```

### 2. Gmail API Setup
1. Go to Google Cloud Console
2. Enable Gmail API
3. Create credentials
4. Download `credentials.json`
5. Place in project root

### 3. Agent Configuration
```python
# config/agent_config.yaml
senior_analyst:
  model: "gpt-4"
  temperature: 0.3
  filter_rules:
    - "newsletter"
    - "promotional"
    
action_specialist:
  model: "gpt-4"
  temperature: 0.4
  summary_length: 150
  
response_writer:
  model: "gpt-4"
  temperature: 0.7
  style_matching: true
```

## Implementation

### 1. Initialize AI Agents
```python
from crew_ai import Agent
from config import Config

class EmailManagementSystem:
    def __init__(self):
        self.analyst = Agent(
            role="Senior Email Analyst",
            goal="Filter and prioritize emails",
            backstory="Experienced in identifying important communications",
            tools=["email_filter", "priority_analyzer"]
        )
        
        self.specialist = Agent(
            role="Email Action Specialist",
            goal="Summarize email content",
            backstory="Expert in extracting key information",
            tools=["content_summarizer", "style_analyzer"]
        )
        
        self.writer = Agent(
            role="Email Response Writer",
            goal="Generate appropriate responses",
            backstory="Skilled in professional communication",
            tools=["response_generator", "style_matcher"]
        )
```

### 2. Email Monitoring Setup
```python
class EmailMonitor:
    def __init__(self, check_interval=180):
        self.interval = check_interval
        self.gmail_service = self.initialize_gmail_service()
        
    def start_monitoring(self):
        while True:
            self.check_new_emails()
            time.sleep(self.interval)
            
    def check_new_emails(self):
        messages = self.gmail_service.users().messages().list(
            userId='me',
            q='is:unread'
        ).execute()
        
        if 'messages' in messages:
            for message in messages['messages']:
                self.process_email(message['id'])
```

### 3. Email Processing Pipeline
```python
class EmailProcessor:
    def process_email(self, email_id):
        # Get email content
        email = self.gmail_service.users().messages().get(
            userId='me',
            id=email_id
        ).execute()
        
        # Filter email
        if self.analyst.should_process(email):
            # Generate summary
            summary = self.specialist.summarize(email)
            
            # Generate response
            response = self.writer.generate_response(
                email_content=email,
                summary=summary
            )
            
            # Send response
            self.send_response(email_id, response)
```

### 4. Running the System
```python
def main():
    # Initialize system
    email_system = EmailManagementSystem()
    
    # Start monitoring
    monitor = EmailMonitor(check_interval=180)
    monitor.start_monitoring()

if __name__ == "__main__":
    main()
```

## Usage Example

```python
from email_management import EmailManagementSystem

# Initialize the system
system = EmailManagementSystem()

# Configure monitoring preferences
system.configure(
    check_interval=180,
    priority_threshold=0.7,
    response_delay=60
)

# Start the system
system.start()
```

## Customization

### 1. Filter Rules
```yaml
# config/filter_rules.yaml
ignore_patterns:
  - "unsubscribe"
  - "newsletter"
  - "promotion"
  - "marketing"

priority_keywords:
  - "urgent"
  - "important"
  - "deadline"
```

### 2. Response Templates
```yaml
# config/response_templates.yaml
formal:
  greeting: "Dear {name},"
  closing: "Best regards,"

casual:
  greeting: "Hi {name},"
  closing: "Thanks,"
```

## Troubleshooting

### Common Issues
1. Gmail API Authentication
```python
# Test Gmail API connection
def test_gmail_connection():
    try:
        service = build('gmail', 'v1', credentials=creds)
        results = service.users().labels().list(userId='me').execute()
        print("Connection successful")
    except Exception as e:
        print(f"Error: {e}")
```

2. Agent Communication
```python
# Test agent communication
def test_agents():
    test_email = {
        "subject": "Test Email",
        "body": "This is a test email"
    }
    
    try:
        result = system.process_single_email(test_email)
        print(f"Processing successful: {result}")
    except Exception as e:
        print(f"Error: {e}")
```

## Contributing
1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request



## Acknowledgments
- CrewAI team
- LangGraph developers
- OpenAI for GPT models
- Google for Gmail API
