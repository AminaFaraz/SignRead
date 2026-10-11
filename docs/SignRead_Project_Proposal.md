# SignRead: Project Proposal

**Real-time sign language recognition that runs entirely in the browser**

| | |
|---|---|
| **Team** | Amina Faraz, Zoha Tanveer, Zainab Chaudhary |
| **Institution** | Beaconhouse National University (BNU), SCIT, BSc (Hons) Software Engineering |
| **Contact** | f2024-1104@bnu.edu.pk |
| **Domain** | Assistive technology / accessibility, with an education use case |
| **Current status** | Working prototype (runs locally, not yet publicly hosted) |

---

## 1. Project Overview & Problem Statement

Pakistan is home to an estimated **10 million Deaf and hard-of-hearing people** (a figure commonly cited from UN estimates by advocacy organizations). Communication barriers keep many of them out of everyday life: reports indicate that **fewer than 5% of deaf children attend school**, largely because teachers, classmates, and families cannot communicate with them, not because of any lack of ability.

The gap is practical and daily. Sign language interpreters are scarce, and very few everyday digital tools understand sign language at all. Where tools do exist, they often require specialized hardware, a separate app installation, or uploading video of the user to a remote server, which adds cost, friction, and privacy concerns.

**Problem statement:** There is no simple, free, private way for a hearing person to understand basic sign language using only a device they already own.

---

## 2. Proposed Solution

**SignRead** is a web-based tool that reads sign language through an ordinary webcam and converts it into text in real time. The entire recognition pipeline runs **inside the user's browser**. No app install, no account, and no video ever leaves the device.

### How it works

1. The browser opens the webcam and shows a guide box.
2. The user holds a sign steady inside the box.
3. Every ~130 ms, the center region of the frame is cropped, resized to 224x224, and normalized.
4. A trained neural network (running locally via ONNX Runtime Web) predicts the sign and a confidence score.
5. If the same sign is held with enough confidence for about one second, it is added to the text on screen, so words and short phrases can be built up.

### Three specialist models

Instead of one model trying to recognize everything, SignRead uses separate models selected by a toggle in the interface (**Letters / Numbers / Words**). All share the same architecture (EfficientNet-B0, pretrained on ImageNet, fine-tuned with a custom classification head), but are trained on different data.

| Mode | Model | Classes | Training data | Held-out test result |
|---|---|---|---|---|
| Letters | Original combined model (36 classes) | A-Z (and 0-9) | ~255,000 images from three public Kaggle datasets | 96.99% overall, **99.36% on letters** |
| Numbers | Dedicated digit model | 0-9 | ~26,200 digit images | 99.92% (2 errors in 2,620 images) |
| Words | Dedicated word model | good, help, like, sorry, thank-you, hello, bye (7 shown in the interface) | 1,520 frames from 95 self-recorded takes | 100% on held-out takes (one signer; see Section 8) |

**Why specialists:** In the first combined model, digit accuracy was only 70.66% because many ASL digit signs resemble letter signs (for example, 1 and D, or 2 and V). Training a digit-only model removed that confusion and raised digit accuracy to 99.9% on its test set.

---

## 3. Objectives & Expected Outcomes

### Objectives

1. Provide a free, private, install-free tool that recognizes sign language letters, numbers, and everyday words in real time.
2. Demonstrate that accurate sign recognition can run fully on-device, without a server.
3. Grow from single-sign recognition toward word, sentence, and two-way communication.
4. Extend support from ASL to Pakistan Sign Language (PSL) and other regional sign languages.

### Expected outcomes

- **Achieved so far:** a working three-mode prototype, three trained and evaluated models, a project website, and a presentation.
- **Near term:** publicly hosted demo; a vocabulary of about 100 everyday words; accuracy validated across multiple signers, backgrounds, and lighting conditions (target: 90% or higher in live testing).
- **Medium term:** sentence construction, text-to-speech output, and speech/text-to-sign output for two-way conversation.
- **Long term:** mobile app, PSL support, and adoption pilots in schools and community organizations.

---

## 4. Target Users

**Primary**
- Deaf and hard-of-hearing students and individuals who sign.
- Teachers, classmates, and family members who do not sign and want to communicate with them.
- Sign language learners and beginners practicing basic vocabulary.

**Secondary**
- Schools, special-education centers, and NGOs working with the Deaf community.
- Front-desk and public-service staff (clinics, banks, government offices) who occasionally need to communicate with Deaf visitors.

---

## 5. Key Features / Deliverables

**Delivered**
- Live webcam recognition with confidence display and top-3 predictions.
- Three recognition modes (Letters, Numbers, Words) with live model switching.
- Word builder: holds a sign for ~1 second to commit it to text, with space, delete, and clear controls.
- 100% client-side inference (ONNX Runtime Web / WebAssembly); no uploads.
- Project website (landing page plus live tool) and a presentation.
- A custom browser-based recording tool for collecting consistent training data.

**Planned**
- Public hosting.
- Expanded word vocabulary (about 100 words).
- Sentence construction and text-to-speech.
- Speech/text to sign output (two-way communication).
- Mobile app.
- Pakistan Sign Language and additional languages.

