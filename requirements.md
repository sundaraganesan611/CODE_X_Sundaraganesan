# Requirements Document

## Introduction

CODE X STUDY AI is an AI-powered educational assistant web application that provides students with an interactive interface for learning through multiple modes: conversational chat, structured note generation, and interactive quizzes. The application uses the Groq API with the llama-3.1-8b-instant model to generate educational content and supports multiple languages with voice interaction capabilities.

## Glossary

- **Study_AI**: The web application system that provides AI-powered educational assistance
- **User_Interface**: The web-based visual interface through which users interact with the system
- **Chat_Area**: The scrollable display region where conversation messages appear
- **Input_Bar**: The fixed bottom bar containing input field and action buttons
- **Sidebar**: The left navigation panel containing utility buttons
- **Mode_Selector**: Dropdown menu for selecting between Chat, Notes, and Quiz modes
- **Language_Selector**: Dropdown menu for selecting interface language (English, Hindi, Tamil)
- **Level_Selector**: Dropdown menu for selecting quiz difficulty (Easy, Medium, Hard)
- **Message**: A single chat bubble displayed in the Chat_Area (user or bot)
- **Groq_API**: The external API service that processes questions and generates responses
- **System_Prompt**: The mode-specific instruction that defines the AI assistant's behavior
- **Voice_Input**: Speech-to-text functionality for hands-free question input
- **Voice_Output**: Text-to-speech functionality for audio responses
- **Theme**: Visual color scheme (dark or light mode)
- **Chat_History**: The collection of all messages in the current session
- **File_Upload**: Functionality to load text content from local files

## Requirements

### Requirement 1: Application Layout

**User Story:** As a user, I want to see a well-organized interface with sidebar and main content area, so that I can easily access all features.

#### Acceptance Criteria

1. THE User_Interface SHALL display a Sidebar on the left with width of 220 pixels
2. THE User_Interface SHALL display a main application area to the right of the Sidebar
3. THE Sidebar SHALL contain utility buttons arranged vertically with 10-pixel gaps
4. THE main application area SHALL contain a header, Chat_Area, and Input_Bar
5. THE User_Interface SHALL use a flexbox layout that fills the entire viewport height
6. THE User_Interface SHALL prevent body scrolling (overflow: hidden)

### Requirement 2: Theme System

**User Story:** As a user, I want to toggle between dark and light themes, so that I can use the app comfortably in different lighting conditions.

#### Acceptance Criteria

1. THE User_Interface SHALL default to dark theme on initial load
2. WHEN in dark theme, THE User_Interface SHALL use background color #020617
3. WHEN in dark theme, THE User_Interface SHALL use app background color #0f172a
4. WHEN in dark theme, THE User_Interface SHALL use white text color
5. WHEN in dark theme, THE User_Interface SHALL use #38bdf8 for user message bubbles
6. WHEN in dark theme, THE User_Interface SHALL use #334155 for bot message bubbles
7. WHEN in light theme, THE User_Interface SHALL use background color #e5e7eb
8. WHEN in light theme, THE User_Interface SHALL use white app background
9. WHEN in light theme, THE User_Interface SHALL use black text color
10. WHEN in light theme, THE User_Interface SHALL use #2563eb for user message bubbles
11. WHEN in light theme, THE User_Interface SHALL use #e5e7eb for bot message bubbles
12. WHEN the theme toggle button is clicked, THE User_Interface SHALL switch between dark and light themes

### Requirement 3: Sidebar Navigation

**User Story:** As a user, I want quick access to utility functions through the sidebar, so that I can manage my session efficiently.

#### Acceptance Criteria

1. THE Sidebar SHALL display a "📜 History" button
2. THE Sidebar SHALL display a "⏹ Stop Voice" button
3. THE Sidebar SHALL display a "🧹 Clear Chat" button
4. THE Sidebar SHALL display a "📤 Export Chat" button
5. THE Sidebar SHALL display a "🌗 Theme" button
6. THE Sidebar buttons SHALL have background color #334155
7. THE Sidebar buttons SHALL have white text color
8. THE Sidebar buttons SHALL have 12-pixel padding
9. THE Sidebar buttons SHALL have 10-pixel border radius
10. THE Sidebar buttons SHALL display a pointer cursor on hover

### Requirement 4: Header and Mode Selection

**User Story:** As a user, I want to select different learning modes and languages, so that I can customize my learning experience.

#### Acceptance Criteria

1. THE header SHALL display the title "🎓 CODE X STUDY AI"
2. THE header SHALL display a Mode_Selector with options: Chat, Notes, Quiz
3. THE header SHALL display a Language_Selector with options: English, Hindi, Tamil
4. THE header SHALL display a Level_Selector (initially hidden)
5. WHEN Quiz mode is selected, THE Level_Selector SHALL become visible
6. WHEN Chat or Notes mode is selected, THE Level_Selector SHALL be hidden
7. THE Level_Selector SHALL display options: Easy, Medium, Hard
8. THE selectors SHALL have aqua background color
9. THE selectors SHALL have black text color
10. THE selectors SHALL have rounded corners with 20-pixel border radius

