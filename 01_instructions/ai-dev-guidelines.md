# AI Development Guidelines

This document outlines the development process to ensure it is reproducible for other Feature Requests (FRs).

## Phase 1: Implementation Planning with AI Assistance

After grooming and refining all user stories and requirements, including acceptance criteria, and placing them in the [Requirements](/../requirements) directory, an implementation plan should be drafted by an AI and reviewed for logic and comprehensiveness by human eyes. It is recommended to add all user stories to the repository as Markdown files to serve as the foundation for an [Implementation Plan](../requirements/implementation-plan.md). It is recommended to use "Ask" mode for this step.

## Phase 2: Automated Code Development

Based on the [Implementation Plan](../requirements/implementation-plan.md), GitHub Copilot can begin development in *Edit* or *Agent* mode, depending on the human developers appetite for control vs automation. *Agent* will provide a lot of automated coding, directory and file creation, as well as autonomous codebase research ind one go and offer a large chunk of work at the end of an iteration. *Edit* will take existing steps and suggest smaller increments of changes specific to which files are provided.
When using *Agent*, carefully review the proposal for logical errors, security vulnerabilities, or other known anti-patterns that a human developer might identify but an AI might not (yet).

## Phase 3: Result Documentation & Retrospective

Check the directory [Results](../results) for the five proposed documentation steps.

### 01-Requirement Deviations

Requirements cannot be perfect and might be deviated from during development. It is important to document this and to let your PO and QA colleagues know why that makes sense.

### 02-Definition-of-Done

The DoD will help you make sure that all ACs and team- or organization-wide agreements have been considered. It's not always sensible to follow these strictly, but they serve as good cornerstones to help you work in a high-quality way.

### 03-Lessons Learned

For personal and team development, it is advisable to write down interesting lessons, new techniques, identified issues, and general notes concerning cooperation in the team or adaptation to the guidelines and process. In Scrum, we often speak of this as "scrumming your scrum".

### 04-Peer Review Checklist

Help your fellow peer reviewer to guide their thoughts along the most relevant challenges of the development. This will make it more likely to get quick and productive feedback and prevent overloading them with energy-consuming comprehensive tasks. It'll get them onboard quickly and give them something to hold on to and get started.

### 05-Success Story

Often you need to or want to present your feature to the team or stakeholders. It makes sense to write down the most valuable aspects for the product, the improvement of the process, toolset or internal system cohesion, and potential for other team members to reuse your work to speed up team velocity.
