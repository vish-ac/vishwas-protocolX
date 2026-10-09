# Chat Catch-up

Upload an exported WhatsApp chat (.txt) and see what you missed: a summary, what needs your attention, tasks, decisions, timeline, search, and "Ask your chat" with source messages. **Your chat never leaves your device.**

## How privacy works
- The website is a static page. The chat file is read and parsed **inside your browser**; there is no backend and no upload.
- **Quick Analysis** uses keyword rules and makes no network requests.
- **Local AI** sends text only to an [Ollama](https://ollama.com) server on `localhost` (the app refuses non-local addresses). There is no cloud fallback: if Ollama fails, you get an error and can use Quick Analysis.
- Nothing is saved to disk or storage. "Delete chat & analysis" clears everything from memory; closing the tab does the same.
- GitHub Pages only serves the code. It never sees your chat. Do not commit real chats (`.gitignore` blocks common export names).

## Run on Windows 11
1. Install **Node.js 22 LTS** from https://nodejs.org (accept defaults) and **Git** from https://git-scm.com.
2. Open *Terminal* (PowerShell) in the project folder:
   ```
   npm install
   npm run dev
   ```
3. Open the address it prints (usually http://localhost:5173).

## Tests and build
```
npm test          # parser + analysis tests (Node 22.6+)
npm run build     # type-check + production build into dist/
```

## Local AI setup (optional)
1. Install Ollama from https://ollama.com/download, then run: `ollama pull llama3.2`
2. Allow the website to talk to Ollama. In PowerShell (replace with your Pages address, no path):
   ```
   setx OLLAMA_ORIGINS "http://localhost:5173,https://YOUR-USERNAME.github.io"
   ```
   Quit Ollama from the tray icon and start it again.
3. In the app choose **Local AI → Connect to Ollama**.

Notes: Chrome and Edge allow an HTTPS page to call `http://localhost`. Safari may block it; use Quick Analysis or run the app locally with `npm run dev`. Long chats are truncated to the most recent ~24,000 characters for the model.

## Publish to GitHub Pages
```
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPO.git
git push -u origin main
```
On GitHub: **Settings → Pages → Source: GitHub Actions**. The included workflow tests, builds and deploys on every push to `main`.

## Limitations
- Quick Analysis is keyword-based: it misses context and works best on English; results can be wrong, so check the linked original messages.
- Ambiguous dates (e.g. 01/02) default to day/month unless the file shows otherwise.
- System messages are detected as lines with no "Name:" prefix; unusual formats may be mis-split. Lines before the first timestamp are counted and reported, not hidden.
- Chat text is treated as untrusted: it is rendered as plain text and wrapped in delimiters for the model, but no prompt defence is perfect.
