# 🏡 Context-Aware Real Estate Agent

**AI-powered, rule-inferring messaging agent for real estate lead nurturing**

[![.NET Version](https://img.shields.io/badge/.NET-8.0-512BD4?logo=dotnet)](https://dotnet.microsoft.com/download)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Anthropic API](https://img.shields.io/badge/Anthropic-Claude-7C3AED)](https://www.anthropic.com/)

---

## 📖 Overview

The **Context-Aware Real Estate Agent** is a C# console application that uses Claude (Anthropic's LLM) to automate real estate lead communication. Unlike traditional rule-based bots, this agent **infers all rules from the data** — no hardcoded if/else logic, no rigid decision trees.

### How It Works

```
┌─────────────────────────────────────────────────────────────────┐
│  JSONL Input → Claude API → Structured Output → Colored Panel  │
│                                                                 │
│  📝 Input Record   🤖 Claude Infers   📋 Decision & Action    │
│  ───────────────   ────────────────   ────────────────────    │
│  • Persona         • Channel selection • Next message        │
│  • Lifecycle       • Timing/Cadence   • Reasoning           │
│  • Consent         • Message content   • Compliance check   │
│  • Channel pref    • Personalization   • Semantic score     │
│  • Property name   • CTA generation   • Follow-up schedule  │
└─────────────────────────────────────────────────────────────────┘
```

---

## ✨ Features

| Feature | Description |
|---------|-------------|
| **🧠 Context-Aware** | Claude infers the best messaging strategy from each record's context |
| **🔌 No Hardcoded Rules** | Zero if/else logic — the agent learns from data, not code |
| **📊 Structured Output** | Returns reasoning, action plan, compliance checks, and a semantic score |
| **🎨 Color-Coded Terminal** | Beautiful panels using Spectre.Console for readability |
| **📁 JSONL Support** | Process batches of leads from a JSONL file |
| **🔒 Compliance Checks** | Built-in consent verification (email/SMS/voice opt-in) |
| **⏰ Cadence Planning** | Intelligent follow-up scheduling based on lifecycle stage |

---

## 🏗️ Project Structure

```
RealEstateAgent/
├── RealEstateAgent.csproj          # .NET 8 SDK + Spectre.Console
├── Models.cs                       # Input/Output record types
├── MessagingAgent.cs               # Claude API call + response parsing
├── Program.cs                      # Entry point + terminal rendering
├── sample.jsonl                    # Sample test cases
└── README.md                       # This file
```

### Core Components

| File | Responsibility |
|------|----------------|
| **Models.cs** | Defines `InputRecord`, `Consent`, `Profile`, and `ClaudeResponse` types |
| **MessagingAgent.cs** | Handles API communication with Claude, builds system prompts, parses JSON responses |
| **Program.cs** | Orchestrates the pipeline: load → process → render |
| **sample.jsonl** | Example records demonstrating the agent's capabilities |

---

## 🚀 Getting Started

### Prerequisites

- [.NET 8 SDK](https://dotnet.microsoft.com/download) or later
- An [Anthropic API key](https://www.anthropic.com/api)

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/realestateagent.git
cd RealEstateAgent

# Restore packages
dotnet restore
```

### Environment Setup

```bash
# macOS / Linux
export ANTHROPIC_API_KEY=sk-ant-...

# Windows Command Prompt
set ANTHROPIC_API_KEY=sk-ant-...

# Windows PowerShell
$env:ANTHROPIC_API_KEY="sk-ant-..."
```

> **💡 Tip:** For permanent setup, add the environment variable to your shell profile (`.bashrc`, `.zshrc`, or Windows System Variables).

### Run the Application

```bash
# Run with default sample data
dotnet run

# Run against your own JSONL file
dotnet run -- path/to/your/records.jsonl
```

---

## 📁 Input Data Format

Each line in your JSONL file must be a valid JSON object. Here's the complete schema:

```json
{
  "task_id": "prospect_welcome_001",
  "persona": "prospect",
  "lifecycle_stage": "new",
  "consent": {
    "email_opt_in": true,
    "sms_opt_in": false,
    "voice_opt_in": false
  },
  "channel_preferences": ["email"],
  "input": {
    "property_name": "1111 Ocean Drive",
    "timezone": "America/New_York",
    "language": "en",
    "profile": {
      "first_name": "Sarah"
    }
  }
}
```

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `task_id` | string | Unique identifier for this task |
| `persona` | string | Role (e.g., "prospect", "buyer", "seller", "agent") |
| `lifecycle_stage` | string | Current stage ("new", "engaged", "nurturing", "ready", "closed") |
| `consent.email_opt_in` | boolean | Whether the contact has opted in to email |
| `consent.sms_opt_in` | boolean | Whether the contact has opted in to SMS |
| `consent.voice_opt_in` | boolean | Whether the contact has opted in to voice |
| `channel_preferences` | string[] | Preferred channels (e.g., ["email", "sms"]) |
| `input.property_name` | string | Property address or name |
| `input.timezone` | string | IANA timezone (e.g., "America/New_York") |
| `input.language` | string | Language code (e.g., "en", "es") |
| `input.profile.first_name` | string | Contact's first name |

### Optional Fields

| Field | Type | Description |
|-------|------|-------------|
| `expected` | object | Optional expected output for testing/validation |

---

## 📤 Output Structure

Claude returns a structured JSON object with the following format:

```json
{
  "reasoning": "Sarah is a new prospect at 1111 Ocean Drive. Based on her consent and preferences, email is the appropriate channel. A welcome message with property details and a clear call-to-action is recommended.",
  "output": {
    "next_message": {
      "channel": "email",
      "send_at": "2026-06-24T10:00:00.000Z",
      "subject": "Welcome to 1111 Ocean Drive, Sarah!",
      "body": "Hi Sarah,\n\nWelcome to 1111 Ocean Drive! We're excited to help you explore this beautiful property...",
      "cta": "Schedule a viewing"
    },
    "next_action": {
      "action": "send_welcome_email",
      "cadence_days": 3,
      "follow_up_trigger": "viewing_scheduled"
    }
  },
  "assertions_passed": [
    "email_opt_in_confirmed",
    "no_sms_without_opt_in"
  ],
  "semantic_match_score": 0.92
}
```

### Output Fields

| Field | Description |
|-------|-------------|
| `reasoning` | Human-readable explanation of why each decision was made |
| `output.next_message.channel` | Channel to use ("email", "sms", "voice", "push") |
| `output.next_message.send_at` | When to send the message (ISO 8601) |
| `output.next_message.subject` | Message subject/headline |
| `output.next_message.body` | Full message content |
| `output.next_message.cta` | Call-to-action text |
| `output.next_action.action` | Action to take ("send_welcome_email", "schedule_viewing", etc.) |
| `output.next_action.cadence_days` | Days until next follow-up |
| `output.next_action.follow_up_trigger` | Condition that triggers next action |
| `assertions_passed` | List of compliance checks that passed |
| `semantic_match_score` | 0-1 score comparing output to expected (if provided) |

---

## 🎨 Terminal Output

The app renders color-coded panels for each task:

```
┌─────────────────────────────────────────────────────────────────┐
│  📨 Task: prospect_welcome_001                    Score: 0.92 │
│  ────────────────────────────────────────────────────────────  │
│  Persona: prospect   Lifecycle: new   Channel: email        │
│                                                              │
│  💬 Subject: Welcome to 1111 Ocean Drive, Sarah!           │
│                                                              │
│  Hi Sarah,                                                   │
│                                                              │
│  Welcome to 1111 Ocean Drive! We're excited to help you...  │
│                                                              │
│  📌 CTA: Schedule a viewing                                  │
│                                                              │
│  ⏰ Send: Today at 10:00 AM  (1 follow-up in 3 days)      │
│                                                              │
│  ✅ Consent checks passed: email_opt_in_confirmed           │
│  💡 Reasoning: Sarah is a new prospect...                   │
└─────────────────────────────────────────────────────────────────┘
```

### Color Legend

| Color | Element |
|-------|---------|
| 🟩 **Green** | Successful processing, consent verified |
| 🟨 **Yellow** | Warnings, semantic score < 0.7 |
| 🟥 **Red** | Errors, failed consent checks |
| 🟦 **Blue** | Information, metadata |

---

## 🧪 Sample Test Cases

The `sample.jsonl` file includes two test cases:

### 1. Prospect Welcome (`prospect_welcome`)
- **Persona:** Prospect (new)
- **Consent:** Email opted in
- **Goal:** Send a warm welcome message with property details
- **Expected Output:** Welcome email with viewing CTA

### 2. Long Horizon (`long_horizon`)
- **Persona:** Prospect (nurturing stage)
- **Consent:** Email opted in
- **Goal:** Provide market insights and maintain engagement
- **Expected Output:** Market update email with educational CTA

---

## 🔧 Customization

### Adding Custom Prompts

To customize the system prompt, modify the `GetSystemPrompt()` method in `MessagingAgent.cs`:

```csharp
private string GetSystemPrompt()
{
    return @"
    You are a real estate messaging agent for a luxury property company.
    You respond to inbound lead signals with personalized, compliant messages.
    Your goal is to warm up leads and book showings.

    [Add your custom instructions here...]
    ";
}
```

### Adding New Fields

1. Add the field to `InputRecord` in `Models.cs`
2. Update the JSON deserialization if needed
3. The system prompt should reference the new field for context

---

## 📊 Performance Considerations

| Aspect | Recommendation |
|--------|----------------|
| **Batch Processing** | Process records sequentially to avoid rate limits |
| **Caching** | Cache common property data to reduce token usage |
| **Timeouts** | API calls timeout after 120 seconds |
| **Retries** | Implement exponential backoff for failed requests |

---

## 🐛 Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| **"API key not found"** | Set `ANTHROPIC_API_KEY` environment variable |
| **"JSON parse error"** | Validate your JSONL file format |
| **"Rate limit exceeded"** | Add delays between requests |
| **"Invalid consent"** | Ensure consent fields are boolean values |

### Debug Mode

Enable debug logging by setting `Debug = true` in `Program.cs`:

```csharp
var agent = new MessagingAgent(debug: true);
```

---

## 📚 Further Reading

- [Anthropic Claude API Documentation](https://docs.anthropic.com/)
- [Spectre.Console Documentation](https://spectreconsole.net/)
- [JSONL Specification](https://jsonlines.org/)
- [Real Estate CRM Best Practices](https://www.realtor.com/guides/)

---

## 🤝 Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development Guidelines

- Follow C# coding conventions
- Write tests for new features
- Update documentation
- Use meaningful commit messages

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

## 👥 Authors

- [Your Name](https://github.com/yourusername) — Initial work

---

## 🙏 Acknowledgments

- **Anthropic** for the Claude API
- **Spectre.Console** for the beautiful terminal UI
- **.NET Community** for the awesome ecosystem

---

## ⭐ Support

If you find this project useful, please give it a star on GitHub!

[![GitHub stars](https://img.shields.io/github/stars/yourusername/realestateagent?style=social)](https://github.com/yourusername/realestateagent)

---

**Built with ❤️ for real estate professionals and developers**