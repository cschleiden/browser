# Architecture Diagram

This document provides a visual overview of the browser's architecture and rendering pipeline.

## What Does This Repository Do?

This repository implements a **simple web browser in Go** that:

1. **Parses HTML** documents into a DOM (Document Object Model) tree
2. **Parses CSS** stylesheets into a structured representation
3. **Computes styles** by matching CSS selectors to DOM elements
4. **Calculates layout** using the CSS box model and visual formatting rules
5. **Renders the result** as a PNG image

It's designed for **educational purposes**, following W3C specifications closely while keeping the codebase clean and understandable.

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              BROWSER RENDERING PIPELINE                          │
└─────────────────────────────────────────────────────────────────────────────────┘

                    ┌──────────────────┐
                    │   Input Source   │
                    │  (HTML file or   │
                    │   HTTP/HTTPS)    │
                    └────────┬─────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              1. PARSING PHASE                                    │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│    ┌─────────────────────────────────┐     ┌─────────────────────────────────┐  │
│    │         HTML Parser             │     │          CSS Parser             │  │
│    │         (html/)                 │     │          (css/)                 │  │
│    ├─────────────────────────────────┤     ├─────────────────────────────────┤  │
│    │  ┌─────────────┐                │     │  ┌─────────────┐                │  │
│    │  │ Tokenizer   │ HTML String    │     │  │ Tokenizer   │ CSS String     │  │
│    │  │ tokenizer.go│ ──────────►    │     │  │ tokenizer.go│ ──────────►    │  │
│    │  └──────┬──────┘                │     │  └──────┬──────┘                │  │
│    │         │ Tokens                │     │         │ Tokens                │  │
│    │         ▼                       │     │         ▼                       │  │
│    │  ┌─────────────┐                │     │  ┌─────────────┐                │  │
│    │  │  Parser     │ ──────────►    │     │  │  Parser     │ ──────────►    │  │
│    │  │  parser.go  │ DOM Tree       │     │  │  parser.go  │ Stylesheet     │  │
│    │  └─────────────┘                │     │  └─────────────┘                │  │
│    └─────────────────────────────────┘     └─────────────────────────────────┘  │
│                          │                               │                       │
└──────────────────────────┼───────────────────────────────┼───────────────────────┘
                           │                               │
                           ▼                               ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                           2. STYLE COMPUTATION PHASE                             │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│    ┌───────────────────────────────────────────────────────────────────────┐    │
│    │                        Style Engine (style/)                           │    │
│    ├───────────────────────────────────────────────────────────────────────┤    │
│    │                                                                        │    │
│    │   DOM Tree ─────┐                                                      │    │
│    │                 │     ┌─────────────────────────────┐                  │    │
│    │                 ├────►│   Selector Matching         │                  │    │
│    │                 │     │   (CSS 2.1 §5, §6)          │                  │    │
│    │   Stylesheet ───┘     │                             │                  │    │
│    │                       │   • Match selectors to      │                  │    │
│    │                       │     DOM elements            │                  │    │
│    │                       │   • Calculate specificity   │                  │    │
│    │                       │   • Apply cascade rules     │                  │    │
│    │                       └──────────────┬──────────────┘                  │    │
│    │                                      │                                 │    │
│    │                                      ▼                                 │    │
│    │                       ┌─────────────────────────────┐                  │    │
│    │                       │      Styled Tree            │                  │    │
│    │                       │   (DOM + computed styles)   │                  │    │
│    │                       └─────────────────────────────┘                  │    │
│    └───────────────────────────────────────────────────────────────────────┘    │
│                                          │                                       │
└──────────────────────────────────────────┼───────────────────────────────────────┘
                                           │
                                           ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              3. LAYOUT PHASE                                     │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│    ┌───────────────────────────────────────────────────────────────────────┐    │
│    │                      Layout Engine (layout/)                           │    │
│    ├───────────────────────────────────────────────────────────────────────┤    │
│    │                                                                        │    │
│    │   Styled Tree ──────►┌─────────────────────────────┐                   │    │
│    │                      │   Box Model Calculation     │                   │    │
│    │   Viewport ─────────►│   (CSS 2.1 §8, §9, §10)     │                   │    │
│    │   (width x height)   │                             │                   │    │
│    │                      │   • Content, padding,       │                   │    │
│    │                      │     border, margin          │                   │    │
│    │                      │   • Width/height resolution │                   │    │
│    │                      │   • Position calculation    │                   │    │
│    │                      │   • Block/inline layout     │                   │    │
│    │                      └──────────────┬──────────────┘                   │    │
│    │                                     │                                  │    │
│    │                                     ▼                                  │    │
│    │                      ┌─────────────────────────────┐                   │    │
│    │                      │       Layout Tree           │                   │    │
│    │                      │   (boxes with dimensions    │                   │    │
│    │                      │    and positions)           │                   │    │
│    │                      └─────────────────────────────┘                   │    │
│    └───────────────────────────────────────────────────────────────────────┘    │
│                                          │                                       │
└──────────────────────────────────────────┼───────────────────────────────────────┘
                                           │
                                           ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              4. RENDERING PHASE                                  │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│    ┌───────────────────────────────────────────────────────────────────────┐    │
