# Design Document: CODE X STUDY AI

## Overview

CODE X STUDY AI is a single-page web application that provides an AI-powered educational assistant with three distinct learning modes: conversational chat, structured note generation, and interactive quizzes. The application is implemented as a self-contained HTML file with embedded CSS and JavaScript, featuring multi-language support (English, Hindi, Tamil), voice input/output capabilities, theme switching, and various utility functions for managing learning sessions.

The architecture follows a client-side pattern where user interactions trigger asynchronous API calls to the Groq API service (llama-3.1-8b-instant model). The application maintains session state including chat history, current mode, language preferences, quiz state, and theme settings. All functionality is browser-based with no backend server required.

## Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Browser (Client)                         │
│  ┌───────────────────────────────────────────────────────┐  │
│  │              HTML Document                            │  │
│  │  ┌──────────────┐  ┌─────────────┐  ┌─────────────┐  │  │
│  │  │  UI Layer    │  │   State     │  │   Event     │  │  │
│  │  │  (DOM/CSS)   │←→│  Manager    │←→│  Handlers   │  │  │
│  │  │              │  │             │  │             │  │  │
│  │  │ - Sidebar    │  │ - Mode      │  │ - Click     │  │  │
│  │  │ - Header     │  │ - Language  │  │ - Keyboard  │  │  │
│  │  │ - Chat Area  │  │ - Theme     │  │ - File      │  │  │
│  │  │ - Input Bar  │  │ - Quiz      │  │             │  │  │
│  │  └──────────────┘  └─────────────┘  └─────────────┘  │  │
│  │         ↓                  ↓                ↓         │  │
│  │  ┌───────────────────────────────────────────────┐   │  │
│  │  │        API Communication Layer                │   │  │
│  │  │          (Fetch API)                          │   │  │
│  │  └───────────────────────────────────────────────┘   │  │
│  │         ↓                                             │  │
│  │  ┌───────────────────────────────────────────────┐   │  │
│  │  │      Browser APIs                             │   │  │
│  │  │  - SpeechRecognition (Voice Input)            │   │  │
│  │  │  - SpeechSynthesis (Voice Output)             │   │  │
│  │  │  - FileReader (File Upload)                   │   │  │
│  │  │  - Blob (Export)                              │   │  │
│  │  └───────────────────────────────────────────────┘   │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                           ↓ HTTPS
┌─────────────────────────────────────────────────────────────┐
│              Groq API Service                               │
│         (llama-3.1-8b-instant model)                        │
└─────────────────────────────────────────────────────────────┘
```

### Component Interaction Flow

**Standard Question Flow:**
1. User enters question in input field (or uses voice/file input)
2. User clicks send button or presses Enter
3. Input validation (non-empty check)
4. User message added to chat area
5. "Thinking..." placeholder added
6. System prompt constructed based on current mode
7. API request sent to Groq with system prompt and user message
8. Response received and parsed
9. "Thinking..." message removed
10. AI response added to chat area
11. Response spoken via voice output
12. Chat area auto-scrolls to bottom

**Mode-Specific Flows:**
- **Chat Mode**: Standard conversational flow with generic assistant prompt
- **Notes Mode**: System prompt requests structured notes on user's topic
- **Quiz Mode**: Two-phase flow (question generation → answer checking)

## Components and Interfaces

### UI Components

#### Sidebar Component
- **Purpose**: Provides quick access to utility functions
- **Width**: 220px fixed
- **Background**: Uses theme-specific app background color
- **Padding**: 80px top, 10px gap between buttons
- **Buttons**:
  - History (📜): Toggles chat visibility
  - Stop Voice (⏹): Cancels speech synthesis
  - Clear Chat (🧹): Removes all messages
  - Export Chat (📤): Downloads chat as text file
  - Theme (🌗): Switches between dark/light themes
- **Button Styling**: 
  - Background: #334155
  - Color: white
  - Padding: 12px
  - Border-radius: 10px
  - Cursor: pointer

#### Header Component
- **Purpose**: Displays title and mode/language selectors
- **Layout**: Centered column flex layout
- **Elements**:
  - Title: "🎓 CODE X STUDY AI"
  - Mode Selector: Dropdown with Chat/Notes/Quiz options
  - Language Selector: Dropdown with English/Hindi/Tamil options
  - Level Selector: Dropdown with Easy/Medium/Hard (visible only in Quiz mode)
- **Selector Styling**:
  - Background: aqua
  - Color: black
  - Border-radius: 20px
  - Padding: 6px 14px
  - Font-weight: bold

#### Chat Area Component
- **Purpose**: Displays conversation history
- **Layout**: Scrollable vertical flex column
- **Padding**: 15px top, 0 left/right, 120px bottom
- **Max-width**: 800px for messages
- **Auto-scroll**: Scrolls to bottom when new message added
- **Message Types**:
  - User messages: Right-aligned, theme-specific user color
  - Bot messages: Left-aligned, theme-specific bot color
- **Message Styling**:
  - Padding: 10px vertical, 14px horizontal
  - Margin: 6px vertical
  - Border-radius: 14px
  - Line breaks: Preserved via `<br>` tag conversion

#### Input Bar Component
- **Purpose**: Provides input methods and submission controls
- **Position**: Fixed at bottom (95% from top, 63% from left)
- **Max-width**: 900px
- **Layout**: Horizontal flex with 10px gaps
- **Elements**:
  - File Upload Button (📁): Triggers hidden file input
  - Text Input Field: Flex-grow to fill space
  - Voice Input Button (🎤): Activates speech recognition
  - Send Button (✈️): Submits message
- **Styling**:
  - Border-radius: 35px
  - Background: Theme-specific app color
  - Buttons: 42px diameter circles
  - Send button: Cyan background (#38bdf8)

### JavaScript Functions and Interfaces

#### Message Management

**`addMessage(text, cls)` Function**
```javascript
Parameters:
  text: string  // Message content
  cls: string   // CSS class ("user" or "bot")

