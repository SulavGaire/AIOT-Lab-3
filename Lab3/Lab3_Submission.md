# Lab 3 Submission Sheet

**ECE 4930/6930 – Artificial Intelligence of Things**

**TinyML with XIAO ESP32-S3**

Complete every applicable field. Use “N/A” only with a reason. Attach evidence or provide links that course staff can open. This template accompanies [Lab3_Instruction.md](Lab3_Instruction.md).

## 1 — Student and project

- Name / student ID:
- Course / section: ECE 4930/6930 /
- Submission date:
- Project title:
- Route: custom image / custom audio / custom detection / approved pretrained baseline
- Application and operating conditions:
- Model input and output:
- Edge Impulse project URL and export version, or pretrained model URL and version:
- Adapted example URL and changes made:

## 2 — Hardware and software

| Item | Your configuration |
| --- | --- |
| Board and sensor/camera model | |
| Arduino IDE version | |
| ESP32 board package version | |
| Board selection and port | |
| PSRAM option | |
| Flash size and partition scheme | |
| USB CDC setting and serial baud rate | |
| Exported library name/version | |
| Model precision and optimization setting | |
| Image size/color format or audio sample rate/window | |

## 3 — Data and model

For a pretrained route, identify the model and mark unavailable training fields with a reason. Report your own live evaluation separately.

| Class | Training examples or duration | Test examples or duration | Data source |
| --- | --- | --- | --- |
| | | | |
| | | | |
| | | | |

- How did you keep capture sessions separate across the data split?
- Processing block / learning block / important training settings:
- Deployed model variant and evidence of quantization:
- Test metric name, value, test-set size, and thresholds:
- One example prediction: actual label, predicted label, confidence, and evidence filename:
- Failure case and likely cause:

## 4 — Performance results

Report Flash and RAM separately. Keep estimates, whole-firmware use, and hardware measurements distinct. Preserve the units shown in the source.

| Metric | Result and units | Source or evidence filename |
| --- | --- | --- |
| Held-out test accuracy, or named detection metric | | |
| Test sample count / class balance | | |
| Edge Impulse estimated latency and target | | |
| Board DSP time, mean | | |
| Board neural-network time, mean | | |
| Board anomaly time, if used | | |
| Board latency for stated scope: mean / min / max | | |
| Timing run count | | |
| Edge Impulse estimated Flash | | |
| Edge Impulse estimated RAM | | |
| Arduino program storage: used / limit | | |
| Arduino static RAM: used / limit | | |
| Runtime heap / PSRAM observation, optional | | |
| Live quality metric and trial count | | |

- Timing scope and timer resolution:
- Warm-up handling and test conditions:
- For live classification: correct trials / total trials; for detection: metric and matching rule:
- Resource limit or optimization considered; effect if measured:
- Explain any unavailable values or baseline substitutions:

## 5 — Evidence and reproducibility

| ID | Evidence | Filename or accessible link |
| --- | --- | --- |
| E1 | Board/port selection and PSRAM setting | |
| E2 | Dataset split, test results, and example prediction; baseline uses model identity and live trial records | |
| E3 | Deployment estimates with target and variant; explain unavailable baseline values | |
| E4 | Successful build and upload; approved browser route uses deployment confirmation | |
| E5 | Live board inference screenshot/video and readable timing output | |
| E6 | Final sketch, dependency versions, and reproduction instructions | |

Suggested package contents: completed sheet; `src/` for your sketch; `evidence/` for captures and logs; `results/` for timing and trial records. Include the exported library if course submission limits permit, or identify an accessible export and exact version. Do not assume a project link alone reproduces that export.

## 6 — Discussion and final check

- What worked, and under what conditions?
- What failed, and what would you change next?
- How did accuracy, latency, and memory affect your design?

- [ ] The submitted model version matches the demonstrated version.
- [ ] The demo uses the board's live sensor and shows on-device inference.
- [ ] Estimates and measurements are labeled correctly.
- [ ] Every evidence link opens for course staff.
- [ ] Adapted sources and contributions are identified.
- [ ] Shared files contain no API keys or Wi-Fi credentials.

Grading weights, due date, and submission location follow the course announcement.
