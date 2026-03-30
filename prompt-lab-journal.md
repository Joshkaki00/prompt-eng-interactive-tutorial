# Prompt Engineering Lab Journal

## Chapter Reflections

### Chapter 4: Separating Data and Instructions

**What it does:** This technique uses XML tags or clear delimiters to separate variable data (like user input, documents, or content) from the instructions Claude should follow. This prevents Claude from getting confused about what is data versus what are commands.

**Real scenario:** When building a content moderation system for user-generated posts, I would use XML tags like `<post>` to wrap user content and keep moderation guidelines separate. This ensures Claude doesn't accidentally interpret harmful content in a user post as instructions to follow.

**Best prompt:** The email classification exercise where I wrapped email content in XML tags while keeping classification categories and instructions separate. It worked because Claude could clearly distinguish between "this is the data to analyze" versus "these are your instructions for analyzing it."

### Chapter 6: Precognition (Thinking Step by Step)

**What it does:** This technique asks Claude to think through its reasoning step-by-step before providing a final answer, often by using XML tags like `<brainstorm>` or `<thinking>`. It improves accuracy for complex tasks by forcing Claude to work through the problem methodically rather than jumping to conclusions.

**Real scenario:** When debugging production issues, I'd ask Claude to first brainstorm possible causes in `<analysis>` tags before suggesting solutions. This would help catch edge cases and prevent overlooking obvious issues that a hasty answer might miss.

**Best prompt:** The movie review sentiment analysis where asking Claude to consider both positive and negative arguments in separate XML tags before answering. It worked because Claude processed the sarcasm more accurately when forced to evaluate both interpretations explicitly.

### Chapter 7: Using Examples (Few-Shot Prompting)

**What it does:** This technique provides Claude with concrete examples of the desired input-output behavior, allowing Claude to learn the pattern and format without needing lengthy explanations. Examples are often more effective than describing what you want in words.

**Real scenario:** When building a code review bot that needs to follow specific formatting standards for comments, I'd provide 2-3 examples of good reviews. This would be faster and more reliable than writing detailed style guidelines, especially for maintaining consistent tone and structure.

**Best prompt:** The email classification with few-shot examples showing the exact format "The correct category is: A" combined with a prefill. It worked because the examples demonstrated the precise output format I wanted, and the prefill ensured Claude followed that pattern consistently.

## Combined Techniques Prompt

### Prompt Text

**Techniques Used:** 
1. **Role Prompting (Chapter 3)** - Assigned Claude the role of a senior software engineer
2. **XML Tags for Data Separation (Chapter 4)** - Used `<code>`, `<requirements>`, and `<output>` tags
3. **Few-Shot Examples (Chapter 7)** - Provided example of desired code review format

```
You are a senior software engineer conducting a code review. Your goal is to identify bugs and suggest improvements.

<requirements>
- Focus on edge cases and error handling
- Suggest specific fixes, not just problems
- Keep feedback concise and actionable
</requirements>

Here's an example of how to structure your review:

<example>
<code>
def calculate_average(numbers):
    return sum(numbers) / len(numbers)
</code>

<output>
**Issue:** Division by zero error when list is empty.

**Fix:** Add validation:
```python
def calculate_average(numbers):
    if not numbers:
        return 0
    return sum(numbers) / len(numbers)
```
</output>
</example>

Now review this code:

<code>
def get_user_age(users, user_id):
    for user in users:
        if user['id'] == user_id:
            return user['age']
</code>
```

### Claude's Response

```
**Issue 1:** No handling for when user_id is not found - function returns None implicitly, which could cause confusing behavior.

**Issue 2:** Assumes 'id' and 'age' keys exist in user dictionaries - will raise KeyError if missing.

**Issue 3:** No input validation for users parameter - will fail if users is None or not iterable.

**Improved Version:**
```python
def get_user_age(users, user_id):
    if not users:
        return None
    
    for user in users:
        if user.get('id') == user_id:
            return user.get('age')
    
    return None  # Explicit return when user not found
```

**Additional suggestions:**
- Consider raising a custom exception when user not found if that's an error condition
- Add type hints: `def get_user_age(users: list[dict], user_id: int) -> int | None:`
```

### Why It Worked

This prompt combined three powerful techniques effectively: **Role prompting** gave Claude the right mindset and expertise level, **XML tags** kept the code, requirements, and example clearly separated so Claude understood the structure, and **few-shot examples** demonstrated the exact review format I wanted (issue identification + specific fix + improved code). The combination resulted in a thorough, well-structured code review that found multiple real issues and provided actionable solutions without being verbose or adding unnecessary explanations. The techniques reinforced each other - the role set expectations, the XML tags organized information, and the example ensured consistent output format.