Behavior:
  1. Create div element
  2. Set className to "msg " + cls
  3. Convert \n to <br> in text
  4. Append to chat area
  5. Scroll chat to bottom
```

#### Mode Management

**Mode Change Handler**
```javascript
Trigger: mode selector change event

Behavior:
  IF mode === "Quiz" THEN
    Show level selector
  ELSE
    Hide level selector
  END IF
```

#### Theme Management

**`toggleTheme()` Function**
```javascript
Behavior:
  Toggle "light" class on body element
  
CSS Variables Update:
  Dark theme (default):
    --bg: #020617
    --app: #0f172a
    --chat: #020617
    --user: #38bdf8
    --bot: #334155
    --text: white
  
  Light theme:
    --bg: #e5e7eb
    --app: white
    --chat: #f3f4f6
    --user: #2563eb
    --bot: #e5e7eb
    --text: black
```

#### History Management

**`toggleHistory()` Function**
```javascript
State: historyVisible (boolean)

Behavior:
  Toggle historyVisible flag
  IF historyVisible THEN
    chat.style.display = "block"
  ELSE
    chat.style.display = "none"
  END IF
```

#### Chat Management

**`clearChat()` Function**
```javascript
Behavior:
  Set chat.innerHTML = ""
```

**`exportChat()` Function**
```javascript
Behavior:
  1. Extract chat.innerText
  2. Create Blob with text/plain type
  3. Create temporary anchor element
  4. Set href to Blob URL
  5. Set download attribute to "chat.txt"
  6. Trigger click
```

#### File Upload

**File Input Change Handler**
```javascript
Trigger: file input change event

Behavior:
  1. Get selected file
  2. Return if no file
  3. Create FileReader
  4. On load: Set input.value to first 2000 characters
  5. Read file as text
```

#### Voice Input

**`startVoice()` Function**
```javascript
Behavior:
  1. Create webkitSpeechRecognition instance
  2. Set language based on language selector:
     - "en" → "en-IN"
     - "hi" → "hi-IN"
     - "ta" → "ta-IN"
  3. On result: Set input.value to transcript
  4. Start recognition
```

#### Voice Output

**`speak(text)` Function**
```javascript
Parameters:
  text: string  // Text to speak

Behavior:
  1. Cancel any ongoing speech
  2. Create SpeechSynthesisUtterance
  3. Set language based on language selector:
     - "en" → "en-IN"
     - "hi" → "hi-IN"
     - "ta" → "ta-IN"
  4. Speak utterance
```

**`stopVoice()` Function**
```javascript
Behavior:
  speechSynthesis.cancel()
