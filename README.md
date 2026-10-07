# 📚 SpiltStudy — Advanced Learning Studio

> A full-stack academic study assistant powered by AI, Google Search, and Firebase.

---

## 🌟 Features

| Feature | Description |
|---|---|
| 🔐 **Authentication** | Username/password login & registration backed by Firestore |
| 🔍 **Topic Search** | Fetches academic notes via Google Custom Search API |
| 🎬 **Video Recommendations** | Embeds relevant YouTube tutorials alongside notes |
| 🤖 **AI Chat Assistant** | Powered by Groq's `llama-3.3-70b-versatile` model |
| 📓 **Personal Notebook** | Save, view, and delete study notes (up to 10 per user) |
| 📜 **Search History** | Persists and displays recent searches per user |
| 💡 **Themes** | Dark mode, light mode, and study mode toggles |
| 📐 **Resizable Panes** | Drag-to-resize notes/video layout |

---

## 🛠️ Tech Stack

### Backend
- **Java 17** + **Spring Boot 3.2.2**
- **Spring Data JPA** (H2 in-memory DB — entities defined but all persistence goes to Firestore)
- **Firebase Admin SDK 9.2.0** (Firestore for all persistent data)
- **Groq API** (LLM chat via OpenAI-compatible endpoint)
- **Google Custom Search API** (notes + YouTube video discovery)
- **Jsoup** (web scraping of top search result)
- **org.json** (JSON parsing)

### Frontend
- Vanilla **HTML5 / CSS3 / JavaScript**
- Served as static files via Spring Boot (`src/main/resources/static/`)
- **Lucide Icons** (CDN)
- **Google Fonts — Outfit**

### Infrastructure
- **Docker** (multi-stage build, Eclipse Temurin JRE 17 runtime)
- **Render.com** (free tier web service via `render.yaml`)

---

## 📁 Project Structure

```
SpiltStudy/
├── src/
│   └── main/
│       ├── java/com/example/splitstudy/
│       │   ├── SplitstudyApplication.java      # Entry point + RestTemplate bean
│       │   ├── FirebaseConfig.java             # Firebase/Firestore initialization
│       │   ├── FirestoreService.java           # All DB operations (users, notes, history, chat)
│       │   ├── AuthController.java             # POST /api/auth/register & /login
│       │   ├── StudyController.java            # GET /api/summarize & /history
│       │   ├── ChatController.java             # POST /api/chat & GET /api/chat/history
│       │   ├── NoteController.java             # CRUD /api/notes
│       │   ├── User.java                       # JPA entity (also used as request DTO)
│       │   ├── Note.java                       # JPA entity (unused at runtime — Firestore used instead)
│       │   ├── ChatMessage.java                # JPA entity (unused at runtime)
│       │   ├── SearchTopic.java                # JPA entity (unused at runtime)
│       │   ├── UserRepository.java             # JPA repo (unused at runtime)
│       │   ├── NoteRepository.java             # JPA repo (unused at runtime)
│       │   ├── ChatMessageRepository.java      # JPA repo (unused at runtime)
│       │   └── SearchTopicRepository.java      # JPA repo (unused at runtime)
│       └── resources/
│           ├── application.properties
│           └── static/
│               ├── index.html
│               ├── style.css
│               ├── script.js
│               └── bg1.png / bg2.png / bg3.png
├── firebase-service-account.json.json          # ⚠️ LOCAL credentials (do NOT commit to git)
├── Dockerfile
├── render.yaml
└── pom.xml
```

---

## ⚙️ Configuration

### Environment Variables

| Variable | Purpose |
|---|---|
| `GROQ_API_KEY` | Groq LLM API key |
| `GOOGLE_SEARCH_API_KEY` | Google Custom Search API key |
| `FIREBASE_CONFIG_JSON` | Firebase service account JSON as a string (for cloud deployment) |
| `PORT` | Server port (defaults to `8080`) |

> **Note:** For local development, place your Firebase service account file at the project root named `firebase-service-account.json.json`. The app will find it automatically.

### `application.properties` (key settings)
```properties
server.port=${PORT:8080}
groq.api.key=${GROQ_API_KEY:}
google.search.api.key=${GOOGLE_SEARCH_API_KEY:}
spring.h2.console.enabled=true
spring.datasource.url=jdbc:h2:mem:testdb
```

---

## 🚀 Running Locally

### Prerequisites
- Java 17+
- Maven 3.x

### Steps

```bash
# Clone the repo
git clone <your-repo-url>
cd SpiltStudy

# Set environment variables (Windows)
set GROQ_API_KEY=your_groq_key
set GOOGLE_SEARCH_API_KEY=your_google_key

# Run with Maven wrapper
mvnw spring-boot:run
```

Then open `http://localhost:8080` in your browser.

---

## 🐳 Running with Docker

```bash
# Build the image
docker build -t splitstudy .

# Run the container
docker run -p 8080:8080 \
  -e GROQ_API_KEY=your_groq_key \
  -e GOOGLE_SEARCH_API_KEY=your_google_key \
  -e FIREBASE_CONFIG_JSON='{"type":"service_account",...}' \
  splitstudy
```

---

## 🔌 API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/auth/register` | Register a new user |
| `POST` | `/api/auth/login` | Login with credentials |
| `GET` | `/api/summarize?topic=&username=` | Fetch notes + videos for a topic |
| `GET` | `/api/history?username=` | Get search history for a user |
| `POST` | `/api/chat` | Send a message to the AI assistant |
| `GET` | `/api/chat/history?username=` | Get chat history for a user |
| `POST` | `/api/notes` | Save a note |
| `GET` | `/api/notes?username=` | Get all notes for a user |
| `DELETE` | `/api/notes/{id}` | Delete a note by ID |

---

## ☁️ Deploying to Render

1. Push the project to a GitHub repository.
2. Create a new **Web Service** on [Render](https://render.com), connecting your repo.
3. Render will auto-detect the `render.yaml` and use Docker runtime.
4. Set the following environment variables in the Render dashboard:
   - `GROQ_API_KEY`
   - `GOOGLE_SEARCH_API_KEY`
   - `FIREBASE_CONFIG_JSON` (paste the full JSON contents of your service account file)

---

## 📝 Notes & Limitations

- Notes are capped at **10 per user** (enforced server-side in Firestore).
- Chat history loads the last **50 messages**.
- Search history shows the last **10 topics**.
- The notes scraper only reads the **top search result** (Jsoup). Paywalled or JS-rendered sites will fail silently with a fallback message.
- Google Custom Search CX IDs are **hardcoded** in `StudyController.java`.

---

## 🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

---

## 📄 License

This project was built as an academic semester project. All rights reserved.

