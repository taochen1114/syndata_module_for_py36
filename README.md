# Synthetic Data Module on Python 3.6
> Author: Tao Chen

## 環境安裝
python version 3.6.15

```
python -m venv venv
source venv/bin/activate
```

```
pip install --upgrade pip setuptools wheel
pip install numpy==1.19.5 cython==0.29.36 pandas==0.24.2
pip install sdv==0.3.6
pip install jupyterlab==3.2.9
pip install scipy==1.2.3 sdmetrics==0.0.2.dev0
```

## 使用步驟

測試範例資料
form https://www.kaggle.com/c/boston-housing/overview

放在 [input/data.csv](input/data.csv)

### 1. 訓練合成資料產生器
使用 [syndata_model_train_py36.ipynb](syndata_model_train_py36.ipynb)

修改以下三個參數，執行合成資料模型訓練

參數說明:
- input_path: 真實資料 csv 路徑
- output_dir: 合成資料與模型輸出路徑
- primary_key: 表單的 key 值欄位名稱
```python
args = set_args([
    "--input_path", "input/data.csv", # input 真實資料 csv 格式的表單
    "--output_dir", "output/",   # 合成資料輸出路徑
    "--synth_model", "GaussianCopula",  # 合成資料模型演算法
    "--primary_key", "Id",  # key 欄位名稱
    "--num_rows", "100",  # 生成的資料筆數
    "--save_model",    # 設定模型
    "--save_output"    # 設定儲存
])

main(args)
```
這步驟需要一段時間，依 CPU 規格與輸入資料大小決定等待時間長短

步驟完成後可以在 output_dir 看到一個  `syn_model_GaussianCopula.pkl` 檔案，那個就是合成資料模型 (合成資料產生器)

除了模型外這個步驟也會產出 100(預設) row 的合成資料 csv 檔放在 output_dir 中

### 2. 使用合成資料產生器生成合成資料
使用 [syndata_generator_py36.ipynb](syndata_generator_py36.ipynb)
載入上一步驟訓練好的合成資料模型 `syn_model_GaussianCopula.pkl` 檔，生成合成資料

參數說明:
- input_syn_model: `syn_model_GaussianCopula.pkl` 的檔案路徑
- output_fpath: 合成資料 csv 檔案輸出路徑
- num_rows: 欲生成的合成資料的資料筆數

修改以下設定
```python
args = set_args([
    "--input_syn_model", "output/syn_model_GaussianCopula.pkl", # 合成資料生成模型路徑 
    "--output_fpath", "output/syn_data.csv",   # 合成資料輸出路徑
    "--num_rows", "100",  # 生成的資料筆數
    "--save_output"
])

main(args)
```
這步驟需要一段時間，依 CPU 規格與輸入資料大小決定等待時間長短
步驟完成後可以在 output_dir 看到合成資料的 csv 檔案


### 3. 使用評估工具比較合成資料與真實資料的統計相似性
[syndata_quality_evaluate.ipynb](syndata_quality_evaluate.ipynb)

修改真實資料與合成資料 csv 檔案路徑
```python
## inputs
real_data = pd.read_csv("input/data.csv")  ## 真實資料路徑
synthetic_data = pd.read_csv("output/data_GaussianCopula_output.csv") ## 合成資料路徑

syndata_eval_df = evaluate_all_columns(real_data, synthetic_data)  # 注意順序！ 真實資料放前面，合成資料放後面
```

可視覺化比較欄位
![](img/demo_compare_numeric.png)

![](img/demo_compare_categorical.png)