---

## 6. Implementation Plan & Timeline

The first phase is complete. The remaining phases are a proposed timeline and can be adjusted with mentor input.

| Phase | Timeline | Work | Status |
|---|---|---|---|
| **0. Prototype** | Completed | Data collection and preprocessing; training of letters, numbers, and words models in PyTorch; evaluation; ONNX export; browser integration; website and presentation | Done |
| **1. Harden and publish** | Months 1-2 | Deploy publicly on static hosting; collect data from at least 10 different signers; add a "no sign / background" class to reduce false triggers; re-evaluate accuracy on unseen people | Planned |
| **2. Expand vocabulary** | Months 2-4 | Grow to about 100 everyday words; add handling for motion-based signs (clip-based or hand-landmark approach); improve robustness to lighting and backgrounds | Planned |
| **3. Sentences and speech** | Months 4-6 | Assemble recognized words into sentences; add text-to-speech and speech-to-text | Planned |
| **4. Two-way and mobile** | Months 6-9 | Speech/text to sign output; mobile app (Android first) | Planned |
| **5. PSL and pilots** | Months 9-12 | Pakistan Sign Language support; pilot with schools and Deaf community organizations; usability study and feedback loop | Planned |

---

## 7. Resources or Requirements

- **Compute:** GPU access for training larger and more varied models (currently using a free Colab T4 GPU; paid or cloud GPU credits would speed up iteration).
- **Hosting:** Because inference is client-side, simple static hosting is sufficient and low-cost.
- **Data and people:** Volunteer signers, ideally including Deaf community members, with informed consent for any recorded data.
- **Expertise:** Mentorship from AI/ML and startup advisors, and guidance from qualified PSL interpreters or educators to validate vocabulary and signs.
- **Devices:** Android and iOS test devices and a range of webcams and laptops for performance testing.
- **Partnerships:** Schools and Deaf community organizations for real-world testing and feedback.

---

## 8. Potential Challenges or Risks

Every new project has challenges. These are the main ones we see, explained simply, along with what we plan to do about each.

**1. Working well for everyone.** So far we have tested mostly with our own hands and our own cameras. For example, our word model learned from recordings of one person. Different people sign a little differently, and rooms have different lighting and backgrounds. *Our plan:* test with many more people in different places, and keep improving the models with what we learn.

**2. Signs that involve movement.** Some words, like "hello" and "bye," are signed with a moving hand, but our current system looks at one picture at a time. *Our plan:* teach the system to look at a short sequence of pictures so it can understand movement.

**3. Wrong guesses when no one is signing.** Sometimes the system guesses a word even when there is no hand in view. We have reduced this with a confidence setting and by hiding the one word that triggered it too often. *Our plan:* add a "no sign" option so the system stays quiet when nothing is being signed.

**4. Signs that look alike.** A few signs look similar, and the system can occasionally mix them up. *Our plan:* collect more varied examples of those signs and check the accuracy of each sign separately.

**5. Different sign languages.** Our prototype uses American Sign Language (ASL), because that is where large public training data exists. Pakistan's main sign language is Pakistan Sign Language (PSL), which is a different language. *Our plan:* work with PSL signers and interpreters to add PSL support in a later phase.

**6. A small word list.** For now the system knows letters, numbers, and a few everyday words. *Our plan:* grow the word list step by step, checking accuracy at each step.

**7. Slower devices.** Older phones and laptops may run the tool slowly. *Our plan:* test on a range of devices and use lighter versions of the models if needed.

**8. A helper, not a replacement.** SignRead is meant for basic everyday communication, not to replace professional interpreters. *Our plan:* involve the Deaf community in the design and feedback so the tool serves their real needs.

---

## 9. Additional Details

### Technology stack

- **Model training:** PyTorch, torchvision (EfficientNet-B0, ImageNet-pretrained), trained on a Google Colab T4 GPU.
- **Data handling and evaluation:** Pillow, OpenCV, scikit-learn, Matplotlib.
- **Deployment format:** ONNX, run in the browser with ONNX Runtime Web (WebAssembly).
- **Front end:** HTML, CSS, JavaScript; webcam access via the browser `getUserMedia` API.

### Data sources

- **Letters and digits:** three public Kaggle datasets (ASL Alphabet, Sign Language MNIST, ASL 0-9 + A-Z).
- **Words:** frames recorded by the team using a custom browser recording tool (16 frames per ~1.6-second take), after public word datasets proved too small or too inconsistent for reliable training.

### Privacy

All processing happens on the user's device. No images, video, or predictions are uploaded or stored.

### A note on evaluation

All accuracy figures above are measured on held-out test data that the models did not see during training. We report them with their limits: the digit and word datasets come from a narrow range of sources or a single signer, so live testing and multi-signer evaluation (Phase 1) are the real validation step.

### Running the prototype

The prototype currently runs locally: unzip the project folder, start a local server with `python -m http.server 8000`, and open `http://localhost:8000` in a browser.

### Vision

SignRead starts with letters, numbers, and everyday words, and aims to become a two-way communication aid between Deaf and hearing people, available on web and mobile, and supporting multiple sign languages including Pakistan Sign Language.
