---
title: "The relative non-importance of color"
date: 2021-02-13
draft: false
tags: ["art", "philosophy"]
---
# Introduction
Quick! Describe this!
![A multi-colored bird](https://cdn.download.ams.birds.cornell.edu/api/v1/asset/202984001/1200)
When asked to describe something, our minds jump quickly to visual information. A basic visual description will include color and brightness. For instance, I would say the image contains "a bright yellow, green, and blue bird with dark patches." Formally, brightness we call "luminance" or "luma." Independent of luminance, color we call "chrominance" or "chroma."
# JPEG compression
As it turns out, humans have finer spatial sensitivity to luminance than to chrominance. As a practical example, let's take the JPEG compression process.

To compress an image under JPEG, we first convert from the familiar RGB (red, green, and blue) to the YCbCr color space.[^1] Like RGB, YCbCr has 3 components: Y for luminance, Cb for blue(-difference) chrominance, and Cr for red(-difference) chrominance. The YCbCr covers the RGB color space; each RGB value has a YCbCr value according to [this formula](https://en.wikipedia.org/wiki/YCbCr#JPEG_conversion).
[^1]: You might see YCbCr also spelled like Y′CbCr. For the purposes of this post, the difference between Y and Y′ doesn't matter.

Now to actually compress the data, we keep the luminance channel and down-sample the chrominance channels. [Typically, we down-sample 2:1 horizontally and 2:1 or 1:1 vertically.](http://www.robertstocker.co.uk/jpeg/jpeg_new_8.htm)

For example, starting from a 12x12 pixel image, we keep our luminance channel 12x12 values. We then down-sample (2:1 horizontally and 2:1 vertically) our chrominance channels to 6x6 values each. In the resulting 6x6 values, each chrominance value covers 2x2 pixels in the compressed image.

Keeping the luminance at the same resolution and dropping the resolution of the chrominance lets us significantly reduce the memory size of the image, without significantly reducing the perceived quality of it. Without looking very closely, you probably don't notice a difference between a lossless (e.g. PNG) and a JPEG-compressed image. As a sniff test, 

![A](https://matthews.sites.wfu.edu/misc/jpg_vs_gif/JpgCompTest/orig.png)
![B](https://matthews.sites.wfu.edu/misc/jpg_vs_gif/JpgCompTest/icdimq1.jpg)

which one of the images taken from [here](https://matthews.sites.wfu.edu/misc/jpg_vs_gif/JpgCompTest/) uses JPEG compression? Learn how to inspect elements with your browser to find out!
# Conclusion
In a [previous post]({{< ref "color.md" >}}), I made the point that people experience color differently. As a more common example, I'll point out that [roughly 1 in 12 men](https://www.colourblindawareness.org/colour-blindness) experience some form of color blindness. As it turns out, both in sensitivity and universality, color doesn't have the importance I thought it did after all.
