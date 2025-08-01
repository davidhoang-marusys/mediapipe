# MarusysVina MediaPipe Custom Framework

This repository is a customized fork of the original [MediaPipe](https://github.com/google-ai-edge/mediapipe) project, tailored for building `.aar` frameworks for vision-based tasks on low-end Android devices such as set-top boxes.

---

## 🚀 Getting Started

### ✅ Requirements

**Recommend OS:**

- macOS
- Ubuntu

**Dependencies:**

- Bazel (recommended: Bzl aka Barelisk)
- OpenCV
- FFmpeg
- Android SDK (≥ 35.0.0)
- Android NDK (≥ r28)
- Java 17 or Java 11
- C++17 compatible compiler

📚 Refer to the official MediaPipe documentation for installation:

- [Install MediaPipe Framework](https://ai.google.dev/edge/mediapipe/framework/getting_started/install)
- [Set up for Android](https://ai.google.dev/edge/mediapipe/framework/getting_started/android)

---

## 📦 Building the `.aar` Library

This section shows how to build an `.aar` package for the Pose Landmarker task.

### 1. Create the `BUILD` File

Create a `mediapipe_aar()` target by editing or creating the following file:

📄 `mediapipe/examples/android/src/java/com/google/mediapipe/apps/aar_example/BUILD`

```python
load("//mediapipe/java/com/google/mediapipe:mediapipe_aar.bzl", "mediapipe_aar")

mediapipe_aar(
    name = "mediapipe_pose_tracking",
    calculators = ["//mediapipe/graphs/pose_tracking:pose_tracking_gpu_deps"],
)
```

### 2. Build the `.aar`

Run the following Bazel command:

```bash
bazel build -c opt \
  --strip=Always \
  --host_crosstool_top=@bazel_tools//tools/cpp:toolchain \
  --config=android_arm \
  --copt=-fno-omit-frame-pointer \
  --copt=-g \
  --copt=-O0 \
  //mediapipe/examples/android/src/java/com/google/mediapipe/apps/aar_example:mediapipe_pose_tracking.aar
```

📌 **Arguments:**

- `-c opt`: Build in release mode (`dbg` for debug mode).
- `--config`: Target ABI. Use `android_arm` for `armeabi-v7a`, `android_arm64` for `arm64-v8a`.

### 3. Copy the `.aar` File

```bash
cp bazel-bin/mediapipe/examples/android/src/java/com/google/mediapipe/apps/aar_example/mediapipe_pose_tracking.aar \
/absolute/path/to/your/preferred/location
```

### 4. Build the Graph Binary

```bash
bazel build -c opt mediapipe/graphs/pose_tracking:pose_tracking_gpu_binary_graph
```

Copy the result:

```bash
cp bazel-bin/mediapipe/graphs/pose_tracking/pose_tracking_gpu.binarypb \
/absolute/path/to/your/preferred/location
```

---

## 🧰 Troubleshooting

### ❌ Missing `libc++_shared.so`

If you encounter an error due to a missing `libc++_shared.so`, follow these steps:

1. Rename the `.aar` file to `.zip`.
2. Extract the `.zip` file.
3. Download the appropriate `libc++_shared.so` based on the target architecture:
   - For 32-bit: `jni/armeabi-v7a/`
   - For 64-bit: `jni/arm64-v8a/`
   - You can find it in `$ANDROID_NDK_HOME/sources/cxx-stl/llvm-libc++/libs` (for NDK < 25)
4. Add the `.so` file into the correct `jni/` folder.
5. Re-zip the folder and rename it back to `.aar`.

---

## 📋 Notes

- This customization is optimized for lightweight Android devices.
- You can reuse this setup to build `.aar` libraries for other vision-based graphs by changing the `calculators` target accordingly.

---

Happy building! 🚀
