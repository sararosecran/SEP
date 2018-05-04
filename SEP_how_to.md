SEP is a python based library that utilizes the SExtractor algorithm to perform background estimation, object detection, and photometry on astronomiacal images.

# Background Subtraction
In a similar manner to SExtractor, SEP will estimate an image's background. The background map will be subtracted from an image before running photometry, and photometry errors are estimated using the rms of the backgorund. The image is broken up into user defined sum-image, and the mean and error for each is calculated. A histogram of each sub-image's pixels is constructed and all values greater than 3 times the error are discarded. The process is iterated until all values fall within 3 times the error from the mean. The local background in each sub-image will correspond to either the mean pixel value or 2.5 X median - 1.5 X mean. The former represents a "non-crowded" case wherein the error was changed by less than 20% per cut. The latter represents a crowded case. The local backgrounds within each sub-image are then interpolated and the result is a global background. 

The SEP executable to perform background estimation is given by:   
**sep.Background(data, mask, maskthresh, bw, bh, fw, fh, fthresh)**.   
Here,
* **data** is the image data (2D array)
* **mask** is a mask array
* **maskthresh** determines the threshold in which pixels in "mask" are not considered in background estimation. 
* **bw, bh** are the size of the sub-images
* **fw, fh** are the filter sizes if one uses a filter to convolve with the data
* **fthresh** is the filter threshold

# Object Detection
The following executable is used to perform object detection in SEP:   
**sep.extract(data, thresh, err, mask, minarea, filter_kernel, filter_type, deblend_nthresh, deblend_cont, clean, clean_param, segmentation_map)**   
* **data** is the background subtracted image   
Objects must adere to three requirements. Firstly, all pixels in the object must be above **thresh** where **thresh** is either an absolute threshold (surface brightness) or a relative threshold. The relative threshold is described by **thresh** times **err**, where **err** is usually the background rms. Secondly, all pixels must be adjacent. Lastly, there must be more than the minimum number of pixels defined by **minarea**. 

A filter may also be applied to smooth an image before object detection. 
* **filter_kernel** is the array used for smoothing (set to None if not performing smoothing)
* **filter_type** can be either 'matched' or 'conv', where the former accounts for pixel-to-pixel noise in the kernel and 'conv' does not. 
 
The deblending parameters help determine the distinct objects The process in which SEP deblends begins by generating a 2D light profile model with a "tree structure." Branches are generated when pixel values are larger than the number of deblending thresholds relative to the initial pixel value. The process is iterated until all branches are found. The algorithm identifies the top of the "tree" and locates the first branch. If the first branch has a fraction (defined by the contrast ratio) of counts above the total counts of the not yet de-blended object and there exists another branch also above this fraction, then two objects can be distinguished.
* **deblend_nthresh** is the number of deblending thresholds
* **deblend_cont** is the contrast ratio

The user can choose to clean, which will account for artifacts from bright objects. The algorithm checks that objects would be detected if other, closer, objects were not there. 
* **clean** set equal to 'True' will turn on cleaning
* **clean_param** sets how aggressively the objects are cleaned

Finally,
* **segmentation_map** set equal to 'True' will return the segmentation map. 

# Circular Aperture Photometry 
To perform circular photometry the following is used:   
**sep.sum_circle(data, x, y, r, err, mask, maskthresh, gain, subpix)**.
* **data** is the background subtracted image
* **x** and **y** are the centroids of objects
* **r** is the radii of apertures desired for photometry
* **err** is the error; usually given by the background rms
* **mask** is a mask array
* **maskthresh** determines the threshold in which pixels in "mask" are not considered in background estimation.
* **gain** is used to estimate Poisson noise error
* **subpix** is the subpixel sampling factor

# Elliptical Aperture Photometry
To perform elliptical aperture photometry on all sources one would use:   
**sep.sum_ellipse(data, x, y, a, b, theta, r, err, mask, maskthresh, gain, subpix)**   
The only difference here is that one inputs the elliptical parameters determined in the object detection phase. The apertures to be used would be **a(b)** multiplied by **r** and positioned at **theta**, where **theta** is the angle in radians between the x and major axes. 

# MAG_AUTO Photometry.
To perform MAG_AUTO photometry where some sources require circular apertures and some require elliptical apertures given a certain criterion, one would use logic. The logic looks like:    
**sep.sum_ellipse(data, x, y, a, b, theta, r, err, mask, maskthresh, gain, subpix)**   
**use_circle = (np.sqrt(a * b)) < r_min**   
**sep.sum_circle(data, x, y, r, err, mask, maskthresh, gain, subpix)**.   
Here **r_min** is the minimum radius at which one would like to perform elliptical aperture photometry.
 


 