```

#### Main Question Handler

**`ask()` Function**
```javascript
Behavior:
  1. Get trimmed input value
  2. Return if empty
  3. Add user message to chat
  4. Clear input field
  5. Add "Thinking..." bot message
  6. Construct system prompt based on mode
  7. Send API request
  8. Remove "Thinking..." message
  9. Add AI response to chat
  10. Speak AI response

System Prompt Construction:
  IF mode === "Notes" THEN
    prompt = "You are a study assistant. Create clear, structured notes on the topic: {question}. Use bullet points and simple language."
    quizStarted = false
  
  ELSE IF mode === "Quiz" THEN
    IF NOT quizStarted THEN
      quizStarted = true
      prompt = "You are a quiz master. Ask ONE {level} level MCQ with 4 options. Do NOT give answer."
    ELSE
      prompt = "You are a quiz master. Check answer. Say Correct or Wrong. Then ask NEXT {level} question."
    END IF
  
  ELSE (Chat mode)
    quizStarted = false
    prompt = "You are a helpful assistant."
  END IF
```

#### Keyboard Handler

**Input Keydown Handler**
```javascript
Trigger: keydown event on input field

Behavior:
  IF key === "Enter" THEN
    call ask()
  END IF
```

## Data Models

### Application State

```javascript
{
  quizStarted: boolean,      // Tracks if quiz is in progress
  historyVisible: boolean,   // Tracks chat visibility
  currentMode: string,       // "Chat" | "Notes" | "Quiz"
  currentLanguage: string,   // "en" | "hi" | "ta"
  currentLevel: string,      // "Easy" | "Medium" | "Hard"
  currentTheme: string       // "dark" | "light" (implicit via CSS class)
}
```

### Message Model

```javascript
{
  text: string,              // Message content
  type: string,              // "user" | "bot"
  timestamp: implicit        // Order in DOM
}
```

### API Request Payload

```javascript
{
  model: "llama-3.1-8b-instant",
  messages: [
    {
      role: "system",
      content: string        // Mode-specific system prompt
    },
    {
      role: "user",
      content: string        // User's question
    }
  ]
}
```

### API Response Structure

```javascript
{
  choices: [
    {
      message: {
        role: "assistant",
        content: string      // AI-generated response
      }
    }
  ]
}
```

### Theme Configuration

```javascript
{
  dark: {
    bg: "#020617",
    app: "#0f172a",
    chat: "#020617",
    user: "#38bdf8",
    bot: "#334155",
    text: "white"
  },
  light: {
    bg: "#e5e7eb",
    app: "white",
    chat: "#f3f4f6",
    user: "#2563eb",
    bot: "#e5e7eb",
    text: "black"
  }
}
```

## Data Flow and State Management

### State Transitions

**Mode Transitions:**
```
Initial State: Chat mode, level selector hidden

Chat → Notes:
  - Hide level selector
  - Reset quizStarted to false
  - Update system prompt template

Chat → Quiz:
  - Show level selector
  - Reset quizStarted to false
  - Update system prompt template

Notes → Quiz:
  - Show level selector
  - Reset quizStarted to false
  - Update system prompt template

Notes → Chat:
  - Hide level selector
  - Reset quizStarted to false
  - Update system prompt template

Quiz → Chat/Notes:
  - Hide level selector (if going to Chat/Notes)
  - Reset quizStarted to false
  - Update system prompt template
```

**Quiz State Machine:**
```
State: NOT_STARTED
  On question submission:
    → Generate first question
    → Transition to IN_PROGRESS

State: IN_PROGRESS
  On answer submission:
    → Check answer
    → Generate next question
    → Stay in IN_PROGRESS

On mode change away from Quiz:
  → Transition to NOT_STARTED
```

**Theme State:**
```
Initial: Dark theme (no "light" class)

On toggle:
  IF dark THEN
    Add "light" class → Light theme
  ELSE
    Remove "light" class → Dark theme
  END IF
```

**History Visibility State:**
```
Initial: Visible (historyVisible = true)

On toggle:
  IF visible THEN
    Hide chat area
    historyVisible = false
  ELSE
    Show chat area
    historyVisible = true
  END IF
```

### Data Flow Diagrams

**Message Submission Flow:**
```
User Input → Validation → User Message Display → API Request
                ↓                                      ↓
            (if empty)                          Thinking Display
                ↓                                      ↓
              Return                            API Response
                                                       ↓
                                              Remove Thinking
                                                       ↓
                                              Bot Message Display
                                                       ↓
                                                Voice Output
