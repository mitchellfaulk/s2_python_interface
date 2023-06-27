# Setting up a Python interface for the S2 Geometry Library

## Requirements

Install a package manager such as [Homebrew](https://brew.sh/). 

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Use homebrew to install cmake, swig, and openssl 

```
brew install cmake swig openssl
```

## Cloning and building

Create a local directory, such as `source`, in which to clone the following forked version of the S2 Geometry Library:
```
mkdir source
cd source
git clone https://github.com/google/s2geometry.git
```

Navigate into the cloned repository:

```
cd s2geometry
```

Create a build directory, and navigate into it

```
mkdir build
cd build
```

Use `cmake` with the Python wrapper set to ON:
```
cmake -DWITH_PYTHON=ON ..
```
(Note: You must call `cmake` on the parent directory `..` because the `build` directory is empty.)

Make and install the build:
```
sudo make install 
```

## Build the Python wheels

Install the necessary requirements 
```
python3 -m pip install cmake_build_extension wheel
```

Navigate to the parent directory to find the `setup.py` file:
```
cd ..
```

Then, build the wheels:
```
cd python
python3 setup.py bdist_wheel
```

The newly created wheel file(s) will be located inside of the `dist` subdirectory. 

## Install from the wheels

Begin by checking which configuration(s) of wheels your machine supports using the following steps. 

Install `packaging`:

```
python3 -m pip install packaging
```

Then view a list of 5 recent tags about configurations that your machine supports:

```
python3 -c"from packaging import tags; print('\n'.join([str(t) for t in tags.sys_tags()]))" |head -5
```

Using one of these configurations, rename a wheels file inside of `dist` appropriately. For example, suppose that one of the displayed configurations is 
```
cp311-cp311-macosx_13_0_universal2
```

Then navigate into the `dist` directory and rename one of the existing wheels file(s):
```
cd dist
mv s2geometry-0.10.0-existing-configuration.whl s2geometry-0.10.0-cp311-cp311-macosx_13_0_universal2.whl
```

Use `pip3` to install the package from the wheels (continuing with the previous example):

```
python3 -m pip install s2geometry-0.10.0-cp311-cp311-macosx_13_0_universal2.whl
```
