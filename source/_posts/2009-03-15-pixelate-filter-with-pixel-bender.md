---
layout: post
title: "Pixelate filter with Pixel Bender"
date: 2009-03-15 00:00
comments: true
categories: [experiment, filter, pixel bender]
---

{% img left /images/posts/2009/pixelate-filter.jpg Pixelate filter %}
Well, I finally found some time to dive into the Pixel Bender pool and start playing around with that new technology
that came from Adobe-land. And this is what I came up with for my very first attempt: nothing too fancy, just the Pixel
Bender version of the well known ”Pixelate” PhotoShop filter. Give it a whirl by adjusting the pixel size with the
slider. Flash Player 10 required of course.

----

Here's a working example. Just play around with the `Pixel size` slider.
{% swfobject /labs/pixel_bender/PixelBender.swf width:498px height:402px %}{% endswfobject %}

You can [check the ActionScript source code][1] and here comes the Pixel Bender kernel source:

``` c Pixelate kernel
    <languageVersion : 1.0;>

    kernel Pixelate
    <
       namespace : "net.riastar.shader";
       vendor : "RIAstar";
       version : 1;
       description : "pixelates an image";
    >
    {
       input image4 src;
       output pixel4 dst;

       parameter float size
       <
          minValue: 1.0;
          maxValue: 20.0;
          defaultValue: 4.0;
       >;

       void evaluatePixel() {
          float2 pos = outCoord();
          float center = (size + 1.0) / 2.0;

          dst = sampleNearest(src, float2(
             pos.x - mod(pos.x, size) + center,
             pos.y - mod(pos.y, size) + center
          ));

          //make sure not to sample outside the borders
          if (dst.a &lt;= 0.0)
             dst = sampleNearest(src, float2(
                pos.x - mod(pos.x, size),
                pos.y - mod(pos.y, size)
             ));
       }
    }
```

I had quite a hard time getting it to work in ActionScript, apparently because I installed the Flex 3.3 SDK just a week
ago. Just putting a declaration of a `flash.display.Shader` class in my code, was sufficient to stop all code from
executing, although the VM did not crash. I also got no error message whatsoever.

So after a whole hour of trying everything I could think of, I told the compiler to use the old 3.2 SDK and tadaa:
working like a charm. Is it a bug in 3.3 (which was meant to be a bugfix release in the first place) or did I do
something wrong?

[1]: /labs/pixel_bender/srcview/index.html
