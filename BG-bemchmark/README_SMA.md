# I. Run BG benchmark with dataset 'SMA insects'

## Step 1: Get the benchmark-ready 'SMA insects' dataset

Download the dataset from [this link](https://www.kaggle.com/datasets/fleurpetit/bg-insects-adain-5thd).

## Step 2: Get the adapted BG code

Download the adapted BG code from [this link](https://www.kaggle.com/datasets/fleurpetit/sma-insects-bg-benchmark-code).

## Step 3: Build the working environment

In the code downloaded in Step 2, you will find a file named `requirements.txt`. Install the required dependencies by running the following command in your terminal:

```bash
pip install -q -r sma-insects-bg-benchmark-code/requirements.txt
```	

## Step 4: Launch the benchmark job
To run the benchmark job, use the following command:
```bash
python sma-insects-bg-benchmark-code/train.py --data_path path_to_dataset/bg-insects-adain-5thd --output_dir ./main_sweep --method subg --num_hparams_seeds 0,1 --num_init_seeds 0,1 --epochs 100
```	
The arguments can be tuned to customize the job:

- `output_dir`: This is the directory where the results (best model, output, errors) will be saved.
- `method`: This is the name of the benchmark algorithm to be run. The available choices are: `erm`, `subg`, `erm`, `suby`, `rwy`, `rwg`, `dro`, `jtt`. The default value is `erm`.
- `num_hparams_seed`: This is the HP seed number the job will run, allowing you to compute the error bars across different HP. For example, using `'0,1'` means running with HP seed 0, and using `'0,5'` means running with HP seeds {0,1,2,3,4}. If you want to test the same algorithm with different HP, you can change these numbers.
- `num_init_seeds`: This is the initial random seed the job will run, allowing you to compute the error bars for ONE HP. For example, using `'0,5'` will run the algorithm with the HPs (specified with `num_hparams_seed`) using 5 random seeds (seeds 0,1,2,3,4).
- `epochs`: This specifies the number of epochs to run for the job. The default value is 100.





# II. Create a new benchmark-ready dataset

Suppose we have a newly generated SMA dataset, such as SMA_flowers, with the structure 'SMA_flowers/stylized/Category_style/XXX.jpg', and we want to make it 'benchmark-ready':

## Step 1: Dataset setup with dominant style

To create a benchmark-ready dataset with dominant styles, follow these steps using the `helpers_SMA.py` file:

1. `bias_spliter()`: Assign a major style to each category. The proportion of major and minor styles can be customized. By default, for a major style 'z', we keep all the 'n_max' images where (Y=y, Z=z). For the remaining minor styles, we randomly select 'n_min' images, where 'n_min' is randomly sampled from [0.25, 0.5] x 'n_max'.

2. `train_val_test_split()`: Given the biased dataset generated in Step 1, split it into train (50%), validation (20%), and test (30%) sets as suggested in the original paper.

3. `Datasets.py` and `setup_datasets.py`: Add entries for the new SMA dataset.


5. Launch 
```bash
python setup_datasets.py SMA_flowers --data_path 'path_to/SMA_flowers' --flatten
```
This will generate:
- `metadata_SMA_flowers.csv`: the sampled images and their info (category, group i.e. style, etc.) 
- `stylized_flatten/XXX.jpg` containing images mentioned in `metadata_SMA_flowers.csv`

These 2 things form the benchmark-ready dataset. 

Note: the current code is specifically adapted for SMA_insect. For new SMA datasets, some adjustments may be required.


## Step 2: Cast the dataset to a reasonable size

To ensure a reasonable benchmark time, we aim to keep the number of examples for each class around 3000. If the dataset resulting from Step 1 is too large, we can recast it by following these steps:

For the dataset 'SMA-insects', we have chosen to keep only 20% (randomly sampled) of each class. This helps in reducing the dataset size while still maintaining representative samples.

By adjusting the dataset size, we strike a balance between having sufficient data for benchmarking purposes and ensuring manageable computation time.

