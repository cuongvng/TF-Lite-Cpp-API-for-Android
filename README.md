# Building shared libraries (`.so`) to use TF Lite C++ API in Android applications

When following [Android quick start guide](https://www.tensorflow.org/lite/guide/android#use_tflite_c_api) for building the C++ shared libraries to use their APIs on Android apps, I experienced many Bazel build errors,
such as `does not contain a toolchain for cpu 'arm64-v8a' when building tensorflow lite`.
After 2 days looking for a solution, I finally managed to make it work.
The quick start guide was not informative enough, I had to configure somethings before executing the simple

```
bazel build -c opt --config=android_arm //tensorflow/lite:libtensorflowlite.so
# or
bazel build -c opt --config=android_arm64 //tensorflow/lite:libtensorflowlite.so
```
commands listed on it.
This repo shows more detailed instructions to build the libraries for using C++ API on Android.

If you don't care about the process, just go ahead and get the generated libraries (for *32bit armeabi-v7a* and *64bit arm64-v8a*) in the folder [generated-libs](./generated-libs).

### How to build TF Lite C++ library
<details><summary>Click to expand/collapse</summary>
<p>

- Step 1: [Install Bazel](https://docs.bazel.build/versions/4.0.0/install.html) 

- Step 2: Clone the [TensorFlow repo](https://github.com/tensorflow/tensorflow)
```
git clone https://github.com/tensorflow/tensorflow
cd ./tensorflow/
```

- **Step 3: Configure Android build **
**Before running the `bazel build ...` command, you need to configure the build process. Do so by executing 

```
./configure
```
The `configure` file is at the root of the tensorflow directory, which you `cd` to at Step 2.
Now you have to input some configurations on the command line:

```
$ ./configure
You have bazel 3.7.2-homebrew installed.
Please specify the location of python. [Default is /Library/Developer/CommandLineTools/usr/bin/python3]: /Users/cuongvng/opt/miniconda3/envs/style-transfer-tf-lite/bin/python
```
First is the location of python, because `./configure` executes the `.configure.py` file.
Choose the location that has Numpy installed, otherwise the later build will fail.
Here I point it to the python executable of a conda environment.
Next, 

```
Found possible Python library paths:
  /Users/cuongvng/opt/miniconda3/envs/style-transfer-tf-lite/lib/python3.7/site-packages
Please input the desired Python library path to use.  Default is [/Users/cuongvng/opt/miniconda3/envs/style-transfer-tf-lite/lib/python3.7/site-packages]
```
I press `Enter` to use the default site-packages, which contains necessary libraries to build TF.
Next,
```
Do you wish to build TensorFlow with ROCm support? [y/N]: N
No ROCm support will be enabled for TensorFlow.

Do you wish to build TensorFlow with CUDA support? [y/N]: N
No CUDA support will be enabled for TensorFlow.

Do you wish to download a fresh release of clang? (Experimental) [y/N]: N
Clang will not be downloaded.

Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -Wno-sign-compare]: 
```
Key in as showed above, on the last line type `Enter`.
Then it asks you whether to configure ./WORKSPACE for Android builds, type **y** to add configurations.

```
Would you like to interactively configure ./WORKSPACE for Android builds? [y/N]: y
Searching for NDK and SDK installations.

Please specify the home path of the Android NDK to use. [Default is /Users/cuongvng/library/Android/Sdk/ndk-bundle]: /Users/cuongvng/Library/Android/sdk/ndk/21.1.6352462
```
That is the home path of the Android NDK (version 21.1.6352462) on my local machine. Note that when you `ls` the path, it must include `platforms`, e.g.:
```
$ ls /Users/cuongvng/Library/Android/sdk/ndk/21.1.6352462
CHANGELOG.md      build             ndk-stack         prebuilt          source.properties wrap.sh
NOTICE            meta              ndk-which         python-packages   sources
NOTICE.toolchain  ndk-build         package.xml       shader-tools      sysroot
README.md         ndk-gdb           platforms         simpleperf        toolchains
```

For now I ignore the resulting WARNING, then choose the min NDK API level
```
WARNING: The NDK version in /Users/cuongvng/Library/Android/sdk/ndk/21.1.6352462 is 21, which is not supported by Bazel (officially supported versions: [10, 11, 12, 13, 14, 15, 16, 17, 18]). Please use another version. Compiling Android targets may result in confusing errors.

Please specify the (min) Android NDK API level to use. [Available levels: ['16', '17', '18', '19', '21', '22', '23', '24', '26', '27', '28', '29']] [Default is 21]: 29
```

Next
```
Please specify the home path of the Android SDK to use. [Default is /Users/cuongvng/library/Android/Sdk]: /Users/cuongvng/Library/Android/sdk

Please specify the Android SDK API level to use. [Available levels: ['28', '29', '30']] [Default is 30]: 30

Please specify an Android build tools version to use. [Available versions: ['29.0.2', '29.0.3', '30.0.3', '31.0.0-rc1']] [Default is 31.0.0-rc1]: 30.0.3
```

That is all for Android build configs. Choose `N` for all questions appearing later:

- Step 4: Build the shared library (`.so`)
Now you can run the bazel build command to generate libraries for your target architecture:

```
bazel build -c opt --config=android_arm //tensorflow/lite:libtensorflowlite.so
# or
bazel build -c opt --config=android_arm64 //tensorflow/lite:libtensorflowlite.so
```

It should work without errors.
The generated library would be saved at `./bazel-bin/tensorflow/lite/libtensorflowlite.so`.

</p>
</details>

### How to integrate the libraries in Android apps
<details><summary>Click to expand/collapse</summary>
<p>
- Include all header file
- FlatBuffers
- Abseil
</p>
</details>

---
*If you find this repo useful, please give it a star!*
