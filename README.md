# Deprecation Notice

As of [this announcement](https://pytorch.org/blog/pytorch-1-dot-3-adds-mobile-privacy-quantization-and-named-tensors/) PyTorch 1.3 now officialy supports an end-to-end workflow from Python to deployment on iOS and Android through [**PyTorch Mobile**](https://pytorch.org/mobile/home/). Thank you for your interest with this project.

---

# PyTorch on Android

PyTorch on Android is a project to demo how to use [PyTorch](https://pytorch.org/) **and ONNX** to build an Android mobile application doing real time object classification.

![demo](https://thumbs.gfycat.com/MiniatureBlissfulGermanspaniel-size_restricted.gif)

_The source code for the demo in this repo was originally based on [AICamera repo](https://github.com/bwasti/AICamera) and as of 2018-12-31, the codebase was based on [Soumith's AICamera repo](https://github.com/soumith/AICamera)._

**Project Status**:

- 2019-05-13:
  - Introducing [**PyTorch Lite**](https://github.com/cedrickchee/pytorch-lite) — a lightweight machine learning framework for ON-DEVICE mobile inference. No cumbersome ONNX, no learning Caffe2. Just PyTorch and libtorch.
- 2018-12-31:
  - PyTorch core maintainers have updated AICamera example to work with latest PyTorch master. Once that PR is merged into PyTorch master, you can use the README below to get a working Android app, including changing the Protobuf with your own `init.pb` / `predict.pb` files.
  - [Android OSS fixes PR](https://github.com/pytorch/pytorch/pull/15509).
- 2019-01-01:
  - I have tested the Android OSS fixes with my own ResNet18 pre-trained ImageNet model (`resnet18_init_net_v1.pb` and `resnet18_predict_net_v1.pb` Protobuf files) and the Android app is working fine.
- Early release. Still in heavy development. What this means is, important features are missing, things might be moved around quickly and things will break.

## Goal

Make it easier to ship and test your neural network model in PyTorch on mobile devices.

## Documentation

### Updating the AICamera Android app to work with Caffe2 from PyTorch/master

This is an example for using Caffe2 on Android.

1. git clone PyTorch source and switch to remote branch, `android_oss_fixes`:

```sh
$ git clone --recursive https://github.com/pytorch/pytorch.git
$ cd pytorch
# We are using this PyTorch master commit 39381964bbb2686cf84c78aed638985455f6d3fe (Dec 23 02:08:33 2018 -0500, "fix build_android for newest NDK, and make install work")
$ git checkout --track origin/android_oss_fixes

M third_party/QNNPACK
M third_party/cpuinfo
M third_party/fbgemm
M third_party/ideep
Branch 'android_oss_fixes' set up to track remote branch 'android_oss_fixes' from 'origin'.
Switched to a new branch 'android_oss_fixes'
```

2. You'll need to download the [Android NDK (latest r18 release)](https://developer.android.com/ndk/downloads/) if you have not.

3. First, we will compile Caffe2 Android libraries like libcaffe2, libqnnpack for arm-v7a ABI and then for x86.

Set build environment:
- PyTorch folder is at `$PYTORCH_ROOT`
- This repository folder is at `$AICAMERA_ROOT`
- Android NDK folder is at `$ANDROID_NDK`

```sh
# make sure $PYTORCH_ROOT, $AICAMERA_ROOT and $ANDROID_NDK are set
export ANDROID_NDK=~/android/sdk/ndk-bundle/
export PYTORCH_ROOT=~/dev/gh/pytorch/
export AICAMERA_ROOT=~/dev/android/android-studio-projects/aicamera/

pushd $PYTORCH_ROOT
```

Then, do the following:

Build Caffe2 android libs and copy them over into AICamera app folder

```sh
./scripts/build_android.sh
```
If you encountered build errors related to "quantized/int8_*.cc", get the patch from this "[Update QNNPACK](https://github.com/pytorch/pytorch/pull/15561/files)" PR. Patch these files in your local copies.

You should see "Install configuration: "Release"" output in your terminal if the process completed successfully.

```sh
mv build_android build_android_arm

# copy headers
cp -r install/include/* $AICAMERA_ROOT/app/src/main/cpp/

# copy arm libs
rm -rf $AICAMERA_ROOT/app/src/main/jniLibs/armeabi-v7a/
mkdir $AICAMERA_ROOT/app/src/main/jniLibs/armeabi-v7a
cp -r build_android_arm/lib/lib* $AICAMERA_ROOT/app/src/main/jniLibs/armeabi-v7a/


./scripts/build_android.sh -DANDROID_ABI=x86
mv build_android build_android_x86

# copy x86 libs
rm -rf $AICAMERA_ROOT/app/src/main/jniLibs/x86/
mkdir $AICAMERA_ROOT/app/src/main/jniLibs/x86
cp -r build_android_x86/lib/lib* $AICAMERA_ROOT/app/src/main/jniLibs/x86/
```

3. Build the AICamera app using the `Build -> Make Project` menu option in Android Studio

_If you prefer not to build PyTorch from master, I have commited the files for the Caffe2 bits into this git repo. Unfortunately, not all files are uploaded to GitHub due to file size limit._

_Grab the remaining large files from my [Google Drive](https://drive.google.com/drive/folders/1e1lFa8W2rkA8M7mhahe9VSiSMnl0a4Ab?usp=sharing)._

### Developer Guide

We created a developer guide on [how to ship a convolutional neural network (Resnet18 and SqueezeNet) on Android with PyTorch and Android Studio](https://github.com/cedrickchee/data-science-notebooks/blob/master/notebooks/deep_learning/fastai_mobile/README.md). Check it out!

### Step by Step Guide

We'll walk you through every step, from problem all the way to building and deploying the Android app to mobile phones.

## Features, Functionalities and TODO

- [X] Android (Java/C++) with Caffe2
- [x] Test SqueezeNet v1.1 model with your own video stream from camera
- [x] Live (real-time) detection
- [ ] Performance optimization
- [x] Test build in models (MobileNetV2, ShuffleNet, Resnet18) with your own video stream from camera
- [ ] Download your own model on the fly and test it
- [ ] Manage models locally on your Android device
- [ ] Memory consumption and time elapse data
- [ ] Overall control on every layer (from beginner to expert)
- [ ] Warm community and welcome to contribute
- [ ] Transfer learning SqueezeNet with new datasets (i.e. insect species, not hotdog)
- [ ] New classifier for insect species dataset using pre-trained ImageNet weights (transfer learning)
- [ ] Upload pre-trained models
- [ ] Bug fixes
- [x] Fix intermittent crashes
- [ ] React Native native module
- [x] Upload large files from PyTorch 'build_android' to Google Drive and share it here

## Android Project

You can download the Android project source code by running this command:

`git clone https://github.com/cedrickchee/pytorch-android.git`

## Android Development Environment

### Dependencies

- Android Studio: 3.2.1 and above
- Android SDK
- Android NDK r18 and above
- CMake Android SKD component
- Gradle 4.6 and above

## Tests

| Device             | Network       |  FPS (^)  |
| ------------------ | ------------- | --------- |
| Google Nexus 6P    | SqueezeNet    |  3.0      |
| Google Nexus 6P    | Resnet18      |  0.6      |
| Samsung Note 8     | SqueezeNet    |  _TBD_    |
| Galaxy Note 3      | SqueezeNet    |  4.0      |
| Samsung Galaxy S7  | SqueezeNet    |  5.8      |
| Google Pixel       | SqueezeNet    |  5.7      |

_Note:_
_^ the number of FPS is subjective to the camera photo (image) size you send to the mobile device as well as type of the device._

## Demo

If building this repo is too much of a trouble for you, we also plan to put this in Google Play Store (TBD).

## Network Graph Comparison

Visualize SqueezeNet 1.1 prediction network serialized as Protobuf file:

| Before update (Oct 2018)                       | After update (Jan 2019)                           |
|:----------------------------------------------:|:-------------------------------------------------:|
| ![](static/images/squeeze_predict_net_viz.png) | ![](static/images/squeeze_predict_net_v1_viz.png) |

## Performance

_TBD_

## Future Work

Scope of work for this project: _TBD_

## Other Useful Resources

- [PyTorch and the merged Caffe2 repo](https://github.com/pytorch/pytorch)

### PyTorch official forums

- [PyTorch team response to "Getting error on Caffe2 AICamera example"](https://discuss.pytorch.org/t/getting-error-on-caffe2-aicamera-example/32828)
- [Troubleshoot "Compiling PyTorch C++ for Android"](https://discuss.pytorch.org/t/compiling-pytorch-c-for-android/27384)

### GitHub Issues and Commits

- [t-vi's PyTorch proposal to "Improve build_android experience"](https://github.com/pytorch/pytorch/issues/13116)
- [t-v1's work to get Android app working again](https://github.com/t-vi/pytorch/tree/caffe2_android)

### Fast.ai

- [Convert fast.ai trained image classification model to iOS app via ONNX and Apple Core ML](https://medium.com/@hungminhnguyen/convert-fast-ai-trained-image-classification-model-to-ios-app-via-onnx-and-apple-core-ml-5fdb612379f1) - _note: for older fastai library version 0.7_

### Fast Neural Style Transfer (PyTorch → ONNX→ CoreML/NNAPI)

- [Building a Neural Style Transfer app on iOS with PyTorch and CoreML](https://medium.com/@alexiscreuzot/building-a-neural-style-transfer-app-on-ios-with-pytorch-and-coreml-76e00cd14b28)
  - [PyTorch fast neural style model with built-in ONNX exported](https://github.com/pytorch/examples/tree/master/fast_neural_style)
- [Fast neural style transfer for Android Neural Networks API (NNAPI)](https://github.com/caffe2/AICamera-Style-Transfer)

## Support

Feel free to ask any questions, from preparing development environment to debugging on Android Studio. We are happy to help you.

## License

This repository contains a variety of content; some developed by Cedric Chee, and some from third-parties. The third-party content is distributed under the license provided by those parties.

*I am providing code and resources in this repository to you under an open source license.  Because this is my personal repository, the license you receive to my code and resources is from me and not my employer.*

The content developed by Cedric Chee is distributed under the following license:

### Code

The code in this repository, including all code samples in the notebooks listed above, is released under the [MIT license](LICENSE). Read more at the [Open Source Initiative](https://opensource.org/licenses/MIT).

### Text

The text content of the book is released under the CC-BY-NC-ND license. Read more at [Creative Commons](https://creativecommons.org/licenses/by-nc-nd/3.0/us/legalcode).
