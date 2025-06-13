# TAML (Terminal ANSI Markup Language) Specification

**Version:** 1.0  
**Date:** December 2024  
**Status:** Stable

## Table of Contents

1. [Introduction & Overview](#introduction--overview)
2. [Language Fundamentals](#language-fundamentals)
3. [Complete Tag Reference](#complete-tag-reference)
4. [Advanced Features](#advanced-features)
5. [Implementation Guidelines](#implementation-guidelines)
6. [Best Practices & Patterns](#best-practices--patterns)
7. [Real-world Examples](#real-world-examples)
8. [Technical Appendices](#technical-appendices)

---

## Introduction & Overview

### What is TAML?

TAML (Terminal ANSI Markup Language) is a human-readable domain-specific language (DSL) designed to represent terminal text formatting using HTML-like tags instead of raw ANSI escape sequences. It provides a clean, intuitive syntax for expressing colored and styled text that would traditionally require complex escape codes.

**Key Benefits:**
- **Human-readable**: Easy to write, read, and maintain
- **Version control friendly**: Clear diffs and meaningful changes
- **IDE support**: Syntax highlighting and validation possible
- **Cross-platform**: Works consistently across different terminals
- **Nested formatting**: Supports complex style combinations

### The Problem with ANSI Escape Sequences

Traditional ANSI escape sequences are cryptic and difficult to work with:

```bash
# Raw ANSI - Hard to read and maintain
echo "\033[1m\033[31mError:\033[0m \033[33mSomething went wrong\033[0m"
```

This produces bold red "Error:" followed by yellow "Something went wrong", but the intent is completely obscured by the escape codes.

### TAML Solution

TAML makes the same formatting clear and intuitive:

```html
<bold><red>Error:</red></bold> <yellow>Something went wrong</yellow>
```

The visual intent is immediately obvious, making code more maintainable and accessible to developers.

### Side-by-Side Comparison

| ANSI Escape Sequence | TAML Equivalent | Visual Result |
|---------------------|----------------|---------------|
| `\033[31mRed text\033[0m` | `<red>Red text</red>` | Red text |
| `\033[1mBold\033[0m` | `<bold>Bold</bold>` | **Bold** |
| `\033[1m\033[31mBold Red\033[0m` | `<bold><red>Bold Red</red></bold>` | **Red text** |
| `\033[42mGreen BG\033[0m` | `<bgGreen>Green BG</bgGreen>` | Green background |
| `\033[4mUnderline\033[0m` | `<underline>Underline</underline>` | Underlined text |

### Quick Start Examples

Here are some common patterns to get you started:

```html
<!-- Basic colors -->
<red>Error message</red>
<green>Success message</green>
<yellow>Warning message</yellow>

<!-- Text styling -->
<bold>Important text</bold>
<italic>Emphasized text</italic>
<underline>Highlighted text</underline>

<!-- Combinations -->
<bold><red>Critical Error</red></bold>
<bgYellow><black>Highlighted note</black></bgYellow>

<!-- Real-world usage -->
<green>[INFO]</green> Application started successfully
<yellow>[WARN]</yellow> Deprecated API usage detected
<red>[ERROR]</red> Database connection failed
```

---

## Language Fundamentals

### Basic Syntax Rules

TAML follows these fundamental syntax rules:

1. **Case Sensitive**: Tag names are case-sensitive (`<red>` ≠ `<Red>`)
2. **XML-like Structure**: Uses opening and closing tags (`<tag>content</tag>`)
3. **Proper Nesting**: Tags must be properly nested (no overlapping)
4. **UTF-8 Encoding**: Supports full Unicode character set
5. **Whitespace Preservation**: All whitespace is preserved exactly as written

### Tag Structure

Every TAML tag follows this structure:

```html
<tagName>content</tagName>
```

**Components:**
- **Opening Tag**: `<tagName>` - Starts the formatting
- **Content**: Text or nested tags
- **Closing Tag**: `</tagName>` - Ends the formatting

**Valid Examples:**
```html
<red>Simple text</red>
<bold><green>Nested formatting</green></bold>
<bgBlue><white>Background with foreground</white></bgBlue>
```

**Invalid Examples:**
```html
<red>Unclosed tag
<red>Overlapping <bold>tags</red></bold>
<Red>Wrong case</Red>
```

### Nesting Rules

TAML supports unlimited nesting depth (subject to parser implementation limits):

```html
<!-- Simple nesting -->
<bold><red>Bold red text</red></bold>

<!-- Multiple levels -->
<bold><italic><underline>Triple formatting</underline></italic></bold>

<!-- Complex combinations -->
<bgRed><white><bold>White bold text on red background</bold></white></bgRed>
```

**Nesting Guidelines:**
- Inner tags inherit outer formatting
- Tags must be properly closed in reverse order
- No overlapping tags allowed
- Empty tags are valid: `<red></red>`

### Text Content Handling

TAML preserves all text content exactly as written:

```html
<!-- Whitespace preservation -->
<red>  Text with spaces  </red>

<!-- Newlines preserved -->
<green>Line 1
Line 2</green>

<!-- Special characters -->
<blue>Unicode: 🎨 ñ ü ß</blue>

<!-- Mixed content -->
Plain text <red>colored text</red> more plain text
```

### Escaping Special Characters

To prevent ambiguity between literal text and markup tags, certain characters are treated specially.

-   The less-than character (`<`) **must** be escaped as `&lt;` to prevent it from being interpreted as the start of a tag.
-   The ampersand character (`&`) is treated as a literal character, **unless** it is part of a valid entity.

TAML defines two essential entities:

| Literal Character | Entity | Description |
| :--- | :--- | :--- |
| `<` | `&lt;` | Represents the less-than sign. Required. |
| `&` | `&amp;`| Represents the ampersand sign. Required for writing literal text like `&lt;`. |

Any `&` that is not followed by `lt;` or `amp;` is treated as a literal ampersand and does not need to be escaped.

**Examples:**

```html
<!-- Correct: Escaping is required for the less-than sign -->
<blue>5 &lt; 10</blue>

<!-- Correct: The ampersand here is treated literally -->
<yellow>This is used for text like AT&T and R&D.</yellow>

<!-- Correct: To write the literal text "&lt;", the ampersand must be escaped -->
<green>The entity for a less-than sign is &amp;lt;</green>
```

### Character Encoding

TAML uses UTF-8 encoding and supports:
- All Unicode characters
- Emoji and symbols
- International text
- Special punctuation
- Mathematical symbols

---

## Complete Tag Reference

TAML supports **37 total tags** across four categories:

### Standard Colors (8 tags)

Basic ANSI colors with standard intensity:

| Tag | ANSI Code | Hex Color | Usage |
|-----|-----------|-----------|-------|
| `<black>` | `\033[30m` | `#000000` | Dark text, rarely used |
| `<red>` | `\033[31m` | `#800000` | Errors, warnings |
| `<green>` | `\033[32m` | `#008000` | Success, positive status |
| `<yellow>` | `\033[33m` | `#808000` | Warnings, cautions |
| `<blue>` | `\033[34m` | `#000080` | Information, links |
| `<magenta>` | `\033[35m` | `#800080` | Special highlights |
| `<cyan>` | `\033[36m` | `#008080` | Secondary information |
| `<white>` | `\033[37m` | `#c0c0c0` | Default text, emphasis |

**Examples:**
```html
<black>Black text (rarely visible)</black>
<red>Error: File not found</red>
<green>✓ Build successful</green>
<yellow>⚠ Deprecated function</yellow>
<blue>ℹ For more info, see docs</blue>
<magenta>★ Special feature</magenta>
<cyan>→ Next step</cyan>
<white>Default appearance</white>
```

### Bright Colors (8 tags)

High-intensity versions of standard colors:

| Tag | ANSI Code | Hex Color | Usage |
|-----|-----------|-----------|-------|
| `<brightBlack>` | `\033[90m` | `#808080` | Gray text, comments |
| `<brightRed>` | `\033[91m` | `#ff0000` | Critical errors |
| `<brightGreen>` | `\033[92m` | `#00ff00` | Success highlights |
| `<brightYellow>` | `\033[93m` | `#ffff00` | Important warnings |
| `<brightBlue>` | `\033[94m` | `#0000ff` | Primary information |
| `<brightMagenta>` | `\033[95m` | `#ff00ff` | Special emphasis |
| `<brightCyan>` | `\033[96m` | `#00ffff` | Highlights |
| `<brightWhite>` | `\033[97m` | `#ffffff` | Maximum emphasis |

**Examples:**
```html
<brightBlack>// This is a comment</brightBlack>
<brightRed>FATAL: System crash</brightRed>
<brightGreen>🎉 All tests passed!</brightGreen>
<brightYellow>⚡ Performance warning</brightYellow>
<brightBlue>📘 Documentation link</brightBlue>
<brightMagenta>🔮 Advanced feature</brightMagenta>
<brightCyan>💡 Pro tip</brightCyan>
<brightWhite>⭐ Featured content</brightWhite>
```

### Background Colors (16 tags)

Background colors for both standard and bright variants:

#### Standard Backgrounds (8 tags)

| Tag | ANSI Code | Usage |
|-----|-----------|-------|
| `<bgBlack>` | `\033[40m` | Dark backgrounds |
| `<bgRed>` | `\033[41m` | Error highlights |
| `<bgGreen>` | `\033[42m` | Success highlights |
| `<bgYellow>` | `\033[43m` | Warning highlights |
| `<bgBlue>` | `\033[44m` | Information highlights |
| `<bgMagenta>` | `\033[45m` | Special highlights |
| `<bgCyan>` | `\033[46m` | Secondary highlights |
| `<bgWhite>` | `\033[47m` | Light backgrounds |

#### Bright Backgrounds (8 tags)

| Tag | ANSI Code | Usage |
|-----|-----------|-------|
| `<bgBrightBlack>` | `\033[100m` | Gray backgrounds |
| `<bgBrightRed>` | `\033[101m` | Critical highlights |
| `<bgBrightGreen>` | `\033[102m` | Success emphasis |
| `<bgBrightYellow>` | `\033[103m` | Warning emphasis |
| `<bgBrightBlue>` | `\033[104m` | Information emphasis |
| `<bgBrightMagenta>` | `\033[105m` | Special emphasis |
| `<bgBrightCyan>` | `\033[106m` | Highlight emphasis |
| `<bgBrightWhite>` | `\033[107m` | Maximum contrast |

**Background Examples:**
```html
<!-- Standard backgrounds -->
<bgRed><white>Error message</white></bgRed>
<bgGreen><black>Success message</black></bgGreen>
<bgYellow><black>Warning message</black></bgYellow>

<!-- Bright backgrounds -->
<bgBrightRed><white>Critical alert</white></bgBrightRed>
<bgBrightGreen><black>All systems go</black></bgBrightGreen>

<!-- Complex combinations -->
<bgBlue><brightWhite><bold>Featured announcement</bold></brightWhite></bgBlue>
```

### Text Styles (5 tags)

Text formatting and decoration options:

| Tag | ANSI Code | Effect | Usage |
|-----|-----------|--------|-------|
| `<bold>` | `\033[1m` | Bold/increased intensity | Emphasis, headers |
| `<dim>` | `\033[2m` | Dim/decreased intensity | Secondary text |
| `<italic>` | `\033[3m` | Italic text | Emphasis, quotes |
| `<underline>` | `\033[4m` | Underlined text | Links, highlights |
| `<strikethrough>` | `\033[9m` | Strikethrough text | Deletions, deprecated |

**Style Examples:**
```html
<bold>Important: Read this carefully</bold>
<dim>Optional configuration parameter</dim>
<italic>"The best way to predict the future is to invent it"</italic>
<underline>https://example.com</underline>
<strikethrough>Deprecated function</strikethrough>

<!-- Combined styles -->
<bold><underline>Section Header</underline></bold>
<italic><dim>Subtle emphasis</dim></italic>
```

### Tag Combinations

TAML supports complex combinations of colors, backgrounds, and styles:

```html
<!-- Color + Style -->
<bold><red>Bold red error</red></bold>
<italic><blue>Italic blue note</blue></italic>

<!-- Background + Foreground -->
<bgYellow><black>Black text on yellow</black></bgYellow>
<bgRed><brightWhite>Bright white on red</brightWhite></bgRed>

<!-- Multiple styles -->
<bold><italic><underline>Triple emphasis</underline></italic></bold>

<!-- Complex real-world example -->
<bgBrightBlue><white><bold>
  🚀 DEPLOYMENT STATUS
</bold></white></bgBrightBlue>

<green>✓ Frontend deployed</green>
<green>✓ Backend deployed</green>
<yellow>⚠ Database migration pending</yellow>
```

---

## Advanced Features

### Complex Nesting Patterns

TAML supports sophisticated nesting for rich text formatting:

```html
<!-- Multi-level nesting -->
<bold>
  <red>Error in </red>
  <blue>function </blue>
  <italic>processData()</italic>
  <red> at line 42</red>
</bold>

<!-- Nested backgrounds and foregrounds -->
<bgRed>
  <white>Alert: </white>
  <brightYellow>
    <bold>System overload detected</bold>
  </brightYellow>
</bgRed>

<!-- Complex document structure -->
<bold><underline>Project Status Report</underline></bold>

<green>✓ Completed Tasks:</green>
  <dim>- User authentication</dim>
  <dim>- Database setup</dim>
  <dim>- API endpoints</dim>

<yellow>⚠ In Progress:</yellow>
  <italic>- Frontend components</italic>
  <italic>- Testing suite</italic>

<red>✗ Blocked:</red>
  <strikethrough>- Third-party integration</strikethrough>
  <dim>(waiting for API keys)</dim>
```

### Error Handling

TAML parsers should handle various error conditions gracefully:

#### Invalid Tag Names
```html
<!-- Invalid: Unknown tag -->
<invalidTag>This should error</invalidTag>

<!-- Invalid: Wrong case -->
<Red>Should be lowercase</Red>

<!-- Invalid: Numbers in tag names -->
<red1>Not allowed</red1>
```

#### Unclosed Tags
```html
<!-- Invalid: Missing closing tag -->
<red>This text is never closed

<!-- Invalid: Partial closing -->
<bold>Text</bol>
```

#### Mismatched Tags
```html
<!-- Invalid: Wrong closing tag -->
<red>Text</blue>

<!-- Invalid: Overlapping tags -->
<red>Start <bold>middle</red> end</bold>
```

#### Malformed Tags
```html
<!-- Invalid: Missing closing bracket -->
<red Text without proper closing

<!-- Invalid: Empty tag name -->
<>Empty tag name</>

<!-- Invalid: Whitespace in tag name -->
<red blue>Spaces not allowed</red blue>
```

### Performance Considerations

#### Parser Efficiency
- **Tokenization**: Linear time complexity O(n)
- **Parsing**: Linear time for well-formed input
- **Memory**: Proportional to nesting depth
- **Caching**: Parsed results should be memoized

#### Optimization Strategies
```html
<!-- Efficient: Minimal nesting -->
<red>Error: </red><bold>File not found</bold>

<!-- Less efficient: Deep nesting -->
<red><bold><italic><underline>Over-formatted</underline></italic></bold></red>

<!-- Efficient: Reuse common patterns -->
<green>[INFO]</green> Message 1
<green>[INFO]</green> Message 2
<green>[INFO]</green> Message 3
```

#### Recommended Limits
- **Maximum nesting depth**: 100 levels
- **Maximum tag name length**: 32 characters
- **Maximum content length**: No limit (memory permitting)

### Edge Cases

#### Empty Content
```html
<!-- Valid: Empty tags -->
<red></red>
<bold><italic></italic></bold>

<!-- Valid: Whitespace-only content -->
<blue>   </blue>
<green>
</green>
```

#### Special Characters
```html
<!-- Valid: Unicode content -->
<red>🚨 Alert: 危険</red>
<green>✅ Success: Успех</green>
```

#### Whitespace Handling
```html
<!-- Preserved: Leading/trailing spaces -->
<red>  spaced text  </red>

<!-- Preserved: Internal formatting -->
<green>Line 1
    Indented line 2
Line 3</green>
```

---

## Implementation Guidelines

### Parser Requirements

An TAML parser must implement these core components:

#### 1. Tokenizer
Breaks input into tokens:
- **Text tokens**: Plain text content
- **Open tag tokens**: `<tagName>`
- **Close tag tokens**: `</tagName>`
- **EOF token**: End of input

The tokenizer should handle entity parsing within text content. When an ampersand (`&`) is encountered, the tokenizer must look ahead:
- If it is part of a valid entity (`&lt;` or `&amp;`), the corresponding decoded character (`<` or `&`) should be part of the resulting text.
- If the characters following `&` do not form a valid entity, the `&` should be treated as a literal character.

```
Input: "<red>Hello</red>"
Tokens: [OPEN_TAG(red), TEXT(Hello), CLOSE_TAG(red), EOF]
```

#### 2. Validator
Ensures tokens form valid TAML:
- Tag names are valid
- Tags are properly nested
- All tags are closed
- No overlapping tags

#### 3. AST Builder
Creates abstract syntax tree:
```
<red>Hello <bold>world</bold></red>

AST:
ElementNode(red)
├── TextNode("Hello ")
└── ElementNode(bold)
    └── TextNode("world")
```

### AST Structure

Recommended AST node types:

```typescript
interface TamlNode {
  type: 'element' | 'text';
  start: number;  // Position in source
  end: number;    // Position in source
}

interface ElementNode extends TamlNode {
  type: 'element';
  tagName: string;
  children: TamlNode[];
}

interface TextNode extends TamlNode {
  type: 'text';
  content: string;
}
```

### Tokenization Rules

#### Tag Recognition
```regex
Opening tag: /<([a-zA-Z]+)>/
Closing tag: /<\/([a-zA-Z]+)>/
```

#### Valid Tag Names
- Must start with letter
- Only letters allowed (a-z, A-Z)
- Case sensitive
- No numbers, hyphens, or underscores

#### Text Content
- Everything between tags
- Preserve all whitespace
- Support full Unicode

### Validation Guidelines

#### Syntax Validation
1. **Tag matching**: Every open tag has corresponding close tag
2. **Proper nesting**: No overlapping tags
3. **Valid names**: Only recognized tag names allowed
4. **Case sensitivity**: Exact case matching required

#### Semantic Validation
1. **Depth limits**: Prevent stack overflow
2. **Content limits**: Reasonable size restrictions
3. **Performance**: Linear time complexity

### Error Reporting

Provide helpful error messages with context:

```
Error: Invalid tag name 'Red' at line 1, column 2
Expected: 'red' (tag names are case-sensitive)

Error: Unclosed tag 'bold' at line 3, column 5
Expected: '</bold>' before end of input

Error: Mismatched closing tag at line 2, column 15
Expected: '</red>' but found '</blue>'
```

---

## Best Practices & Patterns

### Readability Guidelines

#### Use Semantic Tags
```html
<!-- Good: Semantic meaning -->
<red>Error: Connection failed</red>
<green>Success: File saved</green>
<yellow>Warning: Disk space low</yellow>

<!-- Avoid: Arbitrary colors -->
<magenta>Random purple text</magenta>
```

#### Consistent Patterns
```html
<!-- Good: Consistent log format -->
<green>[INFO]</green> Application started
<yellow>[WARN]</yellow> Deprecated API used
<red>[ERROR]</red> Database connection failed

<!-- Good: Consistent emphasis -->
<bold>Important:</bold> Read the documentation
<bold>Note:</bold> This feature is experimental
<bold>Warning:</bold> This action cannot be undone
```

#### Minimal Nesting
```html
<!-- Good: Simple and clear -->
<red>Error: </red><bold>File not found</bold>

<!-- Avoid: Unnecessary complexity -->
<red><bold><italic>Over-formatted error</italic></bold></red>
```

### Common Patterns

#### Log Levels
```html
<brightBlack>[DEBUG]</brightBlack> Detailed diagnostic information
<blue>[INFO]</blue> General information about application flow
<yellow>[WARN]</yellow> Something unexpected happened
<red>[ERROR]</red> Error occurred but application continues
<brightRed>[FATAL]</brightRed> Critical error, application cannot continue
```

#### Git-style Output
```html
<!-- Git status -->
<green>M</green> modified-file.txt
<red>D</red> deleted-file.txt
<blue>A</blue> new-file.txt
<yellow>??</yellow> untracked-file.txt

<!-- Git diff -->
<green>+</green> Added line
<red>-</red> Removed line
<cyan>@@</cyan> Hunk header
```

#### Progress Indicators
```html
<!-- Progress bar -->
Progress: [<green>████████</green><dim>░░</dim>] 80%

<!-- Step indicators -->
<green>✓</green> Step 1: Initialize project
<green>✓</green> Step 2: Install dependencies
<yellow>⚠</yellow> Step 3: Configure database
<dim>○</dim> Step 4: Deploy application
```

#### CLI Tool Output
```html
<!-- Command results -->
<bold>$ npm install</bold>

<dim>added 1204 packages in 45s</dim>

<green>✓</green> <bold>Installation complete!</bold>

<yellow>⚠</yellow> <bold>3 vulnerabilities found</bold>
Run <cyan>npm audit fix</cyan> to resolve them.
```

### Accessibility Considerations

#### Color-blind Friendly
```html
<!-- Good: Use symbols with colors -->
<red>✗ Error:</red> Connection failed
<green>✓ Success:</green> File saved
<yellow>⚠ Warning:</yellow> Low disk space

<!-- Good: Use text indicators -->
<red>[ERROR]</red> Database connection failed
<green>[SUCCESS]</green> Backup completed
<yellow>[WARNING]</yellow> Memory usage high
```

#### High Contrast
```html
<!-- Good: Sufficient contrast -->
<bgRed><white>Critical Alert</white></bgRed>
<bgGreen><black>Success Message</black></bgGreen>

<!-- Avoid: Poor contrast -->
<bgYellow><yellow>Hard to read</yellow></bgYellow>
<bgWhite><white>Invisible text</white></bgWhite>
```

#### Screen Reader Friendly
```html
<!-- Good: Meaningful text -->
<red>Error: Authentication failed</red>
<green>Success: File uploaded successfully</green>

<!-- Avoid: Color-only meaning -->
<red>Failed</red>  <!-- What failed? -->
<green>OK</green>  <!-- OK for what? -->
```

### Migration from ANSI

#### Automated Conversion
Common ANSI to TAML mappings:

```bash
# ANSI escape sequences
\033[31m → <red>
\033[32m → <green>
\033[1m  → <bold>
\033[0m  → </tag> (close current)

# Example conversion
echo "\033[1m\033[31mError:\033[0m File not found"
# Becomes:
<bold><red>Error:</red></bold> File not found
```

#### Manual Migration Tips
1. **Identify patterns**: Look for repeated ANSI sequences
2. **Extract to variables**: Create reusable TAML snippets
3. **Test thoroughly**: Verify visual output matches
4. **Document changes**: Note any behavioral differences

---

## Real-world Examples

### Terminal Output Scenarios

#### NPM Installation
```html
<bold>$ npm install express</bold>

<dim>npm WARN deprecated</dim> <yellow>mkdirp@0.5.1</yellow><dim>: Legacy versions</dim>

<green>added</green> <bold>57</bold> packages, and <green>audited</green> <bold>58</bold> packages in <bold>3s</bold>

<bold>3</bold> packages are looking for funding
  run <cyan>npm fund</cyan> for details

found <bold><green>0</green></bold> vulnerabilities
```

#### Git Status
```html
<bold>On branch</bold> <green>main</green>
<bold>Your branch is up to date with</bold> <red>'origin/main'</red><bold>.</bold>

<bold>Changes to be committed:</bold>
  <dim>(use "git restore --staged <file>..." to unstage)</dim>
        <green>new file:   README.md</green>
        <green>modified:   package.json</green>

<bold>Changes not staged for commit:</bold>
  <dim>(use "git add <file>..." to update what will be committed)</dim>
  <dim>(use "git restore <file>..." to discard changes in working directory)</dim>
        <red>modified:   src/index.js</red>

<bold>Untracked files:</bold>
  <dim>(use "git add <file>..." to include in what will be committed)</dim>
        <red>temp.log</red>
```

#### Build Process
```html
<bold><blue>Building application...</blue></bold>

<green>✓</green> <dim>Compiling TypeScript</dim>
<green>✓</green> <dim>Bundling assets</dim>
<green>✓</green> <dim>Optimizing images</dim>
<yellow>⚠</yellow> <dim>Large bundle size detected</dim>
<green>✓</green> <dim>Generating source maps</dim>

<bold><green>Build completed successfully!</green></bold>

<dim>Output:</dim>
  <cyan>dist/index.html</cyan>    <dim>2.1 kB</dim>
  <cyan>dist/main.js</cyan>      <dim>145.3 kB</dim>
  <cyan>dist/styles.css</cyan>   <dim>23.7 kB</dim>

<bold>Total size:</bold> <bold>171.1 kB</bold>
```

### Log Formatting

#### Application Logs
```html
<dim>2024-12-07 10:30:15</dim> <blue>[INFO]</blue> <bold>Application starting</bold>
<dim>2024-12-07 10:30:16</dim> <blue>[INFO]</blue> Database connection established
<dim>2024-12-07 10:30:16</dim> <blue>[INFO]</blue> Server listening on port 3000
<dim>2024-12-07 10:30:45</dim> <yellow>[WARN]</yellow> <bold>High memory usage:</bold> 85%
<dim>2024-12-07 10:31:02</dim> <red>[ERROR]</red> <bold>Database query failed:</bold>
  <dim>Query:</dim> SELECT * FROM users WHERE id = ?
  <dim>Error:</dim> <red>Connection timeout after 30s</red>
<dim>2024-12-07 10:31:02</dim> <blue>[INFO]</blue> Retrying database connection...
<dim>2024-12-07 10:31:05</dim> <green>[INFO]</green> <bold>Database reconnected successfully</bold>
```

#### Structured Logging
```html
<dim>{"timestamp":"2024-12-07T10:30:15Z","level":"</dim><blue>info</blue><dim>","message":"</dim>User login successful<dim>","user_id":"12345","ip":"192.168.1.100"}</dim>

<dim>{"timestamp":"2024-12-07T10:30:20Z","level":"</dim><yellow>warn</yellow><dim>","message":"</dim>Rate limit approaching<dim>","user_id":"12345","requests_per_minute":58}</dim>

<dim>{"timestamp":"2024-12-07T10:30:25Z","level":"</dim><red>error</red><dim>","message":"</dim>Payment processing failed<dim>","user_id":"12345","error_code":"CARD_DECLINED","amount":"$99.99"}</dim>
```

### CLI Tool Output

#### Test Results
```html
<bold><blue>Running test suite...</blue></bold>

<bold>Authentication Tests</bold>
  <green>✓</green> should login with valid credentials <dim>(15ms)</dim>
  <green>✓</green> should reject invalid password <dim>(8ms)</dim>
  <green>✓</green> should handle expired tokens <dim>(12ms)</dim>

<bold>API Tests</bold>
  <green>✓</green> should create new user <dim>(45ms)</dim>
  <red>✗</red> should update user profile <dim>(23ms)</dim>
    <red>AssertionError:</red> Expected status 200 but got 500
    <dim>at test/api.test.js:42:15</dim>
  <green>✓</green> should delete user <dim>(18ms)</dim>

<bold>Database Tests</bold>
  <yellow>⚠</yellow> should connect to database <dim>(skipped)</dim>
  <yellow>⚠</yellow> should migrate schema <dim>(skipped)</dim>

<bold>Results:</bold>
  <green><bold>4 passing</bold></green> <dim>(98ms)</dim>
  <red><bold>1 failing</bold></red>
  <yellow><bold>2 skipped</bold></yellow>
```

#### Deployment Status
```html
<bold><blue>🚀 Deploying to production...</blue></bold>

<bold>Pre-deployment checks:</bold>
  <green>✓</green> Code quality gates passed
  <green>✓</green> Security scan completed
  <green>✓</green> Performance tests passed
  <yellow>⚠</yellow> Database migration required

<bold>Deployment steps:</bold>
  <green>✓</green> Building Docker image <dim>(2m 15s)</dim>
  <green>✓</green> Pushing to registry <dim>(45s)</dim>
  <green>✓</green> Updating Kubernetes deployment <dim>(30s)</dim>
  <blue>⏳</blue> Running database migrations <dim>(in progress...)</dim>
  <dim>○</dim> Health check verification <dim>(pending)</dim>
  <dim>○</dim> DNS update <dim>(pending)</dim>

<bold>Current status:</bold> <yellow>Deploying</yellow>
<bold>ETA:</bold> <dim>3 minutes remaining</dim>
```

### Documentation Examples

#### API Documentation
```html
<bold><underline>User Authentication API</underline></bold>

<bold>POST</bold> <cyan>/api/auth/login</cyan>

<bold>Request Body:</bold>
<dim>{</dim>
  <blue>"email"</blue><dim>:</dim> <green>"user@example.com"</green><dim>,</dim>
  <blue>"password"</blue><dim>:</dim> <green>"secretpassword"</green>
<dim>}</dim>

<bold>Response:</bold>
<green>200 OK</green> - Login successful
<dim>{</dim>
  <blue>"token"</blue><dim>:</dim> <green>"jwt-token-here"</green><dim>,</dim>
  <blue>"expires_in"</blue><dim>:</dim> <yellow>3600</yellow>
<dim>}</dim>

<red>401 Unauthorized</red> - Invalid credentials
<dim>{</dim>
  <blue>"error"</blue><dim>:</dim> <green>"Invalid email or password"</green>
<dim>}</dim>
```

#### Code Examples
```html
<bold>Example: Error Handling</bold>

<dim>// TypeScript</dim>
<blue>try</blue> <dim>{</dim>
  <blue>const</blue> result <dim>=</dim> <blue>await</blue> <cyan>fetchUserData</cyan><dim>(</dim>userId<dim>);</dim>
  <green>console</green><dim>.</dim><cyan>log</cyan><dim>(</dim><green>'Success:'</green><dim>,</dim> result<dim>);</dim>
<dim>}</dim> <blue>catch</blue> <dim>(</dim>error<dim>) {</dim>
  <red>console</red><dim>.</dim><cyan>error</cyan><dim>(</dim><red>'Failed to fetch user:'</red><dim>,</dim> error<dim>);</dim>
<dim>}</dim>
```

#### Tutorial Content
```html
<bold><underline>Getting Started with TAML</underline></bold>

<bold>Step 1:</bold> <green>Install the TAML parser</green>
<dim>npm install @taml/parser</dim>

<bold>Step 2:</bold> <blue>Import and use</blue>
<dim>import { parseAml } from '@taml/parser';</dim>

<bold>Step 3:</bold> <yellow>Parse your first TAML</yellow>
<dim>const result = parseAml('<red>Hello World</red>');</dim>

<green>✓ Congratulations!</green> You've successfully parsed TAML.

<bold>Next steps:</bold>
• Learn about <cyan>nesting tags</cyan>
• Explore <magenta>color combinations</magenta>
• Try <italic>advanced patterns</italic>
```

---

## Technical Appendices

### Appendix A: Complete ANSI Mapping Table

| TAML Tag | ANSI Escape | Reset Code | Hex Color | RGB Values |
|---------|-------------|------------|-----------|------------|
| `<black>` | `\033[30m` | `\033[39m` | `#000000` | `0,0,0` |
| `<red>` | `\033[31m` | `\033[39m` | `#800000` | `128,0,0` |
| `<green>` | `\033[32m` | `\033[39m` | `#008000` | `0,128,0` |
| `<yellow>` | `\033[33m` | `\033[39m` | `#808000` | `128,128,0` |
| `<blue>` | `\033[34m` | `\033[39m` | `#000080` | `0,0,128` |
| `<magenta>` | `\033[35m` | `\033[39m` | `#800080` | `128,0,128` |
| `<cyan>` | `\033[36m` | `\033[39m` | `#008080` | `0,128,128` |
| `<white>` | `\033[37m` | `\033[39m` | `#c0c0c0` | `192,192,192` |
| `<brightBlack>` | `\033[90m` | `\033[39m` | `#808080` | `128,128,128` |
| `<brightRed>` | `\033[91m` | `\033[39m` | `#ff0000` | `255,0,0` |
| `<brightGreen>` | `\033[92m` | `\033[39m` | `#00ff00` | `0,255,0` |
| `<brightYellow>` | `\033[93m` | `\033[39m` | `#ffff00` | `255,255,0` |
| `<brightBlue>` | `\033[94m` | `\033[39m` | `#0000ff` | `0,0,255` |
| `<brightMagenta>` | `\033[95m` | `\033[39m` | `#ff00ff` | `255,0,255` |
| `<brightCyan>` | `\033[96m` | `\033[39m` | `#00ffff` | `0,255,255` |
| `<brightWhite>` | `\033[97m` | `\033[39m` | `#ffffff` | `255,255,255` |

#### Background Colors

| TAML Tag | ANSI Escape | Reset Code |
|---------|-------------|------------|
| `<bgBlack>` | `\033[40m` | `\033[49m` |
| `<bgRed>` | `\033[41m` | `\033[49m` |
| `<bgGreen>` | `\033[42m` | `\033[49m` |
| `<bgYellow>` | `\033[43m` | `\033[49m` |
| `<bgBlue>` | `\033[44m` | `\033[49m` |
| `<bgMagenta>` | `\033[45m` | `\033[49m` |
| `<bgCyan>` | `\033[46m` | `\033[49m` |
| `<bgWhite>` | `\033[47m` | `\033[49m` |
| `<bgBrightBlack>` | `\033[100m` | `\033[49m` |
| `<bgBrightRed>` | `\033[101m` | `\033[49m` |
| `<bgBrightGreen>` | `\033[102m` | `\033[49m` |
| `<bgBrightYellow>` | `\033[103m` | `\033[49m` |
| `<bgBrightBlue>` | `\033[104m` | `\033[49m` |
| `<bgBrightMagenta>` | `\033[105m` | `\033[49m` |
| `<bgBrightCyan>` | `\033[106m` | `\033[49m` |
| `<bgBrightWhite>` | `\033[107m` | `\033[49m` |

#### Text Styles

| TAML Tag | ANSI Escape | Reset Code |
|---------|-------------|------------|
| `<bold>` | `\033[1m` | `\033[22m` |
| `<dim>` | `\033[2m` | `\033[22m` |
| `<italic>` | `\033[3m` | `\033[23m` |
| `<underline>` | `\033[4m` | `\033[24m` |
| `<strikethrough>` | `\033[9m` | `\033[29m` |

#### Universal Reset
- **Full Reset**: `\033[0m` - Resets all formatting to default

### Appendix B: Formal Grammar Specification

TAML grammar in Extended Backus-Naur Form (EBNF):

```ebnf
(* TAML Grammar Specification *)

document = { element | text } ;

element = open_tag , { element | text } , close_tag ;

open_tag = "<" , tag_name , ">" ;
close_tag = "</" , tag_name , ">" ;

tag_name = standard_color | bright_color | background_color | text_style ;

(* Standard Colors *)
standard_color = "black" | "red" | "green" | "yellow" |
                "blue" | "magenta" | "cyan" | "white" ;

(* Bright Colors *)
bright_color = "brightBlack" | "brightRed" | "brightGreen" | "brightYellow" |
              "brightBlue" | "brightMagenta" | "brightCyan" | "brightWhite" ;

(* Background Colors *)
background_color = bg_standard | bg_bright ;
bg_standard = "bgBlack" | "bgRed" | "bgGreen" | "bgYellow" |
             "bgBlue" | "bgMagenta" | "bgCyan" | "bgWhite" ;
bg_bright = "bgBrightBlack" | "bgBrightRed" | "bgBrightGreen" | "bgBrightYellow" |
           "bgBrightBlue" | "bgBrightMagenta" | "bgBrightCyan" | "bgBrightWhite" ;

(* Text Styles *)
text_style = "bold" | "dim" | "italic" | "underline" | "strikethrough" ;

(* Text Content *)
text = { unicode_char - "<" } ;
unicode_char = ? any valid Unicode character ? ;
```

### Appendix C: Character Encoding Details

#### UTF-8 Support
TAML fully supports UTF-8 encoding, including:

- **Basic Latin**: A-Z, a-z, 0-9, punctuation
- **Extended Latin**: àáâãäå, çñü, etc.
- **Cyrillic**: абвгдеёжз, АБВГДЕЁЖЗ
- **Greek**: αβγδεζηθι, ΑΒΓΔΕΖΗΘΙ
- **CJK**: 中文, 日本語, 한국어
- **Emoji**: 🎨🚀💡⚡🔥✨🎯🌟
- **Symbols**: ←→↑↓, ✓✗⚠, ♠♥♦♣

#### Special Characters
```html
<!-- Mathematical symbols -->
<blue>∑∏∫∆∇∂∞</blue>

<!-- Currency symbols -->
<green>$€£¥₹₿</green>

<!-- Arrows and symbols -->
<yellow>←→↑↓⇄⇅⇆⇇</yellow>

<!-- Box drawing -->
<cyan>┌─┐│└─┘</cyan>

<!-- Emoji combinations -->
<red>🚨</red> <yellow>⚠️</yellow> <green>✅</green> <blue>ℹ️</blue>
```

#### Whitespace Handling
```html
<!-- Preserved: Leading/trailing spaces -->
<red>  spaced text  </red>

<!-- Preserved: Internal formatting -->
<green>Line 1
    Indented line 2
Line 3</green>
```

### Appendix D: Performance Benchmarks

#### Parsing Performance
Based on reference implementation benchmarks:

| Input Size | Parse Time | Memory Usage | Tokens/sec |
|------------|------------|--------------|------------|
| 1 KB | 0.1ms | 2 KB | 10M |
| 10 KB | 0.8ms | 15 KB | 12M |
| 100 KB | 7.2ms | 120 KB | 14M |
| 1 MB | 68ms | 1.1 MB | 15M |

#### Optimization Guidelines
- **Tokenization**: O(n) linear time complexity
- **Parsing**: O(n) for well-formed input
- **Memory**: O(d) where d is maximum nesting depth
- **Caching**: Recommended for repeated parsing

#### Stress Test Results
```html
<!-- Deep nesting (100 levels) -->
<red><bold><italic>...(97 more levels)...</italic></bold></red>
Parse time: 2.3ms

<!-- Wide content (1000 tags) -->
<red>1</red><green>2</green>...<blue>1000</blue>
Parse time: 15.7ms

<!-- Large text blocks (10MB) -->
<red>Very long text content...</red>
Parse time: 145ms
```

### Appendix E: Implementation Examples

#### Minimal Parser (Pseudocode)
```pseudocode
function parseAml(input):
    tokens = tokenize(input)
    return parseTokens(tokens)

function tokenize(input):
    tokens = []
    position = 0
    
    while position < input.length:
        if input[position] == '<':
            token = parseTag(input, position)
            tokens.append(token)
            position = token.end
        else:
            token = parseText(input, position)
            tokens.append(token)
            position = token.end
    
    return tokens

function parseTokens(tokens):
    nodes = []
    stack = []
    
    for token in tokens:
        if token.type == OPEN_TAG:
            stack.push(token)
        elif token.type == CLOSE_TAG:
            if stack.empty() or stack.top().name != token.name:
                throw MismatchedTagError
            openTag = stack.pop()
            node = createElement(openTag, nodes_since_open)
            nodes.append(node)
        elif token.type == TEXT:
            nodes.append(createTextNode(token))
    
    if not stack.empty():
        throw UnclosedTagError
    
    return nodes
```

#### Error Recovery Strategies
```pseudocode
function parseWithRecovery(input):
    try:
        return parseAml(input)
    catch UnclosedTagError as e:
        // Auto-close unclosed tags
        return parseWithAutoClose(input, e.position)
    catch InvalidTagError as e:
        // Skip invalid tags, continue parsing
        return parseWithSkip(input, e.position)
    catch MismatchedTagError as e:
        // Attempt to fix mismatched tags
        return parseWithCorrection(input, e.position)
```

### Appendix F: Migration Tools

#### ANSI to TAML Converter
```bash
# Command-line converter
ansi2taml input.txt > output.taml

# Example conversion
echo "\033[1m\033[31mError:\033[0m File not found" | ansi2aml
# Output: <bold><red>Error:</red></bold> File not found
```

#### Common Conversion Patterns
```bash
# Simple color codes
s/\\033\[31m/<red>/g
s/\\033\[32m/<green>/g
s/\\033\[0m/<\/[^>]+>/g

# Bold text
s/\\033\[1m/<bold>/g
s/\\033\[22m/<\/bold>/g

# Complex sequences
s/\\033\[1;31m/<bold><red>/g
s/\\033\[0m/<\/red><\/bold>/g
```

#### Validation Tools
```bash
# Validate TAML syntax
taml-validate document.taml

# Check for common issues
taml-lint --check-nesting --check-colors document.taml

# Performance analysis
taml-bench --input large-document.taml --iterations 1000
```

---

## Conclusion

TAML (ANSI Markup Language) provides a human-readable, maintainable alternative to raw ANSI escape sequences. This specification defines:

- **37 total tags** covering all common terminal formatting needs
- **Clear syntax rules** for reliable parsing and validation
- **Comprehensive examples** for practical implementation
- **Performance guidelines** for efficient processing
- **Migration strategies** from existing ANSI-based systems

### Key Benefits Recap

1. **Readability**: Clear, semantic markup instead of cryptic escape codes
2. **Maintainability**: Easy to modify and version control
3. **Reliability**: Structured format prevents malformed output
4. **Accessibility**: Better support for screen readers and color-blind users
5. **Tooling**: Enables syntax highlighting, validation, and automated processing

### Implementation Checklist

For implementers creating TAML parsers:

- [ ] Tokenizer supporting all 37 tag types
- [ ] Proper nesting validation
- [ ] Comprehensive error reporting
- [ ] Performance optimization for large inputs
- [ ] UTF-8 character encoding support
- [ ] Memory-efficient AST representation
- [ ] Configurable parsing options
- [ ] Test suite covering edge cases

### Future Considerations

While this specification is stable, potential future enhancements might include:

- **Custom color definitions**: User-defined color palettes
- **Extended attributes**: Additional text formatting options
- **Conditional formatting**: Environment-aware styling
- **Compression**: Optimized encoding for large documents
- **Streaming**: Support for real-time parsing

---

**Document Version**: 1.0
**Last Updated**: December 2024
**License**: MIT
**Maintainer**: TAML Working Group

For questions, suggestions, or contributions to this specification, please visit the official TAML repository or contact the maintainers.