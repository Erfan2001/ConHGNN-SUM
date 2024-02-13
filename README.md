# ConHGNN-SUM: A Contextualized Heterogeneous Graph Neural Network for Extractive Text Summarization



## Data

We have preprocessed **CNN/DailyMail** dataset for TF-IDF features used in the graph creation, which you can find [here](https://drive.google.com/open?id=1oIYBwmrB9_alzvNDBtsMENKHthE9SW9z).

For **CNN/DailyMail**, we also provide the json-format datasets in [this link](https://drive.google.com/open?id=1JW033KefyyoYUKUFj6GqeBFZSHjksTfr).

The example looks like this:

```
{
  "text":["deborah fuller has been banned from keeping animals ... 30mph",...,"a dog breeder and exhibitor... her dogs confiscated"],
  "summary":["warning : ... at a speed of around 30mph",... ,"she was banned from ... and given a curfew "],
  "label":[1,3,6]
}
```

and each line in the file is an example.  For the *text* key, the value can be list of string (single-document) or list of list of string (multi-document). The example in training set can ignore the *summary* key since we only use *label* during the training phase. All strings need be lowercase and tokenized by [Stanford Tokenizer](https://nlp.stanford.edu/software/tokenizer.shtml), and  ***nltk.sent_tokenize*** is used to get sentences.

After getting the standard json format, you can prepare the dataset for the graph by ***PrepareDataset.sh*** in the project directory. The processed files will be put under the ***cache*** directory.

The default file names for training, validation and test are: *train.label.jsonl*, *val.label.jsonl* and *test.label.jsonl*. If you would like to use other names, please change the corresponding names in  ***PrepareDataset.sh***,  Line 321-322 in ***train.py*** and Line 188 in ***evaluation.py***. (Default names is recommended)



## Train

For training, you can run commands like this:

```shell
python train.py --cuda --gpu 0 --data_dir <data/dir/of/your/json-format/dataset> --cache_dir <cache/directory/of/graph/features> --embedding_path <glove_path> --model [HSG|HDSG] --save_root <model path> --log_root <log path> --lr_descent --grad_clip -m 3
```



We also provide our checkpoints on **CNN/DailyMail** in [this link](https://drive.google.com/open?id=16wA_JZRm3PrDJgbBiezUDExYmHZobgsB). Besides, the outputs can be found [here](https://drive.google.com/open?id=1VArOyIbGO8ayW0uF8RcmN4Lh2DDtmcQz).



## Test

For evaluation, the command may like this:

```shell
python evaluation.py --cuda --gpu 0 --data_dir <data/dir/of/your/json-format/dataset> --cache_dir <cache/directory/of/graph/features> --embedding_path <glove_path>  --model [HSG|HDSG] --save_root <model path> --log_root <log path> -m 3 --test_model multi --use_pyrouge
```

Some options:

- *use_pyrouge*: whether to use pyrouge for evaluation. Default is **False** (which means rouge).
  - Please change Line17-18 in ***tools/utils.py*** to your own ROUGE path and temp file path.
- *limit*: whether to limit the output to the length of gold summaries. This option is only set for evaluation on NYT50 (which uses ROUGE-recall instead of ROUGE-f). Default is **False**.
- *blocking*: whether to use Trigram blocking. Default is **False**.
- save_label: only save label and do not calculate ROUGE. Default is **False**.



To load our checkpoint for evaluation, you should put it under the ***save_root/eval/*** and make the name for test_model to start with ***eval***. For example, if your save_root is "*checkpoints*", then the checkpoint "*cnndm.ckpt*" should be put under "*checkpoints/eval*" and the test_model is *evalcnndm.ckpt*.
