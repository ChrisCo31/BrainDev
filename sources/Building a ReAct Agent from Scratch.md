---
title: "Building a ReAct Agent from Scratch"
source: "https://theneuralmaze.substack.com/p/building-a-react-agent-from-scratch"
author:
  - "[[Miguel Otero Pedrido]]"
published: 2024-12-18
created: 2026-07-01
description: "Agentic Patterns From Scratch: Lesson 3"
tags:
  - "clippings"
---
In previous lessons, we learned how [agents can reflect](https://theneuralmaze.substack.com/p/reflection-pattern-agents-that-think) and [use tools to interact with the outside world](https://theneuralmaze.substack.com/p/agent-tools-the-bridge-to-the-outside). **But what about planning?** How can agents determine the **sequence of steps needed to achieve a larger goal**?

This is where the **Planning Pattern** comes into play. It enables an LLM to break down a complex task into smaller, manageable subgoals without losing track of the overall objective.

The **ReAct technique** - short for **Reason + Act** - is a paradigmatic example of this pattern. In this lesson, you’ll learn how this technique works and implement a **ReAct Agent** from scratch using **Python** and **Groq LLMs**.

**Ready? Let’s go! 👇**

![](https://substackcdn.com/image/fetch/$s_!jdPC!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F81f01126-beec-499d-bb49-d075a05667e5_1080x1080.png)

> This is the **third lesson** of the **Agentic Patterns from Scratch** course. This lesson builds on the theory and code covered in the previous ones, so be sure to check them out if you haven’t already!
> 
> - [Lesson Zero: What is an Agent?](https://theneuralmaze.substack.com/p/what-is-an-agent)
> - [Lesson One: The Reflection Pattern](https://theneuralmaze.substack.com/p/reflection-pattern-agents-that-think)
> - [Lesson Two: The Tool Pattern](https://theneuralmaze.substack.com/p/agent-tools-the-bridge-to-the-outside)

---

## The ReAct System Prompt

Just like with the Tool Pattern, the ReAct technique also requires a System Prompt. This prompt is quite similar, but it also describes the ReAct loop to ensure the LLM understands the three operations it’s allowed to perform:

- **Thought** - The LLM will **think** about which action to take
- **Action** - The LLM will use a **Tool** to interact with the environment and take **action**
- **Observation** - The LLM will **observe** the Tool’s output and reflect on the next step to take

Another key difference from the Tool Pattern System Prompt is that we’ll enclose all messages within tags, like this: **< >**.

While it’s possible to implement the ReAct logic without these tags, I’ve found that using them makes it easier for the LLM to follow the instructions.

Enough theory for now! **Here’s the prompt** **👇**

![](https://substackcdn.com/image/fetch/$s_!8It1!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F76e7a981-0fe1-4694-9d8e-d5e2c3c9dc11_5233x3328.png)

If you take a look at the prompt, you’ll notice that we need a set of tools, which are enclosed within the **\<tools>\</tools>** tags. The example we’ll build involves using **three tools**, as shown in the code snippet below.

![](https://substackcdn.com/image/fetch/$s_!xLjx!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F2fe610a7-7e32-4b25-bd6f-820ce1f76f4c_3692x4756.png)

Remember that the **tool decorator** - implemented in the previous lesson - allows us to automatically convert any Python function into a Tool.

Next, we simply concatenate the tool signatures and add them to the System Prompt.

![](https://substackcdn.com/image/fetch/$s_!I8Mg!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F809a4407-7332-40e5-8d26-c56961ba897f_4840x640.png)

With the System Prompt complete, it’s time to begin the first iteration of the ReAct loop!

## ReAct Loop - Choosing the First Tool

The question we are going to answer is the following:

```markup
I want to calculate the sum of 1234 and 5678 and multiply the result by 5. Then, I want to take the logarithm of this result
```

As you can see, the question is easily solved using the provided tools in the correct order but, can the agent do the same? Let’s generate the first completion and find out.

![](https://substackcdn.com/image/fetch/$s_!tHp-!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fb55b917d-d2b2-460d-acc8-ded19cb1cd38_2653x2656.png)

The output generated is a **\<thought>** and a **<tool\_call>**, that tells the Agent which tool to use.

![](https://substackcdn.com/image/fetch/$s_!WjZL!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F9b32af15-fe7b-4d86-90e1-689e7fddd81b_4875x640.png)

## ReAct Loop - Running the First Tool

Since we have the **<tool\_call>**, it’s very easy to evaluate the Tool to get the result - this is basically the action the agent is going to take.

![](https://substackcdn.com/image/fetch/$s_!cpCf!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe83f5a3b-33c9-4536-83e7-8f6711aa8813_3083x1312.png)

The Tool result is just the sum of **1234** and **5678**, that is **6912**.

## ReAct Loop - Choosing the Second Tool

If you look closely at the content we were adding to the **chat\_history**, you’ll notice that we’re including the Tool result (**6912**)as an **\<observation>.** This allows the LLM to think, once again, about the next Tool to use.

Running a completion as we did before, we should get something like this, where the LLM correctly selects the multiplication tool to multiply the previous result by 5.

![](https://substackcdn.com/image/fetch/$s_!unlE!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F71b3d522-3327-47a6-b835-79e29111a3c8_3800x640.png)

## ReAct Loop - Running the Second Tool

We proceed exactly the same way as before. The tool result in this case is **34560.**

## ReAct Loop - Choosing the Third Tool

After running the next completion, we’ll get the following output.

![](https://substackcdn.com/image/fetch/$s_!is-y!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fc5f7a706-072f-4b8c-a731-6833d4b4658d_3764x640.png)

After having computed the sum and the multiplication, the only operation left is the logarithm, implemented in the **compute\_log** tool.

## ReAct Loop - Running the Third Tool and Final Output

After running the logarithm tool and adding the result to the **chat\_history**, it’s time to run the last completion. The generated output will contain a **\<response>** tag, which marks the end of the loop and contains the final answer.

![](https://substackcdn.com/image/fetch/$s_!IIrp!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F9d279b3c-61b1-4ac3-8dea-162e6aff4d36_4804x640.png)

Our LLM says **10.45**. And that’s … correct! - if you don’t believe me, go ahead and check with your calculator 😂

## The ReAct Agent

As we did in previous posts, you can achieve the same results using my **agentic\_patterns** library, which implements the code above **“the good way”**.

I’ve created a **ReactAgent** class that encapsulates the ReAct loop, accepting a list of available tools. [Check it out here!](https://github.com/neural-maze/agentic_patterns/blob/main/src/agentic_patterns/planning_pattern/react_agent.py)

![](https://substackcdn.com/image/fetch/$s_!mTVz!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F769247bf-0d20-421f-935f-0970fcb7984c_5341x976.png)

---

If you prefer video lectures, I also have a YouTube video covering the **Planning Pattern**! 👇

![](https://www.youtube.com/watch?v=4xkC069wrw8)

That’s all for today! Next week, we’ll talk about the **MultiAgent Pattern**.

**Happy planning! 👋**

Miguel