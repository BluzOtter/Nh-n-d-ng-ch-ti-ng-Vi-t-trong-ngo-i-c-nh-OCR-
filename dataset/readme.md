# Dataset is AI4VN 2022 - VAIPE: Medicine Pill Image Recognition Challenge
## Introduction 
Medicines are used to support patients and help them to decline or cure diseases. However, taking the wrong medication can have serious consequences, 
including reducing the effectiveness of treatment, causing side effects, or even leading to death. According to WHO, one-third of all deaths are caused 
by the misuse of drugs, not by disease. Widespread development and increasing demand for drugs have increased the need for applications that aid in the 
correct identification of drugs.

Capturing that fact, VAIPE (AI-assisted IoT-enabled smart, optimal, and Protective hEalthcare monitoring and supporting system for Vietnamese) - a project 
sponsored by VINIF fund, under the cooperation of many famous universities famous at home and abroad, towards finding intelligent AI solutions in solving 
the problem of pill identification, thereby integrating into an actual phone application. In addition, the goal of the problem is also related to the detection
of over-the-counter drugs to warn users. Therefore, the theme of the contest is Locating and identifying drugs in the image, and focusing on identifying 
non-prescription drugs.

## Task
In this contest, the main tasks are pill detection and Out-of-prescription pills recognition. The teams will come up with solutions to detect and identify
drugs from the images (Pill images), but at the same time are encouraged to use the prescription information corresponding to those images (Prescription information).
Data is collected from major hospitals (prescriptions), and actual snapshots of prescription drugs.

After positioning the drugs in the image by drawing bounding boxes around (Rectangle 2 left-top and right-bottom corners for each pill), we need to identify the 
labels of these boxes. However, the labels of non-prescription drugs will be given more weight in the evaluation of model results. The final evaluation results 
are calculated based on the correct and incorrect positioning and classification boxes compared to the actual data.

## Data
The data consists of 3 sets:

- The training data consists of 9,500 images of pills and 1,171 images of prescriptions
- The test data for the Public Test phase includes 1,500 pill images and 172 prescription images
- The test data for the Private Test phase includes 1,600 pill images and 184 prescription images

### README
Dữ liệu `public_train` được chia nhỏ để thuận tiện trong quá trình tải dữ liệu. Sau khi tải toàn bộ 5 file liên quan: 

```
public_train_s.z01
public_train_s.z02
public_train_s.z03
public_train_s.z04
public_train_s.zip
```

Các bạn sử dụng lệnh sau để gộp thành 1 file và giải nén. 

```
zip -s- public_train_s.zip -O public_train.zip
unzip public_train.zip
```

Lưu ý:

- Các file được lưu trong cùng 1 thư mục.
- Phiên bản ZIP từ 3.0 trở lên.

