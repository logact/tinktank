## section 1
1. raster displays show images as rectangular arrays of pixels . 
2. whats the array store?
	1. mixing varing intensities of red,blue,green light.
3. Even in televisions, the display rarely has the same number of pixels as the image being displayed.
4. It’s best to think of a raster image as a device-independent description of the image to be displayed, and the display device as a way of approximating that ideal image.
5. Other way described the images besides using array of pixels - vector image
6. how vector image store the image?
	1. storing descriptions of shapes—areas of color bounded by lines or curves.
	2. how to srore in program?
	3. In essence this amounts to storing the instructions for displaying the image rather than the pixels needed to display it.
7. whats the advcantage of the vector images?   the vector images are resolution independent and can be display well on very heigh resolution devices.
8. what's the disadvatange of the vector images? the real-time and the efficency because of it is to describe the image shape so it must be rasterized before they can be display
9. so the vector usually used for text diagrams and mechanical drawings. and other applications where crispness and  precisions are import  and photographic images and complex shading aren't needed.

## seciotn 2
raster devices
Categorize
Output 
- display 
	- LCD
	- LED
- Hardcopy
	-  ink-jet printer
	- dye submlimation printer
- input
	- 2D arrays sensor: digtal camera
	- 1D array sensor: flatbed scanner
1. LCD use  transmissive light and itself don't emit light ,it's molecular  structe enbales it to rotate the prolarization of light.
2. the degree of the rotate can be adjusted by an applied voltge.
3. if the voltage is set so that the liquid crystal ratates the polarization by 90 degrees ,then all light pass and the pixel is fully on ,it's intensity achieve the maximum.
4. the physical display's resolution is fixed,the different size pixels images how to display ?
5. there are two types Hardcopy devices: 
6. binary device (ink-jet printer) ,it can't create continues tone color,at each grid it depoist the pigment or not.
7. continues tone device(thermal dy transfer) 
8. Since higher temperatures cause more dye to be transferred, the amount of each dye deposited at each grid position can be controlled, allowing a continuous range of colors to be produced
9. Unlike displays, the resolution of printers is described in terms of the pixel density instead of the total count of pixels.
10. if a dye transfer printer that has elements spaced 300 per inch we say it has a resolution of 300 ppi
11. but for the binary printer like ink-jet we use the dpi(dots per inch)
12. next issue is  about input device
13. the digital camera work by using a color filter or mosaic to allow each pixel to see only red,green,or blue light,leaving the image processing software to fill in the missing values in a process known as demosaicking (why do this?filter the color and leaving the single color)
14. and other camera use three array to store the threee primitive color speratly.
15. The resolution of a camera is determined by the fixed number of pixels in the array and is usually quoted using the total count of pixels

## section 3
1. there are different input and output device ,we need a abstraction that is indepent of the device
2. the form of two-dimensional distribution of light  energy
3. the ligtht emitted from the monitor and the light falling on the monitor
4. a pixel from a camera or scanner is a measurement of the average color of the image over some small area around the pixel
5. a point sample
6. Images stored with floating-point numbers, allowing a wide range of values,are often called high dynamic range(HDR) images
7. fixed-range,or low dynamic range(LDR) images stored  with integers
8. The human perception of intensity is nonlinear!
9. two key issues that must be understood to produce correct images on monitors
10. monitor is not nonlinear with respect to input 
11. displayed intensity = (maximum intensity)aγ the gamma value convert the linear input to nonlinear value
## section3
Alpha Compositing

1. how to handle the transparent or opaque or partially display?
2. the answer is compositing the different colors 
3. c = αcf + (1 − α)cb.
4. there are a variety of situations where α is used differently 
5. Most RGB image formats use eight bits for each of the red, green, and blue channels
6. To reduce the storage requirement, most image formats allow for some kind of compression
7. At a high level, such compression is either lossless or lossy
8. poplular image store formats
	1. jpeg  lossy 
	2. tiff lossless
	3. ppm lossless
	4. png lossless
