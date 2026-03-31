# 🌳 ARBORITO LIBRARY

Welcome to the **Official Arborito Library repository**. This is the decentralized source code for the "forest of wisdom." It contains the raw educational content and the compilation logic that powers the Arborito visual learning interface.

## 🌟 The Vision: Global Knowledge

Arborito is more than a browser; it is the **Arborito Library**, a decentralized open learning ecosystem designed to map the landscape of human understanding. It was born from a radical dream: **what if we could build a library for all of humanity, owned by no one and cultivated by everyone?**

In Arborito, information is not a static list; it is a living tree. Users don't just read; they explore a network of interconnected ideas at their own pace.

### 🧠 The Memory Core: How to write?
To support **Arborito's** pedagogical goals and its integrated **Spaced Repetition System (SRS)**, please follow these authoring principles:

*   **Atomic Lessons:** Each lesson or "leaf" node should be focused on a single concept.
*   **Modular Design:** Don't write long, linear books. Assume a user might arrive at your lesson from any branch. Avoid rigid "linear" references to previous chapters.
*   **Self-Contained:** Each node should provide a complete (even if small) piece of value.
*   **Universal:** Write simply and clearly. Avoid unnecessary jargon unless defined.

---

## 🏛 Content Architecture

To fulfill this dream, we organize human knowledge into main **Study Branches** (Ramas de Estudio). Please try to fit your contributions within this structure:

*   **⚙️ Engineering & Technology**
*   **🧠 AI & Data Science**
*   **⚛️ Exact & Physical Sciences**
*   **🧪 Chemical & Material Sciences**
*   **🧬 Life & Health Sciences**
*   **💼 Business & Leadership**
*   **🎨 Arts & Digital Design**
*   **📜 Humanities & Languages**

---

## 🚀 Quick Start Guide

### 1. Prerequisites
You need **Python 3** installed on your system to run the build script.

### 2. Directory Structure
All content lives in the `content/` folder. The compiler will generate the interactive API in the `data/` folder.

```text
.
├── builder_script.py      # ⚙️ The Compiler (Python script)
├── content/               # ✏️ YOUR CONTENT GOES HERE
│   ├── rolling/           # 🌿 LIVE branch (Continuous updates)
│   │   ├── INTRO.md       # Welcome screen for Live mode
│   │   └── ES/            # Language Root (e.g., Spanish)
│   └── releases/          # 🏛 ARCHIVE branch (Frozen versions)
│       └── 2024/          # Specific year/version
│           ├── INTRO.md   # Welcome screen for 2024
│           └── ES/        # Content for 2024
└── data/                  # 📤 GENERATED OUTPUT (Do not edit manually)
    ├── data.json          # Main API entry point (Rolling)
    ├── arborito-index.json   # Master index of all versions
    ├── nodes/             # Lazy-loaded branches
    └── search/            # Search shards for fast lookup
```

### 3. How to Build
Arborito does not use a dynamic database. It uses a static "Build" process for maximum speed and decentralization.

1.  **Edit Content:** Add folders and `.md` files in `content/rolling/` for ongoing development.
2.  **Run the Builder:**
    Open your terminal in this folder and run:
    ```bash
    python builder_script.py
    ```
    *This script will read `content/` and update the `data/` folder.*
3.  **Deploy:** The `data/` folder is listed in `.gitignore` so local tools (e.g. GitSync) do not upload generated JSON by default. **GitHub Actions** still builds and commits `data/` with `git add -f`. For manual hosting, upload `data/` yourself or remove it from `.gitignore` if you prefer to commit builds locally.

---

## ⚖️ Disclaimer, Licenses & Contribution

**⚠️ IMPORTANT: Before using or contributing to this repository, you must read our full [DISCLAIMER.md](./DISCLAIMER.md) and [CONTRIBUTING.md](./CONTRIBUTING.md) guides.**

This project is a community effort and is provided for educational purposes only. It is not a substitute for professional advice.

### Project Licensing
By contributing, you agree to license your work under the following terms:

