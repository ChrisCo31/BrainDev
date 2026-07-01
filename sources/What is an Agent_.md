---
title: "What is an Agent?"
source: "https://theneuralmaze.substack.com/p/what-is-an-agent"
author:
  - "[[Miguel Otero Pedrido]]"
published: 2024-11-27
created: 2026-07-01
description: "Agentic Patterns From Scratch: Lesson 0"
tags:
  - "clippings"
---
**Agents** seem to be **everywhere** these days **🤖**

You open **LinkedIn**, and your feed is filled with **agent-related posts**.

Heading to **Twitter** for some funny memes? Yep, they’re about **agents** too.

Ready to read some mind-blowing **Medium article**? You guessed it - **agents** once more! 😅

Let’s face it, agents are one of the hottest topics right now, but let’s pause one second and ask: **what** **exactly is an agent**?

In this 5-lessons series, I’ll walk you through everything I’ve learned about agents, covering both the theory and practical implementation. To kick things off, we’ll start with the fundamentals about agents and their basic components.

**Ready to begin? Welcome to Lesson 0!**

---

![](https://substackcdn.com/image/fetch/$s_!o2c6!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F521efef4-895e-4d60-8120-19dfe2d7a7ef_1080x1080.png)

A few years ago, if you asked my about agents, the first thing that would pop into my mind was probably *Agent Smith* from *The Matrix*.

Fast forward to today, and thanks to frameworks like **LangChain**, **LlamaIndex** and **CrewAI,** anyone can build fully functional agents with just a few lines of code.

This is amazing, but it also comes with risks. The layers of abstractions can make it easy to lose sight of what’s happening under the hood.

So, before we start creating our first Crews and LlamaIndex Workflows, let’s take a step back and try to understand what an agent really is.

**Time for a a visual metaphor.**

---

## The Agent’s Brain

This might seem obvious, but it’s worth emphasizing: an agent is essentially a set of *functionalities* or *abstractions* layered on top of an LLM. Without the LLM powering it, the **agent** would be **useless**.

You can think of it like the human brain—if your brain were removed, you’d be out of the game (unless you’re some kind of zombie).

But sadly, a brain in a jar isn’t enough.

We need ways for this “brain” to interact with the external world: to gather information, make plans, reason, and act.

That’s where the **additional components** come in.

---

## The Agent’s Components

### 💠 Planning Capabilities

The ability to break complex tasks into smaller ones, manage subgoals and even reflect on the results and do self-criticism (more on this in future lessons 😜).

We humans are good at these tasks - well, maybe not so much at self-criticism - but how can we enable agents to do the same?

There’s a growing arsenal of techniques for planning and reasoning - expanding exponentially, I might add - but here are some interesting examples:

- [Chain of Thoughts](https://arxiv.org/abs/2201.11903) (CoT)
- [CoT-SC](https://arxiv.org/abs/2203.11171)
- [Tree of Thoughts](https://arxiv.org/abs/2305.10601) (ToT)
- [ReAct](https://arxiv.org/abs/2210.03629)

![](https://substackcdn.com/image/fetch/$s_!lbr4!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fa1f97f50-563c-488b-b084-0bccbfbb9f1c_648x648.gif)

If you look at the diagram above, you might notice that the ReAct technique is missing! Don’t worry, that’s intentional, it’s just that I didn’t want to spoil you the surprise 🤣

**ReAct** is one of the most important planning techniques, and we’ll dive into it in detail in **Lesson 3**, which covers the **Planning Pattern**.

### 💠 Memory Capabilities

A key component of an agent is its **memory**.

The agent needs to be able to “remember” previous thoughts or interactions to plan, act on the external world or even have a simple chat with us! - imagine trying to have a conversation without recalling anything your interlocutor just said …

We can divide memory into **two groups**:

#### 🔹 Short-term memory

The most immediate memory, which for an LLM would be the context window.

![](https://substackcdn.com/image/fetch/$s_!Tb2v!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fd3516a71-dd83-4f19-a3bf-67cdb926fd1e_1050x552.png)

Image from Donato Riccio’s amazing Medium article: https://towardsdatascience.com/extending-context-length-in-large-language-models-74e59201b51f

#### 🔹 Long-term memory

The type of memory the agent would need to retain information for a (potentially) infinite amount of time. It’s also necessary for accessing information the LLM has not stored in its weighs.

**Vector Databases** such as **Qdrant**, **Weaviate** or **Pinecone** (among others) are good examples of these long-term memory solutions.

![What is a Vector Database & How Does it Work? Use Cases + Examples |  Pinecone](https://substackcdn.com/image/fetch/$s_!ZLPM!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F1a8a6a75-d95d-40ce-b267-aaf56f5db440_1399x537.png)

Image from Pinecone’s blog: https://www.pinecone.io/learn/vector-database/

### 💠 Tools

So, up to this point, we have an agent with **memory** and **reasoning capabilities** but … what use would it be if it cannot interact with the external world? 🌍

With **tools**, the agent learns to call external APIs and services for retrieving information that is missing from the model weights (e.g. latest results of UEFA Euro 2024 ⚽ ) and acting on external systems (e.g. adding rows to a MySQL database).

We’ll cover tools in detail in **Lesson 2** of this series, where we’ll even **implement one from scratch! 😮**

---

## To recap

As you can see, despite all the hype around these systems, **agents are pretty simple**! 🙌

All you need is an LLM as the “brain”, a mechanism to reason, a way to “remember” things and tools to interact with the external world!

If you like this post, I can assure you that you’ll love the 4 posts that I have prepared for you. Code, videos, etc. All the resources you’ll need to become an “agentic ninja” 🥷

**See you next week friend! 👋**

Miguel