# Map building off images.

![Combined](combined.png?raw=true "Combined")

## Setup

As tested with Python 3.6 on Windows:

    py -m venv venv
    venv\Scripts\activate.bat
    pip install -r requirements.txt
    pip install opencv-contrib-python==3.3.0.10

### On the Pi, as tested with Python 3.5 on Raspbian Stretch:

    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    chmod 755 serve.sh

Change swap size to 1 GB temporarily by setting CONF_SWAPSIZE to 1024 in:

    /etc/dphys-swapfile

Then running:

    sudo /etc/init.d/dphys-swapfile stop
    sudo /etc/init.d/dphys-swapfile start

aaaaand this bit ... Sorry. This will take a while. And by "a while" I mean
that "make" took **6+ hours on a Pi Zero**. I strongly suggest that you image
your SD once you've got "import cv2" working in your environment just in case.

    sudo apt-get -y update
    sudo apt-get -y upgrade
    sudo apt-get -y install build-essential cmake pkg-config
    sudo apt-get -y install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
    sudo apt-get -y install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
    sudo apt-get -y install libxvidcore-dev libx264-dev
    sudo apt-get -y install libgtk2.0-dev libgtk-3-dev
    sudo apt-get -y install libatlas-base-dev gfortran
    sudo apt-get -y install python2.7-dev python3-dev

    cd ~
    wget -O opencv.zip https://github.com/opencv/opencv/archive/3.3.1.zip
    unzip opencv.zip
    wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/3.3.1.zip
    unzip opencv_contrib.zip
    cd opencv-3.3.1/
    mkdir build
    cd build/
    cmake -D CMAKE_BUILD_TYPE=RELEASE \
        -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib-3.3.1/modules \
        -D BUILD_opencv_java=OFF \
        -D BUILD_opencv_python2=OFF \
        -D BUILD_opencv_python3=ON \
        -D PYTHON_DEFAULT_EXECUTABLE=$(which python3) \
        -D INSTALL_C_EXAMPLES=OFF \
        -D INSTALL_PYTHON_EXAMPLES=OFF \
        -D BUILD_EXAMPLES=OFF \
        -D BUILD_TESTS=OFF \
        -D WITH_FFMPEG=OFF \
        -D BUILD_PERF_TESTS=OFF ..
    make
    sudo make install
    sudo ldconfig

    ln -s /usr/local/lib/python3.5/site-packages/cv2.cpython-35m-arm-linux-gnueabihf.so ~/cheepbot/map_building/venv/lib/python3.5/site-packages/cv2.so

Finally, change the swap file size back to 100 as above.

Credit: https://www.pyimagesearch.com/2017/09/04/raspbian-stretch-install-opencv-3-python-on-your-raspberry-pi/
and https://github.com/ys7yoo/PiOpenCV and
https://github.com/opencv/opencv/issues/10080#issuecomment-343976885

## Running it

As tested with Python 3.6 on Windows:

    venv\Scripts\activate.bat
    python -m mapbuilder.serve

On the Pi, as tested in Python 3.5:

    ./serve.sh

## API

Test page:

    http://[pi IP address]:10001/

### API:

POST image to:

    http://[pi IP address]:10001/

Will return updated map image.

## Tests

    py.test
