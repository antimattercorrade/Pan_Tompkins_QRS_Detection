# Pan Tompkins QRS Detection ⭐

One of the fundamental challenges in the field of image processing and computer vision is image denoising, where the underlying goal is to estimate the original image by suppressing noise from a noise-contaminated version of the image. Image noise may be caused by different intrinsic (i.e., sensor) and extrinsic (i.e.environment) conditions which are often not possible to avoid in practical situations. Therefore, image denoising plays an important role in a wide range of applications such as image restoration, visual tracking, image registration, image segmentation, and image classification, where obtaining the original image content is crucial for strong performance. While many algorithms have been proposed for the purpose of image denoising, the problem of image noise suppression remains an open challenge, especially in situations where the images are acquired under poor conditions where the noise level is very high. We will be exploring non-local means algorithm for image denoising in this repository.


## Non-Local Means 🔥

The general idea of the Non-Local Means (NLM) algorithm is to estimate the actual pixel value in the denoised image using a weighted mean of pixel neighborhoods having some similarity in the noisy image. Ideally pixel similarity should be searched in the complete image but for high resolution images, this procedure can take quite some time. So, pixel similarity is searched only in some neighborhood of each pixel, defined by the variable `big_window`. Also, to increase the performance while calculating the similarity of each pixel, instead of considering only one pixel, a small area of pixels is chosen around it, given by the `small_window` parameter. 

### Implementation Details

Gaussian and salt & pepper noises are added to the input image using the standard procedures and the noisy image is passed as a parameter to the NLMeans’ `solve()` function. Along with the noisy image, the `small_window`, `big_window` and `h` (filtering parameter) are also given. A result image is initialised as a numpy array of zeros having the same dimensions as the input noisy image. To calculate the similarity values by considering a `big_window` * `big_window` neighborhood around each pixel, the noisy image is padded along each of the four boundaries. The padded width is `big_window/2` since the neighborhood is created by considering each pixel in consideration as the center. The mode of the padding is `reflect` to account for better similarity values for the boundary pixels.
  
Since, we want a `small_window` * `small_window` patch for similarity calculation, they are preprocessed by traversing the padded image and storing a 2D array of size `small_window` * `small_window` for each pixel in the input noisy image.  Now, each pixel of the input noisy image is traversed and a `big_window` * `big_window` neighborhood around that pixel is obtained by indexing the previously preprocessed array. So, in each iteration of the loops we get, a 2D `small_window` * `small_window` patch for current pixel, a 4D `big_window` * `big_window` neighborhood around the current pixel having each entry as a 2D `small_window` * `small_window` patch. 

Now, we have all the required elements to calculate the weighted mean for the current pixel. Each pixel is traversed in the calculated 4D `big_window` * `big_window` neighborhood. For each such pixel we get a 2D `small_window` * `small_window` patch and the squared norm of this 2D window and current pixel’s 2D window is taken. This norm is multiplied by `-1/(size of neighborhood window)` and its exponential is taken. This value is used to calculate the numerator of the expected NLMeans value for each pixel (as defined in the paper `A non-local algorithm for image denoising`). The denominator of this fraction is calculated by the summation of such exponential terms.

### Parameters

**Sigma_h:** The `sigma_h` parameter is the filtering parameter which determines the impact of each exponential value in the calculations, i.e. the impact of the similarities as a function of distance. It is used to control the weight of the patch difference values. Optimising `sigma_h` value can lead to significant improvements in the quality of  the denoised image produced.

**Small_window:** The `small_window` parameter is used to create a small patch around each pixel to help find the similarity score between the pixels.  To calculate the weighted value for a pixel, we search for similar pixels, so as to find the expectation of the real value of the pixel without noise. This can be done using the pixel values at each pixel but to increase the accuracy of the expected value, a small patch is created around each pixel and its value if taken to be the weighted average inside this small patch. This is so that a better similarity score can be calculated and hence the resulting image.

**Big_window:** The `big_window` parameter is used to create a neighborhood of pixels around each pixel to search for similar values. Ideally, to find the expected value of each pixel in the denoised image, the weighted average for all pixels in the image can be calculated. But this approach is not suitable for larger sized images and suffers from large 
computation times. Thus, to optimise the calculation a neighborhood is created within which the weighted average of similarity values is taken to calculate the expected value.

## Results :bar_chart:

##### Metrics (MSE and PSNR) obtained for Noisy Image, SKImage, NL Means and Gaussian filtering for all the 10 images

Note: Better NLM values can be obtained using different salt_and_pepper_h value and gaussian_h_value.

**Mean Squared Error (MSE)**

SNP Noise             |  Gaussian Noise 
:-------------------------:|:-------------------------:
<img src="./Images/MSE_SnP.png" width="500"> | <img src="./Images/MSE_Gaussian.png" width="500">

**Peak Signal to Noise Ratio (PSNR)**

SNP Noise              |  Gaussian Noise
:-------------------------:|:-------------------------:
<img src="./Images/PSNR_SnP.png" width="500"> | <img src="./Images/PSNR_Gaussian.png" width="500">


## Observations :notebook:

The Non Local Means algorithm works better than the Gaussian Filtering algorithm for all of the images for both the salt and pepper noise and gaussian noise. For the taken values of `salt_and_paper_h` and `gaussian_h`, the Non Local Means algorithm produces images with lower MSE and higher PSNR than the Gaussian Filtering algorithm. The only exceptions being image 2 and 10 in which the MSE and PSNR of the Gaussian Filtering are quantitatively better but the metrics and visual quality of the image produced by Non Local Means and Gaussian Filtering are comparable. 

The stated deviation for both the images may be because for the values of `h` parameter taken the Non Local Means algorithm cannot find much similarity in a neighborhood defined by `big_window`. This can be rectified by tuning the parameters for the specific image to produce better results or performing a full search of the image for similarity values instead of just a neighborhood defined by `big_window`.

## Instructions to Run :runner:

* The dataset is being downloaded from the given hashed link. However if at a later point of time, the dataset is not available in the input file, kindly use the image dataset present in the repository in place of the downloaded data. 

* To run, open the colab file and select the image using the slider given. Then run the corresponding cells to get the results

## References and Credits 💳

[1] Buades, A., Coll, B., & Morel, J. M. (2005). A non-local algorithm for image denoising. 2005 IEEE Computer Society Conference on Computer Vision and Pattern Recognition (CVPR'05), 2, 60-65. doi: 10.1109/CVPR.2005.38.

[2] Ingraham, K. (2017, February 4). Salt & Pepper Noise and Median Filters, Part II. blog.kyleingraham.com. Retrieved August 18, 2021, from https://blog.kyleingraham.com/2017/02/04/salt-pepper-noise-and-median-filters-part-ii-the-code/

[3] Mahmoudi, M., & Sapiro, G. (2005, December 12). Fast Image and Video Denoising via Nonlocal Means of Similar Neighborhoods. IEEE Signal Processing Letters, 12(12), 839-842. doi:10.1109/lsp.2005.859509

[4] Palou, G. (2015, July 7). An approach to Non-Local-Means denoising. http://dsvision.github.io/. http://dsvision.github.io/an-approach-to-non-local-means-denoising.html

