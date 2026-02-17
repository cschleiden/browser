<div align="center">

# 🌐 Browser

**A simple web browser implementation in Go**

[![CI](https://github.com/cschleiden/browser/actions/workflows/ci.yml/badge.svg)](https://github.com/cschleiden/browser/actions/workflows/ci.yml)
[![Go Report Card](https://goreportcard.com/badge/github.com/cschleiden/browser)](https://goreportcard.com/report/github.com/cschleiden/browser)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

*Parse HTML & CSS, compute styles, calculate layout, and render to PNG – all from scratch in Go*

[**Try the Live Demo →**](https://cschleiden.github.io/browser/)

![Hacker News Rendering](./hackernews_screenshot.png)

</div>

---

## ✨ Features

| Category | Features |
|----------|----------|
| **HTML** | DOM tree construction, character entity decoding, void elements |
| **CSS** | CSS 2.1 parsing, selectors (element, class, ID, descendant), cascade & specificity |
| **Layout** | Box model, block formatting, table layout with auto column sizing |
| **Rendering** | High-quality text with Go fonts, borders, backgrounds, images (PNG/JPEG/GIF/SVG) |
| **Network** | HTTP/HTTPS page loading, external stylesheets, remote images |
| **Modern** | WebAssembly support – run entirely in the browser! |

## 🚀 Quick Start

### Installation

```bash
# Clone the repository
git clone https://github.com/cschleiden/browser.git
cd browser

# Build the browser
go build ./cmd/browser
```

### Usage

```bash
# Render a web page to PNG
./browser -output hackernews.png https://news.ycombinator.com/

# Render a local HTML file
./browser -output output.png test/styled.html

# Custom viewport size
./browser -output output.png -width 1024 -height 768 test/hackernews.html

# View layout tree (text output, no image)
./browser test/styled.html
```

### Run Tests

```bash
go test ./...
```

## 🌐 WebAssembly Demo

The browser compiles to WebAssembly and runs entirely in your web browser!

👉 **[Try the Live Demo](https://cschleiden.github.io/browser/)**

To build and run locally:

```bash
GOOS=js GOARCH=wasm go build -o wasm/browser.wasm ./cmd/browser-wasm
cd wasm && python3 -m http.server 8080
# Open http://localhost:8080
```

## 📸 Screenshots

<table>
<tr>
<td width="50%">

### Font Rendering
High-quality proportional sans-serif fonts with bold, italic, and underline support.

![Font Comparison](./font_comparison_screenshot.png)

</td>
<td width="50%">

### Styled HTML
Borders, colors, text formatting, and box model layout.

![Test Case Rendering](./test_case_screenshot.png)

</td>
</tr>
</table>

## 🏗️ Architecture

The browser follows a classic rendering pipeline:

```
HTML Input → Tokenization → DOM Tree → Style Computation → Layout → Rendering → PNG
                                 ↓
                          CSS Parsing
```

### Project Structure

```
browser/
├── cmd/
│   ├── browser/         # CLI application
│   └── browser-wasm/    # WebAssembly entry point
├── html/                # HTML tokenization & parsing
├── css/                 # CSS tokenization & parsing
├── dom/                 # DOM tree structure
├── style/               # Selector matching & cascade
├── layout/              # Box model & visual formatting
├── render/              # Canvas rendering & PNG output
├── font/                # Font loading (Go fonts)
├── svg/                 # SVG parsing & rasterization
├── wasm/                # WebAssembly demo
└── test/                # Test fixtures
```

## 📋 Specifications

This implementation follows W3C specifications:

| Spec | Coverage |
|------|----------|
| [HTML5 §12 Parsing](https://html.spec.whatwg.org/multipage/parsing.html) | Tokenization, tree construction, void elements |
| [CSS 2.1 §4 Syntax](https://www.w3.org/TR/CSS21/syndata.html) | Tokenization, values, colors |
| [CSS 2.1 §5 Selectors](https://www.w3.org/TR/CSS21/selector.html) | Element, class, ID, descendant |
| [CSS 2.1 §6 Cascade](https://www.w3.org/TR/CSS21/cascade.html) | Specificity, inheritance |
| [CSS 2.1 §8 Box Model](https://www.w3.org/TR/CSS21/box.html) | Content, padding, border, margin |
| [CSS 2.1 §9 Visual Formatting](https://www.w3.org/TR/CSS21/visuren.html) | Block layout, inline layout |
| [CSS 2.1 §17 Tables](https://www.w3.org/TR/CSS21/tables.html) | Table layout, colspan |
| [RFC 2397](https://datatracker.ietf.org/doc/html/rfc2397) | Data URLs (base64, URL-encoded) |

## 📊 Current Status

| Milestone | Status | Description |
|-----------|--------|-------------|
| Foundation | ✅ | Project setup, architecture |
| HTML Parsing | ✅ | Tokenization, DOM tree, entities |
| CSS Parsing | ✅ | Selectors, declarations, cascade |
| Style Computation | ✅ | Matching, specificity, inheritance |
| Layout Engine | ✅ | Box model, block & table layout |
| Rendering | ✅ | Text, colors, borders, backgrounds |
| Image Rendering | ✅ | PNG, JPEG, GIF, SVG support |
| Network Support | ✅ | HTTP/HTTPS, external CSS, remote images |
| Data URLs | ✅ | RFC 2397 inline resources |
| WebAssembly | ✅ | Browser-based rendering |
| Testing | ✅ | 92.3% WPT pass rate |

## 📚 Documentation

- **[MILESTONES.md](MILESTONES.md)** – Implementation progress & roadmap
- **[IMPLEMENTATION.md](IMPLEMENTATION.md)** – Architecture & design decisions
- **[TESTING.md](TESTING.md)** – Test strategy & WPT integration

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
3. **Commit** your changes (`git commit -m 'Add amazing feature'`)
4. **Push** to the branch (`git push origin feature/amazing-feature`)
5. **Open** a Pull Request

Please ensure your code:
- Passes all tests (`go test ./...`)
- Follows Go conventions (`go fmt`, `go vet`)
- Includes tests for new functionality

## 📄 License

This project is licensed under the MIT License – see the [LICENSE](LICENSE) file for details.

---

<div align="center">

**Built with ❤️ in Go**

</div>
