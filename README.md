 # PenCAM

**Single-file browser CAM + sender for pen plotting on a CNC 3018 (GRBL). Now with multi-color drawing using a 8-color pen.**

---

## What is it?

PenCAM turns a hobby CNC router (e.g. 3018 with GRBL) into a pen plotter. Open the website (Deployments => Last deployments) load an image, generate G-code and send it to the machine over Web Serial. No server, no dependencies, no installation.

## Tabs

| Tab              | What it does                                                                                                                                   |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| ✦ **Pen editor** | Single-pen mode: grayscale → edge detection (threshold + hysteresis) → lines → G-code                                                          |
| 🖍 **Color pen** | Multi-color mode with a 8-color pen (see below)                                                                                                |
| ⇄ **Converter**  | Convert existing laser G-code to G-code for pen drawing                                                                                        |
| ⌖ **Machine**    | Connection, jogging, placement on the work area, sending, per-layer job control, click to zone & machine moves there (all same as other CAM's) |

## Color pen mode

The idea: inks on paper don't mix like printer dots, so instead of blending colors, PenCAM **picks one pen per area** and conveys tone with **hatch density**.

**Pipeline**

1. RGB → **CIELab** (sRGB gamma aware)
2. Lightness / contrast / saturation correction in Lab (saturation adjusted on a/b, independent of L)
3. Noise smoothing
4. Nearest pen per pixel by **ΔE** (perceptual color distance)
5. Mode filter + merging of tiny regions into neighbors
6. Transparent PNG backgrounds are handled via the alpha channel, so a transparent background is never confused with real black in the drawing
7. Per-color layers → fill hatching (default step 0.3 mm) + contour outlines
8. G-code per layer with a pause (`M0`) before each color & notifications on screen.

**Features**

- **Solid fill + outlines**: configurable number of concentric outlines, outline inset by half the pen width (pen width setting, default 0.7 mm but best to set 0.) so neighboring colors don't overlap
- **Brightness hatching** (optional): darker areas of a color get more ink, either by **cross-hatching** or by **denser parallel lines**
- **Path optimization**: nearest-neighbor stroke ordering, continuous hatching without lifting the pen, and pen-up on any meaningful gap (threshold tied to the hatch step)
- **Skips colors** that don't appear in the image
- **Drag & drop layer order** with per-layer distance and time estimates
- **Preview modes**: quantized image, pen path in real colors, interactive 3D **Lab sphere** showing your pens inside the color space
- **Color-change banner**: when GRBL enters `Hold` on `M0`, a full-screen prompt shows which pen to switch to; press *Continue* to resume
- **Run each color separately** from the Machine tab. The whole colored image is shown on the work area, and each layer gets a status: *Waiting / Sending / Done / Stopped*
- Placement preview matches exactly what is sent to the machine (offset and scale)

## Usage

1. Go to Deployments => Last deployments and open the website
2. Go to **Color pen**, load an image (drag & drop, `Ctrl+V` or click).
3. Calibrate the palette with the eyedropper (not really necessary) if needed, tune the settings.
4. In **Machine**, connect to the controller, place the drawing on the work area and send the whole job or single color layers.
<img width="347" height="68" alt="image" src="https://github.com/user-attachments/assets/fe61d922-4cc8-4c0d-a5a5-e545cba718ef" />

5. Generate G-code and check the **Pen path** preview.
<img width="284" height="86" alt="image" src="https://github.com/user-attachments/assets/984f57d7-c0c7-4963-87d3-08fa48753e54" />

6. When the banner appears, switch the pen color and press *Continue*.

## Requirements

- CNC with GRBL (tested on 3018)
- A pen holder with spring / lift, a multi-color click pen (8 colors in my setup)
- A browser with Web Serial support (Chrome / Edge)

## Notes

- Without white and mixing, results look like a posterized, linocut-style print rather than a photo. That's expected for this technique.
- Calibrate colors on the paper you'll actually use: real ink is darker and duller than its nominal color.
- <img width="1063" height="815" alt="image" src="https://github.com/user-attachments/assets/8467cc7b-9ba0-41b3-b138-9063285ff873" />


## License

Check License.txt
