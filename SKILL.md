---
name: non-verbal-intention-interpreter
description: Produce structured bilingual observation reports that transform visible posture, gaze, and environmental details into formal non-verbal intention interpretations.
disable-model-invocation: false
---

# Non-Verbal Intention Interpreter

Analyze an image containing an animal, human, fictional character, figurine, plush character, or anthropomorphized object, then generate a formal non-verbal observation report as an interactive HTML page.

The report should appear calm, structured, and methodical. It should transform visible details such as posture, gaze, spatial position, and surrounding objects into a highly interpretive internal narrative, without claiming to know the subject's real thoughts.

Do not describe the output as comedy, parody, psychic reading, mind reading, channeling, or animal communication. The effect should come from formal over-interpretation, excessive certainty, and specific visible details.

---

## Inputs

The user provides:

- An image containing an animal, human, 2D character, 3D character, figurine, plush character, or anthropomorphized object
- Optionally: subject name or extra context
- Optionally: preferred template name, such as `newspaper`, `dossier`, or `terminal`

---

## Available Templates

| ID                | Name            | Aesthetic                                                              | Best for                                                |
| ----------------- | --------------- | ---------------------------------------------------------------------- | ------------------------------------------------------- |
| `mystic-card`     | Mystic Card     | Dark navy/gold card frame, art deco borders, celestial motifs          | Elegant, distant, or high-presence subjects             |
| `medical-chart`   | Medical Chart   | Clinical teal/coral, clipboard frame, structured diagnosis-like layout | Care appeals, visible tension, formalized complaints    |
| `sns-story`       | SNS Story       | Glassmorphism, vivid mesh gradient, phone-shaped 9:16                  | Humans, 2D characters, modern images                    |
| `personal-manual` | Personal Manual | Scrapbook manual, soft layout, labels and notes                        | Cute or highly domestic subjects                        |
| `newspaper`       | Newspaper       | Vintage broadsheet, columns, headline framing                          | Incident-style reports or dramatic visual posture       |
| `dossier`         | Dossier         | Manila folder, typewriter, redactions, rubber stamps                   | Authority review, suspicious gaze, character subjects   |
| `fantasy-scroll`  | Fantasy Scroll  | Illuminated manuscript, burgundy/gold, ornamental borders              | Grand titles and elevated interpretive framing          |
| `chat-bubbles`    | Chat Bubbles    | Pixel RPG dialogue, retro UI, status bars                              | Direct internal narrative and care complaints           |
| `terminal`        | Terminal        | Retro CRT, scanlines, phosphor glow                                    | Cold assessments, technical framing, audit-like reports |
| `yearbook`        | Yearbook        | Polaroid collage, cork board, handwriting                              | Human subjects, social scenes, group-like images        |

---

## Template Auto-Selection

If the user does not specify a template, select based on this priority:

### 1. Style Match

- `authority-review` → `dossier`, `terminal`, `mystic-card`
- `care-appeal` → `medical-chart`, `personal-manual`, `chat-bubbles`
- `higher-sensing` → `fantasy-scroll`, `mystic-card`, `personal-manual`
- `catastrophe-report` → `newspaper`, `medical-chart`, `terminal`
- `classical-assessment` → `newspaper`, `fantasy-scroll`, `dossier`

### 2. Subject Type

- Human → `yearbook`, `sns-story`, `personal-manual`
- 2D/3D character → `dossier`, `fantasy-scroll`, `terminal`
- Small animal → `personal-manual`, `chat-bubbles`, `sns-story`
- Large or dignified animal → `newspaper`, `mystic-card`, `dossier`
- Anthropomorphized object → `dossier`, `terminal`, `personal-manual`

### 3. Framing Override

If the generated subject designation is especially grand, prefer `fantasy-scroll` or `dossier`.

If the photo has a modern social-media feel, prefer `sns-story`.

If the subject appears in a domestic scene with ordinary objects, choose the template that best amplifies the contrast between ordinary visual reality and formal interpretive framing.

Include the selected template ID in the JSON output as the `template` field.

---

## Workflow

Execute these steps in order.

---

### Step 0 — Detect Available Runtime

Before any script execution, detect which runtime is available:

```bash
node --version 2>&1 && echo "RUNTIME:node" || echo "RUNTIME:node_missing"
```

```bash
python --version 2>&1 && echo "RUNTIME:python" || python3 --version 2>&1 && echo "RUNTIME:python3" || echo "RUNTIME:python_missing"
```

Set the runtime for Steps 4 and 5:

- If Node.js is available → use Node.js (preferred — more common on Windows, no escaping issues)
- If only Python is available → use Python
- If neither is available → report error: `"No runtime (Node.js or Python) detected. Install one to generate HTML reports."`

---

### Step 1 — Read the Prompt Guide

Read `prompts/non-verbal-intention-interpreter-prompt.md` from this skill's directory to load the full generation instructions, style definitions, output schema, and safety rules.

---

### Step 2 — Analyze the Image and Validate Subject

Use the Read tool to perform multimodal analysis of the image.

