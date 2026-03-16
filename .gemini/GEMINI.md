# Project Summary

> A curated collection of Claude prompt templates with a modern, responsive web interface.

---

## Project Overview

**Claude Notes** is a high-performance, production-ready web application that provides expert-level prompt templates optimized for Claude and other Large Language Models. The project features a clean, modern UI with dark/light theme support and a favorites system for personalized productivity.

---

## Repository Information

- **Repository**: [dhyan2815/claude-prompt-templates](https://github.com/dhyan2815/claude-prompt-templates/)
- **Live Demo**: [https://dhyan2815.github.io/claude-prompt-templates/](https://dhyan2815.github.io/claude-prompt-templates/)
- **License**: MIT
- **Created**: 2026-03-15

---

## Commit History

| Commit | Date | Description |
|--------|------|-------------|
| `1f65c06` | 2026-03-16 | Feat: Added Report Generation section with 5 new templates (research, test, progress, project status, performance) |
| `fd028ec` | 2026-03-15 | Refactor code structure - moved GEMINI.md to `.gemini/` directory, removed `web_app.html` |
| `96105f3` | 2026-03-15 | Style: Updated prompt text colors for better readability in both themes |
| `fc822d7` | 2026-03-15 | Docs: Upgraded README with detailed features, tech stack, and live link |
| `66a935f` | 2026-03-15 | Refactor: Reorganized project structure with `.claude/` directory for Claude-specific docs |
| `443562c` | 2026-03-15 | Include GEMINI.md in tracking and update memory |
| `a3479f6` | 2026-03-15 | **Initial commit**: Enhanced Universal Claude Prompt Templates with Theme and Favorites |

---

## Key Features

1. **35+ Expert Templates**: Curated prompts for Coding, Data Analysis, Reports, Creative Writing, Planning, and Learning
2. **Dynamic Favorites System**: Save templates to localStorage for quick access
3. **Adaptive UI**: Seamless dark/light mode toggle with localStorage persistence
4. **Lightning Fast Filtering**: Instant search and category-based filtering
5. **One-Click Copy**: Clipboard integration with visual feedback
6. **Responsive Design**: Optimized for desktop, tablet, and mobile viewports
7. **Syntax Highlighting**: Visual cues for placeholders `[LIKE_THIS]`

---

## Technology Stack

| Category | Technology |
|----------|-------------|
| **Frontend** | Vanilla HTML5, CSS3 (Flexbox & Grid), ES6+ JavaScript |
| **Typography** | Syne, JetBrains Mono, Instrument Sans (Google Fonts) |
| **Icons** | Emoji-based and CSS-styled primitives (zero dependencies) |
| **Persistence** | Browser `localStorage` API |
| **Hosting** | GitHub Pages |

---

## Project Structure

```
claude-prompt-templates/
├── .claude/
│   ├── CLAUDE_CODE_CHEATSHEET.md   # Quick reference for Claude Code
│   └── CLAUDE_CODE_GUIDE.md        # Professional guide for Claude Code
├── .gemini/
│   └── GEMINI.md                    # Project summary for Gemini CLI
├── .gitignore                       # Git ignore rules
├── index.html                       # Main web application (1500+ lines)
├── README.md                        # Project documentation
└── (this file) GEMINI.md            # Project summary
```

---

## Development Commands

| Action | Command |
|--------|---------|
| Clone repo | `git clone https://github.com/dhyan2815/claude-prompt-templates.git` |
| Open locally | Open `index.html` in any modern browser |
| Check status | `git status` |
| View history | `git log --oneline` |
| Push changes | `git push origin master` |

---

## Architecture Notes

### Web Application (`index.html`)
- **Single-page application** with embedded CSS and JavaScript
- **No build step required** - serves directly from GitHub Pages
- **Theme system**: CSS custom properties (`--surface`, `--text`, `--border`, etc.)
- **State management**: JavaScript objects with localStorage persistence
- **Categories**: Coding, Data, Reports, Writing, Planning, Learning, Favorites

### Template Structure
Each template includes:
- Title and description
- Category classification
- Copy-ready prompt text
- Placeholder markers `[LIKE_THIS]` for customization

---

## Recent Changes Summary

### 2026-03-16
- **New Report Generation Section**: Added 5 specialized report templates
  - Research Report: Academic and business research documentation
  - Test Report: QA and testing results documentation
  - Progress Report: Periodic progress tracking
  - Project Status Report: Comprehensive project status with RAG status
  - Performance Report: Performance analysis and metrics

### 2026-03-15
- **Initial Release**: Complete web application with 30+ prompt templates
- **Theme Support**: Implemented dark/light mode with smooth transitions
- **Favorites Feature**: Added ability to star and filter favorite templates
- **SEO Enhancement**: Added Open Graph and Twitter Card meta tags
- **Documentation**: Created comprehensive README and project guides
- **Project Organization**: Separated AI-specific guides into `.claude/` and `.gemini/` directories

---

## How to Use

1. **Browse Templates**: Use navigation pills to filter by category
2. **Search**: Enter keywords in the search bar for specific topics
3. **Add Favorites**: Click ★ icon to save templates for quick access
4. **Copy**: Click "Copy" button to copy template to clipboard
5. **Use with LLMs**: Paste into Claude, ChatGPT, or other LLM interfaces

---

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---