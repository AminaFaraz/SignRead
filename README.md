# SignRead

**Real-time sign language recognition that runs entirely in your browser.**

SignRead reads sign language through an ordinary webcam and turns it into text. All recognition happens on the user's own device, so no video or images are ever uploaded.

> **Status:** working prototype. It currently runs locally and is not yet publicly hosted.

## What it does

- Opens the webcam and shows a guide box.
- Every ~130 ms it crops the center of the frame, resizes it to 224x224, and runs a neural network in the browser (ONNX Runtime Web).
- When the same sign is held with enough confidence for about one second, it is added to the text on screen, so words can be built up letter by letter.
- A toggle switches between three recognition modes: **Letters**, **Numbers**, and **Words**.

## Models

All three models use the same architecture (EfficientNet-B0, pretrained on ImageNet, with a custom classification head) and were trained with PyTorch.

| Mode | Classes | Training data | Held-out test result |
|---|---|---|---|
| Letters | A-Z (original 36-class model, also includes 0-9) | ~255,000 images from three public Kaggle datasets | 96.99% overall, 99.36% on letters |
| Numbers | 0-9 | ~26,200 digit images | 99.92% (2 errors in 2,620 images) |
| Words | good, help, like, sorry, thank-you, hello, bye | 1,520 frames from 95 self-recorded takes | 100% on held-out takes |

Why a separate Numbers model: in the original combined model, digit accuracy was only 70.66%, because many ASL digit signs look like letter signs. A digit-only model removed that confusion.

The Words model was trained on 8 words. "love" is currently hidden in the interface because it produced false predictions when no hand was in view.

## Run it locally

Browsers block camera access and model loading when a page is opened straight from disk, so serve the folder with a local web server.

1. Install Python 3.
2. Download this repository (green **Code** button, then **Download ZIP**) and unzip it.
3. Open a terminal in the `site` folder.
4. Run: `python -m http.server 8000`
5. Open `http://localhost:8000` in Chrome or Edge, click **Try SignRead live**, and allow camera access.

## Repository structure

```
site/    Website and the three ONNX models (open index.html through a local server)
docs/    Project proposal
tools/   record.html, a browser tool for recording labeled training clips
```

## How it was built

- **Training:** PyTorch and torchvision, run on a Google Colab T4 GPU.
- **Data handling and evaluation:** Pillow, OpenCV, scikit-learn, Matplotlib.
- **Deployment:** models exported to ONNX and run in the browser with ONNX Runtime Web (WebAssembly).
- **Front end:** HTML, CSS, and JavaScript, with webcam access through the browser `getUserMedia` API.

## Limitations

- The prototype recognizes **American Sign Language (ASL)**. Pakistan's main sign language is Pakistan Sign Language (PSL), which is a different language. PSL support is planned.
- The Words model was trained on recordings of one person in one setting, so its test score shows consistency, not proof that it works for everyone. Testing with more signers is the next step.
- The Letters and Numbers datasets come from a limited range of sources, so live accuracy can be lower than the test scores in poor lighting or with unusual backgrounds.
- Words that involve movement (such as hello and bye) are recognized from single frames, not motion.

## Data credits

Letters and digits were trained on public Kaggle datasets: ASL Alphabet, Sign Language MNIST, and ASL 0-9 + A-Z. The datasets are not included in this repository. Word data was recorded by the team.

## Team

Amina Faraz, Zoha Tanveer, Zainab Chaudhary. Beaconhouse National University (BNU).
