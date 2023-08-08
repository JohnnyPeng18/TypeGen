# TypeGen

This is the tool released in the ASE'23 paper "[Generative Type Inference for Python](https://arxiv.org/abs/2307.09163)".

## Requirements

Python >= 3.9

Linux

## Install

Clone this repo and run the following command in the root directory of this repo:

```sh
pip install -r requirements.txt
```

## Usage

1. Download the dataset from the [release](https://github.com/JohnnyPeng18/TypeGen/releases/tag/data) and unzip it

2. TypeGen relies on the OpenAI services, so fill your OpenAI API keys in `config.py` first. We recommend you to set multiple API keys to avoid insufficient balances given the large dataset.
3. Run TypeGen on the dataset

```
python typegen/typegen.py -s data -r predictions.json 
```

TypeGen will then use default settings to infer the types in the testset.

The default setting is set to `hop=3` and `demo_num=5`. This setting is consistent with the paper. 

You can also use `-p` and `-n` options to indicate the number of hops and number of demos you want to use.

## Baselines

**Ablations:**

To remove COT prompts, you can set the option `-c`, such as

```
python typegen/typegen.py -s data -r predictions.json -c
```

To remove type hints, you can set the option `-u`, such as

```
python typegen/typegen.py -s data -r predictions.json -u
```

To use original code instead of code slices, you can set the option `-o`, such as

```
python typegen/typegen.py -s data -r predictions.json -o
```

The above three options can be combined to remove multiple components of TypeGen.

**Cloze-style Approaches:**

To get the predictions of cloze-style approaches mentioned in the paper, you can use command:

```
python typegen/mask.py -s data -r predictions.json -m [MODEL_NAME]
```

[MODEL_NAME] can be the model names in HuggingFace, such as `facebook/incoder-6B`. Note that it only supports Incoder, Unixcoder and CodeT5 now.

**Other Generative Models:**

To implement TypeGen in other generative models instead of OpenAI models, you can use command:

```
python typegen/gen.py -s data -r predictions.json -m [MODEL_NAME]
```

[MODEL_NAME] can be the model names in HuggingFace. In general it supports any generative models. You can also change the settings of TypeGen use the same options mentioned above.

For the supervised approaches in the paper, they all have replication packages released at GitHub so please go to their repos for re-implementation.

## New Dataset

If you want to use TypeGen in new datasets, you should prepare the data files required by TypeGen in `config.py`.

You first need to prepare the metadata of your new testset and trainset in the format of `testset.json` and `trainset.json`.

If you want to use fixed demonstrations, you also need to prepare `fixed_examples.json`.

Once you have the metadata of datasets, TypeGen can generates other required data files:

1. **Source Code**

You can use the following command to generate `testset_source.json` and `trainset_source.json`:

```
python typegen/preprocess.py -s testset.json/trainset.json -c
```

2. **Transformed Metadata**

You can use the following command to generate `testset_transformed.json` and `trainset_transformed.json`:

```
python typegen/preprocess.py -s testset.json/trainset.json -t
```

3. **Usertypes**

You can use HiTyper to collect the available usertypes and generate `testset_usertypes.json` and `trainset_usertypes.json`, please follow the instructions in [HiTyper](https://github.com/JohnnyPeng18/HiTyper).

4. **Sliced Source Code**

You can use the following command to generate `testset_staticsliced_hop[HOP].json` and `trainset_staticsliced_hop[HOP].json`:

```
python typegen/slice.py -s testset_source.json/trainset_source.json -p [HOP]
```

You need to specify the number of hops.

5. **Similar Demonstrations in Training Set**

You can use the following command to generate `topk_with_label_staticsliced_hopHOP_transformed.json` based on the sliced code:

```
python typegen/demo.py -s data 
```

You can also use `-p` option to indicate the number of hops.

Or `topk_with_label_full_transformed.json` based on the original code:

```
python typegen/demo.py -s data -o
```

You can also use `-k` option to indicate the top N similar demonstrations for each test instances.

6. **COT Prompts**

You can use the following command to generate `trainset_cots_hop[HOP].json`:

```
python typegen/cot.py -s trainset.json -p [HOP]
```

You need to specify the number of hops.

## Results

We include the predictions of TypeGen in our dataset and its ablation results in the `predictions/` folder in the [release](https://github.com/JohnnyPeng18/TypeGen/releases/tag/data).

## Cite Us

If you use TypeGen in your research, please cite us:

```
@misc{peng2023generative,
      title={Generative Type Inference for Python}, 
      author={Yun Peng and Chaozheng Wang and Wenxuan Wang and Cuiyun Gao and Michael R. Lyu},
      year={2023},
      eprint={2307.09163},
      archivePrefix={arXiv},
      primaryClass={cs.SE}
}
```

