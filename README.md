
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>AI Chatbot</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      padding: 20px;
      background: linear-gradient(to right, #667eea, #764ba2);
      color: #fff;
    }

    h2 {
      text-align: center;
    }

    #chatbox {
      width: 100%;
      height: 300px;
      overflow-y: scroll;
      border: none;
      border-radius: 10px;
      padding: 10px;
      background: rgba(255, 255, 255, 0.9);
      color: #000;
      margin-bottom: 15px;
    }

    #userInput {
      width: 75%;
      padding: 10px;
      border-radius: 8px;
      border: none;
      outline: none;
    }

    button {
      padding: 10px 20px;
      background: #4fd1c5;
      border: none;
      border-radius: 8px;
      color: white;
      font-weight: bold;
      cursor: pointer;
      transition: all 0.3s ease;
    }

    button:hover {
      background: #38b2ac;
      transform: scale(1.05);
      box-shadow: 0px 4px 15px rgba(0, 0, 0, 0.2);
    }

    .msg {
      margin-bottom: 10px;
    }

    .user {
      color: #3182ce;
    }

    .bot {
      color: #2f855a;
    }
  </style>
</head>
<body>
  <h2>My AI Chatbot</h2>
  <div id="chatbox"></div>
  <input type="text" id="userInput" placeholder="Ask something..." />
  <button onclick="sendMessage()">Send</button>

  <script>
    // Base64-encoded API key (just to obscure it a bit)
    const encodedKey = "c2stb3ItdjEtMTk5MjM4MThhNjZhMTNjNGJkZGRkM2M4OWQ1Njg1MmUyOWMxMDZjYWYzM2M4NWE3MzVhY2RmMGRhYzQyZDg4ZjY=";
    const API_KEY = atob(encodedKey); // Decoded at runtime

    const chatbox = document.getElementById("chatbox");
    const input = document.getElementById("userInput");
    const messageHistory = [];

    async function sendMessage() {
      const userText = input.value.trim();
      if (!userText) return;

      chatbox.innerHTML += `<div class="msg user"><b>You:</b> ${userText}</div>`;
      input.value = "";

      messageHistory.push({ role: "user", content: userText });

      const response = await fetch("https://openrouter.ai/api/v1/chat/completions", {
        method: "POST",
        headers: {
          "Authorization": `Bearer ${API_KEY}`,
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          model: "openai/gpt-3.5-turbo",
          messages: [
            { role: "system", content: "You are a helpful assistant who guides users to answers through questions and hints, not direct solutions." },
            ...messageHistory
          ]
        })
      });

      const data = await response.json();
      const reply = data.choices?.[0]?.message?.content || "Error getting response";

      chatbox.innerHTML += `<div class="msg bot"><b>Bot:</b> ${reply}</div>`;
      chatbox.scrollTop = chatbox.scrollHeight;

      messageHistory.push({ role: "assistant", content: reply });
    }

    input.addEventListener("keypress", function(event) {
      if (event.key === "Enter") {
        sendMessage();
      }
    });
  </script>
</body>
</html>