*   **Educational Content (`content/` folder):**
    *   **License:** [Creative Commons Attribution-ShareAlike 4.0 (CC BY-SA 4.0)](https://creativecommons.org/licenses/by-sa/4.0/).
    *   **Summary:** You are free to share and adapt the material, as long as you give credit and distribute your contributions under the same license.

*   **Source Code (`builder_script.py`, etc.):**
    *   **License:** [GNU GPLv3](./LICENSE).
    *   **Summary:** The software that builds the knowledge graph is free software, guaranteeing your freedom to share and change it.

### 🤖 AI Transparency Disclosure
Some educational materials and drafts in this repository are created with the assistance of **Large Language Models (LLMs)**. All AI-assisted content is reviewed by human maintainers and is released under the CC BY-SA 4.0 license.

### 🖼️ Third-Party Assets
*   **External Media:** Content linked via URL (e.g., YouTube videos, `picsum.photos` images) is not hosted in this repository and remains the property of its respective owners under their own licenses.
*   **Emojis:** Emojis are Unicode characters rendered by the user's system font and are not image assets distributed by this repository.

---

## 📝 Authoring Guide

Arborito uses standard **Markdown** (`.md`) files. You can use standard Jekyll-style Frontmatter (YAML) or the simplified Arborito metadata tags.

### A. The Header (Metadata)

You can define metadata using the `@` syntax at the top of your `.md` file:

```text
@title: Introduction to Biology
@icon: 🧬
@description: Learn the basics of life.
@order: 1
@discussion: https://community.arborito.org/t/biology-intro/101
```

| Directive | Description |
| :--- | :--- |
| `@title` | The label on the tree node. |
| `@icon` | An emoji (single character) for the node. |
| `@description` | Brief summary shown in search and previews. |
| `@order` | (Optional) Number to sort nodes (1, 2, 3...). |
| `@discussion` | (Optional) URL to a forum thread for discussion. |

### B. Formatting Text
Write your lesson content using standard Markdown.

*   `# Heading 1` (Main Title)
*   `## Heading 2` (Subtitle)
*   `**Bold**` for emphasis
*   `*Italic*` for subtle emphasis
*   `- List item` for bullet points

### C. Rich Media
Use the `@` syntax to embed media players.

**Images:**
```text
@image: https://picsum.photos/800/400
```

**Videos (YouTube):**
```text
@video: https://www.youtube.com/watch?v=dQw4w9WgXcQ
```

**Audio:**
```text
@audio: https://example.com/podcast.mp3
```

### D. Interactive Quizzes
Insert a quiz anywhere in the text. This acts as a "Gate" that the user must pass to reinforce memory.

```text
@quiz: What is the powerhouse of the cell?
@option: Nucleus
@correct: Mitochondria
@option: Ribosome
```
*   `@quiz:` The question text.
*   `@correct:` The right answer.
*   `@option:` A wrong answer.

### E. Structuring Content
You can break your lesson into clear parts using the `@section` tag.

```text
@section: The Kreb's Cycle

Here we discuss the details of the Kreb's cycle...
```

### F. Folder Metadata
To customize a Folder (Branch), place a `meta.json` file inside it.

```json
{
  "name": "Advanced Physics",
  "icon": "⚛️",
  "description": "For 3rd year students.",
  "order": "2"
}
```
*If no `meta.json` is provided, the folder name will be used.*

### G. Special Node Types (Exams)
You can create a "Challenge" or "Exam" node that allows students to **test out** of a module.

To create an exam node, simply add the **`@exam`** tag to the header (no value needed):

```text
@title: Biology Final Exam
@exam
@icon: ⚔️
@description: Prove your skills to skip this module.

@quiz: Question 1...
```

### H. Presentation Files (INTRO.md)
Each version or "universe" can have its own welcome screen.
*   **For the "Live" version:** `content/rolling/INTRO.md`.
*   **For a "Release" (archived):** `content/releases/2024/INTRO.md`.

---

## 🏗️ Construction Mode Integration

If you are using the **Arborito Studio construction mode**, you can connect your GitHub account via a Personal Access Token (see `SECURITY_GUIDE.md`). This enables you to act as an **Architect of Arborito**:

*   **Visual Drag & Drop:** Move lessons between branches visually in the browser.
*   **Studio Editor:** Edit content with a real-time visual preview.
*   **AI Architect:** Use the Sage to generate curriculum blueprints (JSON) and plant them instantly.

---

## ⚠️ Important Rules

1.  **Unique Filenames:** Avoid special characters or spaces in filenames. Use lowercase, underscores, and prefix with numbers for ordering (e.g., `01_intro.md`).
2.  **Valid URLs:** Ensure all `@image` and `@video` links use HTTPS and are publicly accessible.
3.  **No HTML:** Do not write raw HTML tags inside Markdown files. Stick strictly to Markdown and Arborito `@directives`.