# Academic Integrity Decision Tree

An interactive guide for Salem Community College instructors who suspect a student used AI on an assignment. It walks faculty through how the concern came up, the student's history, the conversation with the student, and the course AI policy, then points them to a next step:

- Have a conversation with the student
- Make it a learning opportunity (discussion, consider resubmission)
- Bring in the student's allies (Dropout Detective Academic Alert)
- Submit an academic integrity report
- Use instructor discretion

**Live tool:** https://elbies19.github.io/scc-ai-integrity/

Questions: Lauren Schwartz, Instructional Development Specialist, lschwartz@salemcc.edu

## Files

| File | What it is |
|---|---|
| `index.html` | The whole tool: content, styling, and logic in one self-contained file. No external dependencies. |
| `Academic_Integrity_Occurrence_Report.pdf` | Fillable, tagged version of the Academic Integrity Occurrence Report, linked from the reporting endpoint. |
| `README.md` | This file. |

## Privacy

Nothing faculty enter is saved, tracked, or sent anywhere. Everything happens in the browser and disappears when the page is closed. The summary at the end exists only on the instructor's screen unless they print or save it.

## Editing the content

All the text faculty read lives in the **CONTENT** section at the top of `index.html`, separate from the code. You can edit it directly in GitHub (open the file, select the pencil icon, edit, commit). Changes go live on GitHub Pages within a minute or two.

What's where:

- **`SETTINGS`**: the title, intro, footer, links, and the GPTZero pilot course list.
  - `pilotCourses`: courses that see the GPTZero pilot message when an instructor picks "An AI detection tool flagged it." Add or remove course codes here.
  - `links`: every URL the tool uses, in one place.
- **`CONTACT_TIP`**: the SCC Teaching Tip shown at the bottom of every endpoint.
- **`STUDENT_MESSAGE`**: the message template offered on "Have a conversation with the student."
- **`NODES`**: every question, message, and endpoint in the tree.
- **`COURSES`**: the course dropdown list, from the Fall 2026 textbook list. Add courses as `["CODE123","Course Title"],`.

Text fields accept simple HTML: `<p>`, `<strong>`, `<em>`, `<a>`, `<ul>`, `<ol>`.

### How routing works

Each step in `NODES` has an id (like `"offense"` or `"end-report"`). An answer's `next` is the id of the step it goes to.

When the destination depends on earlier answers, `next` is a list of rules, checked in order. The first match wins, and a rule with no `when` is the fallback:

```js
next: [
  { when: { offense: "second" }, go: "end-escalate" },
  { when: { offense: "third" },  go: "end-report" },
  { go: "policy" }
]
```

A list inside `when` means "any of these": `{ offense: ["second", "third"] }`.

The same `when` format controls conditional content:

- `variants`: swaps a step's title, note, or body based on earlier answers
- `showWhen` / `hideWhen`: shows or hides an answer option, button, or callout

Answers are saved under each question's `saveAs` name (`how`, `basedOn`, `offense`, `raised`, `response`, `proof`, `policy`, `assistiveLanguage`, `outcome`), plus `course`, `pilot` (true for pilot courses), and `detectionRedirect` (true after the non-pilot detection message).

## Embedding in Canvas

In a Canvas page, switch to the HTML editor and add:

```html
<iframe src="https://elbies19.github.io/scc-ai-integrity/" title="Academic Integrity Decision Tree" width="100%" height="1000" style="border:none;"></iframe>
```

Canvas doesn't resize iframes to fit their content, so the height is fixed. 1000px fits the longest step ("Have a conversation with the student," with the message template). Shorter steps will show some empty space below.

## Accessibility

- Tested with axe-core against WCAG 2.0, 2.1, and 2.2 A and AA, plus best practices, across every step type. No violations found.
- Fully keyboard operable. Focus moves to each new step's heading so screen readers announce where the user has landed.
- Reflows without horizontal scrolling at 320px wide (the 400% zoom test).
- Visible focus indicators, spoken error and status messages, respects reduced-motion settings.
- Links that open a new tab say so to screen readers.

Not yet tested with a real screen reader (NVDA, JAWS, or VoiceOver). That's worth a pass before wide release.

The report form PDF has tooltips on all 17 fields, tagged form fields in reading order, logical tab order, alt text on the logo, and a document title.

## Testing after changes

After editing the routing, click through each branch you changed, including Back and Start over. Every path should end at one of the five endpoints, and the summary should list the answers given.
