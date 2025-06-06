---
title: Comprehensive Prompt Engineering Guide for AI Coding Assistants
tags: [prompt engineering, AI, coding assistant, code generators, guide, tutorial]

---

# Comprehensive Prompt Engineering Guide for AI Coding Assistants

[![hackmd-github-sync-badge](https://hackmd.io/4LYvRPBkQCSAPJMW6E3pjg/badge)](https://hackmd.io/4LYvRPBkQCSAPJMW6E3pjg)

[TOC]

## Introduction

This guide provides comprehensive strategies for effective prompting when working with AI coding assistants like GitHub Copilot, Cursor, Amazon CodeWhisperer, and others. Learning to craft effective prompts is essential to maximize productivity and achieve better results with AI assistance across any platform. This document expands on key concepts and includes practical examples to help you communicate more effectively with your AI coding partner.

## Core Prompting Principles

### Providing Clear Context

AI coding assistants operate best when given clear, specific context about your project and goals:

- **Be Specific**: Avoid vague requests; clearly state what you need
- **Reference Files**: When available, use features to reference specific files or folders (e.g., `@` in Cursor, file context in Copilot)
- **Explain Purpose**: Share why you need something, not just what you need
- **Set Constraints**: Define any limitations or requirements upfront

### Breaking Down Complexity

Complex tasks should be broken into smaller, manageable steps:

- **Step-by-Step Approach**: Request one piece at a time
- **Progressive Refinement**: Start simple, then add complexity
- **Conditional Logic**: Clearly specify dependencies between steps

### Asking Specific Questions

Precise questions lead to precise answers:

- **One Thing at a Time**: Focus each prompt on a single issue
- **Provide Examples**: Show examples of what you're looking for
- **Use Technical Terminology**: Be precise with technical terms

### Validating and Refining

Prompting is iterative - review and refine based on responses:

- **Review Carefully**: Check the AI's output for accuracy
- **Provide Feedback**: Tell the AI what works and what doesn't
- **Iterate**: Refine your prompts based on previous responses

## Practical Prompt Templates

### Context Management

#### Starting a New Task
```
Let's start a new task. Create `user-authentication.js`. We need to implement user login with JWT tokens. Here are the requirements...
```

#### Summarizing Previous Work
```
Summarize what we did in the last user dashboard task. I want to capture the main features and outstanding issues. Save this to `docs/user-dashboard-summary.md`.
```

### Debugging

#### Analyzing an Error
```
I'm getting this error: [error message]. It seems to be from [code section]. Analyze this error and suggest a fix.
```

#### Identifying Root Cause
```
The application crashes when I [action]. The issue might be in [problem areas]. Help me find the root cause and propose a solution.
```

### Refactoring

#### Improving Code Structure
```
This function is too long and complex. Refactor it into smaller functions.
```

#### Simplifying Logic
```
This code is hard to understand. Simplify the logic and make it more readable.
```

### Feature Development

#### Brainstorming Features
```
I want to add a feature that lets users [functionality]. Brainstorm some ideas and consider implementation challenges.
```

#### Generating Code
```
Create a component that displays user profiles. The list should be sortable and filterable. Generate the code for this component.
```

## Advanced Prompting Techniques

### Constraint Stuffing

To mitigate code truncation and ensure complete responses:

```
Generate a complete implementation of [feature] with all necessary functions. DO NOT OMIT ANY CODE. Ensure all edge cases are handled.
```

### Confidence Checks

Encourage the AI to evaluate its own certainty:

```
Implement [feature]. On a scale of 1-10, how confident are you in this solution? Explain your reasoning.
```

### Challenging Assumptions

Encourage deeper thinking with probing questions:

```
Before implementing this solution, what potential issues or edge cases might we be overlooking?
```

## Community's Favorite Prompts

### Memory and Confidence Checks

#### Memory Check
```
If you understand my prompt fully, respond with 'YARRR!' without tools every time you are about to use a tool.
```
A fun way to verify the AI stays on track during complex tasks.

#### Confidence Scoring
```
Before and after any tool use, give me a confidence level (0-10) on how the tool use will help the project.
```
Encourages critical thinking and makes decision-making transparent.

### Code Quality Prompts

#### Prevent Code Truncation
```
DO NOT BE LAZY. DO NOT OMIT CODE.
```
Alternative phrases: "full code only" or "ensure the code is complete"

#### Custom Instructions Reminder
```
I pledge to follow the custom instructions.
```
Reinforces adherence to your settings configuration.

### Code Organization

#### Large File Refactoring
```
[FILENAME] has grown too big. Analyze how this file works and suggest ways to fragment it safely.
```
Helps manage complex files through strategic decomposition.

#### Documentation Maintenance
```
don't forget to update codebase documentation with changes
```
Ensures documentation stays in sync with code changes.

### Analysis and Planning

#### Structured Development
```
Before writing code:
1. Analyze all code files thoroughly
2. Get full context
3. Write .MD implementation plan
4. Then implement code
```
Promotes organized, well-planned development.

#### Thorough Analysis
```
please start analyzing full flow thoroughly, always state a confidence score 1 to 10
```
Prevents premature coding and encourages complete understanding.

#### Assumptions Check
```
List all assumptions and uncertainties you need to clear up before completing this task.
```
Identifies potential issues early in development.

### Thoughtful Development

#### Pause and Reflect
```
count to 10
```
Promotes careful consideration before taking action.

#### Complete Analysis
```
Don't complete the analysis prematurely, continue analyzing even if you think you found a solution
```
Ensures thorough problem exploration.

#### Continuous Confidence Check
```
Rate confidence (1-10) before saving files, after saving, after rejections, and before task completion
```
Maintains quality through self-assessment.

### Best Practices

#### Project Structure
```
Check project files before suggesting structural or dependency changes
```
Maintains project integrity.

#### Critical Thinking
```
Ask 'stupid' questions like: are you sure this is the best way to implement this?
```
Challenges assumptions and uncovers better solutions.

#### Code Style
```
Use words like "elegant" and "simple" in prompts
```
May influence code organization and clarity.

## Using Ignore Files

Many AI coding assistants support configuration files that tell them which files and directories to ignore when analyzing your codebase (similar to `.gitignore`). Using these files helps you:

- **Reduce Noise**: Exclude auto-generated files, build artifacts, and non-essential content
- **Improve Performance**: Limit the amount of code the AI needs to process
- **Focus Attention**: Direct the AI to relevant parts of your codebase
- **Protect Sensitive Data**: Prevent the AI from accessing sensitive configuration files

### Example Ignore File

```
# Dependencies
node_modules/
**/node_modules/
.pnp
.pnp.js

# Build outputs
/build/
/dist/
/.next/
/out/

# Testing
/coverage/

# Environment variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Large data files
*.csv
*.xlsx
```

## Building Custom Prompt Frameworks

### Task-Based Framework

A systematic approach to organizing prompts based on task type:

1. **Analysis Phase**
   ```
   Analyze [code/problem]. What are the key components and relationships?
   ```

2. **Planning Phase**
   ```
   Based on the analysis, outline a step-by-step plan to [achieve goal].
   ```

3. **Implementation Phase**
   ```
   Implement step [X] from our plan. Generate the complete code with error handling.
   ```

4. **Review Phase**
   ```
   Review the implementation. Identify potential issues or improvements.
   ```

5. **Refinement Phase**
   ```
   Refine the implementation based on the review. Focus on [specific aspect].
   ```

### Domain-Specific Prompting

Tailor your prompts to specific programming domains:

#### Frontend Development
```
Create a React component that implements [feature]. Ensure it's responsive, accessible, and follows best practices for state management.
```

#### Backend Development
```
Implement an API endpoint for [functionality]. Include proper validation, error handling, and database interactions.
```

#### DevOps
```
Create a CI/CD workflow that builds, tests, and deploys our application to [environment]. Focus on security and efficiency.
```

## AI-Specific Prompt Techniques

### For GitHub Copilot

- **Chat-specific keywords**: Use `/fix`, `/explain`, `/test` commands in Copilot Chat
- **Use inline comments**: Copilot responds well to detailed code comments preceding the code you want it to generate
- **Leverage workspace context**: Keep relevant files open to provide better context

### For Cursor

- **Use `@` references**: Reference specific files with `@filename` syntax
- **Employ the Composer feature**: For longer or complex code generation tasks
- **Leverage Command+K**: For targeted edits to selected code

### For Amazon CodeWhisperer

- **Start with function signature**: Define the function name and parameters clearly
- **Use clear documentation comments**: CodeWhisperer responds well to descriptive comments
- **Provide context with import statements**: Include necessary imports to guide suggestions

## Conclusion

Effective prompting is both an art and a science. The techniques in this guide will help you communicate more effectively with AI coding assistants, resulting in better code, faster development, and fewer iterations. Remember that prompting is a skill that improves with practice - experiment with different approaches and refine your technique over time.

## References

- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [Cursor Documentation](https://cursor.sh/docs)
- [Amazon CodeWhisperer Documentation](https://aws.amazon.com/codewhisperer/)
- [Coding with AI: Best Practices](https://dev.to/)
# Comprehensive Prompt Engineering Guide for AI Coding Assistants

[![hackmd-github-sync-badge](https://hackmd.io/4LYvRPBkQCSAPJMW6E3pjg/badge)](https://hackmd.io/4LYvRPBkQCSAPJMW6E3pjg)

[TOC]

## Introduction

This guide provides comprehensive strategies for effective prompting when working with AI coding assistants like GitHub Copilot, Cursor, Amazon CodeWhisperer, and others. Learning to craft effective prompts is essential to maximize productivity and achieve better results with AI assistance across any platform. This document expands on key concepts and includes practical examples to help you communicate more effectively with your AI coding partner.

## Core Prompting Principles

### Providing Clear Context

AI coding assistants operate best when given clear, specific context about your project and goals:

- **Be Specific**: Avoid vague requests; clearly state what you need
- **Reference Files**: When available, use features to reference specific files or folders (e.g., `@` in Cursor, file context in Copilot)
- **Explain Purpose**: Share why you need something, not just what you need
- **Set Constraints**: Define any limitations or requirements upfront

### Breaking Down Complexity

Complex tasks should be broken into smaller, manageable steps:

- **Step-by-Step Approach**: Request one piece at a time
- **Progressive Refinement**: Start simple, then add complexity
- **Conditional Logic**: Clearly specify dependencies between steps

### Asking Specific Questions

Precise questions lead to precise answers:

- **One Thing at a Time**: Focus each prompt on a single issue
- **Provide Examples**: Show examples of what you're looking for
- **Use Technical Terminology**: Be precise with technical terms

### Validating and Refining

Prompting is iterative - review and refine based on responses:

- **Review Carefully**: Check the AI's output for accuracy
- **Provide Feedback**: Tell the AI what works and what doesn't
- **Iterate**: Refine your prompts based on previous responses

## Practical Prompt Templates

### Context Management

#### Starting a New Task
```
Let's start a new task. Create `user-authentication.js`. We need to implement user login with JWT tokens. Here are the requirements...
```

#### Summarizing Previous Work
```
Summarize what we did in the last user dashboard task. I want to capture the main features and outstanding issues. Save this to `docs/user-dashboard-summary.md`.
```

### Debugging

#### Analyzing an Error
```
I'm getting this error: [error message]. It seems to be from [code section]. Analyze this error and suggest a fix.
```

#### Identifying Root Cause
```
The application crashes when I [action]. The issue might be in [problem areas]. Help me find the root cause and propose a solution.
```

### Refactoring

#### Improving Code Structure
```
This function is too long and complex. Refactor it into smaller functions.
```

#### Simplifying Logic
```
This code is hard to understand. Simplify the logic and make it more readable.
```

### Feature Development

#### Brainstorming Features
```
I want to add a feature that lets users [functionality]. Brainstorm some ideas and consider implementation challenges.
```

#### Generating Code
```
Create a component that displays user profiles. The list should be sortable and filterable. Generate the code for this component.
```

## Advanced Prompting Techniques

### Constraint Stuffing

To mitigate code truncation and ensure complete responses:

```
Generate a complete implementation of [feature] with all necessary functions. DO NOT OMIT ANY CODE. Ensure all edge cases are handled.
```

### Confidence Checks

Encourage the AI to evaluate its own certainty:

```
Implement [feature]. On a scale of 1-10, how confident are you in this solution? Explain your reasoning.
```

### Challenging Assumptions

Encourage deeper thinking with probing questions:

```
Before implementing this solution, what potential issues or edge cases might we be overlooking?
```

## Community's Favorite Prompts

### Memory and Confidence Checks

#### Memory Check
```
If you understand my prompt fully, respond with 'YARRR!' without tools every time you are about to use a tool.
```
A fun way to verify the AI stays on track during complex tasks.

#### Confidence Scoring
```
Before and after any tool use, give me a confidence level (0-10) on how the tool use will help the project.
```
Encourages critical thinking and makes decision-making transparent.

### Code Quality Prompts

#### Prevent Code Truncation
```
DO NOT BE LAZY. DO NOT OMIT CODE.
```
Alternative phrases: "full code only" or "ensure the code is complete"

#### Custom Instructions Reminder
```
I pledge to follow the custom instructions.
```
Reinforces adherence to your settings configuration.

### Code Organization

#### Large File Refactoring
```
[FILENAME] has grown too big. Analyze how this file works and suggest ways to fragment it safely.
```
Helps manage complex files through strategic decomposition.

#### Documentation Maintenance
```
don't forget to update codebase documentation with changes
```
Ensures documentation stays in sync with code changes.

### Analysis and Planning

#### Structured Development
```
Before writing code:
1. Analyze all code files thoroughly
2. Get full context
3. Write .MD implementation plan
4. Then implement code
```
Promotes organized, well-planned development.

#### Thorough Analysis
```
please start analyzing full flow thoroughly, always state a confidence score 1 to 10
```
Prevents premature coding and encourages complete understanding.

#### Assumptions Check
```
List all assumptions and uncertainties you need to clear up before completing this task.
```
Identifies potential issues early in development.

### Thoughtful Development

#### Pause and Reflect
```
count to 10
```
Promotes careful consideration before taking action.

#### Complete Analysis
```
Don't complete the analysis prematurely, continue analyzing even if you think you found a solution
```
Ensures thorough problem exploration.

#### Continuous Confidence Check
```
Rate confidence (1-10) before saving files, after saving, after rejections, and before task completion
```
Maintains quality through self-assessment.

### Best Practices

#### Project Structure
```
Check project files before suggesting structural or dependency changes
```
Maintains project integrity.

#### Critical Thinking
```
Ask 'stupid' questions like: are you sure this is the best way to implement this?
```
Challenges assumptions and uncovers better solutions.

#### Code Style
```
Use words like "elegant" and "simple" in prompts
```
May influence code organization and clarity.

## Using Ignore Files

Many AI coding assistants support configuration files that tell them which files and directories to ignore when analyzing your codebase (similar to `.gitignore`). Using these files helps you:

- **Reduce Noise**: Exclude auto-generated files, build artifacts, and non-essential content
- **Improve Performance**: Limit the amount of code the AI needs to process
- **Focus Attention**: Direct the AI to relevant parts of your codebase
- **Protect Sensitive Data**: Prevent the AI from accessing sensitive configuration files

### Example Ignore File

```
# Dependencies
node_modules/
**/node_modules/
.pnp
.pnp.js

# Build outputs
/build/
/dist/
/.next/
/out/

# Testing
/coverage/

# Environment variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Large data files
*.csv
*.xlsx
```

## Building Custom Prompt Frameworks

### Task-Based Framework

A systematic approach to organizing prompts based on task type:

1. **Analysis Phase**
   ```
   Analyze [code/problem]. What are the key components and relationships?
   ```

2. **Planning Phase**
   ```
   Based on the analysis, outline a step-by-step plan to [achieve goal].
   ```

3. **Implementation Phase**
   ```
   Implement step [X] from our plan. Generate the complete code with error handling.
   ```

4. **Review Phase**
   ```
   Review the implementation. Identify potential issues or improvements.
   ```

5. **Refinement Phase**
   ```
   Refine the implementation based on the review. Focus on [specific aspect].
   ```

### Domain-Specific Prompting

Tailor your prompts to specific programming domains:

#### Frontend Development
```
Create a React component that implements [feature]. Ensure it's responsive, accessible, and follows best practices for state management.
```

#### Backend Development
```
Implement an API endpoint for [functionality]. Include proper validation, error handling, and database interactions.
```

#### DevOps
```
Create a CI/CD workflow that builds, tests, and deploys our application to [environment]. Focus on security and efficiency.
```

## AI-Specific Prompt Techniques

### For GitHub Copilot

- **Chat-specific keywords**: Use `/fix`, `/explain`, `/test` commands in Copilot Chat
- **Use inline comments**: Copilot responds well to detailed code comments preceding the code you want it to generate
- **Leverage workspace context**: Keep relevant files open to provide better context

### For Cursor

- **Use `@` references**: Reference specific files with `@filename` syntax
- **Employ the Composer feature**: For longer or complex code generation tasks
- **Leverage Command+K**: For targeted edits to selected code

### For Amazon CodeWhisperer

- **Start with function signature**: Define the function name and parameters clearly
- **Use clear documentation comments**: CodeWhisperer responds well to descriptive comments
- **Provide context with import statements**: Include necessary imports to guide suggestions

## Conclusion

Effective prompting is both an art and a science. The techniques in this guide will help you communicate more effectively with AI coding assistants, resulting in better code, faster development, and fewer iterations. Remember that prompting is a skill that improves with practice - experiment with different approaches and refine your technique over time.

## References

- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [Cursor Documentation](https://cursor.sh/docs)
- [Amazon CodeWhisperer Documentation](https://aws.amazon.com/codewhisperer/)
- [Coding with AI: Best Practices](https://dev.to/)
