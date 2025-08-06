const chatContainer = document.getElementById('chat-container');
const startBtn = document.getElementById('start-btn');
const voiceSelect = document.getElementById('voice-select');
const textInput = document.getElementById('text-input');
const sendBtn = document.getElementById('send-btn');

let recognition;
let voices = [];

// دالة لإضافة الرسائل إلى واجهة الدردشة
function appendMessage(message, className) {
  const msg = document.createElement('div');
  msg.className = className;
  msg.textContent = message;
  chatContainer.appendChild(msg);
  chatContainer.scrollTop = chatContainer.scrollHeight;
}

// دالة لتحويل النص إلى صوت باستخدام ElevenLabs (صوت عربي)
async function speak(text) {
  const apiKey = ''; // 🔐 ضعي مفتاحك هنا
  const voiceId = 'yoZ06aX4MZl4eT7Ma4JJ'; // 🎙️ صوت Salma (عربي)

  const response = await fetch(`https://api.elevenlabs.io/v1/text-to-speech/${voiceId}`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'xi-api-key': apiKey,
    },
    body: JSON.stringify({
      text: text,
      model_id: 'eleven_monolingual_v1',
      voice_settings: {
        stability: 0.5,
        similarity_boost: 0.75
      }
    })
  });

  const audioBlob = await response.blob();
  const audioUrl = URL.createObjectURL(audioBlob);
  const audio = new Audio(audioUrl);
  audio.play();
}

// دالة لجلب رد من Cohere
async function getBotReply(message) {
  return await callCohere(message);
}

// استدعاء API الخاص بـ Cohere
async function callCohere(message) {
  const response = await fetch('https://api.cohere.ai/v1/chat', {
    method: 'POST',
    headers: {
      'Authorization': ' ',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      message: message,
      chat_history: [],
      model: "command-r-plus"
    })
  });

  const data = await response.json();
  console.log("رد Cohere:", data);

  if (data && data.text && data.text.trim() !== "") {
    return data.text;
  } else if (data && data.message) {
    return "الرد من الخادم: " + data.message;
  } else {
    return "لم أتمكن من الحصول على رد. تأكد من الاتصال أو صحة الـ API.";
  }
}

// إرسال الرسالة من المستخدم إلى الذكاء الصناعي
async function sendMessage(message) {
  if (!message.trim()) return;
  appendMessage(message, 'user-message');
  const botReply = await getBotReply(message);
  appendMessage(botReply, 'bot-message');
  speak(botReply);
  textInput.value = '';
}

// تشغيل الميكروفون والتعرف على الصوت
function startListening() {
  recognition = new (window.SpeechRecognition || window.webkitSpeechRecognition)();
  recognition.lang = 'ar-SA'; // اللغة العربية
  recognition.interimResults = false;
  recognition.maxAlternatives = 1;

  recognition.onstart = () => {
    startBtn.textContent = "🎙️ استمع...";
  };

  recognition.onresult = (event) => {
    const transcript = event.results[0][0].transcript;
    sendMessage(transcript);
  };

  recognition.onerror = (event) => {
    appendMessage("خطأ في التعرف على الصوت: " + event.error, 'bot-message');
  };

  recognition.onend = () => {
    startBtn.textContent = "🎤 اضغط وتحدث";
  };

  recognition.start();
}

// تحميل الأصوات (تُستخدم فقط لواجهة المستخدم)
function populateVoices() {
  voices = speechSynthesis.getVoices();
  voiceSelect.innerHTML = '';
  voices.forEach(voice => {
    const option = document.createElement('option');
    option.value = voice.name;
    option.textContent = `${voice.name} (${voice.lang})`;
    voiceSelect.appendChild(option);
  });
}

window.speechSynthesis.onvoiceschanged = populateVoices;

// عند تحميل الصفحة
window.onload = () => {
  populateVoices();
  const greeting = "مرحباً، أنا المساعد الذكي تم تطويري بواسطة المهندسة طيف. يمكنك التحدث أو الكتابة.";
  appendMessage(greeting, 'bot-message');
  speak(greeting);
};

// الأحداث
startBtn.addEventListener('click', startListening);
sendBtn.addEventListener('click', () => sendMessage(textInput.value));
textInput.addEventListener('keypress', e => {
  if (e.key === 'Enter') sendMessage(textInput.value);
});
