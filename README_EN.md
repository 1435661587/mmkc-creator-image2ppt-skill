# mmkc-creator-image2ppt-skill
> MMKC project description: Rebuild images, screenshots, HTML, or SVG designs into editable PPTX files with text boxes, native shapes, and component-style layout recovery.
> MMKC 项目描述：把图片、截图、HTML 或 SVG 设计稿重建为可编辑 PPTX，支持文本框、原生形状和组件化版式还原。


[中文](./README.md) | English

`mmkc-creator-image2ppt-skill` is a Codex skill for converting images, screenshots, HTML, or SVG designs into editable PowerPoint `.pptx` files. It is useful for rebuilding slide screenshots, turning infographics into slides, converting AI-generated visuals into editable decks, compiling HTML/SVG slide-like pages into PPTX, and reconstructing flat designs as image components plus text boxes plus native PowerPoint shapes.

The skill is built around a structured `manifest.json` intermediate representation and a pure-Python PPTX builder. For bitmap inputs, it is designed to work with Codex `imagegen` by default: Codex first understands the slide layout, uses built-in image generation/editing to create or clean component assets, then the deterministic scripts assemble the final PPTX.

## What It Can Do

- Rebuild PNG/JPEG/WebP bitmap slides into editable PPTX files.
- Convert titles, body text, labels, and formulas into editable PowerPoint text boxes.
- Convert rectangles, rounded rectangles, circles, lines, and arrows into native PowerPoint shapes.
- Keep photos, icons, complex illustrations, textures, and complex charts as separate image components.
- Parse simple HTML/SVG text, images, and basic shapes into a manifest before generating PPTX.
- Store each run under `projects/YYYYMMDD_slug/` with source files, components, imagegen assets, manifest, PPTX, summary, and diagnostics.

## Install

Copy this directory into your Codex skills directory:

```bash
mkdir -p ~/.codex/skills
cp -R mmkc-creator-image2ppt-skill ~/.codex/skills/
```

Or clone the full this repository and symlink the skill:

```bash
git clone <your-github-url>/mmkc-creator-image2ppt-skill.git
mkdir -p ~/.codex/skills
ln -s "$PWD/mmkc-creator-image2ppt-skill" ~/.codex/skills/mmkc-creator-image2ppt-skill
```

Install runtime dependencies:

```bash
python3 -m pip install -r ~/.codex/skills/mmkc-creator-image2ppt-skill/scripts/requirements.txt
```

The main dependencies are `python-pptx`, `Pillow`, `beautifulsoup4`, and `lxml`.

## Use In Codex

Example prompt:

```text
Use mmkc-creator-image2ppt-skill to convert this slide image into a real pptx file.
Make text editable where possible, recreate shapes as native PowerPoint shapes,
and use imagegen for complex background or icon components when needed.
```

HTML/SVG input:

```text
Use mmkc-creator-image2ppt-skill to convert this HTML/SVG page into an editable PPTX.
Prefer native PPT text, rectangles, circles, and lines.
```

## CLI Usage

Initialize a project:

```bash
python3 mmkc-creator-image2ppt-skill/scripts/init_project.py cross_border_formula \
  --source input.png
```

Build PPTX from a manifest:

```bash
python3 mmkc-creator-image2ppt-skill/scripts/image2pptx.py build \
  --manifest mmkc-creator-image2ppt-skill/projects/20260505_cross_border_formula/manifest.json \
  --output mmkc-creator-image2ppt-skill/projects/20260505_cross_border_formula/output.pptx \
  --summary mmkc-creator-image2ppt-skill/projects/20260505_cross_border_formula/summary.json
```

Convert SVG or HTML to a manifest:

```bash
python3 mmkc-creator-image2ppt-skill/scripts/html_svg_to_manifest.py input.svg \
  --output mmkc-creator-image2ppt-skill/projects/20260505_demo/manifest.json

python3 mmkc-creator-image2ppt-skill/scripts/html_svg_to_manifest.py input.html \
  --output mmkc-creator-image2ppt-skill/projects/20260505_demo/manifest.json
```

## Manifest Example

Elements are ordered from back to front.

```json
{
  "deck": {
    "name": "Example Deck",
    "canvas_width": 1600,
    "canvas_height": 900,
    "slide_width_in": 13.333
  },
  "slides": [
    {
      "name": "Cover",
      "elements": [
        {
          "kind": "background",
          "fill": "#f7f4ec"
        },
        {
          "kind": "shape",
          "shape": "roundRect",
          "x": 120,
          "y": 160,
          "w": 620,
          "h": 240,
          "fill": "#ffffff",
          "stroke": "#ffffff"
        },
        {
          "kind": "text",
          "text": "Image to Editable PPTX",
          "x": 160,
          "y": 210,
          "w": 760,
          "h": 110,
          "font_size_px": 64,
          "font_family": "Arial",
          "bold": true,
          "color": "#17120d"
        },
        {
          "kind": "image",
          "file": "component_images/icon.png",
          "x": 1120,
          "y": 260,
          "w": 180,
          "h": 180,
          "fit": "contain"
        }
      ]
    }
  ]
}
```

## Project Output Structure

Each real run lives under:

```text
mmkc-creator-image2ppt-skill/projects/YYYYMMDD_slug/
├── original_inputs/
├── component_images/
├── imagegen_assets/
├── diagnostics/
├── exports/
├── manifest.json
├── output.pptx
├── summary.json
└── process_notes.md
```

Runtime outputs are ignored by Git. The open-source repository keeps only `projects/.gitkeep`.

## Notes

- Rebuilding an editable deck from a single bitmap is an approximate reconstruction, not lossless decompilation.
- For bitmap inputs, Codex handles visual analysis and calls `imagegen` to generate or clean components; the scripts themselves do not call model APIs.
- The HTML/SVG parser covers common absolutely positioned layouts and basic SVG nodes. Complex paths, filters, gradients, masks, and clip paths should usually become image-component fallbacks.
- `python-pptx` does not render previews directly. Inspect results in PowerPoint, Keynote, LibreOffice, or macOS Quick Look.

## License

MIT
