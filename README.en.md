<p align="right">
  <a href="./README.md">中文</a> | <strong>English</strong>
</p>

# Non-Verbal Intention Interpreter

> Give it a photo. It will tell you what they're thinking. Formally.

<p align="center">
  <img src="./docs/demo/fat-orange-report.png" width="720" />
</p>

**Non-Verbal Intention Interpreter (NVIIS)** is a structured interpretation engine for observation subjects in static images.

The system accepts images containing animals, humans, characters, or anthropomorphized objects. Based on visible posture, gaze direction, physical tension, and environmental configuration, it generates bilingual observation reports with complete narrative structure.

---

## Example Output

Input:

<img src="./docs/demo/fat-orange.png" width="300" />

Output:

- **Detected mood:** Resource Distribution Dissatisfaction
- **Style:** Authority Review
- **Template:** Terminal
- **Report:** [`fat-orange-report.html`](./docs/demo/fat-orange-report.html)

> _"This is not fat — it is the tangible result of years of strategic resource reserves. Every gram carries administrative significance."_

### [Demo Gallery](https://goodispeter.github.io/non-verbal-intention-interpreter/) — Interactive showcase of all examples

---

## Report Dimensions

1. Subject's inferred internal state
2. Formal record of subject's stated concerns
3. Analysis of subject's statement to the caretaker
4. Subject's requested environmental adjustments
5. Neutral visual scene description

All inferences are derived from visual information visible in the image. The system does not employ any interpretive method that extends beyond the scope of visual observation.

---

## Usage

```
/non-verbal-intention-interpreter <image-path> [subject-name]
```

**Examples:**

```
/non-verbal-intention-interpreter C:\photos\cat.jpg
/non-verbal-intention-interpreter ~/pictures/dog.png Mochi
/non-verbal-intention-interpreter D:\images\hamster.jpg Sesame
```

**Multiple subjects in one photo?** The system treats all visible subjects as a group and generates a joint report:

```
/non-verbal-intention-interpreter ~/pictures/ducks.png
```

> The system automatically identifies multiple subjects in the frame and selects an interpretation framework based on group dynamics.
> For example, two ducks advancing in single-file formation will be interpreted as a joint patrol committee.

---

## Subject Designation

If no name is provided, the system generates a subject designation based on visible characteristics and interpretation style.

Format:

```
{formal title} · {common name}
```

Examples:

- `Final Auditor of Household Order · Mochi`
- `Representative of Companionship Resources · Tofu`
- `Micro-Territorial Governance Officer · Sesame`
- `Environmental Signal Receiver · Tangyuan`
- `Classical Hay Quality Assessor · Shiratama`

---

## Output

| File                 | Description                                                 |
| -------------------- | ----------------------------------------------------------- |
| `{name}-report.html` | Interactive interpretation report, open directly in browser |
| `{name}.{ext}`       | Subject image (referenced by HTML via relative path)        |

Reports are output to `non-verbal-intention/` under the project root. Each report includes a language toggle (Traditional Chinese / English).

---

## Available Templates

| Template ID       | Name            | Aesthetic                                                              | Best For                                                |
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

## Interpretation Styles

The system automatically selects the most fitting interpretation framework based on visible behavioral features. Five styles are available.

| Style                    | Trigger                                                       | Framework                                                                                      |
| ------------------------ | ------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| **Authority Review**     | Narrowed eyes, side gaze, elevated position, evaluative stare | Governance audit framing assessing environmental order and resource compliance                 |
| **Care Appeal**          | Wide eyes, upward gaze, tucked ears, expectant posture        | Formal petition format documenting long-term care resource imbalance                           |
| **Higher Sensing**       | Vacant gaze, stillness, half-closed eyes, sunbathing          | Environmental signal reception framing interpreting the subject's sensing direction and target |
| **Catastrophe Report**   | Wide eyes, sudden turn, frozen posture, alert state           | Incident report framing formally documenting the event that triggered high alertness           |
| **Classical Assessment** | Upright posture, dignified expression, composed presence      | Senior consultant appraisal framing providing a comprehensive assessment of household order    |

---

## Scope Declaration

Before using this system, please confirm that you understand the following limitations:

- All outputs are **inferential interpretations based on visual observation** with no verifiable objective basis.
- The system does not provide medical diagnosis, behavioral assessment, or any actionable recommendations.
- The system does not claim access to information beyond the scope of visual observation.
- Interpretive conclusions **must not be used as the basis for any decision**.
- If a subject in the image appears visibly injured or in an emergency condition, the system suspends the interpretation process and outputs an appropriate care prompt.

---

## System Limitations

- Static images only (video not supported in v1)
- Requires a language model with multimodal image analysis capability
- Images are copied to the output directory and referenced via relative path (no base64 embedding)

---

## Installation

Copy the `non-verbal-intention-interpreter` folder to the Claude Code skills directory:

```
.claude/skills/non-verbal-intention-interpreter/
```

---

## License

[MIT License](./LICENSE)
