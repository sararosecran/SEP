SEP is a python based library that utilizes the SExtractor algorithm to perform background estimation, object detection, and photometry on astronomiacal images.

# Background Subtraction
In a similar manner to SExtractor, SEP will estimate an image's background. The background map will be subtracted from an image before running photometry. Photometry errors are estimated using the rms of the backgorund. The image is broken up into user defined sum-image sizes, and the mean and $sigma$. A histogram of each sub-image's pixels is constructed and all values greater than $3\sigma# are discarded. The process is iterated until all values fall within $3\sigma$ from the mean. The local background in each subimage will correspond to either the mean pixel value or 2.5 $\times$ median - 1.5 $\times$ mean. The former represents a "non-crowded" case wherein $\sigma$ changed by less than 20% per cut. The latter represents a crowded case. The local backgrounds within each sub-image are then interpolated, and the result is a global background. 

The SEP executable to perform background estimation is given by:
**sep.Background(data, mask, maskthresh, bw, bh, fw, fh, fthresh)**.
Here,
* data is the image data (2D array)
* mask is a mask array
* maskthresh determines which pixels in "mask" are not considered in background estimation. 
* bw, bh are the size of the sub-images
* fw, fh are the filter sizes if one uses a filter to convolve with the data
* fthresh is the filter threshold