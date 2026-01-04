# prompts
A collection of my favourite prompts

## Approach

I have a technique for using AI coding assistance that works.

First, write a design document. You're going to use this to organize your own thinking, also as an input to the LLM that your prompts refer to.

Do not try to vibe-code everything at once. Instead, design from the middle outwards. Think about the classes or modules that implement the core data structures and abstractions in your program. Don't worry so much about top level logic and UI yet.

Walk the AI through coding those, module by module. Keep the LLM focused on well-defined problems for which it can code unit tests you understand, as well as code you understand.

Once you have a clean set of orthogonal primitives for the domain you're thinking about, only then should you start prompting towards whole-program implementation.

For example, if I were writing an editor, the first thing I would prompt for is a text-buffer management layer. I wouldn't even think about look and feel until I already had solid running gears.

More generally, if you want code that is overall clean and legible, you need to make it piecewise coherent, partition it into pieces with well-defined behavior and invariants.

That's how you keep an LLM from writing janky or over-elaborated code that's illegible and unmaintainable.

## Guides

### Claude Code Optimization Tips (via @AlexFinn)

Key tips for optimizing Claude Code, drawn from Anthropic's prompting best practices:

**1. Integrate GitHub early**
Connect CC to your Git repository immediately. This gives it access to your full commit history, significantly reducing hallucinations by providing comprehensive project context.

**2. Maximize context loading**
Leverage recent improvements in CC's context management, which now compacts intelligently for longer retention. Start sessions by having CC review your codebase architecture to build a strong foundation.

**3. Tune eagerness levels**
Claude is highly proactive and prefers making changes without explicit permission. If this is too aggressive, add this rule to your CLAUDE.md file to make it more cautious:

```xml
<do_not_act_before_instructions>
Do not jump into implementation or changes files unless clearly instructed to make changes. When the user's intent is ambiguous, default to providing information, doing research, and providing recommendations rather than taking action. Only proceed with edits, modifications, or implementations when the user explicitly requests them.
</do_not_act_before_instructions>
```

**4. Avoid token-heavy words**
Steer clear of "think," "think more," or "ultrathink" in prompts, as they trigger extra token usage. Opt for alternatives like "evaluate" or "consider" if you're budget-conscious.

**5. Leverage vision capabilities**
Claude Opus 4.5 excels at image analysis. Paste images directly into CC for tasks like debugging or gaining visual inspiration to enhance problem-solving.

**6. Enable parallel tool calls**
For better agentic performance and speed, add this rule to CLAUDE.md to allow simultaneous tool usage where possible:

```xml
<use_parallel_tool_calls>
If you intend to call multiple tools and there are no dependencies between the tool calls, make all of the independent tool calls in parallel. Prioritize calling tools simultaneously whenever the actions can be done in parallel rather than sequentially. For example, when reading 3 files, run 3 tool calls in parallel to read all 3 files into context at the same time. Maximize use of parallel tool calls where possible to increase speed and efficiency. However, if some tool calls depend on previous calls to inform dependent values like the parameters, do NOT call these tools in parallel and instead call them sequentially. Never use placeholders or guess missing parameters in tool calls.
</use_parallel_tool_calls>
```

**7. Minimize hallucinations**
Claude's eagerness can lead to premature edits. Counter this by adding this rule to CLAUDE.md:

```xml
<investigate_before_answering>
Never speculate about code you have not opened. If the user references a specific file, you MUST read the file before answering. Make sure to investigate and read relevant files BEFORE answering questions about the codebase. Never make any claims about code before investigating unless you are certain of the correct - give grounded and hallucination-free answers.
</investigate_before_answering>
```
