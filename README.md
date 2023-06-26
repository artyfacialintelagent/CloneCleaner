# CloneCleaner

An extension for Automatic1111 to work around Stable Diffusion's "clone problem". It automatically modifies your prompts with random names, nationalities, hair style and hair color to create more variations in generated people.

# What it does

Many generations of model finetuning and merging have greatly improved the image quality of Stable Diffusion 1.5 when generating humans - but at the cost of overtraining and loss of variability. This manifests as "clones", where batch generations using the same or similar prompts but different random seeds often have identical facial features.

CloneCleaner adds randomized tokens to your prompt that varies the look of a person for every generated image seed. For example, one seed might get "Elsie from Australia, long waist-length updo ginger hair" and the next "Samreen from Bangladesh, medium shoulder-length frizzy coffee-colored hair". This makes every seed quite unique and effectively mitigates the "sameface" problem of many popular but heavily overtrained Stable Diffusion models. So it's basically wildcards, except the tiresome setup work has been done for you and you're ready to go - with lots of easy customization options to control the randomization.

A key detail is that the token randomization seed is (by default) identical to the main image seed - this ensures that the same "person" will be generated again if you modify the prompt or reload the metadata.

# Installation in Automatic1111

Enter this url **manually** in auto1111's extension tab:

https://github.com/artyfacialintelagent/CloneCleaner.git

This first release of CloneCleaner is a public beta and **currently only works for female characters**. Options for male (and indeterminate gender if I can get it to work) coming soon-ish!

# How it works

Prompts are randomized using wildcards, except they're hardcoded in the extension with logic to match ethnicity with typical names and common hair colors for each country, in order to get consistent appearance and quality of the generated images. Main steps:

1. Set the token randomization seed to the main image seed (or optionally a different random seed or a user-specified one).
2. Select a random REGION among the following: Europe (mainland incl. Russia), Anglosphere (US, Can, Aus, NZ, UK), Latin America, MENA (Middle-East & North Africa), Sub-Saharan Africa, East Asia or South Asia.
3. Select a random COUNTRY in that region - but note that CloneCleaner only has a sample of countries of each region, the database is not (yet) comprehensive.
4. Select a random FIRST NAME for people in that country.
5. Select a random MAIN HAIR COLOR (only black, brown, red, blonde, other), weighted for what is typical in that country. Then select a SPECIFIC HAIR COLOR with more "colorful" (sorry) language for more variability. Color tokens are carefully selected and limited using tricks like reduced attention and prompt editing to minimize "color bleeding" into the rest of your prompt.
6. Select random HAIR STYLE and HAIR LENGTH.
7. Assemble the prompt insert: "[FIRST NAME] from [COUNTRY], [HAIR LENGTH] [HAIR STYLE] [SPECIFIC HAIR COLOR] hair". Insert it at beginning or end of the prompt depending on user options
8. Iterate for remaining images of the batch.

Any of the above token components can be optionally excluded from the prompt randomization. There are also customization options to exclude specific regions, hair lengths and hair color.

# Sample images

The following images are produced using **consecutive seeds**, so they are **NOT CHERRY-PICKED** in any way. But to be fair, not all models work quite as well as these - some models are so overtrained that they just can't be saved. The sample PNGs hosted here on Github should contain full metadata, so you can download and inspect them in the PNGinfo tab, or send them to txt2img to reproduce them.

Right-click and select "Open image in a new tab" to view at 100%, or right-click and select "Save image as" to download.

### Absolute Reality v1

These images were created using the [Absolute Reality model](https://civitai.com/models/81458/absolutereality), from a simple test prompt I made up.

**Absolute Reality v1 (baseline model images)**
![absolutereality_seeds](https://github.com/artyfacialintelagent/CloneCleaner-dev/assets/137619889/04fe1106-8fcd-4a60-866d-03cf2636fa94)
![absolutereality1](https://github.com/artyfacialintelagent/CloneCleaner-dev/assets/137619889/698de3a0-2bb7-43b3-bd3e-7c1c75b38d61)

**Absolute Reality v1 + CloneCleaner (default settings)**
![absolutereality2](https://github.com/artyfacialintelagent/CloneCleaner-dev/assets/137619889/8efdffae-45f5-47e4-8544-8345435fe1ee)

**Absolute Reality v1 + CloneCleaner (East Asia only)**
![absolutereality3](https://github.com/artyfacialintelagent/CloneCleaner-dev/assets/137619889/7059ec65-1f25-4155-bab5-968f6291b994)

**Absolute Reality v1 + CloneCleaner (Anglosphere + Europe only, short hair only)**
![absolutereality4](https://github.com/artyfacialintelagent/CloneCleaner-dev/assets/137619889/149ee7fe-f745-4cf0-af48-46d6d54db59e)

### Photon v1

Using the prompt from the sample image hosted on the [Photon model page on Civitai](https://civitai.com/models/84728/photon), slightly modified to make it more SFW for Github.

**Photon v1 (baseline model images)**
![photon_seeds](https://github.com/artyfacialintelagent/CloneCleaner-dev/assets/137619889/4d66add9-fd6a-42b8-aea1-271e674af951)
![photon1](https://github.com/artyfacialintelagent/CloneCleaner-dev/assets/137619889/bc586d3f-d4f1-4ffa-9859-40ae2deb6660)

**Photon v1 + CloneCleaner (default settings)**
![photon2](https://github.com/artyfacialintelagent/CloneCleaner-dev/assets/137619889/3f2cac67-79a4-4067-8b52-57cda7c6bfdf)

**Photon v1 + CloneCleaner (Africa + South Asia only, no blonde or "other" hair color)**
![photon3](https://github.com/artyfacialintelagent/CloneCleaner-dev/assets/137619889/3d8a6379-1bd8-4c59-8059-26241530d254)

**Photon v1 + CloneCleaner (Europe only, reddish hair only)**
![photon4](https://github.com/artyfacialintelagent/CloneCleaner-dev/assets/137619889/69a295f3-4347-4135-820c-ed62ee3cba0e)

# Some tips

I am very happy with how well this simple prompt modification scheme works. But the best part about CloneCleaner is how it made me completely re-evaluate my opinion of many models, mostly positively. So be sure to retest your models using CloneCleaner - they may yet surprise you!

I recommend using **simple prompts** (< 50 tokens), **simple negatives** (< 30 tokens) and **limited attention weighting** (never > :1.2, except as noted below). An effective minimal negative prompt appears below. Just start with this as a basis and add whatever your image seems to need.

**Negative prompt**: *ugly, asian, underage, 3d render, cartoon, doll, (bad low worst quality:1.3)*

The token "asian" is included to counter the heavy bias towards Korean and Chinese women among most popular models. Asian characters should still appear and look perfectly fine even including this token. Usually the attention setting of the final quality prompt should stick to the range 1.1 - 1.4, but a small number of Asian-oriented models can benefit from high (~1.5) or extreme values (up to 2.0!). Note that this is the exception that proves the rule - in most models such extreme weights would heavily "overcook" your images and destroy both quality and variability.

I rarely use attention weights above 1.0 in my prompts and **never** use attention weights above 1.2 for any other tokens in my prompt other than this general quality negative. In my experience this greatly benefits image consistency and reduces mutations, bad hands and other monstrosities without having to explicitly include these things in your negatives.