### Requirement 5: Chat Display

**User Story:** As a user, I want to see my conversation history in a clear, scrollable format, so that I can review previous interactions.

#### Acceptance Criteria

1. THE Chat_Area SHALL be scrollable vertically
2. THE Chat_Area SHALL have padding of 15 pixels top, 0 left/right, and 120 pixels bottom
3. THE Chat_Area SHALL display messages in a centered column layout
4. THE Chat_Area SHALL have a maximum width of 800 pixels for messages
5. WHEN a new message is added, THE Chat_Area SHALL automatically scroll to the bottom
6. THE user messages SHALL be displayed with user styling and aligned to the right
7. THE bot messages SHALL be displayed with bot styling and aligned to the left
8. THE messages SHALL have 10-pixel vertical padding and 14-pixel horizontal padding
9. THE messages SHALL have 14-pixel border radius
10. THE messages SHALL have 6-pixel vertical margins

### Requirement 6: Input Bar

**User Story:** As a user, I want multiple ways to input my questions, so that I can interact with the AI conveniently.

#### Acceptance Criteria

1. THE Input_Bar SHALL be fixed at the bottom of the screen
2. THE Input_Bar SHALL be positioned at 95% from top and 63% from left
3. THE Input_Bar SHALL have a maximum width of 900 pixels
4. THE Input_Bar SHALL contain a file upload button (📁)
5. THE Input_Bar SHALL contain a text input field
6. THE Input_Bar SHALL contain a voice input button (🎤)
7. THE Input_Bar SHALL contain a send button (✈️)
8. THE Input_Bar SHALL have 35-pixel border radius
9. THE Input_Bar buttons SHALL be circular with 42-pixel diameter
10. THE send button SHALL have cyan background (#38bdf8)
11. THE text input field SHALL have placeholder text "Type message or upload file..."
12. THE text input field SHALL expand to fill available space

### Requirement 7: Chat Mode

**User Story:** As a user, I want to have conversational interactions with the AI, so that I can ask questions and get helpful responses.

#### Acceptance Criteria

1. WHEN Chat mode is selected, THE System_Prompt SHALL be "You are a helpful assistant."
2. WHEN a user submits a question in Chat mode, THE Study_AI SHALL send the question to Groq_API
3. WHEN a user submits a question, THE Study_AI SHALL display the user's message in the Chat_Area
4. WHEN processing a request, THE Study_AI SHALL display "Thinking..." as a bot message
5. WHEN a response is received, THE Study_AI SHALL remove the "Thinking..." message
6. WHEN a response is received, THE Study_AI SHALL display the AI response in the Chat_Area
7. WHEN a response is received, THE Study_AI SHALL speak the response using Voice_Output
8. THE Chat_Area SHALL preserve line breaks in messages by converting \n to <br>

### Requirement 8: Notes Mode

**User Story:** As a user, I want to generate structured study notes on topics, so that I can learn efficiently.

#### Acceptance Criteria

1. WHEN Notes mode is selected, THE System_Prompt SHALL instruct the AI to create clear, structured notes
2. WHEN a user submits a topic in Notes mode, THE System_Prompt SHALL include the topic in the instruction
3. THE System_Prompt SHALL request bullet points and simple language
4. WHEN Notes mode is active, THE quiz state SHALL be reset
5. THE generated notes SHALL be displayed in the Chat_Area as a bot message
6. THE generated notes SHALL be spoken using Voice_Output

### Requirement 9: Quiz Mode

**User Story:** As a user, I want to test my knowledge through interactive quizzes, so that I can assess my learning progress.

#### Acceptance Criteria

1. WHEN Quiz mode is selected, THE Level_Selector SHALL become visible
2. WHEN starting a quiz, THE System_Prompt SHALL request ONE MCQ with 4 options
3. WHEN starting a quiz, THE System_Prompt SHALL include the selected difficulty level
4. WHEN starting a quiz, THE System_Prompt SHALL instruct the AI NOT to give the answer
5. WHEN a user submits an answer, THE System_Prompt SHALL instruct the AI to check the answer
6. WHEN checking an answer, THE System_Prompt SHALL instruct the AI to say "Correct" or "Wrong"
7. WHEN checking an answer, THE System_Prompt SHALL instruct the AI to ask the NEXT question
8. THE quiz questions SHALL be displayed in the Chat_Area
9. THE quiz feedback SHALL be displayed in the Chat_Area
10. WHEN switching away from Quiz mode, THE quiz state SHALL be reset

### Requirement 10: Voice Input

**User Story:** As a user, I want to speak my questions instead of typing, so that I can interact hands-free.

#### Acceptance Criteria

1. WHEN the voice input button is clicked, THE Study_AI SHALL activate speech recognition
2. WHEN English is selected, THE speech recognition SHALL use "en-IN" locale
3. WHEN Hindi is selected, THE speech recognition SHALL use "hi-IN" locale
4. WHEN Tamil is selected, THE speech recognition SHALL use "ta-IN" locale
5. WHEN speech is recognized, THE Study_AI SHALL populate the input field with the transcribed text
6. THE Study_AI SHALL use webkitSpeechRecognition API for voice input

### Requirement 11: Voice Output

**User Story:** As a user, I want to hear AI responses spoken aloud, so that I can learn through audio.

#### Acceptance Criteria

1. WHEN an AI response is received, THE Study_AI SHALL automatically speak the response
2. WHEN English is selected, THE voice output SHALL use "en-IN" locale
3. WHEN Hindi is selected, THE voice output SHALL use "hi-IN" locale
4. WHEN Tamil is selected, THE voice output SHALL use "ta-IN" locale
5. WHEN starting new voice output, THE Study_AI SHALL cancel any currently playing speech
6. WHEN the stop voice button is clicked, THE Study_AI SHALL cancel all speech output
7. THE Study_AI SHALL use SpeechSynthesis API for voice output

### Requirement 12: File Upload

**User Story:** As a user, I want to upload text files to use as input, so that I can ask questions about file content.

#### Acceptance Criteria

1. WHEN the file upload button is clicked, THE Study_AI SHALL open a file selection dialog
2. WHEN a file is selected, THE Study_AI SHALL read the file content as text
3. WHEN file content is loaded, THE Study_AI SHALL populate the input field with the content
4. THE Study_AI SHALL limit file content to 2000 characters
5. THE file input element SHALL be hidden from view

### Requirement 13: History Management

**User Story:** As a user, I want to toggle the visibility of chat history, so that I can focus on the current interaction.

#### Acceptance Criteria

1. WHEN the history button is clicked, THE Chat_Area visibility SHALL toggle
2. WHEN history is visible, THE Chat_Area SHALL display with "block" style
3. WHEN history is hidden, THE Chat_Area SHALL display with "none" style
4. THE history visibility state SHALL default to visible on page load

### Requirement 14: Clear Chat

**User Story:** As a user, I want to clear all messages, so that I can start a fresh conversation.

#### Acceptance Criteria

1. WHEN the clear chat button is clicked, THE Study_AI SHALL remove all messages from the Chat_Area
2. WHEN the chat is cleared, THE Chat_Area SHALL be empty with no messages

### Requirement 15: Export Chat

**User Story:** As a user, I want to export my conversation to a text file, so that I can save it for later reference.

#### Acceptance Criteria

1. WHEN the export button is clicked, THE Study_AI SHALL create a text file containing all chat messages
2. THE exported file SHALL be named "chat.txt"
3. THE exported file SHALL contain the plain text content of all messages
4. THE Study_AI SHALL automatically trigger a download of the exported file
5. THE export SHALL use Blob API to create the file

### Requirement 16: Keyboard Interaction

**User Story:** As a user, I want to press Enter to send messages, so that I can interact quickly without clicking.

#### Acceptance Criteria

1. WHEN the Enter key is pressed in the input field, THE Study_AI SHALL submit the message
2. THE Enter key SHALL trigger the same behavior as clicking the send button

### Requirement 17: API Communication

**User Story:** As a user, I want my questions processed by AI, so that I receive intelligent responses.

#### Acceptance Criteria

1. WHEN a user submits a question, THE Study_AI SHALL send an API request to "https://api.groq.com/openai/v1/chat/completions"
2. THE API request SHALL use HTTP POST method
3. THE API request SHALL include header "Content-Type: application/json"
4. THE API request SHALL include header "Authorization: Bearer {API_KEY}"
5. THE API request SHALL specify model "llama-3.1-8b-instant"
6. THE API request SHALL include the mode-specific System_Prompt
7. THE API request SHALL include the user's question as a user message
8. THE API request SHALL be processed asynchronously
9. WHEN the API response is received, THE Study_AI SHALL extract the message content from the first choice
10. THE input field SHALL be cleared after submitting a question

### Requirement 18: Empty Input Handling

**User Story:** As a user, I want the app to ignore empty submissions, so that I don't send blank messages.

#### Acceptance Criteria

1. WHEN the input field is empty or contains only whitespace, THE Study_AI SHALL not submit the message
2. WHEN the input field is empty, THE send button click SHALL have no effect
3. WHEN the input field is empty, THE Enter key SHALL have no effect

### Requirement 19: Visual Styling

**User Story:** As a user, I want a visually appealing interface, so that the application is pleasant to use.

#### Acceptance Criteria

1. THE User_Interface SHALL use Arial font family for all text
2. THE User_Interface SHALL use box-sizing: border-box for all elements
3. THE input field SHALL have 20-pixel border radius
4. THE input field SHALL have 10-pixel padding
5. THE input field SHALL have no border or outline
6. THE input field SHALL match the theme's chat background color
7. THE input field SHALL match the theme's text color
8. THE message bubbles SHALL have smooth rounded corners
9. THE selectors SHALL have bold font weight
10. THE application SHALL maintain consistent spacing and alignment throughout
