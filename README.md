# artistic-style-transfer
METHODOLOGY
Part 1: Simple Image Enhancement
1.1. Artistic Enhancement:
-
-
-
-
-
1.1.1 Shadow-Map generation
In the shadow map generation, each pixel is assigned a flag to denote
whether it belongs to a light or shadow area (known as the shadow map).
The input image is converted to HSI using the formula mentioned per the
assignment question.
The ratio map for each pixel is calculated from the H and I values.
We calculate the shadow map based on the
1.1.2. Line Draft generation
Image is initially converted to grayscale.
Bilateral Filtering
A bilateral filter is used for smoothening images and reducing
-
noise, while preserving edges.
- n Gaussian smoothing we take a weighted average of pixel values
in the neighborhood. The weights are inversely proportional to the
distance from the center of the neighborhood. Besides these
spatial weights, the bilateral filter adds a tonal weight such that
pixel values that are close to the pixel value in the center are
weighted more than pixel values that are more different.-
-
This tonal weighting makes the bilateral filter capable of
preserving edges (significant differences in tonal value) while
smoothing in the more flat regions (small tonal differences).
Edge Detection
- We Convolve the bilateral filtered image with the Sobel x and
Sobel Y filters separately to obtain the gradients(I X and I Y ) of the
Image.
-
Edge Map, E
= I 2X + I 2Y
-
Thresholding
- We apply a thresholding operation to convert the grayscale edge
map to a binary image with only black and white pixels.
- This step helps emphasize the lines and remove unnecessary
details.
- The output of this stage forms the Line Draft of the Image Line
Draft,
- LD(x, y) =
1, if E(x, y) ≥ T
0, otherwise
- Here, T is the threshold whose value can range between [0,255].
1.2. Colour Adjustment Step:
-
To achieve a painting-like intense and emotional appearance and to
feature vivid colours with strong contrast, we create a chromatic map by
removing the influence of lightness. Specifically, after decomposing the
input image in the LAB color space, its chromatic map can be obtained by
merging the original A and B channels with a neutral light channel set as
the mid-value of the whole range in that image. Using this map, the R, G,
and B colour components of each pixel are enhanced,
SI′ (x, y) = SI(x, y) · [1 + tanh(ρ · (CM(x, y) − 128))]/2
-
Where SI(x, y) is the intensity of the Shadowed Image (obtained after
section 1.1.1) at pixel (x, y)
- CM(x, y) is the colour of the corresponding pixel in the chromatic map
(Convert the chromatic map into RGB and use the corresponding colour).
1.3 Saturation Correction
We perform linear saturation correction with the appropriate saturation
scale
- SIˆ = saturation correction(SI′ )
-
saturation correction(SI′ )= A*saturation(image) + BWhere A and B are hyperparameters that control the saturation of the level (after
converting the image to HSI.
Part 2: Quantized Rendering
2.1 MEDIAN FILTERING
The Median Cut Algorithm is a technique used to create a compact
representation of colors from an image, forming what's called a colormap. The idea is to
group similar colors together so that they can be represented by fewer entries in the
colormap, reducing memory usage while preserving the image's overall appearance.
​
​
​
​
Initial Box: The algorithm starts with a large rectangular box containing all the colors in
the image. The number of different colors in this box depends on the color resolution
used in the image.
Iteration (Splitting): In each iteration, the algorithm shrinks the box to fit around the colors
it contains. Then, it decides which direction to split the box. This splitting is done based
on the longest dimension of the box. It sorts the colors along this dimension and divides
them into two halves at the median point. This creates two smaller boxes.
Recursive Splitting: The above step is repeated recursively for each new box created.
This subdivision continues until a number of smaller boxes, denoted as K, is reached.
Representative Colors: For each of these smaller boxes, a representative color is
computed by averaging the colors contained in that box. These representative colors
form the colormap.
The algorithm aims to reduce the number of unique colors in the image while preserving
its overall visual quality. It's a way of compressing the color information of an image by
creating a smaller set of representative colors that still capture the essence of the
original image.
2.2 DITHERING
Floyd-Steinberg dithering is a technique used to simulate more colors than are
actually available in a limited color palette, like in old computer screens or printers. It's a
way of tricking our eyes into seeing more colors by using patterns of dots.
​
​
​
Adding Noise: One way to start is by adding a tiny bit of random noise to the original
image. This noise introduces small variations in intensity.
Quantization: Next, you "quantize" the modified image. This means that you round the
pixel intensities to the nearest available color in your limited palette.
Error Propagation: Now comes the clever part. When you round a pixel's intensity to a
limited color, there's usually a difference or "error." This error is then distributed or"propagated" to nearby pixels. Those nearby pixels get adjusted slightly to help correct
for the quantization error.
​
The result is an image that uses the available colors in a smart way to create the illusion of
more colors. Our eyes blend these dots together, and we perceive a wider range of colors than
what's actually present in the color palette.
Part 3: Artistic Style Transfer
Our goal here is to reproduce the style of the artistic image obtained in the previous
section to another image of similar background. We do this by using the LAB space of both the
images.
There are two ways by which we perform this style transfer:
3.1 Global Image Matching
We use a unique way of looking at colors called "lαβ space." This way, we can ensure that
changes in one part of a color don't mess up the other parts.
In this lαβ space, colors are separated into different channels. One is for how bright or dark a
color is (luminance), and the other is for color tones. So if we change one color, it won't spoil the
others too much. This space is like using different containers for different types of colors.
Now, to make the colors from the colorful picture fit nicely into the black-and-white picture, we
match them based on how bright they are and how they fit into their surroundings. We look at
the color itself and the colors around it.
Sometimes, the black-and-white picture might have different brightness levels than the colorful
one. We tweaked the brightness a bit to make them fit better. It's like adjusting the lighting so
they look more alike.
The important parts are usually related to the brightness. So, we can check only some of the
colors in the colorful picture to make a good match. We can pick about 200 colors and still get a
nice variety.
For each spot in the black-and-white picture, we find the best matching color from the colorful
one. We look at both brightness and how the colors around it behave. Once we find the best
match, we use the color tones from the colorful picture but keep the brightness from the
black-and-white one.3.2 Swatches
We use manual intervention by selecting areas of image that have high visual
correspodance and then proceed to transfer the color between the corresponding
swatches.
We transfer the color components based on the error value that is calculated
based on the following equation:
E (Ng Ns) =∑ ( I(p) – S (p ) )^2 ,p ∈ N
where I is the greyscale image, S is the luminance channel of the
colorized swatch and p are the pixels in these neighborhoods.
