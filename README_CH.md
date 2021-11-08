
## 下载NLI和STSBenchmark数据
放在./datasets目录下
wget https://sbert.net/datasets/AllNLI.tsv.gz
wget https://sbert.net/datasets/stsbenchmark.tsv.gz

## 下载STS数据
bash get_transfer_data.bash
本地执行该脚本失败（报错：declare: -A: invalid option），而docker无法访问外网。
只能先在本机克隆mosesdecoder项目、下载tokenizer.sed（从SentEval/data/downstream/中复制过来）、下载数据，再上传至docker，并在docker上执行脚本，以执行分词、转小写等操作（需注释掉第17、83行，注释掉与处理STS无关的行）。
需注释掉rm tmp文件的行（第104、125、167行），否则得到的是空input文件（！！！）

## 训练
bash scripts/unsup-consert-base.sh
报错：ModuleNotFoundError: No module named 'amp_C'
临时解决方法：注释掉/home/hadoop-aipnlp/cephfs/data/anaconda3/lib/python3.7/site-packages/apex/transformer/pipeline_parallel/utils.py中导入amp_C模块的行，
且不使用Apex AMP（去掉参数--use_apex_amp --apex_amp_opt_level O1）

## 数据增强实现
模型训练计算loss_value时，AdvCLSoftmaxLoss#forward中设置数据增强方法，在transformers.modeling_bert#forward中执行数据增强（见_replace_position_ids、_replace_embedding_output方法）