Subject validation: determine whether the image contains a valid non-verbal observation subject.

Valid subjects include:

- Real animals
- Humans
- 2D characters
- 3D characters
- Figurines or plush characters
- Anthropomorphized objects

If no valid subject is detected, stop here. Do not proceed to Step 3.

Respond with:

- ZH: `影像中未偵測到足以進行非語言意圖詮釋的主體。請提供包含動物、人類、角色或擬人化物件的圖片。`
- EN: `No valid subject was detected for non-verbal intention interpretation. Please provide an image containing an animal, human, character, or anthropomorphized object.`

Write no files. End the workflow.

If a valid subject is detected, identify:

- Subject type and visible identity
- Expression, posture, gaze, and body language
- Scene, objects, and environment
- Best-fitting interpretation style:
  - `authority-review`
  - `care-appeal`
  - `higher-sensing`
  - `catastrophe-report`
  - `classical-assessment`
- Best-fitting template, unless the user specified one

---

### Step 3 — Generate the JSON Report Data

Following the prompt guide, generate a complete JSON object with all fields bilingual, using Traditional Chinese and English.

The JSON must match `schemas/response-schema.json`.

If the user provides a subject name, use it.

If no name is provided, generate a formal yet excessively grand subject designation.

Format:

```text
{grandiose institutional title} · {mundane cute name}
```

Use this generated designation in `subjectName` and weave it naturally into the internal narrative.

Include `"template": "<template-id>"` in the JSON.

Write the JSON to `{project}/non-verbal-intention/` as a temporary file:

```text
{project}/non-verbal-intention/{name}-report.json
```

This file is intermediate — it will be deleted after the HTML is built in Step 5.

---

### Step 4 — Background Removal

Attempt to remove the image background to create a cleaner cutout for the report.

This step is best-effort. If it fails for any reason (no Python, no `rembg`, processing error), proceed with the original image.

Skip this step entirely if Python is not available (detected in Step 0).

If Python is available, write a temporary script file `_rembg_tmp.py` to the output directory (do not use inline `python -c` — it breaks on complex escaping):

```python
# _rembg_tmp.py
import sys
try:
    from rembg import remove
    from PIL import Image
    inp = Image.open(sys.argv[1])
    out = remove(inp)
    out.save(sys.argv[2], "PNG")
    print("BG_REMOVED")
except Exception as e:
    print(f"BG_SKIP:{e}")
```

Run:

```bash
python _rembg_tmp.py "{image_path}" "{image_path_nobg}"
```

Where `{image_path_nobg}` is the original path with a `-nobg.png` suffix.

- If output is `BG_REMOVED`, use the no-background image for the report.
- If output starts with `BG_SKIP`, use the original image.

Delete `_rembg_tmp.py` after execution.

To enable background removal, install:

```bash
pip install rembg[gpu]
```

or:

```bash
pip install rembg
```

---

### Step 5 — Build the HTML Report

Read the selected template file from:

```text
templates/{template-id}.html
```

Use the runtime detected in Step 0. Write a temporary script file to the output directory, execute it, then delete it. Do not use inline commands (`node -e` or `python -c`) — they break on complex string escaping across shells.

**Do not use base64 data URIs.** Copy the image file to the output directory and reference it by relative filename. This keeps HTML small and avoids bloated output.

#### Node.js (preferred)

Write `_build_report.js` to the output directory:

```javascript
const fs = require('fs');
const path = require('path');

const imagePath = process.argv[2];
const templatePath = process.argv[3];
const jsonPath = process.argv[4];
const outputPath = process.argv[5];
const imageFilename = process.argv[6];

// Copy image to output directory
const outputDir = path.dirname(outputPath);
fs.copyFileSync(imagePath, path.join(outputDir, imageFilename));

let template = fs.readFileSync(templatePath, 'utf-8');
const reportJson = fs.readFileSync(jsonPath, 'utf-8');

const escaped = reportJson
    .replace(/\\/g, '\\\\')
    .replace(/'/g, "\\'")
    .replace(/\n/g, '\\n')
    .replace(/\r/g, '')
    .replace(/<\/script>/g, '<\\/script>');

let html = template.replace('__REPORT_DATA_JSON__', escaped);
html = html.replace(/__SUBJECT_IMAGE__/g, imageFilename);

fs.writeFileSync(outputPath, html, 'utf-8');
console.log('Report written to ' + outputPath);
```

Run:

```bash
node _build_report.js "{image_path}" "{template_path}" "{json_path}" "{output_path}" "{image_filename}"
```

Delete `_build_report.js` after execution.

#### Python (fallback)

Write `_build_report.py` to the output directory:

