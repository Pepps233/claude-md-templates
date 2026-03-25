# Prompt Engineering Best Practices

Guidelines for writing effective prompts for Claude. Sourced from the official Claude prompting best practices documentation.

## General Principles

### Be Clear and Direct

Claude responds well to clear, explicit instructions. Think of Claude as a brilliant but new employee who lacks context on your norms and workflows.

**Golden rule:** Show your prompt to a colleague with minimal context on the task. If they'd be confused, Claude will be too.

- Be specific about desired output format and constraints
- Provide instructions as numbered lists or bullet points when order or completeness matters
- If you want "above and beyond" behavior, explicitly request it

**Less effective:**
```
Create an analytics dashboard
```

**More effective:**
```
Create an analytics dashboard. Include as many relevant features and interactions as possible. Go beyond the basics to create a fully-featured implementation.
```

### Add Context and Motivation

Explaining *why* an instruction matters helps Claude generalize better than a bare rule.

**Less effective:**
```
NEVER use ellipses
```

**More effective:**
```
Your response will be read aloud by a text-to-speech engine, so never use ellipses since the text-to-speech engine will not know how to pronounce them.
```

### Use Examples (Few-Shot / Multishot Prompting)

Examples are one of the most reliable ways to steer output format, tone, and structure. Include 3–5 examples for best results.

Make examples:
- **Relevant:** Mirror your actual use case closely
- **Diverse:** Cover edge cases; vary enough that Claude doesn't pick up unintended patterns
- **Structured:** Wrap in `<example>` tags (multiple in `<examples>` tags) to distinguish from instructions

### Structure Prompts with XML Tags

XML tags help Claude parse complex prompts unambiguously, especially when mixing instructions, context, examples, and variable inputs.

```xml
<instructions>
  Summarize the following document in three bullet points.
</instructions>

<document>
  {{DOCUMENT_CONTENT}}
</document>
```

Best practices:
- Use consistent, descriptive tag names across prompts
- Nest tags for hierarchical content (e.g., `<document index="1">` inside `<documents>`)

## Long Context Prompting (20k+ tokens)

- **Put longform data at the top:** Place documents and inputs near the top of the prompt, above your query and instructions. This can improve performance by up to 30%.
- **Structure documents with XML:** Wrap each document in `<document>` tags with `<source>` and `<document_content>` subtags.
- **Ground responses in quotes:** For long document tasks, ask Claude to quote relevant parts before carrying out the task.

```xml
<documents>
  <document index="1">
    <source>annual_report_2023.pdf</source>
    <document_content>
      {{ANNUAL_REPORT}}
    </document_content>
  </document>
  <document index="2">
    <source>competitor_analysis_q2.xlsx</source>
    <document_content>
      {{COMPETITOR_ANALYSIS}}
    </document_content>
  </document>
</documents>

Find quotes from the documents relevant to the question, then answer based on those quotes.
```

## Output Formatting

### Tell Claude What to Do, Not What to Avoid

- Instead of: "Do not use markdown in your response"
- Try: "Your response should be composed of smoothly flowing prose paragraphs."

### Use XML Format Indicators

```
Write the prose sections of your response in <smoothly_flowing_prose_paragraphs> tags.
```

### Minimize Markdown / Bullet Points

For reports, analyses, and long-form content:

```
<avoid_excessive_markdown_and_bullet_points>
Write in clear, flowing prose using complete paragraphs and sentences. Reserve markdown primarily for inline code, code blocks, and simple headings. Avoid ordered and unordered lists unless presenting truly discrete items or the user explicitly requests a list. Incorporate items naturally into sentences instead.
</avoid_excessive_markdown_and_bullet_points>
```

### Eliminate Preambles

```
Respond directly without preamble. Do not start with phrases like "Here is...", "Based on...", "Certainly!", etc.
```

## Thinking and Reasoning

### Guide Initial Thinking

```
After receiving tool results, carefully reflect on their quality and determine optimal next steps before proceeding. Use your thinking to plan and iterate based on this new information, then take the best next action.
```

### Constrain Overthinking

When excessive exploration is undesirable:

```
When deciding how to approach a problem, choose an approach and commit to it. Avoid revisiting decisions unless you encounter new information that directly contradicts your reasoning. If weighing two approaches, pick one and see it through.
```

### Limit Thinking for Latency-Sensitive Workloads

```
Extended thinking adds latency and should only be used when it will meaningfully improve answer quality — typically for problems requiring multi-step reasoning. When in doubt, respond directly.
```

### Self-Check

Append to any task where correctness matters:

```
Before you finish, verify your answer against [test criteria / requirements].
```

### Manual Chain-of-Thought (when thinking is off)

```
Think through this step by step. Put your reasoning in <thinking> tags and your final answer in <answer> tags.
```

## Tool Use in Prompts

### Make Explicit When Action Is Expected

Claude will suggest rather than implement if the instruction is ambiguous.

**Less effective (Claude will only suggest):**
```
Can you suggest some changes to improve this function?
```

**More effective (Claude will make changes):**
```
Change this function to improve its performance.
```

### Dial Back Aggressive Tool Prompting

Claude's latest models are proactive by default. Instructions like "CRITICAL: You MUST use this tool when..." can cause overtriggering. Use normal prompting:

```
Use this tool when it would enhance your understanding of the problem.
```

## Research Tasks

For complex research, use a structured approach:

```
Search for this information in a structured way. As you gather data, develop several competing hypotheses. Track confidence levels in your progress notes. Regularly self-critique your approach. Update a hypothesis tree or research notes file to persist information and provide transparency.
```

## Agentic Coding

### Prevent Test-Focused / Hard-Coded Solutions

```
Write a high-quality, general-purpose solution. Do not hard-code values or create solutions that only work for specific test inputs. Implement the actual logic that solves the problem generally. Tests verify correctness — they do not define the solution.
```

### Prevent Hallucinations About Code

```
<investigate_before_answering>
Never speculate about code you have not opened. Read the relevant file before answering. Investigate before making claims. Give grounded, hallucination-free answers.
</investigate_before_answering>
```
