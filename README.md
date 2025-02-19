# MIDOTNET - Build CustomGPTs and Assistants with OpenAI

Learn from Mike as he shares insights on developing CustomGPTs and AI Assistants using OpenAI's toolkit, focusing on practical applications like enhancing call center operations through quality assurance augmentation and maintaining data privacy with ChatGPT Team.

Mike Onslow, Director of Technology and Development at Clarity Voice

- Co-host of the [Artificial Antics Podcast](https://antics.tv)
- LinkedIn: [mikeonslow](https://www.linkedin.com/in/mikeonslow)
- X: [@mike_onslow](https://twitter.com/mike_onslow)

Message me on LinkedIn or X!

# Custom GPTs in ChatGPT

Custom GPTs allow users to discovery and create custom versions of ChatGPT that combine instructions, extra knowledge, and any combination of skills (actions, instructions). Custom GPT's allow you to tailor ChatGPT to a specific use case. 

**IMPORTANT**: 
- Custom GPTs recently replaced ChatGPT "Plugins", plugins are no longer available.
- A ChatGPT Plus, Team or Enterprise subscription is **required** to access Custom GPTs.

Explore GPTs: https://chat.openai.com/gpts

<img src="illustrations/Explore_GPTs.jpg" style="border: 1px solid #ffffff; border-radius: 5px;">

### Use Cases:

**Image Generation**
Creating Original Artwork for Digital Marketing
A digital marketing agency wants to generate unique, eye-catching visuals for a campaign. They use Custom GPT integrated with an image generation model to produce high-quality, original images. By inputting specific parameters (e.g., campaign theme, color scheme, desired emotions), the system generates several options of artwork. This capability enables the agency to rapidly prototype and iterate on creative concepts, significantly reducing the time and cost associated with traditional graphic design.

**Writing, Productivity**
Automated Report Generation for Market Analysis
A financial analysis firm employs Custom GPT to automate the creation of market analysis reports. Analysts input raw financial data, market trends, and specific indicators into the system. The model processes this information to produce comprehensive reports that include an executive summary, analysis of market trends, potential investment opportunities, and risk assessments. This automation allows analysts to focus on strategy and decision-making, rather than spending extensive time on report writing.

**Research & Analysis**
Enhancing Drug Discovery with Predictive Analysis
A biotech company integrates Custom GPT into their research process to accelerate drug discovery. By feeding the model extensive data sets on chemical compounds and their biological activities, it can predict potential drug candidates for specific diseases. The system analyzes the structure-activity relationship (SAR) data, providing researchers with insights into which compounds are most likely to be effective as therapeutic agents, thereby streamlining the initial stages of drug discovery.

**Programming**
Code Debugging and Optimization Tool
A software development firm utilizes Custom GPT to build an advanced debugging and optimization tool. Developers input their code, and the system not only identifies bugs but also suggests optimizations and alternative coding approaches to improve efficiency and performance. This tool becomes an essential part of the development process, reducing debugging time and helping developers adhere to best practices in coding.

### Instructions
Instructions are used to customize how the GPT behaves and executes its tasks. Here's an example of the custom GPT instructions used in the Call Center Coach GPT:

```
You are a helpful Call Center Coach that has 30 years of experience. You are assessing calls for the ACME Communications Support Team.

You have a very high level of emotional intelligence. Your job is to read a transcript (with entiment analysis) of a support call and fill in the "Quality Assurance Template".

IMPORTANT:
1. Always double check your work. In your knowledge there are examples of where you have done your job well and where you have not done this job so well. Use those knowledge examples as a guiding light. 
2. Always use a temperature setting of 0.0

Your standard inputs:
1. A URL, if a URL is input in the chat, go ahead and use Deepgram to transcribe the audio URL. When you call the Deepgram API to get then transcript, use these parameters: ?redact=pci&redact=ssn&redact=numbers&language=en
2. The other option is to receive a JSON file as an input, if receiving a JSON file, use code interpreter to read the file and use it for your standard outputs.

Your output should ALWAYS be the following:

1. The names of the speakers
2. The sentiment of all parties
3. What the agent did great! 
4. A list of 3 examples where the AGENT demonstrated the "Clarity Core Values", make sure to name the value the agent demonstrated in each of the 3 examples.
5. Define areas for improvement and set goals for future calls

...
```

Tips for crafting instructions:

- Clearly define the role and responsibilities of the GPT.
- Specify the inputs and outputs the GPT should handle.
- Provide any additional context or guidelines for the GPT to follow.

### Knowledge

Knowledge in custom GPT's are files that give your GPT a basis of knowledge to draw from. This knowledge can be used to guide the GPT's responses, provide examples, or help it understand the context of a conversation.

Some examples of knowledge we use during this talk:

- Core Values for our fictional company "ACME Communications"
- Quality assurance guidelines with examples (10 examples of how to judge each question in the template)

```
### Did Agent State Their Dept?
- **Corresponding Value:** Customer-Centric Approach
- **Yes/No?:** 
- **Explanation:** Stating the department helps the customer understand the agent’s expertise.
- **Gauging Compliance:**
  1. Check recordings for specific department mentions at the beginning of calls.
  2. Evaluate customer responses for signs of understanding the agent's role.
  3. Look for department references when transferring calls.
  4. Analyze the context in which the department is mentioned for appropriateness.
  5. Monitor if the department is stated clearly and confidently.
  6. Examine if the department is stated upon customer request or confusion.
```

Limitations:
- GPTs are limited to 20 knowledge files.
- All files uploaded to a GPT or a ChatGPT conversation have a hard limit of 512MB per file.
- All text and document files uploaded to a GPT or to a ChatGPT conversation are capped at 2M tokens per file. This limitation does not apply to spreadsheets.
- For images, there's a limit of 20MB per image.

Note: An error will be displayed if a user/org cap has been hit. 

### Actions

OpenAI Actions are a way to interact with 3rd party APIs and services. In this example app, I'm reaching out to Deepgram (deepgram.com) to transcribe audio files and add sentiment analysis.

#### How to Build

TIP to create action specs from cURL. You can use ChatGPT to generate an OpenAPI 3.0 spec from a cURL request. Here's an example of what I did with my Deepgram cURL request:

##### ChatGPT Prompt:

>Build an open api 3.0 spec from this curl request. Make the output JSON
>
>curl --location --request POST 'https://api.deepgram.com/v1/listen' \
>--header 'Authorization: Token <DEEPGRAM_TOKEN>' \
>--header 'Content-Type: application/json' \
>--data-raw '{"url":"https://static.deepgram.com/examples/>en_NatGen_CallCenter_BethTom_CancelPhonePlan.wav"}'
>
>Ensure the components / schemas exists
>
>"components": {
>      "schemas": {
>
>The output should contain no comment blocks

##### Output:

```
{
  "openapi": "3.1.0",
  "info": {
    "title": "Deepgram API",
    "version": "1.0.0",
    "description": "API for processing audio transcription requests."
  },
  "servers": [
    {
      "url": "https://api.deepgram.com/v1"
    }
  ],
  "paths": {
    "/listen": {
      "post": {
        "summary": "Transcribe audio from a provided URL",
        "operationId": "transcribeAudio",
        "tags": ["Transcription"],
        "requestBody": {
          "required": true,
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/AudioTranscriptionRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "description": "Successful transcription",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/AudioTranscriptionResponse"
                }
              }
            }
          }
        },
        "security": [
          {
            "ApiKeyAuth": []
          }
        ]
      }
    }
  },
  "components": {
    "schemas": {
      "AudioTranscriptionRequest": {
        "type": "object",
        "required": ["url"],
        "properties": {
          "url": {
            "type": "string",
            "format": "uri",
            "description": "URL of the audio file to transcribe."
          }
        }
      },
      "AudioTranscriptionResponse": {
        "type": "object",
        "properties": {
          "transcription": {
            "type": "string",
            "description": "Transcribed text from the audio file."
          }
        }
      }
    },
    "securitySchemes": {
      "ApiKeyAuth": {
        "type": "apiKey",
        "in": "header",
        "name": "Authorization"
      }
    }
  }
}

```

### How to Test

You can test actions by clicking the "Test" button in the ChatGPT interface. This will allow you to test the action.

#### More on actions
https://platform.openai.com/docs/assistants/tools/actions

Helpful video on building OpenAI Actions that connect Zapier to ChatGPT: https://www.youtube.com/watch?v=qzeaHm4J3bk

### Conversatoin Starters (Prompts)

Conversation starters are a way to guide the conversation with the GPT. They can be used to provide context, ask questions, or give the GPT a starting point for a conversation.

<img src="illustrations/Conversation_Starters.jpg" alt="Conversation Starters Example">


### The Full Picture

<img src="illustrations/Full_Config.jpg" alt="">

# OpenAI Assistants API

https://platform.openai.com/docs/assistants/overview

Well custom GPTs are powerful and helpful, custom GPTs are only part of the ChatGPT interface. That's where OpenAI Assistants come in. OpenAI Assistants are designed for integration into external systems, they offer specialized functionalities beyond the ChatGPT ecosystem.

### Instructions

Instructions in then Assistants API are used to customize how the Assistant behaves and executes its tasks. Here's an example of the custom GPT instructions used in the Call Center Coach GPT:

```
You are a helpful Call Center Coach that has 30 years of experience. You have a very high level of emotional intelligence. Your job is to read a transcript of a support call and fill in the "Quality Assurance Template".  Always double check your work. In your knowledge there are examples of where you have done your job well and where you have not done this job so well. Use those knowledge examples as a guiding light. 

IMPORTANT:
- Always use a temperature setting of 0.0
- Only output JSON not anything else, no explanation, just the JSON code

Your standard inputs:
You will receive a JSON file as an input, if receiving a JSON file, use code interpreter to read the file and use it for your standard outputs.

Your output should ALWAYS be in the following JSON format (You should only output this once)

{
  "callDetails": {
    "user": "<User Identifier>",
    "type": "<Call Type>",
    "from": "<Originating Number>",
    "to": "<Destination Number>",
    "timeStart": "<Call Start Time>",
    "timeEnd": "<Call End Time>",
    "duration": "<Call Duration>",
  },
  "sentiment": {
    "agent": "<Overall Agent Sentiment>",
    "customer": "<Overall Customer Sentiment>"
  },
  "agentPerformance": {
    "greatWork": [
      "<First Aspect Agent Did Great>",
      "<Second Aspect Agent Did Great>",
      "<Third Aspect Agent Did Great>"
    ],
    "areasForImprovement": [
      "<First Area for Improvement>",
      "<Second Area for Improvement>"
    ]
  },
  "coreValueExamples": [
    "<Innovation and Proactivity|Integrity and Transparency|Customer-Centric Approach|Excellence and Quality Focus|Team Collaboration and Support>: <Example of How Agent Was Upfront>",
    "<Innovation and Proactivity|Integrity and Transparency|Customer-Centric Approach|Excellence and Quality Focus|Team Collaboration and Support>: <Example of Demonstrated Knowledge>",
    "<Innovation and Proactivity|Integrity and Transparency|Customer-Centric Approach|Excellence and Quality Focus|Team Collaboration and Support>: <Example of Detailed Questions to Diagnose Issue>"
  ],
  "qualityAssuranceTemplate": {
    "questions": [
      {"Did Agent State Their Name?": "<Yes/No>"},
      {"Did Agent state their dept?": "<Yes/No>"},
      {"Ask or Verify business ph. number and business?": "<Yes/No>"},
      {"Check to see if there is existing ticket?": "<Yes/No>"},
      {"Confirm callback number if call drops?": "<Yes/No>"},
      {"Confirm email address is correct?": "<Yes/No>"},
      {"Record the caller's name?": "<Yes/No>"},
      {"Checked to see if caller authorized to make change?": "<Yes/No>"},
      {"Did Agent actively listen?": "<Yes/No>"},
      {"Was the Agent courteous?": "<Yes/No>"},
      {"Was the Agent empathetic?": "<Yes/No>"},
      {"Did the Agent avoid blaming others?": "<Yes/No>"},
      {"Asks probing/pertinent questions?": "<Yes/No>"},
      {"Uses appropriate Wikis?": "<Yes/No>"},
      {"If placed on hold, was correct procedure used?": "<Yes/No>"},
      {"Requests and changes noted?": "<Yes/No>"},
      {"If changes made, were they tested?": "<Yes/No>"},
      {"Issue resolved or escalated appropriately?": "<Yes/No>"},
      {"Did agent confirm notes and support type stayed intact?": "<Yes/No>"},
      {"Did agent confirm anything else we can do for them?": "<Yes/No>"}
    ]
  }
}

The `coreValueExamples` key should always be generated based on the `Core_Values.txt` file.
```

### Knowledge

Similar to Knowledge in custom GPTs, OpenAI wil store files for retreival upon running your Assistant. One major note is that this can get fairly pricey. The added convenience here is that once a file is uploaded and passed to the Assistant, OpenAI automates the whole RAG process that was usually custom-built by developers. They automatically chunk your documents, index and store the embeddings, and implement vector search to retrieve relevant content to answer user queries.

https://platform.openai.com/docs/assistants/tools/knowledge-retrieval

NOTE: If you're already using the Assistants API, you're likely going to be using a platform like Langchain for other functions, it may be more economical to use Langchain with other tools like PineconeDB, Astra DB etc. for knowledge retrieval.


### Functions, Code Interperter and other Tools

https://platform.openai.com/docs/assistants/tools/code-interpreter


## Comparing Assistants to Custom GPTs

#### Pros:
- You can use the OpenAI API to build chatbots that can be used in a variety of applications including web, mobile, and desktop applications
- You can choose from a variety of OpenAI models including `gpt-4`, `gpt-4-turbo-preview`, `gpt-3.5-turbo-16k` and `gpt-3.5-turbo`

#### Cons:
- More complex to build
- Not available to publish in the OpenAI Explore GPTs interface
- Costly compared to custom GPTs which are included with a ChatGPT Plus, Team or Enterprise subscription
- No native "Web Browsing", you need to bring your own APIs for web scraping and other web-based interactions

### ChatGPT Custom GPTs

#### Pros:
- Quick and easy to build
- No coding required
- No need to host or maintain the model
- Native "Web Browsing"

#### Cons:
- Limited to the ChatGPT interface
- Limited to content window when API calls complete (ran into this many times)

## Utilizing ChatGPT Team for Privacy and Other Benefits

### ChatGPT Team 

https://openai.com/chatgpt/team

- Access to GPT-4 with 32K context window
- Share links to chat sessions within your team
- Share GPTs with your team
- Collaborate on GPTs with your team
- Tools like DALL·E 3, GPT-4 with Vision, Browsing, Advanced Data Analysis—with higher message caps
- No training on your business data or conversations
- Secure workspace for your team
- Create and share custom GPTs with your workspace
- Admin console for workspace and team management
- Early access to new features and improvements

### FAQs

There's some great FAQs on the OpenAI website that cover the basics of ChatGPT Team.

https://help.openai.com/en/articles/8798634-shared-links-faq-chatgpt-team-version



## FILES:

Sample Call: https://static.deepgram.com/examples/en_NatGen_CallCenter_BethTom_CancelPhonePlan.wav
