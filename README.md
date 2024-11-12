

![termtalk-logo"](termtalk-logo.png)

# TermTalk
## Talk to AI from Your Terminal.

by [piotrkepka.com](https://piotrkepka.com)

Current version: `0.1-alpha (12.11.2024)`


### Overview

**TermTalk** is a console client enabling direct access to AI models (like GPT-4) from the terminal.

Created by [Piotr Kepka](https://piotrkepka.com).

It supports both **Interactive Mode** - for ongoing conversations with the AI model - and **Question-Answer Mode** - for quick Q&A sessions, even regarding file content.

### Key Features:

- Access AI anytime directly from your Linux console
- Data is stored locally in a simple SQLite database, ensuring privacy and easy access to your conversation history. You have full control over all conversation content.
- Unlike ChatGPT's fixed $20/month fee, OpenAI API usage is billed based on consumption, so you only pay for what you use.
- While ChatGPT retains conversations for model training, using the OpenAI API directly keeps your conversations private.
- Provide others (e.g., family members or team members) access by managing their API usage with individual API keys, allowing better control over expenses.


### Getting Started

Visit the main page for **TermTalk**: https://piotrkepka.com/termtalk.

The code is hosted on [GitHub](https://github.com/termtalk/termtalk).

Documentation is available at https://termtalk.github.io.

Stay updated and connect with the TermTalk community on [LinkedIn](https://www.linkedin.com/showcase/termtalk).


#### Installation

1) Download the code from [GitHub](https://github.com/termtalk/termtalk).

2) Unpack it to your desired location (e.g., `~/termtalk`).

3) First Run Setup: When you run the application for the first time, the setup process will be completed automatically.


#### First-Time Run

On the initial launch, **TermTalk** will automatically create a Virtual Environment (venv) and install all required dependencies. A configuration file with default settings will also be generated.

You will be prompted to enter your OpenAI API key (refer to the **OpenAI API Key** section of this document).

##### Configuration

**TermTalk** can be configured using either a local or global configuration file.

**Local Configuration**: Created in the user's home directory:

```
~/termtalk/termtalk.conf
```

**Global Configuration**: Can be set by a system administrator (root) for system-wide use:

```
/etc/termtalk/termtalk.conf
```

##### Database

**TermTalk** utilizes SQLite, a local file-based database, which is created on the first run at:

```
~/termtalk/termtalk.db
```
This database stores all conversations and statistics related to OpenAI API usage.

### Usage

#### Interactive Mode

To start an interactive chat session with the AI, run:

```
./tt
```

##### Question-Answer Mode

For a single question-and-answer interaction, run:

```
./tt "How to make ice cream?"
```

##### Command-Line Parameters

To view a list of available parameters, type:

```
./tt -h
```

###### Usage:

```
./tt [-h] [-l] [--show SHOW] [--session-id SESSION_ID] [-np] [-v] [-nmd] [-f FILE] [message]
```

```
TermTalk

positional arguments:
  message               Message to send to the assistant

options:
  -h, --help            Show this help message and exit
  --session-id SESSION_ID
                        UUID for continuing an existing session
  -np, --no-prompts     Disable additional prompts
  -v, --verbose         Enable verbose mode for additional output details
  -nmd, --no-markdown   Disable markdown formatting
  -f FILE, --file FILE  File containing the message to send to the assistant

```

###### Examples:

To analyze text files, use:

```
./tt -f /var/log/syslog "List the errors and provide explanations"
```

```
./tt -f /etc/nginx/nginx.conf "Show me how to change the SSL settings"
```


By default, **TermTalk** presents output from the OpenAI API with parsed, colorful formatting to improve readability. You can switch this off to get plain text and unformatted markdown:

```
./tt -nmd "Show sample markdown"
```

**Verbose Mode** - For additional information, enable verbose mode:

```
./tt -v "I need to know what is going on"
```

**No Prompts** - Disables extra prompts in the output, useful for chaining commands:

```
./tt -np "Tell me a joke"
```

The **No Prompts** option is helpful for piping or chaining commands:

```
cat /etc/passwd | ./tt -np "Explain the file format"
```

```
./tt -np "Explain the file format" < additional_information.txt
```

```
./tt -np -f random_order_data.txt "Sort data from the file" < additional_information.txt
```



##### Interactive Mode Parameters

In **TermTalk**'s Interactive Mode, type `exit` to quit, or `?` for help.

Commands in this mode start with `/`. All other input (except empty lines) is sent directly to the AI model.

###### Available Commands:

```
Help - Available Commands
=========================

  ?, /help, /h, /?        - Display this help message
  exit, bye, quit, q, :q! - Exit the application

Conversation Management:

  /new, /n                - Start a new conversation
  /stats <conv_id>|all|a, /st <conv_id>|all|a 
                          - Show statistics of a specific conversation or all conversations
  /history, /hst, /hist   - Display conversation history

  /list, /l               - List all conversations
  /switch <conv_id>, /s <conv_id>
                          - Switch to a specific conversation by ID

Attachments:

  /file <filename>, /f <filename>
                          - Load the contents of a file
  /rmfile, /rmf           - Unload the file contents
  /ls <dir>               - List files in a specified directory

```


###### Examples:

To start **TermTalk** in Interactive Mode, simply run:

```
./tt
```

At the prompt (`>>>`), type your message to the AI model. Responses typically arrive within 5-10 seconds; while there's no "spinner" indicator, processing continues in the background.

To execute a command, type it directly at the prompt. Commands won't be sent to the model, even if entered incorrectly:

```
>>> /help
```

Statistics are recorded for each interaction, including estimated usage costs. 
**Note**: These estimates may not always be precise.

###### Statistics

To display **Statistics** for the current conversation, type:

```
>>> /stats
```

Sample output:
```
Conversation Statistics - Current Conversation:

            Model: gpt-4o-mini-2024-07-18 (gpt-4o-mini)

   Total messages: 4
     Total tokens: 83
    Prompt tokens: 60
Completion tokens: 23

   Average tokens per Message: 20.75
    Average tokens per Prompt: 15.00
Average tokens per Completion: 5.75

     Total cost of Prompts: $0.00 ($0.0001500, 60 tokens)
 Total cost of Completions: $0.00 ($0.0002300, 23 tokens)
                            ------------------
Total cost of Conversation: $0.00 ($0.0003800, 83 tokens)

```

To display statistics for **all conversations**, type:

```
>>> /stats all
```

To display statistics for a **specific conversation**, type:

```
>>> /stats 11
```
where: `11` is the conversation number (see the conversation list with `/list`).

###### Managing Files in Conversations

To add a **file** to the conversation:

```
>>> /file <file_name>
```

The content of the file will be sent with the next message to the AI model. You can refer to the file as "this file" or "file content" in your prompt.

To remove the file from the conversation so it's no longer sent:

```
>>> /rmfile
```

To list files in a directory:

```
>>> /ls /etc
```

###### Conversation History and Management

Replay the entire conversation using:

```
>>> /history
```

This is especially useful if the conversation was **switched** from one to another.

To **list** all conversations:

```
>>> /list
```

Sample Conversation List:
```

Conversations:

Today
=====
(2) Markdown Formatting Guide

Yesterday
=========
(1) Top 10 TLDs

```

To **continue a previous conversation**, use:

```
>>> /switch 1
```
where `1` is the conversation number.

To **start a new conversation**, type:

```
>>> /new
```

### OpenAI API Key

1) **OpenAI Account**: You'll need an OpenAI account to use **TermTalk** with the OpenAI API.

2) **Creating the API Key**:

- Go to https://platform.openai.com/api-keys

- Click `[ + Create new secret key ]` to generate your API key.

**Required Permissions:**

```
READ

/v1/models

WRITE

/v1/audio
/v1/chat/completions
/v1/embeddings
/v1/images
/v1/moderations

```


3) Monitoring and Controlling Usage:

- Check usage at https://platform.openai.com/usage

- **Pre-pay** a balance on your account, with $10 being a suggested starting amount:
    - Click on your profile image (top-right corner), select `Your profile`, then go to `Billing`.

- **Set Usage Limits** to prevent unexpected charges:
    - [Set limits](https://platform.openai.com/settings/organization/limits), e.g., **Budget Alert** at $8 and **Budget Limit** at $10.


For detailed steps, refer to OpenAI's official documentation on their website.

**Important**: Do not share your API key, as it provides access to your OpenAI account



### Warranty

This application is provided **AS-IS** with no warranty of any kind. While the author has made every effort to ensure functionality, there is no guarantee that the software is free of errors or issues.

- **Privacy**: The application does not collect any personal data. All data remains on your local device.
- **Liability**: The author assumes no responsibility for any damages arising from the use of this application.

Use **TermTalk** at your own risk.












