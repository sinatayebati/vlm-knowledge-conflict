# VLM Knowledge Conflict

<!-- ![Static Badge](https://img.shields.io/badge/vision%20and%20language-blue)
![Static Badge](https://img.shields.io/badge/knowledge%20conflict-blue) -->

Code and data for paper [Unraveling Cross-Modality Knowledge Conflicts in Large Vision-Language Models](https://arxiv.org/abs/2410.03659).

![](figures/case.jpg)

<p align="center">
    [<a href="https://darthzhu.github.io/cross-modality-knowledge-conflict/">Website</a>] •
    [<a href="https://arxiv.org/abs/2410.03659">Paper</a>] •
    [<a href="https://huggingface.co/datasets/DarthZhu/vlm-knowledge-conflict">Dataset</a>] •
    [<a href="https://x.com/_vztu/status/1843350510583374306">Twitter</a>]
</p>

## Data Preview

```json
{
    "id": "infoseek_val_00068862",
    "entity": "Arctic hare",
    "question": "What is the closest upper taxonomy of this animal?",
    "image": "oven_05050169.jpg",
    "multiple_choices": {
        "A": "Lepus",
        "B": "Marmota",
        "C": "Oryctolagus",
        "D": "Sylvilagus"
    },
    "multiple_choices_answer": "A"
}
```

## Environment Setup

By the time we release this code, vllm does not support all the VLMs used in our paper, and different transformers versions will cause conflicts in the environment.
So, we provide two environments: 1) for the latest transformers lib, including all the popular VLMs, and 2) for models that are supported by vllms to accelerate the inference process.

To install the base environment containing only the transformers lib, run the following code:

```bash
conda env create -f base.yml
```

To install the vllm environment, run the following code:

```bash
conda env create -f vllm.yml
```

## Analysis

To conduct the analyses in our paper, run codes in `src/analysis`.
We provide codes for answer prediction, and contrastive metric.
You can refer to the scripts in `scripts/`.

For answer prediction, run the following command:

```bash
python $PREDICT_FILE \
    --dataset $DATASET \
    --model_name $MODEL_NAME \
    --output_dir $OUTPUT_DIR \
    --greedy \
    --max_new_tokens 20 \
    --is_scored
```

To analyze a new model: if the model is supported by vllm, use `src/analysis/predict_vllm.py` for faster prediction; otherwise, implement it in `src/models/local.py` and run `src/analysis/predict.py`.

For contrastive metric, you should first generate both textual and visual logits, then run the following command:

```bash
python src/analysis/post_hoc_contrastive_decoding_metric.py \
    --dataset $DATASET \
    --model_name $MODEL_NAME
```

## Improvements

To run dynamic contrastive decoding, use the following command:

```bash
python src/inference_time/post_hoc_contrastive_decoding.py \
    --dataset $DATASET \
    --model_name $MODEL_NAME \
    --output_dir $OUTPUT_DIR \
    --method dynamic
```

## Evaluation

To calculate the accuracy (Acc), run the following command:

```bash
python src/evaluate/evaluate_mc.py \
    --dataset $DATASET \
    --input_file $FILE_PATH
```

To calculate the recognized accuracy (R. Acc), run the following command:

```bash
python src/evaluate/evaluate_mc.py \
    --dataset $DATASET \
    --input_file $FILE_PATH \
    --cleaned_model $MODEL_NAME
```

To calculate the flip rate (FR), run the following command:

```bash
python src/evaluate/evaluate_mc.py \
    --dataset $DATASET \
    --input_file $FILE_PATH \
    --input_file_2 $FILE_PATH_2 \
    --cleaned_model $MODEL_NAME
```

## Citation

If you find this repo useful, please cite the following paper:

```bib
@article{zhu2024unraveling,
  title={Unraveling Cross-Modality Knowledge Conflict in Large Vision-Language Models},
  author={Zhu, Tinghui and Liu, Qin and Wang, Fei and Tu, Zhengzhong and Chen, Muhao},
  journal={arXiv preprint arXiv:2410.03659},
  year={2024}
}

```