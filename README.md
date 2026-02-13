# todo.md

A plain-text task system for Obsidian. One file. No plugins required.

## Quick Start

Open `todo.md`. Add a task. Done.

```
(A) Write proposal @work [[Project Name]]
(B) Review budget @work
Call mom @personal
x 2026-02-13 Buy groceries @home
```

That's the whole system.

## The Basics

| Element | Example | Purpose |
|---------|---------|---------|
| Priority | `(A)` `(B)` `(C)` | What matters most (optional) |
| Context | `@work` `@personal` | Where/when you can do it |
| Project | `[[Project Name]]` | Links to Obsidian pages |
| Done | `x 2026-02-13` | Prepend when complete |

All elements are optional. A task can be as simple as `Buy milk`.

## Gravity & Drift

New tasks go at the top. Completed tasks stay where they are.

Over time, finished work sinks. Active work floats. An unfinished task surrounded by completed ones creates tension:

```
x 2026-02-05 Refactor parser @work
x 2026-02-07 Fix API bug @work
(A) Finalize migration plan @work [[Infrastructure]]
x 2026-02-09 Update README @work
```

That tension is a signal. Is this blocked? Still important? Should it be promoted or deleted?

Auto-sorting would erase this. Drift preserves it.

## Principles

- Plain text is the source of truth
- No hidden metadata, no databases
- No automatic reordering or archiving
- If Obsidian disappeared, this file still works

---

> **You can stop here.** Everything above is the complete system.
> 
> What follows is optional — power-user features, plugins, and queries for those who want more.

---

# Going Deeper (Power-User Features)

## Optional Plugin: Todo.txt Mode

The system works without plugins. But if you want syntax highlighting, install *Obsidian Todo.txt Mode*.

Use it for:
- Visual clarity
- Automatic completion dates

Avoid its auto-sorting features if you want to preserve drift.

---
## Extended Syntax

todo.md supports `key:value` metadata. These are plain text — useful for filtering with DataviewJS.

```
(A) Submit tax return @work due:2026-04-15
(B) Call vendor @work blocked:waiting-on-legal
(C) Plan vacation @personal t:2026-05-01
(B) Write report @work est:2h
(A) Expense report @work rec:monthly
```

| Key | Purpose |
|-----|---------|
| `due:` | Deadline (informational, not enforced) |
| `t:` | Threshold/start date |
| `est:` | Time estimate |
| `rec:` | Recurrence pattern |
| `blocked:` | Why it's stuck |
| *anything* | Invent your own |

---
## Querying with DataviewJS

Use DataviewJS to create filtered views. These queries use the Todo.txt Mode CSS classes for styling.

### Active @work tasks
```dataviewjs
const file = await dv.io.load("todo.md")
const lines = file.split("\n")
const tasks = lines.filter(line => line.includes("@work") && !line.startsWith("x "))

function styleLine(line) {
  return line
    .replace(/\(([ABC])\)/g, '<span class="todo-txt-mode-priority">($1)</span>')
    .replace(/@(\w+)/g, '<span class="todo-txt-mode-context">@$1</span>')
    .replace(/\[\[([^\]]+)\]\]/g, '<span class="todo-txt-mode-project">[[$1]]</span>')
    .replace(/due:(\d{4}-\d{2}-\d{2})/g, '<span class="todo-txt-mode-due-date">due:$1</span>')
}

dv.paragraph(tasks.map(styleLine).join("<br>"))
```

### Active @personal tasks
```dataviewjs
const file = await dv.io.load("todo.md")
const lines = file.split("\n")
const tasks = lines.filter(line => line.includes("@personal") && !line.startsWith("x "))

function styleLine(line) {
  return line
    .replace(/\(([ABC])\)/g, '<span class="todo-txt-mode-priority">($1)</span>')
    .replace(/@(\w+)/g, '<span class="todo-txt-mode-context">@$1</span>')
    .replace(/\[\[([^\]]+)\]\]/g, '<span class="todo-txt-mode-project">[[$1]]</span>')
    .replace(/due:(\d{4}-\d{2}-\d{2})/g, '<span class="todo-txt-mode-due-date">due:$1</span>')
}

dv.paragraph(tasks.map(styleLine).join("<br>"))
```

### Completed tasks
```dataviewjs
const file = await dv.io.load("todo.md")
const lines = file.split("\n")
const tasks = lines.filter(line => line.startsWith("x "))

function styleLine(line) {
  let styled = line
    .replace(/\(([ABC])\)/g, '<span class="todo-txt-mode-priority">($1)</span>')
    .replace(/@(\w+)/g, '<span class="todo-txt-mode-context">@$1</span>')
    .replace(/\[\[([^\]]+)\]\]/g, '<span class="todo-txt-mode-project">[[$1]]</span>')
    .replace(/^x (\d{4}-\d{2}-\d{2})/g, 'x <span class="todo-txt-mode-completion-date">$1</span>')
  return `<span class="todo-txt-mode-completed">${styled}</span>`
}

dv.paragraph(tasks.map(styleLine).join("<br>"))
```

### Tasks with due dates
```dataviewjs
const file = await dv.io.load("todo.md")
const lines = file.split("\n")
const tasks = lines.filter(line => line.includes("due:") && !line.startsWith("x "))

function styleLine(line) {
  return line
    .replace(/\(([ABC])\)/g, '<span class="todo-txt-mode-priority">($1)</span>')
    .replace(/@(\w+)/g, '<span class="todo-txt-mode-context">@$1</span>')
    .replace(/\[\[([^\]]+)\]\]/g, '<span class="todo-txt-mode-project">[[$1]]</span>')
    .replace(/due:(\d{4}-\d{2}-\d{2})/g, '<span class="todo-txt-mode-due-date">due:$1</span>')
}

dv.paragraph(tasks.map(styleLine).join("<br>"))
```

### Blocked tasks
```dataviewjs
const file = await dv.io.load("todo.md")
const lines = file.split("\n")
const tasks = lines.filter(line => line.includes("blocked:") && !line.startsWith("x "))
dv.list(tasks)
```

The power is in the plain text. Query however you like.
