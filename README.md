# CHAIR 

CHAIR metric is a rule-based metric for evaluating object hallucination in caption generation.

I modified the original code, the original implementation CHAIR can be found at https://github.com/LisaAnne/Hallucination/blob/master/utils/chair.py 

I did NOT changing its calculation to keep consistency.

Espeically, i added a new metric `Recall` to calculate the percentage of recalled gt objects over all gt objects. Other modifications are listed below.

## Modifications

1. adapt calculation of CHAIR-i and CHAIR-s for Python3, supports for both json and jsonl file input.
2. integrate synonyms.txt to make the script standalone.
3. remove machine-translation based metrics BLEU-n, CIDEr, ROGUE
4. add new metric Recall, which represents the node words(i.e. lemmas of objects) coverage overall.
5. add pickle cache mechanism to make it fast for repetitive evaluations.


## Usage

### Install Dependencies

```
pattern
nltk
tqdm
```

### (Optional) Build Annotations

I aleady serilized the inited CHAIR evaluator object for coco into a pickle, you can use it by setting `--cache`, see #Example Run.


Or if you want to built CHAIR evaluator from scratch, do following steps.

Put these files into `coco_annotations` dir first. Download from http://images.cocodataset.org/annotations/annotations_trainval2014.zip 

- captions_train2014.json
- captions_val2014.json
- instances_train2014.json
- instances_val2014.json

and `python chair.py --cache <new_cache_path>`, it should argue about inputs but it is okay to built cache.

### Example Run

```bash
python chair.py \
    --cap_file example_inputs.jsonl \
    --image_id_key image_id \
    --caption_key caption \
    --cache chair.pkl \
    --save_path outputs.json
```

outputs:
```
CHAIRs    : 0.0
CHAIRi    : 0.0
Recall    : 85.7
```

### Explanations of Args

```python
parser.add_argument("--cap_file", type=str, default='',
                    help="path towards json or jsonl saving image ids and their captions in list of dict.")
parser.add_argument("--image_id_key", type=str, default="image_id",
                    help="in each dict of cap_file, which key stores image id of coco.")
parser.add_argument("--caption_key", type=str, default="caption",
                    help="in each dict of cap_file, which key stores caption of the image.")

parser.add_argument("--cache", type=str, default="chair.pkl",
                    help="pre inited CHAIR evaluator object, for fast loading.")
parser.add_argument("--coco_path", type=str, default='coco_annotations',
                    help="only use for regenerating CHAIR evaluator object, will be ignored if uses cached evaluator.")

parser.add_argument("--save_path", type=str, default="",
                    help="saving CHAIR evaluate and results to json, useful for debugging the caption model.")
```

## Disclaimer

Since the original implementation is written in Python2, hard to read and needs intermediate results to run, 
i have not test the original implementation yet. 

But i've tried my best to keep the consistency so i guess it could reproduce CHAIR comparing to the original code.