```

**Voice Input Flow:**
```
Voice Button Click → Speech Recognition Start → Audio Capture
                                                      ↓
                                                Transcription
                                                      ↓
                                              Input Field Update
```

**File Upload Flow:**
```
File Button Click → File Dialog → File Selection → FileReader
                                                        ↓
                                                  Read as Text
                                                        ↓
                                              Truncate to 2000 chars
                                                        ↓
                                              Input Field Update
```

## API Integration Design

### Groq API Configuration

**Endpoint**: `https://api.groq.com/openai/v1/chat/completions`

**Authentication**: Bearer token in Authorization header

**Model**: `llama-3.1-8b-instant`

### Request Construction

```javascript
fetch(endpoint, {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "Authorization": "Bearer " + API_KEY
  },
  body: JSON.stringify({
    model: "llama-3.1-8b-instant",
    messages: [
      { role: "system", content: systemPrompt },
      { role: "user", content: userQuestion }
    ]
  })
})
```

### Response Handling

**Success Path:**
```javascript
const data = await response.json()
const aiMessage = data.choices[0].message.content
```

**Error Path:**
```javascript
try {
  // API call
} catch (error) {
  // Display error in chat
  // Log to console
}
```

### System Prompt Templates

**Chat Mode:**
```
"You are a helpful assistant."
```

**Notes Mode:**
```
"You are a study assistant. Create clear, structured notes on the topic: {topic}. Use bullet points and simple language."
```

**Quiz Mode - Initial Question:**
```
"You are a quiz master. Ask ONE {level} level MCQ with 4 options. Do NOT give answer."
```

**Quiz Mode - Answer Checking:**
```
"You are a quiz master. Check answer. Say Correct or Wrong. Then ask NEXT {level} question."
```


## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: Theme Toggle Behavior

*For any* theme state (dark or light), clicking the theme toggle button should switch to the opposite theme state.

**Validates: Requirements 2.12**

### Property 2: Level Selector Visibility in Quiz Mode

*For any* initial mode state, selecting Quiz mode should make the Level_Selector visible, and selecting Chat or Notes mode should hide the Level_Selector.

**Validates: Requirements 4.5, 4.6**

### Property 3: Auto-Scroll on Message Addition

*For any* message added to the Chat_Area, the scroll position should automatically move to the bottom of the chat area.

**Validates: Requirements 5.5**

### Property 4: API Call on Question Submission

*For any* non-empty question submitted in any mode, an API request should be sent to the Groq API endpoint.

**Validates: Requirements 7.2, 17.1**

### Property 5: User Message Display

*For any* question submitted, the user's message should appear in the Chat_Area with user styling before the API call is made.

**Validates: Requirements 7.3**

### Property 6: Thinking State Display

*For any* question being processed, a "Thinking..." message with bot styling should appear in the Chat_Area until the response is received.

**Validates: Requirements 7.4, 7.5**

### Property 7: Response Display and Voice Output

*For any* AI response received, the response should be displayed in the Chat_Area with bot styling and automatically spoken using voice output.

**Validates: Requirements 7.6, 7.7**

### Property 8: Line Break Preservation

*For any* message text containing newline characters (\n), the displayed message should convert those newlines to HTML <br> tags to preserve formatting.

**Validates: Requirements 7.8**

### Property 9: Topic Inclusion in Notes Mode Prompt

*For any* topic submitted in Notes mode, the system prompt should include that topic in the instruction to the AI.

**Validates: Requirements 8.2**

### Property 10: Quiz State Reset on Mode Change

*For any* mode change away from Quiz mode (to Chat or Notes), the quiz state (quizStarted flag) should be reset to false.

**Validates: Requirements 8.4, 9.10**

### Property 11: Difficulty Level in Quiz Prompt

*For any* difficulty level selected in Quiz mode, the system prompt should include that difficulty level in the instruction to the AI.

**Validates: Requirements 9.3**

### Property 12: Voice Input Activation

*For any* click on the voice input button, speech recognition should be activated with the appropriate language locale based on the current language selection.

**Validates: Requirements 10.1, 10.2, 10.3, 10.4**

### Property 13: Speech Recognition Result Handling

*For any* speech recognized by the voice input system, the transcribed text should populate the input field.

**Validates: Requirements 10.5**

### Property 14: Voice Output Language Mapping

