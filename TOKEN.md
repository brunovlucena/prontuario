# TOKEN Usage Guide

## Overview

This document provides comprehensive information about token usage, estimation, and optimization strategies for AI/LLM services across our projects.

## What are Tokens?

Tokens are the basic units of text processing in Large Language Models (LLMs). They represent pieces of text that can be:

- Whole words (common words like "the", "and")
- Parts of words (word fragments, prefixes, suffixes)
- Individual characters (for special characters or unknown text)
- Punctuation marks

### Token Estimation Rules

**General Estimation:**

- 1 token ≈ 4 characters of English text
- 1 token ≈ 0.75 words on average
- 100 tokens ≈ 75 words
- 1000 tokens ≈ 750 words

**Language Variations:**

- English: ~4 characters per token
- Spanish/Portuguese: ~4.5 characters per token
- Code: ~3.5 characters per token
- JSON/Structured data: ~3 characters per token

## Token Costs by Provider

### OpenAI GPT Models (USD per 1K tokens)

| Model | Input Tokens | Output Tokens | Context Window |
|-------|-------------|---------------|----------------|
| GPT-4o | $0.005 | $0.015 | 128K |
| GPT-4o-mini | $0.000150 | $0.000600 | 128K |
| GPT-4 Turbo | $0.010 | $0.030 | 128K |
| GPT-3.5 Turbo | $0.001 | $0.002 | 16K |

### Anthropic Claude Models (USD per 1K tokens)

| Model | Input Tokens | Output Tokens | Context Window |
|-------|-------------|---------------|----------------|
| Claude 3.5 Sonnet | $0.003 | $0.015 | 200K |
| Claude 3 Haiku | $0.00025 | $0.00125 | 200K |
| Claude 3 Opus | $0.015 | $0.075 | 200K |

### Google Gemini Models (USD per 1K tokens)

| Model | Input Tokens | Output Tokens | Context Window |
|-------|-------------|---------------|----------------|
| Gemini 1.5 Pro | $0.00125 | $0.005 | 2M |
| Gemini 1.5 Flash | $0.000075 | $0.0003 | 1M |

## Usage Estimation Calculator

### Basic Calculation Formula

```sh
Total Cost = (Input Tokens × Input Rate) + (Output Tokens × Output Rate)
```

### Example Calculations

**Scenario 1: Code Review Assistant**

- Input: 2,000 tokens (code + prompt)
- Output: 500 tokens (review comments)
- Model: GPT-4o
- Cost: (2 × $0.005) + (0.5 × $0.015) = $0.0175

**Scenario 2: Document Summarization**

- Input: 8,000 tokens (large document)
- Output: 300 tokens (summary)
- Model: Claude 3.5 Sonnet
- Cost: (8 × $0.003) + (0.3 × $0.015) = $0.0285

**Scenario 3: High-Volume API (1000 requests/day)**

- Average Input: 1,500 tokens
- Average Output: 400 tokens
- Model: GPT-4o-mini
- Daily Cost: 1000 × [(1.5 × $0.00015) + (0.4 × $0.0006)] = $0.465
- Monthly Cost: $13.95

## Token Optimization Strategies

### 1. Input Optimization

**Prompt Engineering:**

- Use clear, concise prompts
- Avoid redundant context
- Leverage system messages effectively
- Use bullet points instead of paragraphs when possible

**Context Management:**

- Implement sliding window for long conversations
- Summarize previous context instead of full history
- Remove unnecessary whitespace and formatting

### 2. Output Optimization

**Response Control:**

- Set appropriate max_tokens limits
- Use stop sequences to prevent over-generation
- Request specific formats (JSON, lists) to reduce verbosity

**Streaming:**

- Implement streaming for better user experience
- Stop generation early if sufficient information is received

### 3. Model Selection

**Cost vs Performance Trade-offs:**

- Use smaller models for simple tasks
- Reserve larger models for complex reasoning
- Consider fine-tuning for repetitive tasks

## Monitoring and Tracking

### Key Metrics to Track

1. **Token Usage Metrics:**
   - Total tokens per request
   - Input/output token ratio
   - Tokens per user session
   - Peak usage periods

2. **Cost Metrics:**
   - Daily/monthly token costs
   - Cost per user
   - Cost per feature/endpoint
   - Cost efficiency trends

