# opencv-python3-aws-lambda

This is how to add opencv-python 4.4.0 to an AWS Lambda layer for use in AWS Lambda.
Shoutout to Marcin of StackOverflow who created this solution when I was stuck. Putting it in a repo to save for future use.


You will need to add a bunch of dependencies to your layer. Below are the steps that I've used for opencv_python on lambda.


### 1. On local workstation (termianl window 1)
```
mkdir /tmp/mylayer && cd /tmp/mylayer

echo opencv-python==4.4.0.42 > ./requirements.txt
```

### 2. On local workstation (termanal window 2)

```
docker run -it -v /tmp/mylayer:/mylayer  lambci/lambda:build-python3.8 bash
```

The above command will put you into the docker container.

Inside the container:
```
cd /mylayer

pip install --no-deps -t python/lib/python3.8/site-packages/ -r requirements.txt

yum install -y mesa-libGL

cp -v /usr/lib64/libGL.so.1 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /usr/lib64/libGL.so.1.7.0 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /usr/lib64/libgthread-2.0.so.0 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /usr/lib64/libgthread-2.0.so.0 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /usr/lib64/libglib-2.0.so.0 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /usr/lib64/libGLX.so.0 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /usr/lib64/libX11.so.6 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /usr/lib64/libXext.so.6 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /usr/lib64/libGLdispatch.so.0 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /usr/lib64/libGLESv1_CM.so.1.2.0 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /usr/lib64/libGLX_mesa.so.0.0.0 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /usr/lib64/libGLESv2.so.2.1.0 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /usr/lib64/libxcb.so.1 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /usr/lib64/libXau.so.6 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /usr/lib64/libXau.so.6 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
cp -v /lib64/libGLdispatch.so.0.0.0 /mylayer/python/lib/python3.8/site-packages/opencv_python.libs/
```

### 3. On local workstation again (termianl window 1)
Pack the python folder into mylayer.zip.
```
zip -r -9 mylayer.zip python
```

### In AWS console
Create lambda layer based on mylayer.zip in the AWS Console. Don't forget to specify Compatible runtimes to python3.8.

Add AWS provide SciPy layer AWSLambda-Python38-SciPy1x and your own layer with cv2 into your function.

So you will have two layers in your function.

Perform basic test of the layer in lambda using the following lambda function:
```
import cv2

def lambda_handler(event, context):    
    print(dir(csv))
```

The function executes correctly (partial printout shown).

```
slation3D', 'exp', 'extractChannel', 'fastAtan2', 'fastNlMeansDenoising', 'fastNlMeansDenoisingColored', 'fastNlMeansDenoisingColoredMulti', 'fastNlMeansDenoisingMulti', 'fillConvexPoly', 'fillPoly', 'filter2D', 'filterHomographyDecompByVisibleRefpoints', 'filterSpeckles', 'find4QuadCornerSubpix', 'findChessboardCorners', 'findChessboardCornersSB', 'findChessboardCornersSBWithMeta', 'findCirclesGrid', 'findContours', 'findEssentialMat', 'findFundamentalMat', 'findHomography', 'findNonZero', 'findTransformECC', 'fisheye', 'fitEllipse', 'fitEllipseAMS', 'fitEllipseDirect', 'fitLine', 'flann', 'flann_Index', 'flip', 'floodFill', 'gemm', 'getAffineTransform', 'getBuildInformation', 'getCPUFeaturesLine', 'getCPUTickCount', 'getDefaultNewCameraMatrix', 'getDerivKernels', 'getFontScaleFromHeight', 'getGaborKernel', 'getGaussianKernel', 'getHardwareFeatureName', 'getNumThreads', 'g
```