*For any* language selected (English, Hindi, or Tamil), voice output should use the corresponding locale (en-IN, hi-IN, or ta-IN).

**Validates: Requirements 11.2, 11.3, 11.4**

### Property 15: Voice Output Cancellation

*For any* new voice output started or stop voice button clicked, any currently playing speech should be cancelled before the new speech begins (or immediately for stop button).

**Validates: Requirements 11.5, 11.6**

### Property 16: File Upload Dialog Trigger

*For any* click on the file upload button, a file selection dialog should open.

**Validates: Requirements 12.1**

### Property 17: File Content Reading and Population

*For any* file selected through the file upload dialog, the file content should be read as text and populate the input field, truncated to 2000 characters if necessary.

**Validates: Requirements 12.2, 12.3, 12.4**

### Property 18: History Visibility Toggle

*For any* click on the history button, the Chat_Area visibility should toggle between visible (display: block) and hidden (display: none).

**Validates: Requirements 13.1, 13.2, 13.3**

### Property 19: Clear Chat Functionality

*For any* chat state with messages, clicking the clear chat button should remove all messages from the Chat_Area, leaving it empty.

**Validates: Requirements 14.1**

### Property 20: Chat Export Functionality

*For any* chat state, clicking the export button should create a text file named "chat.txt" containing the plain text content of all messages and trigger a download.

**Validates: Requirements 15.1, 15.2, 15.3, 15.4**

### Property 21: Enter Key Message Submission

*For any* non-empty input in the input field, pressing the Enter key should trigger the same message submission behavior as clicking the send button.

**Validates: Requirements 16.1, 16.2**

### Property 22: API Request Structure

*For any* question submitted, the API request should include the correct HTTP method (POST), Content-Type header (application/json), Authorization header with API key, model specification (llama-3.1-8b-instant), mode-specific system prompt, and the user's question as a user message.

**Validates: Requirements 17.2, 17.3, 17.4, 17.5, 17.6, 17.7, 17.8**

### Property 23: Input Field Clearing After Submission

*For any* question submitted, the input field should be cleared (empty) after the submission is processed.

**Validates: Requirements 17.10**

### Property 24: Empty Input Rejection

*For any* input that is empty or contains only whitespace characters, attempting to submit (via send button or Enter key) should have no effect and not trigger an API call.

**Validates: Requirements 18.1**

## Error Handling

### Input Validation Errors

**Empty Input Detection**
- Check if input value is empty or contains only whitespace using `trim()`
- Return early from `ask()` function without making API call
- No error message displayed (silent validation)
- User can immediately try again with valid input

### Network Errors

**Connection Failures**
- Catch fetch API network errors in try-catch block
- Log error to console for debugging
- Display error message in chat area as bot message
- Examples: "Failed to fetch", "Network request failed"
- System remains functional for retry

**Timeout Errors**
- Browser-level timeout handling (no custom timeout)
- Treated as network error
- Display error message in chat area
- User can retry after timeout

### API Errors

**Authentication Errors (401)**
- Invalid or expired API key
- Catch in error handling
- Display error message in chat area
- User cannot proceed without valid credentials

**Rate Limiting (429)**
- Too many requests to Groq API
- Display error message in chat area
- User must wait before retrying
- Consider implementing client-side rate limiting

**Server Errors (5xx)**
- Groq API service issues
- Display error message in chat area
- User can retry after service recovers

### Response Parsing Errors

**Malformed JSON**
- API returns invalid JSON
- Catch JSON parsing exception
- Display error message in chat area
- Log error to console

**Missing Response Fields**
- API response lacks expected structure (choices[0].message.content)
- JavaScript error when accessing nested properties
- Catch in error handling
- Display error message in chat area

### Browser API Errors

**Speech Recognition Errors**
- Browser doesn't support webkitSpeechRecognition
- Microphone permission denied
- No speech detected
- Network error during recognition
- Handle gracefully with error messages
- Fall back to text input

**Speech Synthesis Errors**
- Browser doesn't support SpeechSynthesis
- No voices available for selected language
- Handle gracefully with error messages
- Continue without voice output

**File Reader Errors**
- File too large
- File read permission denied
- Invalid file format
- Handle gracefully with error messages
- Clear file input for retry

### Error Recovery Strategy

All errors follow a consistent recovery pattern:

1. **Catch Exception**: Use try-catch blocks around async operations
2. **Log Error**: Console.log for debugging
3. **User Notification**: Display error in chat area as bot message
4. **Preserve State**: Keep existing messages and settings
5. **Enable Retry**: Allow immediate retry without page reload
6. **Graceful Degradation**: Continue with reduced functionality if possible

**Error Message Format**:
```
"Error: {error_description}"
```

**Example Error Handling**:
```javascript
try {
  const response = await fetch(...)
  const data = await response.json()
  const aiMessage = data.choices[0].message.content
  addMessage(aiMessage, "bot")
} catch (error) {
  console.error("API Error:", error)
  addMessage("Error: " + error.message, "bot")
}
```

## Testing Strategy

### Dual Testing Approach

The testing strategy employs both unit tests and property-based tests to ensure comprehensive coverage:

- **Unit tests** verify specific examples, edge cases, and error conditions
- **Property tests** verify universal properties across all inputs
- Both approaches are complementary and necessary for complete validation

### Unit Testing Focus

Unit tests should focus on:

1. **Specific UI Examples**
   - Initial page load: sidebar width is 220px
   - Initial page load: dark theme is active (no "light" class)
   - Initial page load: level selector is hidden
   - Initial page load: history is visible
   - Sidebar contains exactly 5 buttons with correct labels
   - Header contains title "🎓 CODE X STUDY AI"
   - Input bar contains 4 elements (file, input, voice, send)
   - Send button has cyan background (#38bdf8)

2. **Specific CSS Properties**
   - Dark theme colors: --bg: #020617, --user: #38bdf8, etc.
   - Light theme colors: --bg: #e5e7eb, --user: #2563eb, etc.
   - Message border-radius: 14px
   - Input bar border-radius: 35px
   - Button dimensions: 42px diameter

3. **Mode-Specific System Prompts**
   - Chat mode: "You are a helpful assistant."
   - Notes mode: Contains "study assistant", "structured notes", "bullet points"
   - Quiz mode (initial): Contains "quiz master", "ONE", "MCQ", "4 options", "Do NOT give answer"
   - Quiz mode (checking): Contains "Check answer", "Correct or Wrong", "NEXT"

4. **Language Locale Mapping**
   - English → "en-IN"
   - Hindi → "hi-IN"
   - Tamil → "ta-IN"

5. **Edge Cases**
   - Empty input field prevents submission
   - Whitespace-only input prevents submission
   - File content truncated at 2000 characters
   - Multiple theme toggles work correctly
   - Multiple history toggles work correctly

6. **Browser API Usage**
   - webkitSpeechRecognition is instantiated for voice input
   - SpeechSynthesisUtterance is created for voice output
   - FileReader is used for file upload
   - Blob is created for export

### Property-Based Testing Focus

Property tests should verify universal behaviors across randomized inputs:

1. **Theme and UI State Properties**
   - Property 1: Theme toggle behavior (any theme state)
   - Property 2: Level selector visibility (any mode)
   - Property 18: History visibility toggle (any visibility state)

2. **Message Display Properties**
   - Property 3: Auto-scroll on message addition (any message)
   - Property 5: User message display (any question)
   - Property 7: Response display and voice output (any response)
   - Property 8: Line break preservation (any text with \n)

3. **API Communication Properties**
   - Property 4: API call on question submission (any non-empty question)
   - Property 6: Thinking state display (any question being processed)
   - Property 22: API request structure (any question)
   - Property 23: Input field clearing (any submission)
   - Property 24: Empty input rejection (any whitespace-only input)

4. **Mode-Specific Properties**
   - Property 9: Topic inclusion in Notes mode (any topic)
   - Property 10: Quiz state reset (any mode change)
   - Property 11: Difficulty level in quiz prompt (any level)

5. **Voice Interaction Properties**
   - Property 12: Voice input activation (any language)
   - Property 13: Speech recognition result handling (any recognized speech)
   - Property 14: Voice output language mapping (any language)
   - Property 15: Voice output cancellation (any new speech or stop)

6. **File and Input Properties**
   - Property 16: File upload dialog trigger (any click)
   - Property 17: File content reading and population (any file)
   - Property 21: Enter key message submission (any non-empty input)

7. **Utility Function Properties**
   - Property 19: Clear chat functionality (any chat state)
   - Property 20: Chat export functionality (any chat state)

### Property Test Configuration

**Testing Library**: Use **fast-check** for JavaScript property-based testing

**Test Configuration**:
- Minimum 100 iterations per property test
- Each test tagged with: **Feature: codex-bot, Property {N}: {property description}**
- Use appropriate generators:
  - `fc.string()` for general text inputs
  - `fc.lorem()` for realistic question text
  - `fc.constantFrom("Chat", "Notes", "Quiz")` for mode selection
  - `fc.constantFrom("en", "hi", "ta")` for language selection
  - `fc.constantFrom("Easy", "Medium", "Hard")` for difficulty levels
  - `fc.array(fc.lorem())` for chat messages
  - Custom generators for API responses

**Example Test Structure**:
```javascript
// Feature: codex-bot, Property 1: Theme toggle behavior
fc.assert(
  fc.property(fc.boolean(), (isDarkTheme) => {
    // Set initial theme state
    if (isDarkTheme) {
      document.body.classList.remove("light")
    } else {
      document.body.classList.add("light")
    }
    
    // Toggle theme
    toggleTheme()
    
    // Verify theme switched
    const hasLightClass = document.body.classList.contains("light")
    return hasLightClass === isDarkTheme
  }),
  { numRuns: 100 }
);
```

### Test Environment Setup

**DOM Testing**:
- Use **jsdom** or **happy-dom** for DOM manipulation testing
- Load codex.html into test environment
- Mock browser APIs (SpeechRecognition, SpeechSynthesis, FileReader)

**API Mocking**:
- Mock Groq API responses for success cases
- Mock various error responses (network, auth, server, malformed)
- Verify request structure without actual API calls
- Use **MSW (Mock Service Worker)** or **nock** for HTTP mocking

**Browser API Mocking**:
- Mock webkitSpeechRecognition with configurable results
- Mock SpeechSynthesis with tracking for speak/cancel calls
- Mock FileReader with configurable file content
- Mock Blob and URL.createObjectURL for export testing

### Coverage Goals

- **Unit tests**: Cover all specific examples, CSS properties, and edge cases (Requirements 1-6, 19, specific values from 7-18)
- **Property tests**: Cover all 24 correctness properties
- **Integration tests**: Verify complete user flows:
  - Chat mode: question → API → response → voice
  - Notes mode: topic → API → notes → voice
  - Quiz mode: start → question → answer → check → next question
  - Voice input: speak → transcribe → submit → response
  - File upload: select → read → populate → submit
  - Export: messages → file → download
- **Error tests**: Verify all error handling paths remain functional

### Test Execution

Tests should be runnable via:
- `npm test` for all tests
- `npm run test:unit` for unit tests only
- `npm run test:property` for property tests only
- `npm run test:integration` for integration tests only
- Tests should complete in under 60 seconds for rapid feedback

### Test Organization

```
tests/
├── unit/
│   ├── layout.test.js          # Requirements 1, 6
│   ├── theme.test.js            # Requirements 2
│   ├── sidebar.test.js          # Requirements 3
│   ├── header.test.js           # Requirements 4
│   ├── chat-area.test.js        # Requirements 5
│   ├── modes.test.js            # Requirements 7, 8, 9
│   ├── voice.test.js            # Requirements 10, 11
│   ├── file-upload.test.js      # Requirements 12
│   ├── utilities.test.js        # Requirements 13, 14, 15
│   ├── keyboard.test.js         # Requirements 16
│   ├── api.test.js              # Requirements 17
│   ├── validation.test.js       # Requirements 18
│   └── styling.test.js          # Requirements 19
├── property/
│   ├── theme-properties.test.js
│   ├── message-properties.test.js
│   ├── api-properties.test.js
│   ├── mode-properties.test.js
│   ├── voice-properties.test.js
│   ├── file-properties.test.js
│   └── utility-properties.test.js
├── integration/
│   ├── chat-flow.test.js
│   ├── notes-flow.test.js
│   ├── quiz-flow.test.js
│   ├── voice-flow.test.js
│   └── export-flow.test.js
└── helpers/
    ├── dom-setup.js
    ├── api-mocks.js
    └── browser-api-mocks.js
```

### Continuous Testing

- Run tests on every code change during development
- Use watch mode for rapid feedback
- Integrate with CI/CD pipeline for automated testing
- Maintain test coverage above 90% for critical paths
- Review and update tests when requirements change