3. **Performance Metrics:**
   - Response time vs token count
   - Quality metrics vs cost
   - User satisfaction vs expenditure

### Monitoring Tools

**Implementation Example:**

```python
class TokenTracker:
    def __init__(self):
        self.usage_log = []
    
    def log_usage(self, model, input_tokens, output_tokens, cost):
        self.usage_log.append({
            'timestamp': datetime.now(),
            'model': model,
            'input_tokens': input_tokens,
            'output_tokens': output_tokens,
            'total_tokens': input_tokens + output_tokens,
            'cost': cost
        })
    
    def daily_report(self):
        today = datetime.now().date()
        daily_usage = [log for log in self.usage_log 
                      if log['timestamp'].date() == today]
        
        return {
            'total_tokens': sum(log['total_tokens'] for log in daily_usage),
            'total_cost': sum(log['cost'] for log in daily_usage),
            'request_count': len(daily_usage)
        }
```

## Budget Management

### Setting Up Alerts

**Cost Thresholds:**

- Daily budget: $10
- Weekly budget: $50
- Monthly budget: $200

**Usage Thresholds:**

- High-volume warning: >100K tokens/hour
- Anomaly detection: >3x average usage
- Model switching trigger: Cost efficiency drops below threshold

### Budget Allocation by Feature

| Feature | Expected Monthly Tokens | Estimated Cost | Budget Allocation |
|---------|------------------------|----------------|-------------------|
| Code Assistant | 500K | $15 | 30% |
| Documentation | 200K | $6 | 15% |
| Chat Support | 800K | $24 | 45% |
| Analysis Tools | 150K | $5 | 10% |

## Best Practices

### Development Phase

1. **Use cheaper models for testing**
2. **Implement token counting in dev environment**
3. **Set strict limits during development**
4. **Cache responses for repeated queries**

### Production Phase

1. **Implement rate limiting**
2. **Use appropriate model for each task**
3. **Monitor usage patterns continuously**
4. **Set up automated alerts**

### Code Examples

**Token Counting (OpenAI):**

```python
import tiktoken

def count_tokens(text, model="gpt-4"):
    encoding = tiktoken.encoding_for_model(model)
    return len(encoding.encode(text))

# Usage
prompt = "Analyze this code for security issues"
token_count = count_tokens(prompt)
print(f"Token count: {token_count}")
```

**Cost Calculation:**

```python
def calculate_cost(input_tokens, output_tokens, model="gpt-4o"):
    rates = {
        "gpt-4o": {"input": 0.005, "output": 0.015},
        "gpt-4o-mini": {"input": 0.00015, "output": 0.0006},
        "claude-3.5-sonnet": {"input": 0.003, "output": 0.015}
    }
    
    rate = rates.get(model, rates["gpt-4o"])
    return (input_tokens / 1000 * rate["input"]) + (output_tokens / 1000 * rate["output"])
```

## Emergency Procedures

### High Usage Scenarios

1. **Immediate Actions:**
   - Activate rate limiting
   - Switch to cheaper models
   - Pause non-critical features

2. **Investigation Steps:**
   - Check for infinite loops
   - Identify unusual usage patterns
   - Review recent code changes

3. **Recovery Plan:**
   - Implement usage caps
   - Optimize high-cost operations
   - Update monitoring thresholds

## Regular Review Process

### Weekly Reviews

- [ ] Check total token consumption
- [ ] Review cost efficiency metrics
- [ ] Identify optimization opportunities
- [ ] Update usage forecasts

### Monthly Reviews

- [ ] Analyze usage trends
- [ ] Review budget allocation
- [ ] Update cost projections
- [ ] Evaluate model performance vs cost

## Resources and Tools

### Token Counting Tools

- OpenAI Tokenizer: https://platform.openai.com/tokenizer
- Anthropic Console: Token counting built-in
- Hugging Face Tokenizers: For open-source models

### Monitoring Platforms

- Custom dashboards (Grafana)
- Provider-specific dashboards
- Third-party monitoring tools

### Cost Management APIs

- OpenAI Usage API
- Anthropic Billing API
- Google Cloud Billing API

---

*Last updated: January 2025*
*Next review: February 2025* 