│    │                      Render Engine (render/)                           │    │
│    ├───────────────────────────────────────────────────────────────────────┤    │
│    │                                                                        │    │
│    │   Layout Tree ──────►┌─────────────────────────────┐                   │    │
│    │                      │   Canvas Rendering          │                   │    │
│    │                      │   (CSS 2.1 §14, §16)        │                   │    │
│    │                      │                             │                   │    │
│    │                      │   • Background colors       │                   │    │
│    │                      │   • Border drawing          │                   │    │
│    │                      │   • Text rendering          │                   │    │
│    │                      │   • Image rendering         │                   │    │
│    │                      └──────────────┬──────────────┘                   │    │
│    │                                     │                                  │    │
│    │                                     ▼                                  │    │
│    │                      ┌─────────────────────────────┐                   │    │
│    │                      │         PNG Image           │                   │    │
│    │                      └─────────────────────────────┘                   │    │
│    └───────────────────────────────────────────────────────────────────────┘    │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## Package Dependencies

```
                              ┌──────────────┐
                              │  cmd/browser │
                              │   (main)     │
                              └──────┬───────┘
                                     │
           ┌───────────┬─────────────┼─────────────┬───────────┐
           │           │             │             │           │
           ▼           ▼             ▼             ▼           ▼
    ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
    │   html   │ │   css    │ │  style   │ │  layout  │ │  render  │
    │          │ │          │ │          │ │          │ │          │
    └────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘
         │            │            │            │            │
         │            │            │            │            │
         └────────────┴─────┬──────┴────────────┴────────────┘
                            │
                            ▼
                      ┌──────────┐
                      │   dom    │
                      │          │
                      └──────────┘


Legend:
  ──────► depends on
```

---

## Data Structures

### DOM Tree (dom/node.go)

```
┌─────────────────────────────────────────────────────────┐
│                        Node                              │
├─────────────────────────────────────────────────────────┤
│  Type: NodeType (Element, Text, Document)               │
│  Data: string (tag name or text content)                │
│  Attributes: map[string]string                          │
│  Children: []*Node                                      │
│  Parent: *Node                                          │
└─────────────────────────────────────────────────────────┘

Example DOM Tree:
                     ┌──────────────┐
                     │   Document   │
                     └──────┬───────┘
                            │
                     ┌──────┴───────┐
                     │    <html>    │
                     └──────┬───────┘
                            │
              ┌─────────────┴─────────────┐
              │                           │
       ┌──────┴───────┐           ┌───────┴──────┐
       │    <head>    │           │    <body>    │
       └──────┬───────┘           └───────┬──────┘
              │                           │
       ┌──────┴───────┐           ┌───────┴──────┐
       │   <title>    │           │     <div>    │
       └──────────────┘           └───────┬──────┘
                                          │
                               ┌──────────┴──────────┐
                               │                     │
                        ┌──────┴───────┐      ┌──────┴───────┐
                        │     <p>      │      │   "Text"     │
                        └──────────────┘      └──────────────┘
```

### Stylesheet (css/parser.go)

```
┌─────────────────────────────────────────────────────────┐
│                     Stylesheet                           │
├─────────────────────────────────────────────────────────┤
│  Rules: []Rule                                          │
└─────────────────────────────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────────────────────────────┐
│                        Rule                              │
├─────────────────────────────────────────────────────────┤
│  Selectors: []Selector                                  │
│  Declarations: []Declaration                            │
└─────────────────────────────────────────────────────────┘

Example: "div.content, p#intro { color: blue; margin: 10px; }"

         ┌────────────────────────────────────────┐
         │                Rule                    │
         └────────────────────┬───────────────────┘
                              │
         ┌────────────────────┴───────────────────┐
         │                                        │
    Selectors                               Declarations
         │                                        │
    ┌────┴────┐                          ┌────────┴────────┐
    │         │                          │                 │
div.content  p#intro               color: blue      margin: 10px
```

### Styled Tree (style/style.go)

```
┌─────────────────────────────────────────────────────────┐
│                     StyledNode                           │
├─────────────────────────────────────────────────────────┤
│  Node: *dom.Node                                        │
│  Styles: map[string]string (computed CSS properties)    │
│  Children: []*StyledNode                                │
└─────────────────────────────────────────────────────────┘

Example:
       ┌────────────────────────────────────────────┐
       │               StyledNode                   │
       │  Node: <div>                               │
       │  Styles: {                                 │
       │    "display": "block",                     │
       │    "color": "blue",                        │
       │    "margin": "10px"                        │
       │  }                                         │
       └──────────────────┬─────────────────────────┘
                          │
            ┌─────────────┴─────────────┐
            │                           │
     ┌──────┴──────┐             ┌──────┴──────┐
     │ StyledNode  │             │ StyledNode  │
     │ Node: <p>   │             │ Node: "text"│
     │ Styles: {}  │             │ Styles: {}  │
     └─────────────┘             └─────────────┘
```