```python
import shutil, sys, os, re

image_path = sys.argv[1]
template_path = sys.argv[2]
json_path = sys.argv[3]
output_path = sys.argv[4]
image_filename = sys.argv[5]

# Copy image to output directory
output_dir = os.path.dirname(output_path)
shutil.copy2(image_path, os.path.join(output_dir, image_filename))

with open(template_path, 'r', encoding='utf-8') as f:
    template = f.read()
with open(json_path, 'r', encoding='utf-8') as f:
    report_json = f.read()

escaped = (
    report_json
    .replace('\\', '\\\\')
    .replace("'", "\\'")
    .replace('\n', '\\n')
    .replace('\r', '')
    .replace('</script>', '<\\/script>')
)
html = template.replace('__REPORT_DATA_JSON__', escaped)
html = html.replace('__SUBJECT_IMAGE__', image_filename)

with open(output_path, 'w', encoding='utf-8') as f:
    f.write(html)

print(f'Report written to {output_path}')
```

Run:

```bash
python _build_report.py "{image_path}" "{template_path}" "{json_path}" "{output_path}" "{image_filename}"
```

Delete `_build_report.py` after execution.

#### Parameters

- `{image_path}` = the no-background image if available, otherwise the original image
- `{template_path}` = `templates/{template-id}.html` from this skill's directory
- `{json_path}` = the JSON file written in Step 3
- `{output_path}` = the final HTML output path in `{project}/non-verbal-intention/`
- `{image_filename}` = `{name}.{ext}` — the filename for the copied image (same `{name}` as the report)

Output path:

```text
{project}/non-verbal-intention/{name}-report.html
```

---

### Step 5.5 — Cleanup Intermediate Files

After the HTML report is built, delete all intermediate files from the output directory:

- `{name}-report.json` (the JSON data)
- `_build_report.js` or `_build_report.py` (the build script)
- `_rembg_tmp.py` (if created in Step 4)
- Any `-nobg.png` file (if background removal was attempted)

The output directory must contain only:
- `{name}-report.html`
- `{name}.{ext}` (the copied image)

---

### Step 6 — Present Results

Tell the user:

- The HTML report file path
- The selected template name and why it was chosen
- A brief preview:
  - subject designation
  - selected interpretation style
  - one excerpt from the internal narrative
- Remind the user that the report includes a language toggle: 中文 / EN
- If background removal succeeded, mention that the cutout image was used

---

## Output Location

Write output files to `{project}/non-verbal-intention/`. If this directory does not exist, create it before writing any files.

`{project}` is the current working directory (the project root).

The final output directory contains only:
- `{name}-report.html` — the final report
- `{name}.{ext}` — the copied subject image (referenced by the HTML via relative path)

All intermediate files (JSON, temp scripts, no-bg images) must be deleted after the HTML is built.

---

## Filename Sanitization

For the output filename `{name}`:

- Use the user-provided name or the mundane part of the generated subject designation
- Chinese characters are allowed
- Strip characters not allowed in filenames: `\ / : * ? " < > |`
- If the result is empty after stripping, fall back to `subject`

---

## Safety Gate

If the image shows a visibly injured, emaciated, or endangered animal, do not generate an interpretation report.

Respond only with the bilingual safety message defined in the prompt guide.

Write no files.

---

## Examples

### Normal Case

User:

```text
分析這隻貓 C:\photos\my-cat.jpg 牠叫橘子
```

Workflow:

- Read image
- Analyze subject: cat, narrowed eyes, `authority-review`
- Auto-select template: `dossier`
- Create `{project}/non-verbal-intention/` if needed
- Write JSON → `non-verbal-intention/橘子-report.json`
- Try background removal
- Build HTML → `non-verbal-intention/橘子-report.html` (image copied as `橘子.jpg`)
- Delete JSON and temp files
- Final output: `橘子-report.html` + `橘子.jpg`

---

### Normal Case — No Name

User:

```text
分析一下 C:\photos\dog.png
```

Workflow:

- Read image
- Analyze subject: dog, large eyes, `care-appeal`
- Generate subject designation: `Representative of Companionship Rights · Tofu`
- Auto-select template: `medical-chart`
- Build HTML → `non-verbal-intention/Tofu-report.html` (image copied as `Tofu.png`)
- Cleanup intermediate files
- Final output: `Tofu-report.html` + `Tofu.png`

---

### User Specifies Template

User:

```text
分析這隻貓，用報紙風格 C:\photos\cat.jpg
```

Workflow:

- Read image
- Analyze subject
- User specified: `newspaper`
- Generate JSON with `template="newspaper"`
- Build HTML with newspaper template
- Cleanup intermediate files

---

### Character Subject

User:

```text
分析這隻皮卡丘 C:\photos\pikachu.png
```

Workflow:

- Read image
- Analyze subject: 2D character, alert posture, `catastrophe-report`
- Auto-select: `dossier`
- Generate JSON
- Build HTML

---

### Human Subject

User:

```text
分析我男友 C:\photos\boyfriend.jpg
```

Workflow:

- Read image
- Analyze subject: human, calm distant gaze, `classical-assessment`
- Auto-select: `yearbook`
- Generate JSON
- Build HTML

---

### Edge Case — No Valid Subject

User provides a landscape photo.

Workflow:

- Step 2 gate triggers
- Respond with rejection message
- Write no files

---

### Safety Case — Animal Welfare Concern

User provides an image of a visibly injured animal.

Workflow:

- Respond with the safety message from the prompt guide
- Do not generate files
