# ZeroMark 
> (Zero Dependencies + Markdown)

**A Secure, Offline-First, Single-File Markdown Editor for SOC Analysts.**

ZeroMark is designed as a portable, fast, and secure note-taking tool tailored for the specific needs of Security Operations Center (SOC) analysts and other technical users requiring robust offline capabilities and OPSEC awareness. It runs entirely within your browser as a single HTML file, leveraging modern web APIs for local file access without requiring any server-side components or external dependencies (beyond the optional Marked.js for preview).

<!-- Optional: Add a screenshot here later -->
<!-- ![ZeroMark Screenshot](link/to/your/screenshot.png) -->

## Key Features

*   **Truly Portable:** Delivered as a single `.html` file. Just download and open.
*   **Offline First:** Designed for 100% offline use. No network requests are made.
*   **Secure Local File Access:** Uses the **File System Access API** to directly interact with folders *you* explicitly grant permission to.
*   **OPSEC-Focused Storage:**
    *   Uses **IndexedDB** to securely store the *reference* (`FileSystemDirectoryHandle`) to your last opened folder and user preferences (like Zen Mode state).
    *   **Crucially, note content is *never* stored in browser storage (IndexedDB/localStorage).** Content is read live from your disk when needed.
*   **Markdown Support:** Edit notes using standard Markdown syntax.
*   **Live Preview:** Toggleable preview panel rendered using [Marked.js](https://marked.js.org/).
*   **File Tree Navigation:** View and navigate your notes via a collapsible folder tree within the selected directory.
*   **Tag Extraction & Filtering:** Automatically extracts flat (`#tag`) and nested (`#parent/child`) tags from notes. Click tags in the sidebar to filter the file list and TODOs.
*   **TODO Checklist Parsing:** Recognizes Markdown checklists (`- [ ]`, `- [x]`) and displays them in a dedicated sidebar section. You can check/uncheck items directly, which modifies the source file.
*   **Content & Filename Search:** Case-insensitive text and basic Regex search across note content and filenames within the open folder.
*   **Zen Mode:** A distraction-free writing mode that hides the sidebar.
*   **Theming:** Uses the visually appealing Catppuccin Mocha theme via CSS variables.

## How It Works - Architecture

ZeroMark achieves its portability and security through several key web technologies:

1.  **Single HTML File:** All necessary CSS styles and JavaScript logic are embedded directly within the `index.html` file using `<style>` and `<script>` tags. This eliminates the need for build steps or managing separate asset files.
2.  **File System Access API:** This modern browser API allows web applications (even local files) to request permission from the user to directly read from and write to specific files and directories on the user's local machine (`window.showDirectoryPicker`, `FileSystemDirectoryHandle`, etc.). Access is explicitly granted by the user per folder and managed by the browser.
3.  **IndexedDB for Persistence:**
    *   To provide convenience across page refreshes/sessions without compromising security, ZeroMark stores a *reference* (the `FileSystemDirectoryHandle` object) to the last successfully opened folder in the browser's IndexedDB. This is the W3C-recommended secure way to persist folder access.
    *   User preferences (like Zen Mode state) are also stored here.
    *   **Security Note:** Storing the handle itself is secure because it's an opaque reference managed by the browser, bound to the file's origin (`file://...`), and doesn't expose the raw file path string directly in easily accessible storage like `localStorage`. Most importantly, **note content is never stored in IndexedDB.**
4.  **Client-Side Logic:** All operations – file reading/writing (via the obtained handles), Markdown parsing (Marked.js), tag/TODO extraction, searching, and UI updates – happen entirely within the user's browser using JavaScript.

## Security Considerations

Security and OPSEC are primary design goals:

*   **No Network Activity:** ZeroMark makes zero external network calls. Its operation is confined entirely to your local machine and browser sandbox.
*   **User-Granted Permissions:** Access to local files is *never* automatic. The user *must* explicitly select a folder via the "Open Folder" button and grant permission through the browser's native security prompt. Permissions are managed by the browser and can typically be revoked via site settings.
*   **No Server-Side Component:** The lack of a backend eliminates entire classes of web vulnerabilities.
*   **Secure Handle Persistence:** As detailed above, IndexedDB is used for storing opaque folder *handles* and non-sensitive preferences, **not note content**. This prevents accidental exposure of sensitive information via less secure storage mechanisms.
*   **Origin Bound:** Permissions and IndexedDB storage are tied to the origin of the HTML file (`file://...`), preventing other websites from accessing ZeroMark' data or permissions.
*   **Markdown Rendering:** While Marked.js's `sanitize` option is currently disabled for accurate rendering of local file content (like embedded HTML or specific Markdown extensions if used), this is considered acceptable *only* in the context of rendering user-controlled, local files. **Never use this configuration to render Markdown from untrusted remote sources.**

## Usage / Getting Started

1.  Download the `index.html` file.
2.  Open the file in a modern web browser that supports the File System Access API (e.g., Google Chrome, Microsoft Edge).
3.  Click the "📂 Open" button.
4.  Your browser will prompt you to select a folder. Choose the directory containing your Markdown notes.
5.  Grant the necessary read/write permissions when prompted by the browser.
6.  The file tree will populate. Click a file to load it into the editor.
7.  Use the sidebar sections (Tags, TODOs) and search bar to navigate and filter your notes.
8.  Use the "➕ New" button to clear the editor for a new note (use "💾 Save" which acts as Save As for new notes).

## Technology Stack

*   HTML5
*   CSS3 (utilizing CSS Variables for theming)
*   Vanilla JavaScript (ES6+)
*   Browser APIs:
    *   File System Access API
    *   IndexedDB API
*   [Marked.js](https://marked.js.org/): For Markdown-to-HTML conversion in the preview pane.

## Future Plans / Backlog


*   [ ] Embed the Marked.js library directly into the HTML file to remove the CDN dependency and ensure full offline operation.
*   [ ] Hierarchical display and filtering for nested tags (`#parent/child`).
*   [ ] Implement robust file/folder operations (Create, Rename, Delete) with proper UI and permission handling.
*   [ ] Enhance search capabilities (e.g., more advanced regex options, potential fuzzy search).
*   [ ] Add UI for user preferences (e.g., Accent Color Picker) stored in IndexedDB.
*   [ ] Investigate preserving scroll position between editor/preview toggle.
*   [ ] Explore more advanced state management if complexity increases significantly.

## Contributing

Currently, this is a personal project. If you have suggestions or find bugs, feel free to open an issue on GitHub.