### Layout Tree (layout/layout.go)

```
┌─────────────────────────────────────────────────────────┐
│                     LayoutBox                            │
├─────────────────────────────────────────────────────────┤
│  BoxType: BoxType (Block, Inline, Anonymous, Table...)  │
│  Dimensions: Dimensions                                 │
│  StyledNode: *StyledNode                                │
│  Children: []*LayoutBox                                 │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│                    Dimensions                            │
├─────────────────────────────────────────────────────────┤
│  Content: Rect {X, Y, Width, Height}                    │
│  Padding: EdgeSize {Top, Right, Bottom, Left}           │
│  Border:  EdgeSize {Top, Right, Bottom, Left}           │
│  Margin:  EdgeSize {Top, Right, Bottom, Left}           │
└─────────────────────────────────────────────────────────┘

CSS Box Model (CSS 2.1 §8):

┌───────────────────────────────────────────────────────────────┐
│                          MARGIN                               │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │                         BORDER                          │  │
│  │  ┌───────────────────────────────────────────────────┐  │  │
│  │  │                       PADDING                     │  │  │
│  │  │  ┌─────────────────────────────────────────────┐  │  │  │
│  │  │  │                                             │  │  │  │
│  │  │  │                   CONTENT                   │  │  │  │
│  │  │  │              (Width × Height)               │  │  │  │
│  │  │  │                                             │  │  │  │
│  │  │  └─────────────────────────────────────────────┘  │  │  │
│  │  │                                                   │  │  │
│  │  └───────────────────────────────────────────────────┘  │  │
│  │                                                         │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                               │
└───────────────────────────────────────────────────────────────┘
```

---

## Data Flow Summary

```
┌────────────────┐
│  HTML String   │
└───────┬────────┘
        │  html.Parse()
        ▼
┌────────────────┐     ┌────────────────┐
│   DOM Tree     │     │  CSS String    │
│   (*dom.Node)  │     │  (from <style> │
└───────┬────────┘     │   and <link>)  │
        │              └───────┬────────┘
        │                      │ css.Parse()
        │                      ▼
        │              ┌────────────────┐
        │              │   Stylesheet   │
        │              │ (*css.Stylesheet)│
        └────────┬─────┴────────────────┘
                 │
                 │  style.StyleTree()
                 ▼
        ┌────────────────┐
        │  Styled Tree   │
        │(*style.StyledNode)│
        └───────┬────────┘
                │
                │  layout.LayoutTree()
                ▼
        ┌────────────────┐
        │  Layout Tree   │
        │(*layout.LayoutBox)│
        └───────┬────────┘
                │
                │  render.Render()
                ▼
        ┌────────────────┐
        │    Canvas      │
        │ (*render.Canvas)│
        └───────┬────────┘
                │
                │  canvas.SavePNG()
                ▼
        ┌────────────────┐
        │   PNG File     │
        └────────────────┘
```

---

## Supported Features

| Feature | Status | W3C Spec Reference |
|---------|--------|-------------------|
| HTML tokenization | ✅ | HTML5 §12.2.5 |
| DOM tree construction | ✅ | HTML5 §12.2.6 |
| CSS tokenization | ✅ | CSS 2.1 §4 |
| Simple selectors | ✅ | CSS 2.1 §5.2 |
| Descendant selectors | ✅ | CSS 2.1 §5.5 |
| Specificity | ✅ | CSS 2.1 §6.4.3 |
| Box model | ✅ | CSS 2.1 §8 |
| Block layout | ✅ | CSS 2.1 §9.4.1 |
| Inline layout | ✅ | CSS 2.1 §9.4.2 |
| Table layout | ✅ | CSS 2.1 §17 |
| Background colors | ✅ | CSS 2.1 §14.2 |
| Border rendering | ✅ | CSS 2.1 §8.5 |
| Text rendering | ✅ | CSS 2.1 §16 |
| Image rendering | ✅ | HTML5 §4.8.2 |
| Network fetching | ✅ | HTTP/HTTPS |
| WebAssembly | ✅ | WASM target |

---

## Entry Points

### CLI Application (`cmd/browser/main.go`)

```bash
# Render local HTML to PNG
./browser -output output.png test/styled.html

# Render web page to PNG
./browser -output hn.png https://news.ycombinator.com/

# Display layout tree (debug)
./browser -show-layout test/styled.html

# Display render tree (debug)
./browser -show-render test/styled.html
```

### WebAssembly (`cmd/browser-wasm/`)

Compiles to WebAssembly for running entirely in a web browser.

---

## See Also

- [MILESTONES.md](MILESTONES.md) - Implementation progress
- [IMPLEMENTATION.md](IMPLEMENTATION.md) - Technical details
- [TESTING.md](TESTING.md) - Testing strategy
