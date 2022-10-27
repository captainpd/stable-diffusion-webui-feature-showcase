# Stable Diffusion web UI 中文
这是一个针对[Stable Diffusion web UI](https://github.com/AUTOMATIC1111/stable-diffusion-webui)的特性介绍页面。
文档中所有提到的例子都不是举特例，除了那些特别提到的例子.

### Outpainting 扩展画幅

Outpainting 会延展初始图片，并修补空白的区域.

案例:

| 原始图片                      | 扩展一次                     | 再次扩展           |
|------------------------------|------------------------------|------------------------------|
| ![](images/outpainting-1.png) | ![](images/outpainting-2.png) | ![](images/outpainting-3.png) |

原始图片由4chan上的匿名用户提供. 谢谢你，匿名用户.

**使用方式**：img2img——底部——Poor man's outpainting

扩展画幅不像不同的图形生成一样需要大量的步长。比较好的办法是先使用一个符合原始图片的Prompt。将Denoising和FCG scale调整到最大, steps调整为50-100，sampler选择euler ancestral 或者 DPM2 ancestral。

| 81 steps, Euler A                   | 30 steps, Euler A                     | 10 steps, Euler A                    | 80 steps, Euler A                   |
|-------------------------------------|---------------------------------------|--------------------------------------|-------------------------------------|
| ![](images/inpainting-81-euler-a.png) | ![](images/inpainting-30-euler-a.png) | ![](images/inpainting-10-euler-a.png) | ![](images/inpainting-80-dpm2-a.png) |

### 图片修补
**使用方式**：img2img——inpainting——用画笔画出想要修复的区域.

![](images/inpainting.png)

修补图片的选项:
- 在网页编辑器中手绘。
- 在网页编辑器中擦除一部分图片并上传一张有透明度的图片.任何哪怕只有一点点透明的部分都会变成蒙版的一部分. 注意 [有一些网页编辑器](https://docs.krita.org/en/reference_manual/layers_and_masks/split_alpha.html#how-to-save-a-png-texture-and-keep-color-values-in-fully-transparent-areas)会默认把完全透明的部分作为黑色保存下来.
- 选择模式 (在图片下方) 选择"Upload mask" 并选择一个新的黑白图片作为蒙版(白色=修补区域).

##### 蒙版内容
蒙版内容类型会决定将要被修复的蒙版区域将要有什么内容替代。

| mask                                            | fill                                            | original                                            | latent noise                                            | latent nothing                                            |
|-------------------------------------------------|-------------------------------------------------|-----------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------------|
| ![](images/inpainting-initial-content-mask.png) | ![](images/inpainting-initial-content-fill.png) | ![](images/inpainting-initial-content-original.png) | ![](images/inpainting-initial-content-latent-noise.png) | ![](images/inpainting-initial-content-latent-nothing.png) |

##### 以完整分辨率修补图片
正常来说，修补图片的时候整个图片会被缩放为UI中所选择的最终尺寸。选择full resolution将会只缩放被蒙版选中的区域，并在处理完以后粘贴回原始图片
这将时你能修补很大幅的图片，并且可以让被修补的部分以大得多的分辨率被渲染出来。


| 输入图片                               |修补-普通                   | 修补-完整分辨率 
|-------------------------------------|----------------------------------|-----------------------------------|
| ![](images/inpaint-whole-mask.png)  | ![](images/inpaint-whole-no.png) | ![](images/inpaint-whole-yes.png) |



##### 蒙版模式
蒙版现在有两个模式:
- Inpaint masked - 只修补蒙版**内部**的图片
- Inpaint not masked -只修改蒙版**外部**的图片

##### 透明度蒙版

| 输入                        | 输出                        |
|------------------------------|-------------------------------|
| ![](images/inpaint-mask.png) | ![](images/inpaint-mask2.png) |


### Prompt 矩阵
Separate multiple prompts using the `|` character, and the system will produce an image for every combination of them.
For example, if you use `a busy city street in a modern city|illustration|cinematic lighting` prompt, there are four combinations possible (first part of prompt is always kept):

- `a busy city street in a modern city`
- `a busy city street in a modern city, illustration`
- `a busy city street in a modern city, cinematic lighting`
- `a busy city street in a modern city, illustration, cinematic lighting`

Four images will be produced, in this order, all with same seed and each with corresponding prompt:
![](images/prompt-matrix.png)

Another example, this time with 5 prompts and 16 variations:
![](images/prompt_matrix.jpg)

You can find the feature at the bottom, under Script -> Prompt matrix.

### Stable Diffusion upscale
Upscale image using RealESRGAN/ESRGAN and then go through tiles of the result, improving them with img2img.
Also has an let you do the upscaling part yourself in external program, and just go through tiles with img2img.

Original idea by: https://github.com/jquesnelle/txt2imghd. This is an independent implementation.

To use this feature, tick a checkbox in the img2img interface. Input image will be upscaled to twice the original
width and height, and UI's width and height sliders specify the size of individual tiles. Because of overlap,
the size of tile can be very important: 512x512 image needs nine 512x512 tiles (because of overlap), but only
four 640x640 tiles.

Rcommended parameters for upscaling:
 - Sampling method: Euler a
 - Denoising strength: 0.2, can go up to 0.4 if you feel adventureous

| Original                                  | RealESRGAN                                  | Topaz Gigapixel                                         | SD upscale                                  |
|-------------------------------------------|---------------------------------------------|---------------------------------------------------------|---------------------------------------------|
| ![](images/sd-upscale-robot-original.png) | ![](images/sd-upscale-robot-realesrgan.png) | ![](images/sd-upscale-robot-esrgan-topaz-gigapixel.png) | ![](images/sd-upscale-robot-sd-upscale.png) |
| ![](images/sd-upscale-castle-original.png) | ![](images/sd-upscale-castle-realesrgan.png) | ![](images/sd-upscale-castle-esrgan-topaz-gigapixel.png) | ![](images/sd-upscale-castle-sd-upscale.png) |
| ![](images/sd-upscale-city-original.png)  | ![](images/sd-upscale-city-realesrgan.png)  | ![](images/sd-upscale-city-esrgan-topaz-gigapixel.png)  | ![](images/sd-upscale-city-sd-upscale.png)  |

### Attention
Using `()` in prompt increases model's attention to enclosed words, and `[]` decreases it. You can combine
multiple modifiers:

![](images/attention-3.jpg)

### Loopback
A checkbox for img2img allowing to automatically feed output image as input for the next batch. Equivalent to
saving output image, and replacing input image with it. Batch count setting controls how many iterations of
this you get.

Usually, when doing this, you would choose one of many images for the next iteration yourself, so the usefulness
of this feature may be questionable, but I've managed to get some very nice outputs with it that I wasn't abble
to get otherwise.

Example: (cherrypicked result)

![](images/loopback.jpg)

Original image by Anonymous user from 4chan. Thank you, Anonymous user.

### X/Y plot
Creates a grid of images with varying parameters. Select which parameters should be shared by rows and columns using
X type and Y type fields, and input those parameters separated by comma into X values/Y values fields. For integer,
and floating ponit numbers, ranges are supported. Examples:

- `1-5` = 1, 2, 3, 4, 5
- `1-5 (+2)` = 1, 3, 5
- `10-5 (-3)` = 10, 7
- `1-3 (+0.5)` = 1, 1.5, 2, 2.5, 3

![](images/xy_grid-medusa.png)

Here's are settings that create the graph above:

![](images/xy_grid-medusa-ui.png)

### Textual Inversion
Allows you to use pretrained textual inversion embeddings.
See original site for details: https://textual-inversion.github.io/.
I used lstein's repo for training embdedding: https://github.com/lstein/stable-diffusion; if
you want to train your own, I recommend following the guide on his site.

To make use of pretrained embeddings, create `embeddings` directory in the root dir of Stable
Diffusion and put your embeddings into it. They must be .pt files about 5Kb in size, each with only
one trained embedding, and the filename (without .pt) will be the term you'd use in prompt
to get that embedding.

As an example, I trained one for about 5000 steps: https://files.catbox.moe/e2ui6r.pt; it does
not produce very good results, but it does work. Download and rename it to `Usada Pekora.pt`,
and put it into `embeddings` dir and use Usada Pekora in prompt.

![](images/inversion.png)

### Resizing
There are three options for resizing input images in img2img mode:

- Just resize - simply resizes source image to target resolution, resulting in incorrect aspect ratio
- Crop and resize - resize source image preserving aspect ratio so that entirety of target resolution is occupied by it, and crop parts that stick out
- Resize and fill - resize source image preserving aspect ratio so that it entirely fits target resolution, and fill empty space by rows/columns from source image

Example:
![](images/resizing.jpg)

### Sampling method selection
Pick out of multiple sampling methods for txt2img:

![](images/sampling.jpg)

### Seed resize
This function allows you to generate images from known seeds at different resolutions. Normally, when you change resolution,
the image changes entirely, even if you keep all other parameters including seed. With seed resizing you specify the resolution
of the original image, and the model will very likely produce something looking very similar to it, even at a different resolution.
In the example below, the leftmost picture is 512x512, and others are produced with exact same parameters but with larger vertical
resolution.

| Info                      | Image                         |
|---------------------------|-------------------------------|
| Seed resize not enabled   | ![](images/seed-noresize.png) |
| Seed resized from 512x512 | ![](images/seed-resize.png)   |

Ancestral samplers are a little worse at this than the rest.

You can find this ferature by clicking the "Extra" checkbox near the seed.

### Variations
A Variation strength slider and Variation seed field allow you to specify how much the existing picture should be altered to look
like a different one. At maximum strength you will get picture with Variation seed, at minimum -  picture with original Seed (except
for when using ancestral samplers).

![](images/seed-variations.jpg)

You can find this ferature by clicking the "Extra" checkbox near the seed.

### Styles
Press "Save prompt as style" button to write your current prompt to styles.csv, the file with collection of styles. A dropbox to
the right of the prompt will allow you to choose any style out of previously saved, and automatically append it to your input.
To delete style, manually delete it from styles.csv and restart the program.

### Negative prompt

Allows you to use another prompt of things the model should avoid when generating the picture. This works by using the
negative prompt for unconditional conditioning in the sampling process instead of empty string.

| Original                      | Negative: purple                | Negative: tentacles                |
|-------------------------------|---------------------------------|------------------------------------|
| ![](images/negative-base.png) | ![](images/negative-purple.png) | ![](images/negative-tentacles.png) |

### CLIP interrogator

Originally by: https://github.com/pharmapsychotic/clip-interrogator

CLIP interrogator allows you to retrieve prompt from an image. The prompt won't allow you to reproduce this
exact image (and sometimes it won't even be close), but it can be a good start.

![](images/CLIP-interrogate.png)

The first time you run CLIP interrogator it will download few gigabytes of models.

CLIP interrogator has two parts: one is a BLIP model that creates a text description from the picture.
Other is a CLIP model that will pick few lines relevant to the picture out of a list. By default, there
is only one list - a list of artists (from `artists.csv`). You can add more lists by doing the follwoing:

 - create `interrogate` directory in same place as web ui
 - put text files in it with a relevant description on each line

For example of what text files to use, see https://github.com/pharmapsychotic/clip-interrogator/tree/main/data.
In fact, you can just take files from there and use them - just skip artists.txt because you already have a list of
artists in `artists.csv` (or use that too, who's going to stop you). Each file adds one line of text to final description.
If you add ".top3." to filename, for example, `flavors.top3.txt`, three most relevant lines from this file will be
added to the prompt (other numbers also work).

There are settings relevant to this feature:
 - `Interrogate: keep models in VRAM` - do not unload Interrogate models from memory after using them. For users with a lot of VRAM.
 - `Interrogate: use artists from artists.csv` - adds artist from `artists.csv` when interrogating. Can be useful disable when you have your list of artists in `interrogate` directory
 - `Interrogate: num_beams for BLIP` - parameter that affects how detailed descriptions from BLIP model are (the first part of generated prompt)
 - `Interrogate: minimum descripton length` - minimum length for BLIP model's text
 - `Interrogate: maximum descripton length` - maximum length for BLIP model's text
 - `Interrogate: maximum number of lines in text file` - interrogator will only consider this many first lines in a file. Set to 0, default is 1500, which is about as much as a 4GB videocard can handle.

### Interrupt

Press the Interrupt button to stop current processing.

### 4GB videocard support
Optimizations for GPUs with low VRAM. This should make it possible to generate 512x512 images on videocards with 4GB memory.

`--lowvram` is a reimplementation of optimization idea from by [basujindal](https://github.com/basujindal/stable-diffusion).
Model is separated into modules, and only one module is kept in GPU memory; when another module needs to run, the previous
is removed from GPU memory. The nature of this optimization makes the processing run slower -- about 10 times slower
compared to normal operation on my RTX 3090.

`--medvram` is another optimization that should reduce VRAM usage significantly by not processing conditional and
unconditional denoising in a same batch.

This implementation of optimization does not require any modification to original Stable Diffusion code.

### Face restoration
Lets you improve faces in pictures using either GFPGAN or CodeFormer. There is a checkbox in every tab to use face restoration,
and also a separate tab that just allows you to use face restoration on any picture, with a slider that controls how visible
the effect is. You can choose between the two methods in settings.

| Original                | GFPGAN                         | CodeFormer                         |
|-------------------------|--------------------------------|------------------------------------|
| ![](images/facefix.png) | ![](images/facefix-gfpgan.png) | ![](images/facefix-codeformer.png) |


### Saving
Click the Save button under the output section, and generated images will be saved to a directory specified in settings;
generation parameters will be appended to a csv file  in the same directory.

### Correct seeds for batches
If you use a seed of 1000 to generate two batches of two images each, four generated images will have seeds: `1000, 1001, 1002, 1003`.
Previous versions of the UI would produce `1000, x, 1001, x`, where x is an image that can't be generated by any seed.

### Loading
Gradio's loading graphic has a very negative effect on the processing speed of the neural network. 
My RTX 3090 makes images about 10% faster when the tab with gradio is not active. By default, the UI
now hides loading progress animation and replaces it with static "Loading..." text, which achieves
the same effect. Use the `--no-progressbar-hiding` commandline option to revert this and show loading animations.

### Prompt validation
Stable Diffusion has a limit for input text length. If your prompt is too long, you will get a
warning in the text output field, showing which parts of your text were truncated and ignored by the model.

### Png info
Adds information about generation parameters to PNG as a text chunk. You
can view this information later using any software that supports viewing
PNG chunk info, for example: https://www.nayuki.io/page/png-file-chunk-inspector

### Settings
A tab with settings, allowing you to use UI to edit more than half of parameters that previously
were commandline. Settings are saved to config.js file. Settings that remain as commandline
options are ones that are required at startup.

### User scripts
If the program is launched with `--allow-code` option, an extra text input field for script code
is available in the bottom of the page, under Scripts -> Custom code. It allows you to input python
code that will do the work with the image.

In code, access parameters from web UI using the `p` variable, and provide outputs for web UI
using the `display(images, seed, info)` function. All globals from script are also accessible.

A simple script that would just process the image and output it normally:

```python
import modules.processing

processed = modules.processing.process_images(p)

print("Seed was: " + str(processed.seed))

display(processed.images, processed.seed, processed.info)
```

### UI config
You can change parameters for UI elements:
 - radio groups: default selection
 - sliders: defaul value, min, max, step 
 
The file is ui-config.json in webui dir, and it is created automatically if you don't have one when the program starts.

Some settings will break processing, like step not divisible by 64 for width and heght, and some, lie changing default
function on the img2img tab, may break UI. I do not have plans to address those in near future.

### ESRGAN
It's possible to use ESRGAN models on the Extras tab, as well as in SD upscale.

To use ESRGAN models, put them into ESRGAN directory in the same location as webui.py.
A file will be loaded as model if it has .pth extension. Grab models from the [Model Database](https://upscale.wiki/wiki/Model_Database).

Not all models from the database are supported. All 2x models are most likely not supported.
