# DimensionX: Create Any 3D and 4D Scenes from a Single Image with Controllable Video Diffusion

[**Paper**](https://arxiv.org/abs/2411.04928) | [**Project Page**](https://chenshuo20.github.io/DimensionX/) | [**Video**](https://youtu.be/ViDQI1HMY2U?si=f1RGd82n6yj6TOFB)

Official implementation of DimensionX: Create Any 3D and 4D Scenes from a Single Image with Controllable Video Diffusion

[Wenqiang Sun](https://github.com/wenqsun), [Shuo Chen](https://chenshuo20.github.io/), [Fangfu Liu](https://liuff19.github.io/), [Zilong Chen](https://scholar.google.com/citations?user=2pbka1gAAAAJ), [Yueqi Duan](https://duanyueqi.github.io/), [Jun Zhang](https://eejzhang.people.ust.hk/), [Yikai Wang](https://yikaiw.github.io/)

Abstract: *In this paper, we introduce DimensionX, a framework designed to generate photorealistic 3D and 4D scenes from just a single image with video diffusion. Our approach begins with the insight that both the spatial structure of a 3D scene and the temporal evolution of a 4D scene can be effectively represented through sequences of video frames. While recent video diffusion models have shown remarkable success in producing vivid visuals, they face limitations in directly recovering 3D/4D scenes due to limited spatial and temporal controllability during generation. To overcome this, we propose ST-Director, which decouples spatial and temporal factors in video diffusion by learning dimension-aware LoRAs from dimension-variant data. This controllable video diffusion approach enables precise manipulation of spatial structure and temporal dynamics, allowing us to reconstruct both 3D and 4D representations from sequential frames with the combination of spatial and temporal dimensions. Additionally, to bridge the gap between generated videos and real-world scenes, we introduce a trajectory-aware mechanism for 3D generation and an identity-preserving denoising strategy for 4D generation. Extensive experiments on various real-world and synthetic datasets demonstrate that DimensionX achieves superior results in controllable video generation, as well as in 3D and 4D scene generation, compared with previous methods.*

<p align="center">
    <img src="assets/file/teaser.png">
</p>




## Todo List
- [x] Release part of model checkpoints (S-Director).
- [ ] Release all model checkpoints.
    - [ ] The rest S-Directors
    - [ ] T-Director
    - [ ] Long video generation model (145 frames)
    - [ ] Video interpolation model
- [ ] 3dgs optimization code
- [ ] Identity-preserving denoising code for 4D generation
- [ ] Training dataset

## Model checkpoint

We have released part of our model checkpoint: [S-Diretor](https://drive.google.com/file/d/1zm9G7FH9UmN390NJsVTKmmUdo-3NM5t-/view?usp=drive_link)

## Inference code
For better result, you'd better use VLM to caption the input image.

```python
import torch
from diffusers import CogVideoXImageToVideoPipeline
from diffusers.utils import export_to_video, load_image

pipe = CogVideoXImageToVideoPipeline.from_pretrained("THUDM/CogVideoX-5b-I2V", torch_dtype=torch.bfloat16)
lora_path = "your lora path"
lora_rank = 256
pipe.load_lora_weights(lora_path, weight_name="pytorch_lora_weights.safetensors", adapter_name="test_1")
pipe.fuse_lora(lora_scale=1 / lora_rank)
pipe.to("cuda")


prompt = "An astronaut hatching from an egg, on the surface of the moon, the darkness and depth of space realised in the background. High quality, ultrarealistic detail and breath-taking movie-like camera shot."
image = load_image(
    "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/diffusers/astronaut.jpg"
)
video = pipe(image, prompt, use_dynamic_cfg=True)
export_to_video(video.frames[0], "output.mp4", fps=8)
```




## Method
Our framework is mainly divided into three parts. (a) Controllable Video Generation with ST-Director. We introduce ST-Director to decompose the spatial and temporal parameters in video diffusion models by learning dimension-aware LoRA on our collected dimension-variant datasets.  (b) 3D Scene Generation with S-Director. Given one view, a high-quality 3D scene is recovered from the video frames generated by S-Director.  (c) 4D Scene Generation with ST-Director. Given a single image, a temporal-variant video is produced by T-Director, from which a key frame is selected to generate a spatial-variant reference video. Guided by the reference video, per-frame spatial-variant videos are generated by S-Director, which are then combined into multi-view videos. Through the multi-loop refinement of T-Director, consistent multi-view videos are then passed to optimize the 4D scene.

<p align="center">
    <img src="assets/file/pipeline.png">
</p>


## Notion
From ReconX to DimensionX, we are conducting research about X! 

Our X Family coming soon ...


## Acknowledgement
- [CogVideoX](https://github.com/THUDM/CogVideo)
- [ReconX](https://github.com/liuff19/ReconX)


## BibTeX
