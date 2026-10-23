# Lab 3: On-Device AI Deployment on XIAO ESP32-S3

**ECE 4930/6930 – Artificial Intelligence of Things**

> [!NOTE]
> **Platform:** Seeed Studio XIAO ESP32S3 Sense  
> **Software:** Edge Impulse + Arduino IDE (or SenseCraft for the baseline)  
> **Preparation:** Collect training data before class if you choose a custom model.

---

## Overview

In Labs 1–2, you ran AI on the Jetson Nano. In Lab 3, you will deploy AI on a much smaller microcontroller: the **XIAO ESP32S3 Sense**.

Choose **one** path:

| Path | What you will do |
|---|---|
| **A — Custom model (recommended)** | Train an image classification, object detection, or audio model in [Edge Impulse](https://edgeimpulse.com/) and run it on the XIAO. |
| **B — Gesture baseline** | Deploy a [pretrained SenseCraft gesture model](https://sensecraft.seeed.cc/ai/view-model/60111-gesture-detection?tab=public); no model training is required. |

```text
Choose application → Prepare model → Deploy to XIAO → Test → Report results
```

## Learning Objectives

- Deploy a small AI model to a microcontroller.
- Test predictions and measure inference performance.
- Identify the effects of limited memory and computing resources.

---

# Part 0 — Hardware Setup

1. Connect the XIAO ESP32S3 Sense and camera using a **USB data cable**.
2. In Arduino IDE, install **esp32 by Espressif Systems** through Boards Manager, if needed.
3. Under **Tools**, select:

   | Setting | Value |
   |---|---|
   | Board | **XIAO_ESP32S3** |
   | Port | Your XIAO USB port |
   | PSRAM | **OPI PSRAM** |
   | USB CDC On Boot | Enabled, if available |

4. After uploading, open **Serial Monitor** at **115200 baud**.
5. Upload ```files>examples>basic>Blink``` to check succesful hardware setup

> [!IMPORTANT]
> You may choose **ESP-EYE** in Edge Impulse when the XIAO is absent from the *performance-estimation* list. This is only an estimate. **Keep XIAO_ESP32S3 selected in Arduino IDE.**

---

# Part 1 — Prepare and Evaluate the Model

## Path A — Edge Impulse

1. Create a project at [Edge Impulse](https://edgeimpulse.com/).
2. Record or upload samples, label them, and create training and test sets.
3. Under **Impulse Design**, configure and train a small model. Use **INT8 quantization** when supported.
4. Under **Model Testing**, record the test accuracy (or reported detection metric) and save one example prediction.
5. On the **Deployment** page, record the estimated **latency, Flash/ROM, and RAM**, along with the target device used for the estimate.
6. Select Deployment target -> arduino library , Inference engine -> TensorFLow Lite and Quantized (int8)

## Path B — Pretrained Gesture Model

Open the [SenseCraft gesture example](https://sensecraft.seeed.cc/ai/view-model/60111-gesture-detection?tab=public). Follow its supported XIAO deployment instructions. Record the model name and link. **Skip the training steps and Arduino camera-code changes below.**

---

# Part 2 — Deploy to XIAO (Path A)

## Step 1 — Import the Edge Impulse Library

1. In Edge Impulse, open **Deployment → Arduino library**; select **TensorFlow Lite** and **Quantized (INT8)** if available. Build and download the `.zip` file.
2. In Arduino IDE, select **Sketch → Include Library → Add .ZIP Library**.
3. Open **File → Examples → [your Edge Impulse library] → esp32_camera** for camera projects. For audio projects, use the matching microphone example.
4. Keep the generated model include (for example, `#include <my_project_inferencing.h>`) unchanged. **The next edits are in the `esp32_camera` sketch, not in that header file.**

## Step 2 — Change the Camera Pins (camera projects only)

The example uses **ESP-EYE camera pins**, which differ from XIAO pins. Make **only these two edits**.

### Edit A: Replace the GPIO numbers

At the **top of the `esp32_camera` sketch**, find:

```cpp
#define CAMERA_MODEL_ESP_EYE
...
#if defined(CAMERA_MODEL_ESP_EYE)
    // ESP-EYE pin definitions are here
```

**Keep** `#define CAMERA_MODEL_ESP_EYE` and `#if defined(CAMERA_MODEL_ESP_EYE)` as they are. Within that `#if` block, **remove the old ESP-EYE camera pin `#define` lines**, from `PWDN_GPIO_NUM` through `PCLK_GPIO_NUM`, and paste these **XIAO pin definitions in the same place**:

```cpp
#define PWDN_GPIO_NUM   -1
#define RESET_GPIO_NUM  -1
#define XCLK_GPIO_NUM   10
#define SIOD_GPIO_NUM   40
#define SIOC_GPIO_NUM   39
#define Y9_GPIO_NUM     48
#define Y8_GPIO_NUM     11
#define Y7_GPIO_NUM     12
#define Y6_GPIO_NUM     14
#define Y5_GPIO_NUM     16
#define Y4_GPIO_NUM     18
#define Y3_GPIO_NUM     17
#define Y2_GPIO_NUM     15
#define VSYNC_GPIO_NUM  38
#define HREF_GPIO_NUM   47
#define PCLK_GPIO_NUM   13
```

If old pins are present as comments (`// #define ...`), you can remove those comments too. **Do not remove** the nearby `#elif`, `#else`, or `#endif` lines. You do **not** need to rename `CAMERA_MODEL_ESP_EYE` for this lab: it is the name of the code branch, and the branch now contains the XIAO pin values.

### Edit B: Remove two ESP-EYE-only pin settings

Press **Ctrl+F** in the same sketch and search for `ei_camera_init`. Inside that function, find **and delete this entire four-line block**:

```cpp
#if defined(CAMERA_MODEL_ESP_EYE)
  pinMode(13, INPUT_PULLUP);
  pinMode(14, INPUT_PULLUP);
#endif
```

On the XIAO, GPIO 13 and GPIO 14 are camera signal pins. **Leave the rest of `ei_camera_init()` unchanged.**

> [!NOTE]
> This is the same minimal camera-pin modification used in the class example. Audio projects do not need camera GPIO edits.

## Step 3 — Upload and Test

1. Confirm the **XIAO board**, correct **Port**, and **OPI PSRAM** setting.
2. Click **Verify**, then **Upload**. Save a screenshot showing a successful upload.
3. Open **Serial Monitor (115200 baud)**. Present an object or other model input.
4. Check the prediction labels, confidence scores, and inference timing. Save a screenshot or short video of successful **on-device inference**.

---

# Troubleshooting — If You Get an Error

**Only apply a fix if you see that error.**

| Error | First action |
|---|---|
| `Camera init failed` / `Camera probe failed` | Check the camera connection and the GPIO values in **Edit A**. |
| `ERR: Failed to allocate snapshot buffer!` | Enable **Tools → PSRAM → OPI PSRAM**. If it continues, use the buffer fix below. |
| `AllocateTensors() failed` / classifier `(-3)` | Use the tensor arena fix below. |
| `..._inferencing.h: No such file` | Reimport the Edge Impulse `.zip` and verify the generated include name. |
| No Serial Monitor text | Select the correct port and **115200 baud**; enable USB CDC On Boot if available. |

### Buffer fix (only for `Failed to allocate snapshot buffer!`)

In `esp32_camera`, add `#include "esp_heap_caps.h"` near the other includes. In `loop()`, **replace** the `snapshot_buf = (uint8_t*)malloc(...);` statement with:

```cpp
snapshot_buf = (uint8_t*)heap_caps_malloc(
    320 * 240 * 3, MALLOC_CAP_SPIRAM | MALLOC_CAP_8BIT);
```

Keep the existing null check and `free(snapshot_buf)` calls. This moves the 225 KiB RGB image buffer to PSRAM.

### Tensor fix (only for `AllocateTensors() failed`)

In the **installed Edge Impulse library**, open `src/tflite-model/tflite_learn_*.h`. Search for the constant ending in `_arena_size`. **Try doubling its numeric value**, then recompile and upload. For example, change `185036` to `370072` **only if 185036 is the value in your file**. Other models use different values. If your library has a separate maximum arena-size setting, keep it consistent. If this still fails, a smaller model may be needed.

---

# Part 3 — Demonstration and Evaluation

Show successful inference on the physical board. Record the following in the [Lab 3 Submission Report](Lab3_Submission.md):

| Required result | What to submit |
|---|---|
| Model test result | Test metric and example prediction (**Path A**), or five gesture trials (**Path B**) |
| Inference latency | Three on-device timing readings if available, plus their median |
| Model memory | Edge Impulse **Flash/ROM and RAM estimates separately**, if available |
| Evidence | Successful upload/deployment and a live inference screenshot or short video |
| Reflection | One issue or tradeoff in running AI on a microcontroller |

**Label estimated values as estimates.** An ESP-EYE estimate is not a measurement on the XIAO. If a metric is unavailable, write **N/A** rather than guessing.

---

# After the Experiments

Complete [Lab 3 Submission Report](Lab3_Submission.md). Submit **one report per team**.

**Useful resources:** [Seeed XIAO guide](https://wiki.seeedstudio.com/xiao_esp32s3_getting_started/) · [Image classification](https://wiki.seeedstudio.com/tinyml_course_Image_classification_project/) · [Object detection video](https://www.youtube.com/watch?v=7Aucyd10Mtw) · [Deployment video](https://www.youtube.com/watch?v=HDRvZ_BYd08)