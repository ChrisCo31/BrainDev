---
title: "What is a Multiagent system?"
source: "https://theneuralmaze.substack.com/p/what-is-a-multiagent-system"
author:
  - "[[Miguel Otero Pedrido]]"
published: 2024-12-25
created: 2026-07-01
description: "Agentic Patterns From Scratch: Lesson 4"
tags:
  - "clippings"
---
Welcome to the final lesson of the **Agentic Patterns** series! By now, you’re already familiar with the **Reflection Agent**, **Tools**, and the **ReAct Technique.** But I know you’re probably curious about frameworks like [CrewAI](https://www.crewai.com/), [AutoGen](https://microsoft.github.io/autogen/0.2/), or [OpenAI Swarm](https://github.com/openai/swarm) - those nice tools for building **multiagent applications**.

These frameworks are just different takes on the **Multiagent Pattern**, where tasks are broken into smaller subtasks and handled by agents with specific roles - like a software engineer, a project manager, etc.

In this lesson, we’re taking it to the next level by **building a minimalistic multiagent framework from the ground up**.

Sounds fun? Let’s get started! 🚀

![](https://substackcdn.com/image/fetch/$s_!8uK4!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fc5eefb98-c3d1-4917-bec2-c6039b8ff081_1080x1080.png)

> This is the fourth lesson of the Agentic Patterns from Scratch course. This lesson builds on the theory and code covered in the previous ones, so be sure to check them out if you haven’t already!
> 
> - [Lesson Zero: What is an Agent?](https://theneuralmaze.substack.com/p/what-is-an-agent)
> - [Lesson One: The Reflection Pattern](https://theneuralmaze.substack.com/p/reflection-pattern-agents-that-think)
> - [Lesson Two: The Tool Pattern](https://theneuralmaze.substack.com/p/agent-tools-the-bridge-to-the-outside)
> - [Lesson Three: The Planning Pattern](https://theneuralmaze.substack.com/p/building-a-react-agent-from-scratch)

---

## A Minimalistic Multiagent Framework

The framework we’re about to develop is inspired by two fundamental concepts from CrewAI: the **[Crew](https://docs.crewai.com/concepts/crews)** and the **[Agent](https://docs.crewai.com/concepts/agents)**.

In CrewAI, a Crew represents a collaborative group of agents working in unison to accomplish a set of tasks. An Agent, on the other hand, represents the autonomous unit responsible for executing specific tasks.

![Introduction - CrewAI](https://substackcdn.com/image/fetch/$s_!xjws!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fc90f45a0-088e-4339-a308-713cd3502738_2128x1460.bin)

Introduction - CrewAI

I’ve also drawn inspiration from [Apache Airflow](https://airflow.apache.org/) ’s design philosophy, particularly the use of the **\>>** and **<<** operators to establish **dependencies between agents**. In this micro-CrewAI framework, **agents** are anologous to **Airflow Tasks**, while the **Crew** corresponds to an **Airflow DAG**.

![DAGs — Airflow Documentation](https://substackcdn.com/image/fetch/$s_!oo1X!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F107ce94d-9fb6-41ad-a2af-b3dd5293805c_1041x367.png)

DAGs — Airflow Documentation

Let’s begin with the Agent class 👇

## The Agent Class

First of all, we need an **Agent Class**. This class will represent an Agent, incorporating the **ReAct technique internally** (check [Lesson 3](https://theneuralmaze.substack.com/p/building-a-react-agent-from-scratch) if you want to see this technique in detail!). To view the **complete implementation**, feel free to [check out the repository](https://github.com/neural-maze/agentic_patterns/blob/main/src/agentic_patterns/multiagent_pattern/agent.py).

Let’s create an example agent to demonstrate how it works.

![](https://substackcdn.com/image/fetch/$s_!VG_C!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F922c7675-b0ac-48cb-b2ea-2bbbcd6baa39_3621x1816.png)

You can also equip the agent with tools, like we did in [Lesson 2](https://theneuralmaze.substack.com/p/agent-tools-the-bridge-to-the-outside). As a simple example, let’s build a tool that writes text into a CSV file.

![](https://substackcdn.com/image/fetch/$s_!oXte!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F8ba16741-c089-43b5-9786-f303ca275cbc_3692x2992.png)

As you can imagine, running this **Tool Agent** will create a new file named **tool\_agent\_example.txt** in the current directory, which will contain the text " **“This is a Tool Agent”.**

Now that we know how the Agent class works, let’s see how to **define Agent dependencies.**

## Defining Agent Dependencies

Suppose we want to define two agents, where the second agent depends on the first agent.

![](https://substackcdn.com/image/fetch/$s_!2YwG!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fbcc84dd9-c9d0-4e65-a6d3-4ad7ef8bfba1_3621x1564.png)

We can define the agent dependencies using the **\>>** operator.

```markup
agent_1 >> agent_2
```

This means **agent\_2** depends on **agent\_1**. We can check the dependencies and dependents of both agents.

![](https://substackcdn.com/image/fetch/$s_!AgvG!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F0cbf9022-e34b-4f94-85bd-7c34b1101d8f_3740x808.png)

Now, if we run **agent\_1**, the results will be added to **agent\_2** 's context. Then, when we run **agent\_2**, it will use the context received from **agent\_1** to generate its output.

![](https://substackcdn.com/image/fetch/$s_!tBZ_!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F0b48e2f7-ca50-413d-a09b-843401488d72_1772x1160.png)

## The Crew

Having defined the Agents and their dependencies, the only missing piece is the **Crew**, that is, the session object that will enable us to orchestrate the Agents. To view the **complete implementation**, feel free to [check out the repository](https://github.com/neural-maze/agentic_patterns/blob/main/src/agentic_patterns/multiagent_pattern/crew.py).

Our Crew will take care of running the Agents in the correct order, applying an algorithm called [Topological Sorting](https://en.wikipedia.org/wiki/Topological_sorting). It also provides a way to visualize the graph of dependencies, as we’ll see below.

Let’s see the Crew in action.

![](https://substackcdn.com/image/fetch/$s_!LVYN!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F074cd8e8-0ac9-4e52-900e-4ede7dd24b2a_4746x2672.png)

If we want to see the dependency graph, we can simple run **crew.plot()**. This command will generate a diagram like the following.

![](https://substackcdn.com/image/fetch/$s_!y-k5!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F507bad30-c364-4d0c-a518-1af59b4a98c9_416x375.png)

After that, we just need to run the crew and wait for th results!

```markup
crew.run()
```

You should see an output like the following, where the **Poet Agent** will generate a poem about life, the **Poem Translator Agent** will translate the poem into Spanish (yes, I’m from Spain 😛) and finally, the **Writer Agent** will write the translated poem into a **poem.txt** file!

![](https://substackcdn.com/image/fetch/$s_!Ebd6!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F0dc824e6-d61a-497a-9053-d9e8722b551b_1806x1314.png)

And that’s it! We actually got a multiagent framework up and running! I’d say this definitely calls for a meme … 🤣

![](https://substackcdn.com/image/fetch/$s_!EAev!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fcf211ca1-9685-4a56-9c31-525eec020c32_500x500.jpeg)

---

If you prefer video lectures, I also have a YouTube video covering the **Multiagent Pattern**! 👇

![](https://www.youtube.com/watch?v=os22Q7nEXPA)

Merry Christmas! 🎅