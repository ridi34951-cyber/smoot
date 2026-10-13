# Camera — GyroLock V28.6 (frame-lock smoothing fix)

This revision targets visible frame wobble when the phone is tilted slightly. It smooths fallback orientation angles before deriving angular rates, limits fallback spikes, increases gyro rate filtering, and smooths the correction path more strongly. These changes aim to keep the frame calmer without introducing a second image-motion correction. The V28.5 flow estimator integrated global block-match displacement and then added that result on top of the gyro-derived correction. Since both estimates can represent the same camera rotation, this could apply motion compensation twice and create visible jelly/bounce.

The 240-point gyro trajectory, Vulkan render path, 30 FPS output timestamp normalization, audio remux, and portrait rotation handling are retained. The correction trajectory now uses a 1.35-second camera-path window and a 0.34-second correction smoothing window to reduce abrupt counter-rotation. This change is intended to eliminate conflicting correction; it is not a claim that rolling-shutter wobble is fully corrected. The optical-flow class remains in the source tree but is not invoked during rendering.

## Build

Push the contents of this project folder to the root of a GitHub repository and run **Actions → Build GyroLock Vulkan APK → Run workflow**. The workflow installs the Android SDK/NDK and builds a debug APK. This environment did not run a full Android SDK build, so a successful APK build has not been verified here.

Output target remains 30 FPS with the original audio remuxed without re-encoding. Actual stabilization quality depends on gyro alignment, camera readout timing, and device codec behavior.

## Android compatibility

This package sets `minSdk 33` (Android 13+) and `targetSdk 33`. Android 12 and older devices are not supported by this build. The video stabilization source and 240-point gyro trajectory are otherwise retained.


## 2.8.3 — Android 13+ frame-lock smoothing
- Minimum Android version: Android 13 (API 33).
- Sensor selection now prefers the physical gyroscope, then falls back to Rotation Vector, Geomagnetic Rotation Vector, and legacy Orientation when available.
- Fallback orientation samples are low-pass filtered before conversion to approximate angular rates, and fallback angular-rate spikes are limited. Physical gyro rates use stronger low-pass filtering and a slightly wider dead band. This is a compatibility fallback, not equivalent to a physical gyroscope; magnetometer interference may still affect results.
- Vulkan stabilization and the existing 240-point correction curve are retained.
- Version code is 31. The ZIP has been archive-validated; a full Android build and on-device test have not been run here.
