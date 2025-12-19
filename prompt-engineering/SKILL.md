---
name: prompt-engineering
description: Expert prompt engineering skill for crafting optimal LLM prompts. Use when creating prompts, improving prompts, writing system prompts, or when user mentions "prompt", "system message", "instructions for AI", or needs help communicating with LLMs effectively.
allowed-tools: Read, Write, Edit
---

# Expert Prompt Engineering

## Overview
This skill enables Claude to craft highly effective prompts for LLMs (Claude, GPT, etc.) using proven 2025 best practices and techniques.

## Core Principles

### 1. Clarity & Specificity
The most predictive factors for high-quality results are clarity, context, and specificity.

```
❌ Bad: "Write about dogs"
✅ Good: "Write a 200-word informative paragraph about Golden Retrievers,
         focusing on their temperament and suitability as family pets.
         Use a warm, conversational tone suitable for first-time dog owners."
```

### 2. Structure Your Prompts

**Recommended Structure:**
```
[ROLE/PERSONA]
[CONTEXT/BACKGROUND]
[TASK/INSTRUCTION]
[FORMAT/OUTPUT REQUIREMENTS]
[CONSTRAINTS/BOUNDARIES]
[EXAMPLES (if needed)]
```

### 3. Model-Specific Strategies

**For Claude:**
- Put documents/context at the TOP of the prompt
- Use XML-style tags: `<thinking>`, `<answer>`, `<context>`, `<instructions>`
- Ask Claude to "explain your reasoning" for better results
- Use sentence stems: "The key finding is...", "In summary..."
- Set explicit boundaries (Claude tends to over-explain)
- Prefer declarative phrasing over open-ended fragments

**For GPT:**
- Put instructions at the TOP, documents below
- Use crisp numeric constraints: "3 bullets", "under 50 words"
- Use markdown-like syntax: `### Response`, `---`, triple backticks
- Specify format explicitly: "in JSON", "as a table"

## Advanced Techniques

### Chain-of-Thought (CoT)
Instruct the model to reason step-by-step before answering.

```
Solve this problem step by step:
1. First, identify the key variables
2. Then, establish relationships between them
3. Apply the relevant formula
4. Show your calculation
5. State the final answer

Problem: [your problem here]
```

### Few-Shot Prompting
Provide examples to demonstrate desired format/style.

```
Classify the sentiment of these reviews:

Review: "The food was amazing and service was quick!"
Sentiment: Positive

Review: "Waited 2 hours for cold food. Never again."
Sentiment: Negative

Review: "It was okay, nothing special but not bad either."
Sentiment: Neutral

Now classify this review:
Review: "[user's review here]"
Sentiment:
```

### Structured Output Control
Specify exact output format for consistency.

```
Respond in the following JSON format only:
{
  "summary": "2-3 sentence summary",
  "key_points": ["point 1", "point 2", "point 3"],
  "sentiment": "positive|negative|neutral",
  "confidence": 0.0-1.0
}

Do not include any text outside the JSON object.
```

### Role-Based Prompting
Assign a specific persona for domain expertise.

```
You are a senior software architect with 15 years of experience
in distributed systems. You prioritize:
- Scalability and performance
- Clean, maintainable code
- Security best practices
- Clear documentation

Review the following code and provide architectural feedback:
[code here]
```

## Prompt Templates

### System Prompt Template
```
You are [ROLE] with expertise in [DOMAIN].

Your responsibilities:
- [Responsibility 1]
- [Responsibility 2]
- [Responsibility 3]

Communication style:
- [Style guideline 1]
- [Style guideline 2]

Constraints:
- [Constraint 1]
- [Constraint 2]

When responding:
1. [Step 1]
2. [Step 2]
3. [Step 3]
```

### Task Prompt Template
```
<context>
[Background information, documents, or data]
</context>

<task>
[Clear description of what needs to be done]
</task>

<requirements>
- Format: [desired output format]
- Length: [word/character limit]
- Tone: [formal/casual/technical]
- Audience: [who will read this]
</requirements>

<examples>
[Optional: Input/output examples]
</examples>
```

### Analysis Prompt Template
```
Analyze the following [TYPE] and provide:

1. **Summary**: Brief overview (2-3 sentences)
2. **Key Findings**: Top 3-5 important points
3. **Strengths**: What works well
4. **Weaknesses**: Areas for improvement
5. **Recommendations**: Actionable next steps

[Content to analyze]

Format your response using the headers above.
```

## Best Practices Checklist

When crafting prompts, ensure:

- [ ] **Clear objective**: What exactly should the model do?
- [ ] **Sufficient context**: Does the model have all needed information?
- [ ] **Specific constraints**: Length, format, tone, audience defined?
- [ ] **Output format**: How should the response be structured?
- [ ] **Examples provided**: For complex or nuanced tasks?
- [ ] **Edge cases addressed**: What should happen in unusual situations?
- [ ] **Evaluation criteria**: How will you judge success?

## Common Anti-Patterns to Avoid

### 1. Vague Instructions
```
❌ "Make it better"
✅ "Improve readability by using shorter sentences and adding subheadings"
```

### 2. Conflicting Requirements
```
❌ "Be concise but explain everything in detail"
✅ "Provide a 100-word summary, then a detailed explanation if needed"
```

### 3. Missing Context
```
❌ "Fix the bug"
✅ "Fix the null pointer exception in the getUserById function.
    The function should return null instead of throwing when user not found."
```

### 4. Assuming Knowledge
```
❌ "Use the standard format"
✅ "Format as: Title (bold), Date (YYYY-MM-DD), Body (max 200 words)"
```

## Instructions for Claude

When helping users create prompts:

1. **Ask clarifying questions** about:
   - Target model (Claude, GPT, etc.)
   - Use case and context
   - Desired output format
   - Success criteria

2. **Apply the appropriate template** based on task type

3. **Include model-specific optimizations**:
   - XML tags for Claude
   - Markdown/delimiters for GPT

4. **Add examples** for complex or nuanced tasks

5. **Test and iterate**: Suggest A/B testing different phrasings

6. **Document the prompt**: Include comments explaining key decisions

## Sources

This skill is based on research from:
- [Claude Prompt Engineering Best Practices](https://claude.com/blog/best-practices-for-prompt-engineering)
- [Lakera Prompt Engineering Guide 2025](https://www.lakera.ai/blog/prompt-engineering-guide)
- [IBM 2025 Guide to Prompt Engineering](https://www.ibm.com/think/prompt-engineering)
- [Complete Prompt Engineering Guide: 15 Techniques for 2025](https://www.dataunboxed.io/blog/the-complete-guide-to-prompt-engineering-15-essential-techniques-for-2025)
- [PromptingGuide.ai](https://www.promptingguide.ai/)
