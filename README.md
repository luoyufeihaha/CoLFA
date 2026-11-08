# CoLFA: Collaboration-Guided Lightweight Failure Attribution for LLM-Based Multi-Agent Systems

This repository accompanies the paper **CoLFA: Collaboration-Guided Lightweight Failure Attribution for LLM-Based Multi-Agent Systems**.

> **Status.** The code is being organized and will be released in this repository. The paper link will be added once it is available.

## Overview

Failure attribution in LLM-based multi-agent systems aims to identify the **decisive error step**, the earliest erroneous step that plays a decisive role in the final failure, and the **responsible agent**. The final result of a multi-agent system is formed progressively through the division of work among agents, handoffs, and subsequent processing. An error may be introduced upstream and become apparent only after further transfer and processing, or it may arise from downstream interpretation or processing. Failure attribution therefore requires tracing how the erroneous result was formed.

Collaboration makes this tracing difficult in two ways:

- **Interleaved Steps.** Steps involving task requirements, result production, and subsequent processing may belong to different agents, span multiple execution stages, and be interleaved with other work. The chronological order of the execution trace does not readily reveal these connections.
- **Cluttered Content.** The evidence needed for attribution is mixed with extensive reasoning, actions, and environment feedback from different agents, and the same content may be repeated, paraphrased, or modified as it passes across steps.

Existing methods either analyze the complete trace directly, with low cost but limited accuracy, or rely on candidate search and multiple rounds of verification, with much higher token cost and limited accuracy gains. CoLFA aims to locate decisive error steps more accurately while reducing token cost.

## Core Idea

The collaborative process that gives rise to Interleaved Steps and Cluttered Content also leaves evidence for tracing error formation. We refer to these traces as **collaboration cues**:

- **Handoff records** capture the transfer of task requirements and intermediate results between agents.
- **Cross-step content correspondence** connects the production and subsequent processing of the same information.

CoLFA uses these cues to organize the attribution input according to how the final result is formed. It determines **which steps are examined** and **how their content is presented**, so that related executions can be examined together and each decision can be assessed in its own context.

## Method

CoLFA consists of two complementary components followed by a single attribution call.

### Handoff-Guided Step Expansion

Starting from the final result, CoLFA identifies the execution links of each step from handoffs (assignments, mentions of an agent, and changes in the speaking agent) and from recurring numerical values, identifiers, or specific text across steps. Along these links, it selects the **formation steps** used to examine how the result was formed, and sets each agent's expansion scope according to what needs to be checked:

- **Output steps.** All decision steps of the output agent, so that its processing can be examined in sequence.
- **Production steps.** Steps of the upstream agent reached through backward tracing over content correspondence, so that the production of handed-off content can be checked.
- **Supply steps.** Steps of the remaining agents directly linked to output or production steps, providing the requirements and inputs needed for these checks. Expansion stops here.

### Cross-Referenced Content Organization

Given the formation steps, CoLFA builds the attribution input from three parts:

- **Detailed records.** A separate record for each formation step, labeled with its original index and author and presenting its reasoning, action, and tool outputs or environment feedback.
- **Content references and step references.** Repeated text is replaced with references to its earlier occurrence, providing a shared point of comparison while reducing repeated presentation. Step references point to related earlier steps by other agents, so that each execution can be checked against its requirements and inputs.
- **Trace overview.** Brief chronological records of the complete execution, providing overall progress for assessing the role of an error in the final failure.

### One-Call Attribution

The model receives the task description, trace overview, and detailed records, and in a single call identifies the decisive error step among the formation steps. The author of the selected step is returned as the responsible agent.

## Evaluation

We evaluate CoLFA on all failed execution traces from [Who&When](https://arxiv.org/abs/2505.00212) and [LongRCA](https://arxiv.org/abs/2608.15242) with DeepSeek-V4.1-Flash, GLM-5.3-Flash, and Claude Sonnet 5.5 as backbone models. Across all evaluation settings, CoLFA achieves the highest exact step-localization accuracy with the lowest token consumption among the compared methods. Detailed results are reported in the paper.

## Code Release

The following will be released in this repository:

- Implementation of CoLFA, including execution link identification, step expansion, and attribution input construction
- Judgment prompts for Who&When and LongRCA
- Evaluation scripts

## Citation

Citation information will be added once the paper is publicly available.
