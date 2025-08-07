# ImageProcessingFinalProject
Taking the approach from this DDColor: Towards Photo-Realistic Image Colorization via Dual Decoders and changing it to see if the results differ from the paper.

# What is Image Colorization
Like the name suggests, this task adds color to an image. It is Classic computer vision task and has great potential in many real-world applications
Given a grayscale image, colorization aims to recover its two missing color channels. Some examples of this include 
Legacy Photo Restoration,
Video Remastering, and 
Art Creation.

# DDColor
DDColor is a end-to-end method with dual decoders for image colorization. This approach includes a pixel decoder and a query-based color decoder. The former restores the spatial resolution of the image, while the latter utilizes rich visual features to refine color queries.

<img width="1010" height="415" alt="image" src="https://github.com/user-attachments/assets/6bb420db-d563-4cc8-878e-8379a469b30a" />
 Starting with the pixel decoder, it includes Four Stages that gradually expand the image resolution.
Each Stage contains an upsampling layer anda  shortcut layer.
Upsampling layera : simple layer with no weights that will double the dimensions of the input.
Shortcut Layer: skips some of the layers in the neural network and feeds the output of one layer as the input to the next layers.
The shortcut layer uses a convolution to integrate features from the corresponding stages of the encoder through shortcut connections

<img width="909" height="314" alt="image" src="https://github.com/user-attachments/assets/a610d95e-29a6-4ee5-959f-ae825392c1f7" />

The color decoder is composed of a stack of blocks, with each block receiving visual features and color queries as input. The color decoder block (CDB) is designed based on a modified transformer decoder
<img width="961" height="445" alt="image" src="https://github.com/user-attachments/assets/060e285d-ec12-4994-944f-8aa65d726346" />

The Fusion Module is a Lightweight module that combines the outputs of the pixel decoder and the color decoder to generate a color result.
The Inputs to the fusion module are the per-pixel image embedding from the pixel decoder and the embedding dimension and semantic-aware color embedding from the color decoder
<img width="736" height="408" alt="image" src="https://github.com/user-attachments/assets/aa5fc37b-0b52-45c3-8d86-016b81c892f3" />

# Model 
DDColor, colorizes a grayscale image xL in an end-to-end fashion. We first extract its features using a backbone network, which are then input to the pixel decoder to restore the spatial structure of the image. Concurrently, the color decoder performs color queries on visual features of different scales, learning semantic aware color representations. The fusion module combines the outputs of both decoders to produce a color channel output yˆAB. Finally, we concatenate yAB ˆ and xL along the channel dimension to obtain the final colorization result yˆ. (b) Structure of color decoder block. Taking image features and color queries as inputs, the color decoder block establishes the correlation between semantic and color representation by performing cross-attention, self-attention and feed forward operations.

<img width="1500" height="553" alt="image" src="https://github.com/user-attachments/assets/32f741c0-aae6-4293-a27a-2293e512d8c5" />


# Datasets and Metrics
The datasets used in this were ImageNet (50k Images), has been widely used by most existing colorization methods. This dataset was used for Training (testing). 
COCO-Stuff(5k Images) Contains a wide Variety of natural images. They test on the 5,000 images of the original validation set without fine-tuning.
ADE20k(2k Images) is composed of scene-centric images with large diversity. We test on the 2,000 images of validation set without fine-tuning.

Frechet Inception Distance (FID): measures the distribution similarity between generated images and ground truth images. <br />
Colorfulness Score (CF): reflects the vividness of generated images. <br />
Peak Signal-to-Noise Ratio (PSNR): although it is a widely held view that the pixel-level metrics may not well reflect the actual colorization performance
computes the peak signal-to-noise ratio, in decibels, between two images. This ratio is used as a quality measurement between the original and a compressed image


# Limitations and Modifications 
There are still failure cases when dealing with images with transparent/translucent objects.
Further improvement may require extra semantic supervision to help the network better understand such complex scenarios. Also, like most automatic colorization methods, our approach lacks user controls or guidance over the colors produced. 
Incorporating more user inputs, such as text prompts, color graffiti in the colorization process will be a future work.
What if we perform some Image processing transformation techniques? Will any improvements in the metrics be seen?

<img width="1500" height="352" alt="image" src="https://github.com/user-attachments/assets/f9c7658f-7000-4d04-8329-325325d8b8a6" />

# Experiments


Histogram Equalization: This technique adjusts the contrast of an image by using its histogram. To enhance the image's contrast, it spreads out the most frequent pixel intensity values or stretches out the intensity range of the image.
Laplacian Filter: detects sudden intensity transitions in the image and highlights the edges. It convolves an image with a mask [0,1,0; 1,− 4,1; 0,1,0] and acts as a zero crossing detector that determines the edge pixels.
Contrast Stretching: an image enhancement technique that attempts to improve the contrast in an image by `stretching' the range of intensity values. The dataset used for the experiments was ImageNet, which contained 5000 images. The hyperparameters used were
AdamW Optimizer, 1250 Iterations, and before the model trains, I passed the dataset through the transformations function (Histogram Equalization, Laplacian Filter, Contrast Stretching)

# Results

Baseline Model: PSNR: 25.2392, FID: 3.6586, CD: 37.6743 <br />
Histogram Equalization: PSNR: 24.9459, FID: 3.8231, CD: 37.6700 <br />
Laplacian Filter: PSNR: 25.0151, FID: 3.7869, CD: 38.1295 <br />
Contrast Stretching: PSNR: 23.8462, FID: 3.9141, CD: 37.4821 <br />

It seems like passing the entire dataset through a iamge tranformation did not change the baseline results too much. I suspect the model is finely tuned so that even if the image goes through modifications that it will extract important features to colorize the image properly,








