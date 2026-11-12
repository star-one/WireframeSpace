# WireframeSpace

**Build interactive wireframes from plain text and a simple form. Preview them, click through them, then hand them to an AI or a developer.**

WireframeSpace is a single-file, browser-based tool for sketching the structure of a website or app. You describe screens in a small, readable text format (a "Mermaid-like" syntax), or let the built-in form write that text for you. A live preview shows the result as a grey-box wireframe in mobile or widescreen layout, and a Run mode lets people click through it as if it were real.

There is no drag and drop on purpose: text and forms scale better, diff well in version control, and are easy for both people and AI tools to read and write.

> Repository: <https://github.com/star-one/UXSpace>

## Contents

- [Features](#features)
- [Quick start](#quick-start)
- [How it works](#how-it-works)
- [The text format](#the-text-format)
- [Exports](#exports)
- [Projects and saving](#projects-and-saving)
- [Browser support and limits](#browser-support-and-limits)
- [Hosting it](#hosting-it)
- [Development notes](#development-notes)
- [Contributing](#contributing)
- [Licence](#licence)
- [Credits](#credits)

## Features

- **Text-first design.** Screens, layout, content and behaviour are all plain text. Everything the form does, you can do by typing.
- **Form-assisted editing.** An Add form writes correctly indented lines for you: pick a screen, a container to put the element in, an element type, a label and any options. An Edit form changes labels, options, actions and even the element type (for example Text to Paragraph, or Link to Button).
- **Live preview.** Switch between Mobile and Widescreen. Edit mode lets you click an element to select it (the matching lines are highlighted in the text). Run mode makes links, buttons, tabs, modals and states work.
- **Responsive layout without coordinates.** Sections, columns with ratios (equal columns, main plus sidebar and so on), cards and inline groups stack sensibly on narrow screens.
- **Shared components.** Define a header or footer once and use it on every screen.
- **Modals.** Shown separately from the page in Edit mode and in image exports, opened and closed by named actions in Run mode and in the exported page.
- **Screen states.** Empty, Loading, Error, Form completed and any states you invent: show or hide elements per state and switch states with a click.
- **Copy, cut and paste** of selected elements and containers, plus undo and redo.
- **Guided tutorials.** A basic tutorial builds a simple page step by step; an advanced tutorial covers shared components, tabs, tables, modals, states, forms and copy and paste. Each step highlights what to click and moves on automatically.
- **Handoff and sharing.**
  - A ready-made **AI prompt** for several build targets.
  - A **shareable, responsive HTML wireframe** in a single file, for testing with real people.
  - **PNG and SVG** images of screens, states and open modals.
  - Plain-text **project files** you can keep in Git.

## Quick start

1. Download or clone the repository.
2. Open `index.html` (or whatever the file is called in your copy) in a modern browser. There is nothing to install or build.
3. On first load you will see an empty project. Choose **Start the tutorial**, **Load the sample project**, or begin from scratch:
   - open **Within screen** in the **Add** box,
   - choose **+ Add new screen...**, type a name and press **Create**,
   - use **Element**, **Label** and **Add element** to build the page.

The page loads one external script, [html2canvas](https://html2canvas.hertzen.com/), from a CDN. It is only needed for PNG export; everything else works offline.

## How it works

The screen is split into two sides.

**Left: the form and the text**

- **Add** adds an element to a screen. The text it writes goes where **Within screen**, **Inside** and **Position** point.
- **Edit** works on the selected element: move up or down, delete, copy, cut, paste, undo, redo, change its label, options, type or action, and move it into another container.
- The **text editor** holds the whole project. You can edit it directly at any time; the preview updates as you type.

**Right: the preview**

- A row of screen buttons and a **State** row for the current screen.
- **Mobile / Widescreen** changes the preview width.
- **Edit / Run** switches between selecting elements and clicking through the prototype.
- Under the preview, **Save PNG** and **Save SVG** export images.

The **Export** tab produces the AI handoff prompt and the shareable HTML wireframe.

## The text format

Indentation (two spaces) means nesting. Lines starting with `#` are comments. Labels and names are always in double quotes.

```text
# A shared component, defined once
component "Header"
  nav "Main nav"
    link "Home" -> "Home"
    link "Events" -> "Events"

screen "Home"
  use "Header"
  section "Welcome"
    heading "Plan work together"
    paragraph
    inline
      button "Get started" -> "Events"

screen "Events" states="Default|Empty|Form completed"
  use "Header"
  heading "Events"
  text "No events yet." show-in=Empty
  columns "Content" layout=2:1
    column "Main"
      card "Event"
        heading "Launch party"
        button "Book" opens "Book event"
    column "Sidebar"
      form "Search" hide-in="Form completed"
        input "Search" type=search
        date "From"
        button "Find" state="Form completed"
  section "Thanks" show-in="Form completed"
    heading "Thank you"
  modal "Book event"
    text "Event details here"
    button "Close" closes
```

### Screens and components

| Line | Meaning |
| --- | --- |
| `screen "Name"` | A screen (top level). |
| `component "Name"` | A shared block (top level). |
| `use "Name"` | Place a shared component inside a screen. |
| `screen "Name" states="Default\|Empty"` | Declare states for a screen. The first is the default. |

### Layout containers

| Element | Meaning |
| --- | --- |
| `section "Title"` | A full-width, boxed band that stacks its contents. The title is shown. |
| `columns "Name" layout=2:1` | Splits the width by ratio (`1:1`, `1:1:1`, `2:1`, `1:3`, `1:2:1` and so on). Stacks on mobile; extra children wrap onto new rows. |
| `column "Name"` | One column inside `columns`. |
| `card "Name"` | A bordered box. |
| `inline` | Items side by side with no box (for example a pair of buttons). |
| `form "Name"` | A form. |
| `nav "Name"` | A navigation bar. |
| `modal "Title"` | A dialog. Always sits at the bottom of its screen. |

The label on `card`, `columns`, `column`, `nav`, `form` and `inline` is a **reference name** only. It is not shown on the page, but it is how you find the element in the Inside, Position, Edit and Move lists, so give every element a label.

### Content and controls

`heading`, `text`, `paragraph` (Lorem ipsum when empty), `image`, `table`, `divider`, `input`, `select`, `checkbox`, `date`, `button`, `link`, `links` (example links), `tabs` and `note`.

Options are written as attributes:

| Attribute | Used on | Example |
| --- | --- | --- |
| `type=` | `input` | `type=email`, `password`, `number`, `search`, `tel` |
| `options=` | `select` | `options="Low\|Medium\|High"` |
| `kind=` | `date` | `kind=datetime` or `kind=month` |
| `cols=` and `rows=` | `table` | `cols="Name\|Role" rows=3` |
| `items=` | `tabs` | `items=Overview\|Details` |
| `count=` | `links` | `count=4` |
| `style=` | `button` | `style=secondary` |

### Actions (Run mode and the exported page)

| Syntax | Effect |
| --- | --- |
| `button "Go" -> "Screen"` | Go to a screen. |
| `button "Book" opens "Modal title"` | Open a modal on the same screen. |
| `button "Close" closes` | Close the modal it is in (Esc and clicking the backdrop also close it). |
| `button "Retry" state=Error` | Switch the current screen to a state. |

A modal can only be opened by controls on its own screen, or in a shared component that screen uses. The editor warns about a modal that nothing opens.

### Screen states

States are a way to show or hide elements on the same screen: an empty list, an error message, a "thank you" after a form.

- Declare them with `states="Default|Empty|Error"`, or add them from the State row.
- `show-in=Empty` shows an element only in that state; `hide-in=Error` hides it there. Combine states with `|` (`show-in="Empty|Error"`).
- Typing **Form completed** in the State row adds a thank-you message, hides each form in that state and makes each form's plain buttons switch to it.

## Exports

### AI handoff

The Export tab builds a prompt you can paste into an AI tool. Choose a build target:

| Target | What you get |
| --- | --- |
| Demo | A clickable plain HTML, CSS and JavaScript demo with dummy content and stock images matched to image labels. |
| Starter | Empty, well-structured HTML, CSS and JavaScript files with placeholders. |
| PHP + MySQL | A PHP page per screen, shared partials, a PDO connection and a schema inferred from your forms and tables. |
| Framework | A project in React, Next.js, Vue, Nuxt, Angular, SvelteKit, Laravel, Django or Ruby on Rails. |
| Web CMS module, plugin or theme | A Drupal theme or module, a WordPress theme (classic or block), plugin or Gutenberg block, a Joomla template or module, Craft CMS templates, a Shopify theme, a Ghost theme, or another CMS you name in the special instructions. |
| Apply to existing code | Instructions to change only what is needed in code you paste, for all screens or just the current one. |

Every prompt includes:

- the design spec,
- screen navigation,
- a shared-components checklist,
- a requirements checklist built from your notes,
- modal and state behaviour,
- responsive rules based on the elements you used,
- optional reference URLs for the look and feel,
- an optional box for your own special instructions.

### Shareable HTML wireframe

A single, responsive HTML file that behaves like a site: columns and menus adapt to the browser width, links navigate between screens, modals are real `<dialog>` elements, and tabs work. A small yellow state bar lets reviewers flip between states (remove it in production).

### Images

Save PNG or SVG for the current screen or all screens. Every state and every open modal gets its own frame (a modal is drawn over a dimmed copy of its page). Frames can be stacked vertically or placed side by side. Images are always drawn on a light background.

## Projects and saving

- Your work is autosaved in the browser's local storage.
- **Save project** writes a plain text file (`.wfspace.txt`) whose first line is `# WireframeSpace project: Your name`, followed by your text. **Load project** reads it back, so project files are easy to read, diff and keep in Git.
- **Clear**, **Load sample**, **Load tutorial** and **Load project** also reset the Export tab (build target, special instructions, reference URLs). **Clear** empties the text and the project name; **Undo** brings the text back.

## Browser support and limits

- Works in current versions of Chrome, Edge, Firefox and Safari (it uses CSS grid and the `<dialog>` element).
- It is a **wireframing** tool: grey boxes and structure, not visual design, and it has no accounts, collaboration or server.
- Copy and paste uses an internal clipboard (within the open project).
- Opening a modal from a different screen is not supported.
- PNG export needs the html2canvas script to load.

## Hosting it

Because it is one file, it works on any static host. For GitHub Pages, name the file `index.html`, push it, and turn on Pages for the repository.

## Development notes

Everything lives in one HTML file: styles, markup and JavaScript.

1. **Parse.** `parse()` turns the indented text into a tree of nodes (`type`, `label`, `attrs`, `to`, `opens`, `closes`, `children`) and collects problems for the error list.
2. **Render.** `R()` and `R0()` turn a node into preview HTML. The same renderer feeds the shareable HTML, the image frames and the AI handoff.
3. **Form.** The Add and Edit forms never hold state of their own: they compute a line of text and write it into the editor, so the text is always the single source of truth.
4. **Tutorials.** Each step has a check function over the parsed tree, so steps complete themselves when the text matches.

There are no tests or build tools yet. When changing the parser or the form, a useful smoke test is to load the sample project, run the tutorials with **Do it for me**, and check that the error list stays empty.

## Contributing

Issues and pull requests are welcome. Good first areas:

- more form-field types and options,
- a custom ratio picker for columns,
- theming and brand styles for the exported wireframe,
- automated tests for the parser and renderer.

Please keep the project a single dependency-light file where practical, and keep the text format human-readable.

## Licence

Add the licence of your choice here (a `LICENSE` file in the repository root is recommended).

## Credits

A project by Simon Gray. Made with Claude.

PNG export uses [html2canvas](https://html2canvas.hertzen.com/).
