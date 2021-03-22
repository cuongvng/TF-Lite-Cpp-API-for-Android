# Generate shared libraries (`.so`) for using TF Lite C++ API in Android applications

When following [TF Lite documentation](https://www.tensorflow.org/lite/guide/android#use_tflite_c_api) for building the C++ shared libraries to use their APIs on Android apps , I experienced many Bazel build errors.
After searching on the internet for a while, [this solution](https://github.com/tensorflow/tensorflow/issues/34520#issuecomment-557533555) worked for me (on both my local Mac and Linux Google Colab).
This repo shows the instructions to build the libraries, specifically every commands that can be executed sequentially to get the job done!

If you don't care about the process, just go ahead and get the generated libraries (for *32bit armeabi-v7a* and *64bit arm64-v8a*) in the folder [generated-libs](./generated-libs).

### On Mac OS
- [Install Bazel](https://docs.bazel.build/versions/4.0.0/install-os-x.html#step-2-install-bazel-via-homebrew) via Homebrew:
```
brew install bazel
```

- Clone the [TensorFlow repo](https://github.com/tensorflow/tensorflow)
```
git clone https://github.com/tensorflow/tensorflow
cd tensorflow/
```

- Build the libraries
```
# For ARM 64 bit
bazel build -c opt //tensorflow/lite:libtensorflowlite.so --fat_apk_cpu=arm64-v8a
# For ARM 32 bit
bazel build -c opt //tensorflow/lite:libtensorflowlite.so --fat_apk_cpu=arm-v7a
```

The generated library would be saved at `./bazel-bin/tensorflow/lite/libtensorflowlite.so`.

### On Linux
Almost similar, except for installing Bazel (a little more complicated).
For more details, please check out the notebook [run_me.ipynb](./run_me.ipynb).

---
*If you find this repo useful, please give it a star!*