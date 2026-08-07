y# AI Prompt & Context Engineering Curriculum

# Goal

Become effective at using modern AI systems for:

* Prompt engineering
* Context engineering
* Research
* Technical learning
* Coding/debugging
* Document/PDF analysis
* Tool-using AI
* Agentic workflows
* Evaluation and reliability
* Building reusable AI workflows rather than one-off prompts

---

# Phase 1 — Prompt Engineering Foundations

## Day 1 — OpenAI Foundations

* [ ] Complete [OpenAI Academy Courses](https://academy.openai.com/pages/courses)
* [ ] Focus on AI Foundations
* [ ] Focus on Applied AI Foundations
* [ ] Learn how instructions, context, constraints, and output requirements affect model behavior
* [ ] Create one Obsidian note titled `Prompt Engineering - Core Principles`
* [ ] Record examples of good vs bad instructions

### Concepts to understand

* [ ] Objective / task definition
* [ ] Explicit constraints
* [ ] Providing relevant context
* [ ] Output formatting
* [ ] Giving examples
* [ ] Asking the model to verify its work
* [ ] Separating instructions from source material

---

## Day 2 — OpenAI Prompting Practice

* [ ] Continue [OpenAI Academy](https://academy.openai.com/pages/courses)
* [ ] Study material related to workflows and effective AI usage
* [ ] Take 5 prompts you normally use and rewrite them systematically

Use this structure:

```text
Goal:
Context:
Input:
Constraints:
Expected Output:
Verification:
```

* [ ] Compare the original result against the structured-prompt result
* [ ] Record which changes actually improved output quality

---

# Phase 2 — Systematic Prompt Engineering

## Day 3 — DeepLearning.AI + OpenAI

* [ ] Complete [ChatGPT Prompt Engineering for Developers](https://learn.deeplearning.ai/courses/chatgpt-prompt-eng/information)
* [ ] Understand iterative prompt development
* [ ] Understand summarization
* [ ] Understand information extraction
* [ ] Understand transformation
* [ ] Understand expansion
* [ ] Understand multi-stage workflows

### Practice

* [ ] Build one prompt for explaining a technical topic
* [ ] Build one prompt for summarizing a research paper
* [ ] Build one prompt for reviewing code
* [ ] Build one prompt for comparing two architectural designs

---

# Phase 3 — Anthropic Prompt Engineering

## Day 4 — Anthropic Interactive Tutorial Part 1

* [ ] Start [Anthropic Interactive Prompt Engineering Tutorial](https://github.com/anthropics/prompt-eng-interactive-tutorial)
* [ ] Complete basic prompt structure sections
* [ ] Complete clarity/directness exercises
* [ ] Complete instruction separation exercises
* [ ] Complete formatting exercises
* [ ] Complete examples / few-shot prompting exercises

### Practice

* [ ] Rewrite one real prompt after each major concept
* [ ] Save successful patterns in Obsidian
* [ ] Do not save prompts blindly; write why each technique works

---

## Day 5 — Anthropic Interactive Tutorial Part 2

* [ ] Continue [Anthropic Interactive Prompt Engineering Tutorial](https://github.com/anthropics/prompt-eng-interactive-tutorial)
* [ ] Study hallucination reduction
* [ ] Study complex prompt construction
* [ ] Study chained prompting
* [ ] Study tool-use prompting
* [ ] Study retrieval/search prompting

### Create this note

* [ ] Create `AI Failure Modes.md`

Track:

```text
Missing context
Incorrect assumptions
Hallucination
Poor source selection
Incorrect interpretation
Instruction conflict
Overly broad prompt
Context overload
Poor output specification
Failure to verify
```

---

# Phase 4 — Stop Learning "Prompt Tricks"

## Day 6 — Real Task Engineering

Do **not** take another beginner prompt course today.

Choose real problems you actually encounter.

* [ ] Technical research task
* [ ] Research-paper understanding task
* [ ] Coding/debugging task
* [ ] Computer-architecture learning task
* [ ] Project-planning task

For every task define:

```text
Goal
↓
Required Context
↓
Source of Truth
↓
Constraints
↓
Tools
↓
Expected Output
↓
Verification
```

* [ ] Run each task once with a simple prompt
* [ ] Run it again with engineered context
* [ ] Compare the results
* [ ] Record what improved
* [ ] Record what made results worse

---

# Phase 5 — Context Engineering

## Day 7 — Context Engineering Fundamentals

* [ ] Read [LangChain Context Engineering Guide](https://docs.langchain.com/oss/python/langchain/context-engineering)

Focus especially on:

* [ ] System instructions
* [ ] Messages
* [ ] Tool context
* [ ] Runtime context
* [ ] State
* [ ] Memory
* [ ] Structured outputs
* [ ] Context selection
* [ ] Context compression
* [ ] Context summarization

### Understand this principle

```text
More context ≠ better context.

Relevant context > maximum context.
```

* [ ] Identify information that should always be provided
* [ ] Identify information that should be retrieved only when necessary
* [ ] Identify information that should be discarded

---

## Day 8 — Deep Agents

* [ ] Start [LangChain Academy - Introduction to Deep Agents](https://academy.langchain.com/courses/foundation-introduction-to-deepagents)
* [ ] Study agent architecture
* [ ] Study system prompts
* [ ] Study tools
* [ ] Study messages and threads
* [ ] Study state management
* [ ] Study persistence/checkpointing

---

## Day 9 — Context Management

* [ ] Continue [Introduction to Deep Agents](https://academy.langchain.com/courses/foundation-introduction-to-deepagents)
* [ ] Focus heavily on the Context Management module
* [ ] Study summarization
* [ ] Study context offloading
* [ ] Study memory
* [ ] Study skills
* [ ] Study selective retrieval

### Build a context hierarchy

* [ ] Create `Context Engineering.md`

Use:

```text
1. Permanent instructions
2. User preferences
3. Task-specific instructions
4. Current working state
5. Retrieved documents
6. Tool results
7. Previous relevant decisions
8. Temporary scratch information
```

Then determine:

```text
What must remain?
What can be summarized?
What should be retrieved?
What should be discarded?
```

---

# Phase 6 — Tool Use and AI Workflows

## Day 10 — Tool-Oriented AI

* [ ] Review the tool-use sections from [Anthropic Courses](https://github.com/anthropics/courses)
* [ ] Study how models decide when to use tools
* [ ] Understand web-search workflows
* [ ] Understand document retrieval
* [ ] Understand code execution
* [ ] Understand APIs
* [ ] Understand structured tool outputs

### Practice workflow

Build:

```text
Question
↓
Determine whether external information is needed
↓
Search / Retrieve
↓
Filter useful context
↓
Reason over evidence
↓
Generate answer
↓
Verify against sources
```

* [ ] Test this workflow with one technical research question

---

# Phase 7 — Evaluation

## Day 11 — Anthropic Evaluation

* [ ] Explore [Anthropic Educational Courses](https://github.com/anthropics/courses)
* [ ] Complete material related to prompt evaluations
* [ ] Learn why manually judging one answer is insufficient
* [ ] Learn test-case construction
* [ ] Learn expected-output criteria
* [ ] Learn regression testing for prompts

### Create an evaluation sheet

For every important AI workflow measure:

```text
Correctness
Completeness
Source accuracy
Instruction following
Formatting
Hallucination
Unnecessary information
Consistency
```

* [ ] Score outputs from 1–5 on each criterion

---

## Day 12 — Reliable AI Systems

* [ ] Complete relevant sections of [LangChain Academy - Building Reliable Agents](https://academy.langchain.com/courses/building-reliable-agents)
* [ ] Study datasets
* [ ] Study experiments
* [ ] Study tracing / observability
* [ ] Study code-based evaluation
* [ ] Study LLM-as-judge evaluation
* [ ] Study pairwise evaluation
* [ ] Study production monitoring

### Important concept

* [ ] Understand the difference between `prompt optimization` and `system optimization`

Think in terms of:

```text
Model
+
Prompt
+
Context
+
Retrieval
+
Tools
+
Memory
+
Workflow
+
Evaluation
=
AI System
```

---

# Phase 8 — Building LLM Applications

## Day 13 — Full Stack Deep Learning

* [ ] Start [Full Stack Deep Learning - LLM Bootcamp](https://fullstackdeeplearning.com/llm-bootcamp/)
* [ ] Study LLM application architecture
* [ ] Study prompting in production systems
* [ ] Study retrieval-based systems
* [ ] Study application design
* [ ] Study limitations of LLM applications

You do **not** need to finish the entire bootcamp immediately.

Use it as the transition from:

```text
AI User
→
AI Power User
→
AI Workflow Designer
→
AI System Engineer
```

---

# Phase 9 — Final Project

## Day 14 — Build Your Own AI Workflow

Choose one workflow you regularly need.

Recommended project:

* [ ] Build a `Technical Research Assistant Workflow`

Design:

```text
USER QUESTION
      ↓
DEFINE EXACT OBJECTIVE
      ↓
IDENTIFY REQUIRED KNOWLEDGE
      ↓
CHECK AVAILABLE CONTEXT
      ↓
RETRIEVE MISSING INFORMATION
      ↓
RANK SOURCE RELIABILITY
      ↓
BUILD MINIMAL RELEVANT CONTEXT
      ↓
ANALYZE
      ↓
GENERATE STRUCTURED ANSWER
      ↓
VERIFY CLAIMS
      ↓
CHECK REQUIREMENTS
      ↓
FINAL RESPONSE
```

* [ ] Test it on 5 different research questions
* [ ] Record failures
* [ ] Modify the workflow
* [ ] Test again
* [ ] Create version `v1`
* [ ] Create version `v2` after evaluation

---

# Phase 10 — Build Your Personal AI Playbook

## Do this after the 14-day curriculum

* [ ] Create folder `AI Playbook`

Create these notes:

* [ ] `00 - AI Usage Principles.md`
* [ ] `01 - Prompt Engineering.md`
* [ ] `02 - Context Engineering.md`
* [ ] `03 - Research Workflow.md`
* [ ] `04 - Technical Learning Workflow.md`
* [ ] `05 - Research Paper Workflow.md`
* [ ] `06 - Coding and Debugging Workflow.md`
* [ ] `07 - Architecture Analysis Workflow.md`
* [ ] `08 - Document Analysis Workflow.md`
* [ ] `09 - Project Planning Workflow.md`
* [ ] `10 - Evaluation Framework.md`
* [ ] `11 - AI Failure Modes.md`
* [ ] `12 - Useful Prompt Patterns.md`
* [ ] `13 - Tool Selection Rules.md`
* [ ] `14 - Context Management Rules.md`

---

# Courses — Master Checklist

## Essential

* [ ] [OpenAI Academy](https://academy.openai.com/pages/courses)
* [ ] [ChatGPT Prompt Engineering for Developers — DeepLearning.AI + OpenAI](https://learn.deeplearning.ai/courses/chatgpt-prompt-eng/information)
* [ ] [Anthropic Interactive Prompt Engineering Tutorial](https://github.com/anthropics/prompt-eng-interactive-tutorial)
* [ ] [LangChain Context Engineering Guide](https://docs.langchain.com/oss/python/langchain/context-engineering)
* [ ] [LangChain Academy — Introduction to Deep Agents](https://academy.langchain.com/courses/foundation-introduction-to-deepagents)
* [ ] [LangChain Academy — Building Reliable Agents](https://academy.langchain.com/courses/building-reliable-agents)

## Advanced / After Essentials

* [ ] [Anthropic Courses Repository](https://github.com/anthropics/courses)
* [ ] [Full Stack Deep Learning — LLM Bootcamp](https://fullstackdeeplearning.com/llm-bootcamp/)

---

# What I Would NOT Spend Much Time On

* [ ] Do not memorize hundreds of "magic prompts"
* [ ] Do not collect prompts beginning with `Act as an expert…`
* [ ] Do not spend weeks watching introductory prompt-engineering videos
* [ ] Do not assume longer prompts are automatically better
* [ ] Do not put every available document into the context
* [ ] Do not rely on one successful output as proof that a prompt works
* [ ] Do not treat the model as the entire AI system
* [ ] Do not optimize wording before checking whether the correct context was provided

---

# Skills I Should Have at the End

* [ ] Clearly define an AI task
* [ ] Identify the minimum required context
* [ ] Separate instructions from data
* [ ] Create effective system/task instructions
* [ ] Provide useful examples
* [ ] Define structured outputs
* [ ] Break complex tasks into stages
* [ ] Determine when web search is necessary
* [ ] Determine when document retrieval is necessary
* [ ] Determine when code execution is necessary
* [ ] Design tool-using workflows
* [ ] Manage long contexts
* [ ] Summarize context without losing important information
* [ ] Design short-term and long-term memory
* [ ] Prevent irrelevant context from polluting the task
* [ ] Construct evaluation datasets
* [ ] Evaluate output systematically
* [ ] Identify hallucinations
* [ ] Build repeatable AI workflows
* [ ] Build reusable context templates
* [ ] Design basic AI agents
* [ ] Think about AI usage as a **system architecture problem**, rather than merely writing better prompts

The most important transition in this curriculum is:

```text
Prompt Engineering
        ↓
Context Engineering
        ↓
Workflow Engineering
        ↓
Evaluation
        ↓
Reliable AI Systems
```

That is the direction I would recommend prioritizing in 2026 rather than trying to become exceptionally good only at writing individual prompts.
