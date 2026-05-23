# fixed_cuda_mog2

Standalone build of OpenCV's CUDA MOG2 background subtractor with a bug fix.

## The Bug

In OpenCV's `cv::cuda::BackgroundSubtractorMOG2`, setter methods for parameters like 
background ratio, var thresholds, shadow threshold, etc. update host-side values 
but never upload them to the GPU. This means parameter changes have no effect after 
the model is initialized.

This project fixes that by tracking dirty state and uploading constants to the 
device before each `apply()` / `getBackgroundImage()` call.

`setNMixtures()` also now throws if called after initialization, since changing 
the number of mixtures requires reallocating GPU memory.

## Usage

Drop-in replacement — use `fixed_cuda_mog2::BackgroundSubtractorMOG2` instead of 
`cv::cuda::BackgroundSubtractorMOG2`:

```cpp
#include <opencv2/cudabgsegm.hpp>

auto mog2 = fixed_cuda_mog2::createBackgroundSubtractorMOG2();
mog2->apply(frame, fgmask, learningRate, stream);
mog2->setBackgroundRatio(0.5);  // now actually works
```

Build as a CMake dependency:

```bash
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
make
```

Link against `fixed_cuda_mog2::fixed_cuda_mog2`.

## Dependencies

- CUDA
- OpenCV (core, video, cudaarithm)