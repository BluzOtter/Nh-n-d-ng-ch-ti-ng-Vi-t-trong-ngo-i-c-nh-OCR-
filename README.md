# Scene Text Detect + Recognition

## Install Library 
Move into folder PaddleOCR-Vietnamese
```buildoutcfg
!pip install -r requirements.txt
!pip install paddlepaddle-gpu 
```
> Both detect and recognize parts include the following steps: <br>
Step 1: Data preprocessing <br> 
Step 2: Configure the config file <br> 
Step 3: Train model <br> 
Step 4: Evaluation <br> 
Step 5: Convert to model inference <br> 
Step 6: Predict <br> 

## Download Dataset
```buildoutcfg
!gdown --id 1UUQhNvzgpZy7zXBFQp0Qox-BBjunZ0ml
!unzip vietnamese_original.zip -d train
```

## Model Detect SAST
SAST (A Single-Shot Arbitrarily-Shaped Text Detector based on Context Attended Multi-Task Learning) is an efficient model in predicting text of arbitrary shape but small text areas are a problem with it.

![image](https://i.ibb.co/J7Bv8L1/nh2.png)

### Data preprocessing, convert to PaddleOCR format to train:
```buildoutcfg
!python3 ConvertFromBtcToFormatPaddle.py
```
### Download pretrain model SAST
```buildoutcfg
!wget https://paddleocr.bj.bcebos.com/dygraph_v2.0/en/det_r50_vd_sast_icdar15_v2.0_train.tar -P ./pretrain_models
!tar -xf ./pretrain_models/det_r50_vd_sast_icdar15_v2.0_train.tar -C /content/drive/MyDrive/Scene_TextVN/pretrain_models
```

### Training
```buildoutcfg
!python3 tools/train.py -c ./configs/det/SAST.yml
```
To continue training from the checkpoint without editing the config file, do the following:
```buildoutcfg
!python3 tools/train.py -c ./configs/det/SAST.yml    \
    -o Global.checkpoints=./output/SAST/number-epoch
```

## Model Recognition SRN
SRN (Semantic Reasoning Network) combines both visual information and global semantic information, which run in parallel during training and output. <br>

SRN is an end-to-end trainable pipeline for the scene text recognition problem, consisting of four parts: <br>

* Backbone network
* Parallel visual attention module (PVAM)
* Global semantic reasoning module (GSRM)
* Visual semantic fusion decoder (VSFD)

> Pipeline: Khi đưa ảnh đầu vào, đầu tiên, backbone network sẽ được dùng để trích xuất các thuộc tính 2D (V). Sau đó, PVAM được dùng để tạo ra N thuộc tính 1D sau khi được căn chỉnh, tại đó, mỗi thuộc tính tương ứng với một ký tự trong văn bản. N thuộc tính 1D này sau đó sẽ được đưa vào GSRM để thu thập thông tin ngữ nghĩa (S). Cuối cùng, các thuộc tính ảnh sau khi được căn chỉnh và ngữ nghĩa của nó sẽ được VSFD hợp nhất để dự đoán các ký tự.

![image](https://i.ibb.co/zVjpqsh/nh3.png)

### Data Processing 
```buildoutcfg
# Create folder img_crop
!mkdir ./train/img_crop
!python3 /content/drive/MyDrive/Scene_TextVN/PaddleOCR-Vietnamese/crop_image.py
```
### Create Dictionary
To predict Vietnamese, we also need a dictionary file for Vietnamese containing all the characters. Because PaddleOCR does not yet support Vietnamese, you have to make a separate dictionary file to train. The dictionary is stored at ppocr/utils/dict/vi_vietnam.txt.

```buildoutcfg
!python3 tools/train.py -c ./configs/rec/SRN.yml 
```

## Evalution
```buildoutcfg
#evaluation model detect 
!python3 /content/drive/MyDrive/Scene_TextVN/PaddleOCR-Vietnamese/tools/eval.py -c /content/drive/MyDrive/Scene_TextVN/PaddleOCR-Vietnamese/configs/det/SAST.yml \
                       -o Global.checkpoints_model=path_checkpoints
```

```buildoutcfg
#evaluation model recognition
!python3 /content/drive/MyDrive/Scene_TextVN/PaddleOCR-Vietnamese/tools/eval.py -c /content/drive/MyDrive/Scene_TextVN/PaddleOCR-Vietnamese/configs/rec/SRN.yml  \
                   -o Global.checkpoints=path_checkpoints \
                      Global.character_type=ch  \
                       Global.character_dict_path=/content/drive/MyDrive/Scene_TextVN/PaddleOCR-Vietnamese/ppocr/utils/dict/vi_vietnam.txt  
```
## Inference
```buildoutcfg
 !python3 tools/export_model.py -c ./configs/det/SAST.yml  \
                               -o Global.pretrained_model= #path_pretrained \
                                  Global.save_inference_dir=./inference/SAST
```

```buildoutcfg
 !python3 tools/export_model.py -c ./configs/rec/SRN.yml  \
                               -o Global.pretrained_model= #path_pretrained \
                                  Global.save_inference_dir=./inference/SRN
```

## Combining two models detection and recognition
```buildoutcfg
!python3 /content/drive/MyDrive/Scene_TextVN/PaddleOCR-Vietnamese/tools/infer/predict_system.py --use_gpu=True  \
           --det_algorithm="SAST"  \
           --det_model_dir="/content/drive/MyDrive/Scene_TextVN/inference/SAST"  \
           --rec_algorithm="SRN" \
           --rec_model_dir="/content/drive/MyDrive/Scene_TextVN/inference/SRN/"  \
           --rec_image_shape="1, 64, 256"  \
           --image_dir="image_path" \
           --rec_char_type="ch"  \
           --drop_score=0.7  \
           --rec_char_dict_path="/content/drive/MyDrive/Scene_TextVN/PaddleOCR-Vietnamese/ppocr/utils/dict/vi_vietnam.txt" 
```
