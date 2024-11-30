# Gmagent - A Llama Powered Gmail Agent

This Gmagent app shows how to build a Gmail agent app powered by Llama 3.1 8B running locally via Ollama (for privacy concern since Gamgent is about your Gmail), starting with building from scratch a basic agent with custom tool calling natively supported in Llama 3.1. The goal is to cover all components of a production-ready agent app with a great user experience: intuitive, engaging, efficient and reliable.  

Currently you can ask Gmagent to:
* search for emails and attachments
* reply to a specific email 
* 

# Overview

Email is an essential and one top killer app people use daily. A recent [State of AI Agents](https://www.langchain.com/stateofaiagents) survey by LangChain finds that "The top use cases for agents include performing research and summarization (58%), followed by streamlining tasks for personal productivity or assistance (53.5%)." 

Andrew Ng wrote a 5-part [Agentic Design Patterns](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/) in March 2024 predicting "AI agent workflows will drive massive AI progress this year". Deloitte published in November 2024 a report [AI agents and multiagent systems](https://www2.deloitte.com/content/dam/Deloitte/us/Documents/consulting/us-ai-institute-generative-ai-agents-multiagent-systems.pdf) stating that "Through their ability to reason, plan, remember and act, AI agents address key limitations of typical language models." and "Executive leaders should make moves now to prepare for and embrace this next era of intelligent organizational transformation." - Enough big words for motivating understanding what a GenAI agent truly is and how to start building an agent app.

## What is an agent?

Lilien Weng in her popular June 2023 blog [LLM Powered Autonomous Agents](https://lilianweng.github.io/posts/2023-06-23-agent/) defines LLM-powered agent system to have four key components:
 * Planning and Reflection: can break down large tasks into smaller ones; can do self-reflection over past actions and self improve; 
 * Memory: can use contextual info and recall info over extended periods (for other components to use);
 * Tool Use: can understand what external APIs to use for info or action not built into LLMs;
 * Action: can actually run the tools.

Andrew Ng describes four agentic design patterns:
* Reflection
* Planning
* Tool calling
* Multi-agent collaboration, where "memory" is mentioned: Each agent implements its own workflow, has its own memory (itself a rapidly evolving area in agentic technology: how can an agent remember enough of its past interactions to perform better on upcoming ones?)

In Deloitte's report, AI agents are reasoning engines that can understand context, plan workflows, connect to external tools and data, and execute actions to achieve a defined goal.

Also in a November 2024 blog by Letta [The AI agents stack](https://www.letta.com/blog/ai-agents-stack), LLM powered agent is described as the combination of tools use, autonomous execution, and memory.

In addition, Harrison Chase defines agent in the blog [What is an AI] agent(https://blog.langchain.dev/what-is-an-agent/) as "a system that uses an LLM to decide the control flow of an application." 

If this seems too simple of a definition, a summary of all the above definitions would be: agents are systems that take a high-level task, use an LLM as a reasoning and planning engine, with the help of contextual info and long-term memory if needed, to decide what actions to take, reflect and improve on the actions, and eventually execute those actions to accomplish the task.

If this seems too long of a definition, it's time to walk the walk, see the app in action, and do some coding. Below is a preview of the questions or requests one may ask Gmagent:  

# Example Asks to Gmagent

* do i have any emails with attachments?
* what's the content of the email from LangSmith
* do i have emails with attachment larger than 1mb?
* what kind of attachments for the email with subject papers to read?
* give me a summary of the pdf thinking_llm.pdf
* Draft an email to jeffxtang@meta.com saying working on it and will keep you updated. thanks for your patience.
* send the draft
* do i have any emails with attachment larger than 10mb?
* how about 5mb
* reply to the email saying thanks for sharing! 
* forward the email to jeffxtang@meta.com

[Here](./examples_log.txt) is a complete example interaction log with Gmagent.

# Setup and Installation

## Enable Gmail API
1. Go to the [Google Cloud Console](https://console.cloud.google.com/).
2. Create a new project by clicking the drop down on the top left then click NEW PROJECT.
3. Enter a Project name then click CREATE.
4. Under "APIs & Services" > "Enabled APIs & services", search for "gmail" and then Enable the "Gmail API" for your project.
5. Under "APIs & Services" > "OAuth consent screen", click "GO TO NEW EXPERIENCE", then click "GET STARTED", enter App name, select your gmail as User support email, choose External under Audience, enter your gmail again as Contact Information, and finally check the I agree to the Google API Services under Finish and click Continue - Create. 
5. Again under "APIs & Services", go to Credentials. Click on + CREATE CREDENTIALS, then choose OAuth client ID (NOT API key).
Select Desktop App (NOT Web application, because you're assumed to want to start your Gmail agent locally first) as the application type and name it. Click Create to generate your client ID and client secret.
6. Click Download JSON and rename the downloaded file as credentials.json. This file will be used in your Python script for authentication.

If you ever want to give up during this process, you may want to check out the example asks (to see what you can ask to the agent) and the example log (to see the whole conversation with gmagent) and think again - the devil's in the detail and all the glorious description of a powerful trendy agent doesn't mention the little details one has to deal with to build it.


## Install Ollama with Llama 3.1 8B

Download Ollama (available for macOS, Linux, and Windows) [here](). Then download and test run the Llama 3.1 8B model by running on a Terminal:
```commandline
ollama run llama3.1
```

This will download a quantized version of Llama 3.1 and the downloaded size will be 4.7GB.


## Install required packages
First, create a Conda or virtual env:

```commandline
conda create -n gmagent python=3.10
conda activate gmagent
```
or
```commandline
python  -m venv gmagent
source gmagent/bin/activate # on Linux, macOS:
source gmagent\Scripts\activate # on Windows
```

Then install the required Python libraries:
```
git clone https://github.com/jeffxtang/gmagent
cd gmagent
pip install -r requirements.txt
```


# Build and Run Gament

To run Gament, you need to first copy the `credentials.json` file downloaded and renamed above in step 6 of Enable Gmail API to the gmagent folder, then run:
```
python main.py --user_email <your_gmail_address>
```

The first time you run it, you'll get a prompt like this;
```
Please visit this URL to authorize this application: https://accounts.google.com/o/oauth2/auth?response_type=code&client_id=xxxx
Enter the authorization code: 
```

You need to copy the URL above and open it in a browser - if you Sign in with Google using the same Gmail you enabled for the Gmail API, then you'll see "You’ve been given access to an app that’s currently being tested. You should only continue if you know the developer that invited you.", otherwise if you sign in with another Gmail, you'll see "Gmail Agent App has not completed the Google verification process. The app is currently being tested, and can only be accessed by developer-approved testers. If you think you should have access, contact the developer." In the latter case, go to APIs & Services > OAuth consent screen > Test users, and click the + ADD USERS button, and you'll see this message: While publishing status is set to "Testing", only test users are able to access the app. Allowed user cap prior to app verification is 100, and is counted over the entire lifetime of the app.

After clicking Continue, check the Select all checkbox to enable both settings required for running Gmaget:

View your email messages and settings. 
Manage drafts and send emails.

Finally, copy the Authorization code and paste it to the Terminal, hit Enter and you'll see Gmagent's initial greeting (which will likely differ because the default temperature value 0.8 is used here - see [Ollama's model file](https://github.com/ollama/ollama/blob/main/docs/modelfile.md#valid-parameters-and-values) for detail) such as:
```
Hello! I'm Gmagent, here to help you manage your Gmail account with ease.

What would you like to do today? Do you want me to:

Check and respond to new emails
Compose a new email
Organize your inbox with filters or labels
Delete unwanted emails
Something else?

Let me know how I can assist you!

Your ask:
```

If you cancel here and run the command `python main.py --user_email <your_gmail_address>` again you should see the Gmagent greeting right away without the need to enter an authorization code, unless you enter a different Gmail address for the first time - in fact, for each authorized Gmail address, a file token_xxxx@gmail.com.pickle will be created which contains the authorized token.

See example asks and interaction log above for the types of asks you may enter.


# Implementation Notes and Tips



# Available Custom Functions (Tools)

* list_emails_function
* get_email_function
* send_email_function (new, reply, forward)
* get_pdf_summary_function
* create_draft_function
* send_draft_function


# TODOs

1. Port the app to using [Llama Stack](https://github.com/meta-llama/llama-stack) Agents API.
2. Improve the search, reply, forward, create email draft, and query about attachments.  
3. Improve the fallback and error handling mechanism when the user asks don't lead to a correct function calling spec or the function calling fails. 
4. Improve the user experience by showing progress when some Gmail search API calls take long (minutes) to complete.
5. 




# Resources
* Lilien Weng's blog [LLM Powered Autonomous Agents](https://lilianweng.github.io/posts/2023-06-23-agent/) 
* Andrew Ng's posts [Agentic Design Patterns](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/)
* LangChain's survey [State of AI Agents](https://www.langchain.com/stateofaiagents)
* Deloitte's report [AI agents and multiagent systems](https://www2.deloitte.com/content/dam/Deloitte/us/Documents/consulting/us-ai-institute-generative-ai-agents-multiagent-systems.pdf)
* Letta's blog [The AI agents stack](https://www.letta.com/blog/ai-agents-stack)
* Microsoft's multi-agent system [Magentic-One](https://www.microsoft.com/en-us/research/articles/magentic-one-a-generalist-multi-agent-system-for-solving-complex-tasks)
* Amazon's [Multi-Agent Orchestrator framework](https://awslabs.github.io/multi-agent-orchestrator/)
* Deeplearning.ai's [agent related courses](https://www.deeplearning.ai/courses/?courses_date_desc%5Bquery%5D=agents) (Meta, AWS, Microsoft, LangChain, LlamaIndex, crewAI, AutoGen) and some [lessons ported to using Llama](https://github.com/meta-llama/llama-recipes/tree/main/recipes/quickstart/agents/DeepLearningai_Course_Notebooks). 
