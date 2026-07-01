---
title: "Agent Tools: The bridge to the outside world"
source: "https://theneuralmaze.substack.com/p/agent-tools-the-bridge-to-the-outside"
author:
  - "[[Miguel Otero Pedrido]]"
published: 2024-12-11
created: 2026-07-01
description: "Agentic Patterns From Scratch: Lesson 2"
tags:
  - "clippings"
---
When we first explored the **Reflection Pattern** - [check Lesson 1 of the series](https://theneuralmaze.substack.com/p/reflection-pattern-agents-that-think) - we believed our agents were unbeatable. An agent that reflects on its own output, I mean, can we improve that? 🤔

The answer is yes! Actually, the **Reflection Agent** has a significant **weakness**. Consider this: what happens if you ask this agent about **NVIDIA’s stock price right now**? Or **yesterday’s temperature in Madrid**?

As you might already know, the information encoded in the LLM’s weights is not enough to provide accurate, fresh and context-specific answers to these questions.

**That’s why we need to equip our agent with ways to access the outside world** 🌍

This is were **Tools** come in. At their core, tools are like functions the LLM can call to enhance its capabilities. And this brings us to the focus of the second pattern: the **Tool Pattern.**

In this post, you’ll learn how tools work and how to build them from scratch using **Python** and **Groq LLMs.**

**Ready? Let’s begin! 🧑💻**

> You can find the code for the whole series on [GitHub](https://github.com/neural-maze/agentic_patterns)!

![](https://substackcdn.com/image/fetch/$s_!3kuk!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fbed0a61b-a747-4d5b-b2bb-48ee2e520f8e_1080x1080.png)

---

## Building a Tool from Scratch

Let’s start with the basics. To begin with, take a look at this function 👇

![](https://substackcdn.com/image/fetch/$s_!PB2G!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F976aaf37-3b4e-4df7-9c3a-17921446964e_2848x1640.png)

Simple, right? Embarrassingly simple Id’ say … but the question is: **How can we make this function available to an LLM?**

---

## A System Prompt that works

For the LLM to be aware of this function, we need to provide some relevant information about it in the context. I’m referring to the function name, attributes, description, etc. Take a look at the following **System Prompt.**

![](https://substackcdn.com/image/fetch/$s_!ZwcG!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F496740b3-2d98-4816-bca5-103d326985fd_4752x2888.png)

As you can see, the System Prompt enforces the LLM to behave as a **function calling AI model** who, given a list of function signatures inside the **XML tags** will select which one to use.

![](https://substackcdn.com/image/fetch/$s_!BZtX!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fb417c78d-728a-4da1-8c09-c6f85f2edb90_2080x724.png)

Let’s see how it works in practise! Let's ask a very simple question: **“What’s the current temperature in Madrid in Celsius?”**

![](https://substackcdn.com/image/fetch/$s_!ILCZ!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe9e32bb0-fbbb-4ba9-bca2-2c6aa539ca22_2796x2320.png)

After running this code, we should see something like this:

![](https://substackcdn.com/image/fetch/$s_!tmMi!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Ff21ec4f3-bc3a-4a2b-9eb9-82b39e7c66a2_2295x892.png)

**That's an improvement!** We may not have the *proper* answer but, with this information, we can obtain it! How? Well, we just need to:

1️⃣ Parse the LLM output. By this I mean deleting the XML tags

2️⃣ Load the output as a proper Python dict

These two steps are straightforward to implement, as you can see [in this Python module.](https://github.com/neural-maze/agentic_patterns/blob/main/src/agentic_patterns/utils/completions.py) With all the arguments in place, running the function is now a simple task 👇

![](https://substackcdn.com/image/fetch/$s_!Q51K!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F008e7d77-ebfb-4e18-872d-582a4781d308_2366x556.png)

If you run the function above, you’ll get the following result:

```markup
'{"temperature": 25, "unit": "celsius"}'
```

Exactly what we expected from the **get\_current\_weather** function! A temperature of 25 degrees Celsius in Madrid!

Now, we can simply add the **parsed\_output** to the **chat\_history** so that the LLM knows the information it has to return to the user.

![](https://substackcdn.com/image/fetch/$s_!8zwX!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F201b316c-ffa5-4c52-af87-ebcdcc6fad46_1685x1228.png)

In my case, this is the output returned by the LLM:

```markup
The current temperature in Madrid is 25 degrees Celsius.
```

And this, my dear friend, is the way a Tool works under the hood!

Easy, huh? Now it’s time to create a **tool decorator**, just like frameworks such as **LlamaIndex, CrewAI** or **LangChain** do!

---

## Tool Decorator

To recap, we have a way for the LLM to generate **tool\_calls** that we can use later to **properly** run the functions. But, as you might imagine, there are some pieces missing:

1️⃣ We need to automatically transform any function into a description like we saw in the initial system prompt.

2️⃣ We need a way to tell the agent that this function is a tool

This can be accomplish using an elegant solution, the **tool decorator**, that will transform any Python function into a **Tool** object. You can see the implementation of the [tool\_decorator](https://github.com/neural-maze/agentic_patterns/blob/main/src/agentic_patterns/tool_pattern/tool.py#L89) and the [Tool](https://github.com/neural-maze/agentic_patterns/blob/main/src/agentic_patterns/tool_pattern/tool.py#L58) in the repo!

To see the tool decorator in action, we’ll build a tool that interacts with [Hacker News](https://news.ycombinator.com/), **fetching the top n stories**.

![](https://substackcdn.com/image/fetch/$s_!cv8a!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F37ef1afe-151c-4d41-8fa5-4c14399dc632_3836x4420.png)

The block of code above will transform the Python function - **fetch\_top\_hacker\_news\_stories** - into a Tool.

The Tool has the following parameters: a **name**, a **fn\_signature** and the **fn** - this is the function we are going to call, in this case **fetch\_top\_hacker\_news\_stories**.

> Under the hood, the Tool class will generate the function signature automatically. You can see how the signature is extracted [here](https://github.com/neural-maze/agentic_patterns/blob/main/src/agentic_patterns/tool_pattern/tool.py#L5).

Now that we have a tool, let's run the [agent](https://github.com/neural-maze/agentic_patterns/blob/main/src/agentic_patterns/tool_pattern/tool_agent.py#L38).

![](https://substackcdn.com/image/fetch/$s_!tOvz!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F3a52fada-2cb1-423e-b258-71c2eba21a90_3236x704.png)

And there you have it! A fully functional Tool! 🛠️

![](https://substackcdn.com/image/fetch/$s_!nb43!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F0a84cb6b-f27e-4e6b-9c0a-3284a912fc00_3772x1008.png)

---

**If you prefer video lectures, I also have a YouTube video covering the Tool Pattern! 👇**

![](https://www.youtube.com/watch?v=ApoDzZP8_ck)

That’s all for today! Next week, we’ll talk about the **Planning Pattern**, the **ReAct technique** and how to implement **reasoning heuristics** for our Agents.

**Happy tooling! 👋**

Miguel