# 🤖 AI Chatbot with Voice & Smart Responses

This project is an intelligent chatbot that can understand and reply to user messages using natural language processing — and even respond **with a voice**!

## 🧠 Features

- 💬 Chatbot with context-aware responses
- 🗣️ Voice output using ElevenLabs Text-to-Speech
- 🧠 Smart text generation using Cohere API
- 🖥️ Simple and clean interface (terminal or GUI optional)

## 🛠️ Technologies Used

- **Python**
- **Cohere API** – for generating smart, human-like responses
- **ElevenLabs API** – for converting text replies to voice
- `requests`, `playsound`, `os`, `time`, and other standard Python libraries

## 🔐 API Keys

This project uses two APIs:

1. **Cohere API Key** – used to generate intelligent responses
2. **ElevenLabs API Key** – used to generate voice from text

Make sure you create a `.env` file or safely store your keys like this:

```env
COHERE_API_KEY=your_cohere_key_here
ELEVENLABS_API_KEY=your_elevenlabs_key_here
```

Or directly insert them in the Python file (not recommended for production):

```python
cohere_api = "your_cohere_key_here"
elevenlabs_api = "your_elevenlabs_key_here"
```

## 🚀 How to Run

1. Clone this repo:
   ```bash
   git clone https://github.com/your-username/ai-chatbot
   cd ai-chatbot
   ```

2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. Add your API keys in the code or in a `.env` file

4. Run the chatbot:
   ```bash
   python chatbot.py
   ```

## 📦 Output Example

User: "Hi, how are you?"  
Chatbot: "I'm doing great! How can I assist you today?"  
🔊 Voice plays using ElevenLabs...
