# Setting up a Python interface for the S2 Geometry Library

The purpose of this note is to document the process of setting up a Python interface for the S2 Geometry Library. The installation process outlined on the S2 Geometry [site](https://s2geometry.io/about/platforms.html) was unsuccessful on my local machine, despite many hours of effort and troubleshooting. Along the way, I discovered a workaround, that is able to interface with an older version of the S2 Geometry Library (through a branch on GitHub that was forked a few years ago). 

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
git clone https://github.com/figroc/s2geometry
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
cmake -DBUILD_PYWRAPS2=ON ..
```
(Note: You must call `cmake` on the parent directory `..` because the `build` direcotyr is empty.)

Make and install the build:
```
sudo make install 
```

## Build the python wheels

Install the necessary requirements 
```
python3 -m pip install cmake_build_extension wheel
```

Assuming you are currently still in the `build` directory, build the wheel:
```
cd python
python3 setup.py bdist_wheel
```

The newly created wheel file(s) will be located inside of the `dist` subdirectory. 

## Install from the wheel

Begin by checking which configuration(s) of wheel files your machine supports. 

Install `packaging`:

```
python3 -m pip install packaging
```

Then view a list of 5 recent tags about configurations that your machine supports:

```
python3 -c"from packaging import tags; print('\n'.join([str(t) for t in tags.sys_tags()]))" |head -5
```

Using one of these configurations, rename a wheel file inside of `dist` appropriately. For example, suppose that one of the displayed configurations is 
```
cp311-cp311-macosx_13_0_universal2.
```

Then navigate into the `dist` directory and rename one of the existing wheel file(s):
```
cd dist
mv s2_geometry-0.10.0-existing-configuration.whl s2_geometry-0.10.0-cp311-cp311-macosx_13_0_universal2.whl
```

Use `pip3` to install the package from the wheel (continuing with the previous example):

```
python3 -m pip install s2_geometry-0.10.0-cp311-cp311-macosx_13_0_universal2.whl
```

## Rename the newly installed package

The installed package from the previous steps will be called `s2-geometry`. This is not ideal because the hyphen will cause the statement `import s2-geometry` to throw a syntax error. As a result, it is desirable to change the name of the package to something else, such as `s2_geometry`. 

To do this, first identify the `Location` the package is installed:
```
pip3 show s2-geometry
```

For example, suppose that the command line displays 
```
Location: /opt/homebrew/lib/python3.11/site-packages
```

Navigate to this location:
```
cd /opt/homebrew/lib/python3.11/site-packages
```
and change the name of the package
```
mv s2-geometry s2_geometry
```

Congratulations, you may now use the s2_geometry package.  

## Testing the package with a script

To test your installation, navigate to an appropriate directory, and create a new file 
```
touch s2_test.py
```

Then copy the following code into the newly created file 
```
import s2_geometry as s2

london = s2.S2LatLng.FromDegrees(51.5001525, -0.1262355)
point = london.ToPoint()
polygon = s2.S2Polygon(s2.S2Cell(s2.S2CellId(london)))

print(polygon.num_loops()==1)
print(polygon.Contains(point))
print(s2.S2CellId(london).level())
```

Run the code with the command 
```
python3 s2_test.py
```

You should see the following results displayed:
```
True
True
30
```
