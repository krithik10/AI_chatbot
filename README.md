# AI Chatbot CLI (Go)

A minimal command-line chatbot written in **Go** that connects to Google’s Generative AI (Gemini) and demonstrates **function-calling** by letting the model write files to your desktop.

---

## ✨ Key features

- **Gemini-1.5-Flash** integration via the official [`generative-ai-go`](https://github.com/google/generative-ai-go) SDK  
- Simple **REPL**: type a prompt, get an answer
- **Function calling** support (example: `file_write`) with automatic error handling
- Clean, idiomatic Go code ready for your own tools and models

---

## 🗂️ Project layout

```
.
├── main.go         # entry point & chat loop
├── tools.go        # FileTool + WriteDesktop helper (add your own tools here)
├── go.mod / sum    # Go modules
└── README.md
```

---

## 🚀 Quick start

1. **Clone the repo**

   ```bash
   git clone https://github.com/your-user/ai-chatbot-go.git
   cd ai-chatbot-go
   ```

2. **Install dependencies**

   ```bash
   go mod tidy
   ```

3. **Create a `.env` file**

   > `.env`

   ```env
   GENAI_API_KEY=YOUR_REAL_KEY_HERE
   ```

4. **Run**

   ```bash
   go run .
   ```

5. **Chat!**

   ```text
   > Hello, who are you?
   2025/07/05 10:42:58 Response: I’m Gemini-Flash—ask me anything ✨
   ```

---

## 🛠️ How it works

| Step | File / Function | What happens |
|------|-----------------|--------------|
|①     | `main()`        | Loads `.env`, creates a `genai.Client`, picks the model |
|②     | `StartChat()`   | Opens a session (`ChatSession`) so history is preserved |
|③     | **User input**  | `SendMessage()` passes the prompt to Gemini |
|④     | **Model output**| If the model returns a **function call** part, it’s routed to `buildResponse()` |
|⑤     | `file_write`    | Validates args → `WriteDesktop()` writes the file → sends a `FunctionResponse` back to Gemini |
|⑥     | **Final text**  | Gemini continues and returns the normal textual answer |

---

## ➕ Adding your own tools

1. Create a `*genai.Tool` describing the schema  
2. Add it to `model.Tools` before starting the chat

```go
myTool := &genai.Tool{ /* … */ }
genaiApp.model.Tools = []*genai.Tool{FileTool, myTool}
```

Inside `buildResponse`, extend the switch on `functionCall.Name` to invoke your Go logic.

---

## 🧪 Environment variables

| Name            | Purpose                                     |
|-----------------|---------------------------------------------|
| `GENAI_API_KEY` | Google Generative AI key (required)         |
| `HOME` / `USER` | Used by `WriteDesktop()` to find the desktop|

---

## ⚡ Troubleshooting

| Symptom                            | Fix |
|------------------------------------|-----|
| `Error loading .env file`          | Ensure the file is in the project root and readable |
| `invalid API key` or 403 responses | Double-check **GENAI_API_KEY** and its billing status |
| `unknown function call`            | The model asked for a tool you haven’t implemented—add it or guard against it |

---

## 📦 Building a static binary

```bash
go build -o chatbot .
./chatbot
```

---

## 📝 License

MIT — use it, fork it, ship it. If you improve it, consider opening a PR!

---

### 🙏 Acknowledgements

- Google - [Generative AI Go SDK](https://github.com/google/generative-ai-go)
- [joho/godotenv](https://github.com/joho/godotenv) for zero-config env loading
