# AudioLDM 2

### Required flags on MacOS:
PHONEMIZER_ESPEAK_LIBRARY="/opt/homebrew/Cellar/espeak/1.48.04_1/lib/libespeak.1.1.48.dylib"
PYTORCH_ENABLE_MPS_FALLBACK=1





[![arXiv](https://img.shields.io/badge/arXiv-2308.05734-brightgreen.svg?style=flat-square)](https://arxiv.org/abs/2308.05734)  [![githubio](https://img.shields.io/badge/GitHub.io-Audio_Samples-blue?logo=Github&style=flat-square)](https://audioldm.github.io/audioldm2/)  [![Hugging Face Spaces](https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-Spaces-blue)](https://huggingface.co/spaces/haoheliu/audioldm2-text2audio-text2music)  

This repo currently support Text-to-Audio (including Music) and Text-to-Speech Generation. 

- [AudioLDM 2](#audioldm-2)
    - [Required flags on MacOS:](#required-flags-on-macos)
  - [TODO](#todo)
  - [Web APP](#web-app)
  - [Commandline Usage](#commandline-usage)
    - [Installation](#installation)
    - [Run the model in commandline](#run-the-model-in-commandline)
  - [Random Seed Matters](#random-seed-matters)
  - [Pretrained Models](#pretrained-models)
  - [Other options](#other-options)
  - [Cite this work](#cite-this-work)

<hr>

## TODO
- [x] Add the text-to-speech checkpoint
- [ ] Add the text-to-audio checkpoint that does not use FLAN-T5 Cross Attention
- [ ] Open-source the AudioLDM training code.
- [ ] Support the generation of longer audio (> 10s)
- [ ] Optimizing the inference speed of the model.
- [ ] Integration with the Diffusers library

## Web APP

1. Prepare running environment
```shell
conda create -n audioldm python=3.8; conda activate audioldm
pip3 install git+https://github.com/haoheliu/AudioLDM2.git
git clone https://github.com/haoheliu/AudioLDM2; cd AudioLDM2
```
2. Start the web application (powered by Gradio)
```shell
python3 app.py
```
3. A link will be printed out. Click the link to open the browser and play.

## Commandline Usage

### Installation
Prepare running environment
```shell
# Optional
conda create -n audioldm python=3.8; conda activate audioldm
# Install AudioLDM
pip3 install git+https://github.com/haoheliu/AudioLDM2.git
```

If you plan to play around with text-to-speech generation. Please also make sure you have installed [espeak](https://espeak.sourceforge.net/download.html). On linux you can do it by 
```shell
sudo apt-get install espeak
```

### Run the model in commandline
- Generate sound effect or Music based on a text prompt

```shell
audioldm2 -t "Musical constellations twinkling in the night sky, forming a cosmic melody."
```

- Generate sound effect or music based on a list of text

```shell
audioldm2 -tl batch.lst
```

- Generate speech based on (1) the transcription and (2) the description of the speaker

```shell
audioldm2 -t "A female reporter is speaking full of emotion" --transciption "Wish you have a good day"

audioldm2 -t "A female reporter is speaking" --transciption "Wish you have a good day"
```

Text-to-Speech use the *audioldm2-speech-gigaspeech* checkpoint by default. If you like to run TTS with LJSpeech pretrained checkpoint, simply set *--model_name audioldm2-speech-ljspeech*.

## Random Seed Matters

Sometimes model may not perform well (sounds wired or low quality) when changing into a different hardware. In this case, please adjust the random seed and find the optimal one for your hardware. 
```shell
audioldm2 --seed 1234 -t "Musical constellations twinkling in the night sky, forming a cosmic melody."
```

## Pretrained Models

You can choose model checkpoint by setting up "model_name":

```shell
# CUDA
audioldm2 --model_name "audioldm2-full-large-1150k" --device cuda -t "Musical constellations twinkling in the night sky, forming a cosmic melody."

# MPS
audioldm2 --model_name "audioldm2-full-large-1150k" --device mps -t "Musical constellations twinkling in the night sky, forming a cosmic melody."
```

We have five checkpoints you can choose:

1. **audioldm2-full** (default): Generate both sound effect and music generation. 
2. **audioldm2-full-large-1150k**: Larger version of audioldm2-full. 
3. **audioldm2-music-665k**: Music generation. 
4. **audioldm2-speech-gigaspeech** (default for TTS): Text-to-Speech, trained on GigaSpeech Dataset.
5. **audioldm2-speech-ljspeech**: Text-to-Speech, trained on LJSpeech Dataset.

We currently support 3 devices:
- cpu
- cuda
- mps ( Notice that the computation requires about 20GB of RAM. )

## Other options
```shell
  usage: audioldm2 [-h] [-t TEXT] [-tl TEXT_LIST] [-s SAVE_PATH]
                 [--model_name {audioldm2-full,audioldm2-music-665k,audioldm2-full-large-1150k,audioldm2-speech-ljspeech,audioldm2-speech-gigaspeech}] [-d DEVICE]
                 [-b BATCHSIZE] [--ddim_steps DDIM_STEPS] [-gs GUIDANCE_SCALE] [-n N_CANDIDATE_GEN_PER_TEXT]
                 [--seed SEED]

  optional arguments:
    -h, --help            show this help message and exit
    -t TEXT, --text TEXT  Text prompt to the model for audio generation
    --transcription TRANSCRIPTION
                        Transcription used for speech synthesis
    -tl TEXT_LIST, --text_list TEXT_LIST
                          A file that contains text prompt to the model for audio generation
    -s SAVE_PATH, --save_path SAVE_PATH
                          The path to save model output
    --model_name {audioldm2-full,audioldm2-music-665k,audioldm2-full-large-1150k,audioldm2-speech-ljspeech,audioldm2-speech-gigaspeech}
                          The checkpoint you gonna use
    -d DEVICE, --device DEVICE
                          The device for computation. If not specified, the script will automatically choose the device based on your environment. [cpu, cuda, mps, auto]
    -b BATCHSIZE, --batchsize BATCHSIZE
                          Generate how many samples at the same time
    --ddim_steps DDIM_STEPS
                          The sampling step for DDIM
    -gs GUIDANCE_SCALE, --guidance_scale GUIDANCE_SCALE
                          Guidance scale (Large => better quality and relavancy to text; Small => better diversity)
    -n N_CANDIDATE_GEN_PER_TEXT, --n_candidate_gen_per_text N_CANDIDATE_GEN_PER_TEXT
                          Automatic quality control. This number control the number of candidates (e.g., generate three audios and choose the best to show you). A Larger value usually lead to better quality with
                          heavier computation
    --seed SEED           Change this value (any integer number) will lead to a different generation result.
```

## Cite this work
If you found this tool useful, please consider citing

```bibtex
@article{liu2023audioldm2,
  title={{AudioLDM 2}: Learning Holistic Audio Generation with Self-supervised Pretraining},
  author={Haohe Liu and Qiao Tian and Yi Yuan and Xubo Liu and Xinhao Mei and Qiuqiang Kong and Yuping Wang and Wenwu Wang and Yuxuan Wang and Mark D. Plumbley},
  journal={arXiv preprint arXiv:2308.05734},
  year={2023}
}
```

```bibtex
@article{liu2023audioldm,
  title={{AudioLDM}: Text-to-Audio Generation with Latent Diffusion Models},
  author={Liu, Haohe and Chen, Zehua and Yuan, Yi and Mei, Xinhao and Liu, Xubo and Mandic, Danilo and Wang, Wenwu and Plumbley, Mark D},
  journal={Proceedings of the International Conference on Machine Learning},
  year={2023}
}
```

