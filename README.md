# Cloud Notepad (GitHub-backed)

This is a single-file web Notepad that can load, edit and save plain text files directly to a GitHub repository using the GitHub REST API. No backend is required — the page runs entirely in the browser.

Files:
- `cloud_notepad.html` — main single-page web app.
- Other sample HTML files in the folder are examples that also show GitHub API usage.
 - `cloud_notepad_simple.html` — minimal, focused implementation (Load/Edit/Save) that follows the requested spec exactly.

Features:
- Load a file from a GitHub repository and display it in a textarea.
- Edit the text and save it back to GitHub (create or update).
- Clear the editor.
- Basic status messages for success and failure.

How it works (brief):
- The app calls the GitHub API endpoint `GET /repos/{owner}/{repo}/contents/{path}` to read files. GitHub returns file contents base64-encoded.
- To save, it calls `PUT /repos/{owner}/{repo}/contents/{path}` with the base64-encoded content and commit message. If the file exists, include its `sha` to update it; otherwise omit `sha` to create.

Important: Authentication and token creation
- You must create a Personal Access Token (PAT) on GitHub to allow the page to read and write repository contents.
- Recommended scopes:
  - For private repositories: `repo` (full control of private repositories).
  - For public-only use: `public_repo` is sufficient.
- Token expiration: When creating the token, choose an expiration of at least 90 days (the UI will let you select this). The app does not refresh tokens automatically.

Security notes
- This is a client-side app: the PAT you enter is only sent to GitHub and stored in-memory in your browser during the session. Do NOT paste a token you cannot revoke.
- Revoke the token from your GitHub account if it gets exposed or after you finish testing.

Usage
1. Open `cloud_notepad.html` in your browser (double-click or open via a local web server).
2. Fill in the repository owner, repository name, branch (usually `main`), and the file path (e.g., `notes.txt` or `folder/notes.txt`).
3. Paste your PAT into the token field.
4. Click "Load File" to fetch the file. If the file doesn't exist, you'll see a notice and can create it by editing and clicking "Save File".
5. Edit text in the editor and click "Save File" to commit changes to the repo. The app will show success or failure messages.
6. Use "Clear Text" to clear the editor.

Running from a local web server (recommended)
- Some browsers restrict local file features when opening plain HTML from the filesystem. For the best experience, serve the folder with a tiny local server. From the project folder run:

```bash
# Python 3
python -m http.server 8000

# or, if you prefer Node.js and have http-server installed:
npx http-server -p 8000
```

Then open http://localhost:8000/cloud_notepad.html in your browser.

Token session storage
- The Notepad UI includes a "Save token" button that stores your PAT in sessionStorage (only for this browser tab/session). This keeps you from pasting it repeatedly but does not persist across new browser sessions. Use the "Clear token" button to remove it.

Uploading local files
- You can also upload a local file from your machine to the repo. Click the folder icon next to the File path input, choose a file. If it's a text file (.txt, .md, .json, .csv, or detected as text), it will load into the editor for editing. For binary files (images, etc.) the app prepares the base64 payload and pressing "Save File" will upload the binary to the selected path.

Example repository
- The app can interact with your repository such as: https://github.com/LouiseBrianBaclig/Baclig_-Exercise-1-GitHub-File-Manager — set owner to `LouiseBrianBaclig` and repo to `Baclig_-Exercise-1-GitHub-File-Manager` and the desired branch/path.

UI improvements
- The app includes a polished dark UI, autosave toggle, Cmd/Ctrl+S to save, and inline status messages.

Troubleshooting
- 401 Unauthorized: Check your token and ensure it has the required scopes.
- 404 Not Found: The file path doesn't exist. You can save to create it.
- CORS/network issues: Run the page from a proper http(s) origin or allow local file fetches depending on your browser. For more stable testing, serve the file via a lightweight local server (e.g., `python -m http.server`).

Extending this app
- You can adapt the same UI and logic to other cloud providers (Dropbox, Google Drive) by swapping the API calls and auth flow.
- To improve UX, implement token storage in local encrypted storage or an OAuth flow with a backend to avoid exposing PATs.

License
- This example is provided as-is for educational purposes.
