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

**使用方式**：img2img —> 底部 —> Poor man's outpainting

扩展画幅不像不同的图形生成一样需要大量的步长。比较好的办法是先使用一个符合原始图片的Prompt。将Denoising和FCG scale调整到最大, steps调整为50-100，sampler选择euler ancestral 或者 DPM2 ancestral。

| 81 steps, Euler A                   | 30 steps, Euler A                     | 10 steps, Euler A                    | 80 steps, Euler A                   |
|-------------------------------------|---------------------------------------|--------------------------------------|-------------------------------------|
| ![](images/inpainting-81-euler-a.png) | ![](images/inpainting-30-euler-a.png) | ![](images/inpainting-10-euler-a.png) | ![](images/inpainting-80-dpm2-a.png) |

### 图片修补
**使用方式**：img2img —> inpainting -> 用画笔画出想要修复的区域.

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
**使用方式**：Script -> Prompt Matrix
将多个prompts使用`|`分隔开，系统将会产生一个逐个组合起prompt的图片.
举例来说，如果你使用 `a busy city street in a modern city|illustration|cinematic lighting` prompt，将会有四种可能的组合（第一个prompt将一直会被保留着）。


- `a busy city street in a modern city`
- `a busy city street in a modern city, illustration`
- `a busy city street in a modern city, cinematic lighting`
- `a busy city street in a modern city, illustration, cinematic lighting`

四个图片都会被按照上述顺序产生出来，他们都共享一样的种子值分别对应如下的prompt:
![](images/prompt-matrix.png)

另外一个例子如下, 这次我们有5个prompts和16个变种:
![](images/prompt_matrix.jpg)


### Stable Diffusion 向上缩放（放大）

放大模块使用RealESRGAN/ESRGAN遍历每一小块tile，使用img2img来优化它们。
你可以在其他项目中手动使用img2img处理每一小块tile达到一样的效果。

原始想法来自这里: https://github.com/jquesnelle/txt2imghd. 这是一个独立的实现内容。

**使用方式**：Extras ->选择 UpScaler -> 定义每个tile的大小。

推荐的参数：
 - Sampling method: Euler a
 - Denoising strength: 0.2, can go up to 0.4 if you feel adventureous

| 原始图片                                  | RealESRGAN                                  | Topaz Gigapixel                                         | SD upscale                                  |
|-------------------------------------------|---------------------------------------------|---------------------------------------------------------|---------------------------------------------|
| ![](images/sd-upscale-robot-original.png) | ![](images/sd-upscale-robot-realesrgan.png) | ![](images/sd-upscale-robot-esrgan-topaz-gigapixel.png) | ![](images/sd-upscale-robot-sd-upscale.png) |
| ![](images/sd-upscale-castle-original.png) | ![](images/sd-upscale-castle-realesrgan.png) | ![](images/sd-upscale-castle-esrgan-topaz-gigapixel.png) | ![](images/sd-upscale-castle-sd-upscale.png) |
| ![](images/sd-upscale-city-original.png)  | ![](images/sd-upscale-city-realesrgan.png)  | ![](images/sd-upscale-city-esrgan-topaz-gigapixel.png)  | ![](images/sd-upscale-city-sd-upscale.png)  |

### 注意力
圆括号() —> 增加注意力
方括号[]-> 减少注意力，
你可以结合多个括号使用

![](images/attention-3.jpg)

### 重播
使用Loopback功能可以自动是使你的输出图片作为下一个迭代的输入，等同于先保存第一步的输出图片，作为第二步的输入图片。Batch Count设置会控制迭代多少次。

在正常情况下，我们会自己选择下一次迭代的输入图片，而不是自动连续迭代，因此这个功能的实用性也许存疑，但是笔者本人已经成功使用它产出了许多非常不错的结果，要不是使用Loopback也许很难得到这样的结果。


例子: (经过挑选)

![](images/loopback.jpg)

原始图片由4chan上的匿名用户提供. 谢谢你，匿名用户.

### X/Y plot

通过变化的参数创建出一个图片的网格。通过选择X与Y中的类型，我们可以选择哪一些参数会被行和列所共享，我们也可以通过逗号分割把参数输入x/y的区域。在使用整数，浮点数时，支持使用范围，例子如下：

- `1-5` = 1, 2, 3, 4, 5
- `1-5 (+2)` = 1, 3, 5
- `10-5 (-3)` = 10, 7
- `1-3 (+0.5)` = 1, 1.5, 2, 2.5, 3

![](images/xy_grid-medusa.png)

这里显示的就是使用上述设置产出的图片:

![](images/xy_grid-medusa-ui.png)

### Textual Inversion
这个功能将让你能够使用预训练的textual inversion embeddings。
在原始网站上查看细节: https://textual-inversion.github.io/.
我使用了lstein的仓库来训练embedding: https://github.com/lstein/stable-diffusion; if
如果你想要训练自己的embedding,我推荐你在他的网站跟随下述指导.

如果想要使用预训练好的embeddings，在Stable Diffusion文件夹的根目录下创建`embeddings`文件夹，并且把embedding文件放进去。
他们必须是.pt文件，大小在5kb左右，每一个文件只包含一种embedding，文件名将会用来作为prompt中使用的提示词.

举例来说，我花费了5000步训练了一个embedding: https://files.catbox.moe/e2ui6r.pt; 它没有产生特别好的效果，但是可以看出它起作用了.
把它下载下来并且重命名为`Usada Pekora.pt`,并且把它放到`embeddings` 文件夹即可在prompt中使用Usada Pekora作为关键词.

![](images/inversion.png)

### 调整大小
我们在img2img模式下有三个选项来调整图片大小:

- Just resize - 简单的把原始图片缩放到目标分辨率, 结果很可能是错误的长宽比例
- Crop and resize - 将原始图片保留长宽比缩放，裁掉多余部分
- Resize and fill - 将原始图片保留长宽比缩放, 将空白区域用原始图片中的行/列填充

例子如下:
![](images/resizing.jpg)

### 选择采样方法
我们可以在text2img中选择多个采样方法中的一个:

![](images/sampling.jpg)

### 种子值缩放
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
