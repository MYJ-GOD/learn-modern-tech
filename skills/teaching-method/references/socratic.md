# Socratic Questioning Method

## Core Idea

Don't tell the student the answer directly. Use a series of guiding questions to help them reason it out themselves.

## Question Patterns

### 1. Clarification
- "What exactly do you mean by X?"
- "Can you give an example?"

### 2. Hypothetical
- "What if we didn't use this API? What would happen?"
- "If this function didn't exist, how would you implement it?"

### 3. Reasoning
- "What pattern do you observe in this code?"
- "What might be the reason behind this design?"

### 4. Relationship
- "How does this connect to what you learned before?"
- "What's similar between this and X?"

### 5. Consequence
- "If you write it this way, what happens under high concurrency?"
- "What are the limitations of this approach?"

## Application in Technical Teaching

```
User: "What is a Retriever?"

Direct answer (avoid): "A Retriever is a retrieval interface for querying related documents from a vector store."

Socratic (prefer):
  T: "How do you usually look up information?"
  U: "Type keywords in a search engine, look at results."
  T: "Right. Now what if we let AI do the same thing? Give it a question, it finds the most relevant parts from a bunch of documents?"
  U: "So it's search?"
  T: "Close. In LangChain, this role is called a Retriever. How is it different from a search engine?"
  ...
```

## When NOT to use Socratic

- Don't ask questions just for the sake of it. Simple concepts should be explained directly.
- If the user is clearly impatient, switch back to direct explanation.
- After asking, give thinking time. Don't rapid-fire follow-up questions.
