# Docker构建
- 简单介绍飞浆
- 通过docker构建OCR应用 
- 在 Docker 中构建 PaddleOCR 的运行环境
## 项目说明
* [飞浆介绍](https://www.paddlepaddle.org.cn/hub/scene/ocr)
* [PaddleHub](https://aistudio.baidu.com/aistudio/projectdetail/50715)一键OCR中文识别
* [PaddleHub](https://github.com/PaddlePaddle/PaddleHub)项目
* 模型说明: [chinese_ocr_db_crnn_mobile](https://github.com/PaddlePaddle/PaddleHub/tree/release/v2.2/modules/image/text_recognition/chinese_ocr_db_crnn_mobile)
## Docker常用命令
##### 创建python的运行环境
```
docker run -itd --name pyrun -p 9000:9000 -v 主机目录:/app python:3.7.10-slim /bin/bash
```
###### 进入容器调试
```
docker exec -it pyrun  /bin/bash 
```


## 调试Docker
###### 创建python的运行环境
```
docker run -itd --name pyrun -p 9000:9000 -v 主机目录:/app python:3.7.10-slim /bin/bash
```
####### 进入容器调试
```
docker exec -it pyrun  /bin/bash 
```
###### 进入app目录
```
cd /app
```
###### 安装系统依赖
```
apt update && apt install g++ libglib2.0-dev libgl1-mesa-glx libsm6 libxrender1 libgl1 -y
```
###### 安装Python依赖
* 根据上面的文档按照包 发现安装的都是最新版 那是运行不起来的
* 所以这里我把版本都指定好了就都可以运行了
* requirements.txt
```
pip3 install --no-cache-dir -r /app/requirements.txt -i https://mirror.baidu.com/pypi/simple
```
```
shapely
pyclipper
scikit-image
imgaug
lmdb
tqdm
numpy
visualdl
protobuf==3.20.0
python-Levenshtein
opencv-contrib-python
opencv-python
paddlenlp==2.2.6
paddlepaddle==2.2.2
paddle2onnx==0.9.4
paddlehub 
```
###### 调试代码
```
.
├── main.py
└── test.png
```
`main.py` 用于调试docker环境
```python
import paddlehub as hub
import cv2

# 加载移动端预训练模型
ocr = hub.Module(name="chinese_ocr_db_crnn_mobile")

np_images =[cv2.imread(image_path) for image_path in ['test.png']]
results = ocr.recognize_text(
                    images=np_images,         # 图片数据，ndarray.shape 为 [H, W, C]，BGR格式；
                    use_gpu=False,            # 是否使用 GPU；若使用GPU，请先设置CUDA_VISIBLE_DEVICES环境变量
                    output_dir='ocr_result',  # 图片的保存路径，默认设为 ocr_result；
                    visualization=False,       # 是否将识别结果保存为图片文件；
                    box_thresh=0.5,           # 检测文本框置信度的阈值；
                    text_thresh=0.5)          # 识别中文文本置信度的阈值；
for result in results:
    data = result['data']
    save_path = result['save_path']
    for infomation in data:
        print('text: ', infomation['text'], '\nconfidence: ', infomation['confidence'], '\ntext_box_position: ', infomation['text_box_position'])
```

###### 测试环境是否成功
```
#运行他会自动下载模型然后预测
python main.py
```
###### 测试API接口
* 此时运行环境已经构建好了
* 服务器部署Api接口 直接使用下面命令行即可
```
hub serving start -m chinese_ocr_db_crnn_mobile -p 9000
```
###### 测试代码
```python
import requests
import base64

def ocr(imagePath):
    with open(imagePath, 'rb') as f:
        data = f.read(-1)
    image = str(base64.b64encode(data), encoding='utf-8')
    data = '{"images":["' + image + '"]}'
    txt = requests.post("http://127.0.0.1:9000/predict/chinese_ocr_db_crnn_mobile", data=data,
                        headers={'Content-Type': 'application/json'})
    return txt.content.decode("utf-8")

print(ocr("./test.png"))
```
###### 清理缓存
```
rm -rf /root/.cache/* \
&& rm -rf /var/lib/apt/lists/* \
&& rm -rf /app/test/pg/*
```
###### 镜像保存
- 保存为镜像
```
docker commit 容器名称 镜像名称:1.3
```
- 导出为文件
```
docker export -o 文件名.tar 容器名称
```
###### 镜像推送
```
docker login --username=用户名 registry.cn-hongkong.aliyuncs.com
docker commit pyrun pyrun:1.0
docker tag pyrun:1.0 registry.cn-hongkong.aliyuncs.com/duolabemng/pyrun:1.0
docker push registry.cn-hongkong.aliyuncs.com/duolabemng/pyrun:1.0
```
#### 删除Docker缓存
###### 观察一下新增了什么文件
```
docker container diff pyrun > 容器修改记录.log
```
###### 清理缓存减少容器大小
```
rm -rf /root/.cache/* && rm -rf /var/lib/apt/lists/*
```
## 开始构建新的镜像
###### Dockerfile文件
```
FROM python:3.7.10-slim

#安装系统依赖
RUN apt update && apt install g++ libglib2.0-dev libgl1-mesa-glx libsm6 libxrender1 libgl1 -y \
     && apt-get clean && rm -rf /root/.cache/* && rm -rf /var/lib/apt/lists/*

#安装Python依赖
RUN pip3 install --no-cache-dir shapely pyclipper scikit-image imgaug lmdb tqdm numpy visualdl \
protobuf==3.20.0 python-Levenshtein opencv-contrib-python opencv-python \
paddlenlp==2.2.6 paddlepaddle==2.2.2 paddle2onnx==0.9.4 paddlehub \
-i https://mirror.baidu.com/pypi/simple

#清理缓存
RUN rm -rf /root/.cache/* && rm -rf /var/lib/apt/lists/*

#复制文件
COPY PaddleOCR /PaddleOCR
WORKDIR /PaddleOCR

# RUN mkdir -p /PaddleOCR/inference/
# ADD https://paddleocr.bj.bcebos.com/dygraph_v2.0/ch/ch_ppocr_server_v2.0_det_infer.tar /PaddleOCR/inference/
# RUN tar xf /PaddleOCR/inference/ch_ppocr_server_v2.0_det_infer.tar -C /PaddleOCR/inference/

# ADD https://paddleocr.bj.bcebos.com/dygraph_v2.0/ch/ch_ppocr_mobile_v2.0_cls_infer.tar /PaddleOCR/inference/
# RUN tar xf /PaddleOCR/inference/ch_ppocr_mobile_v2.0_cls_infer.tar -C /PaddleOCR/inference/

# ADD https://paddleocr.bj.bcebos.com/dygraph_v2.0/ch/ch_ppocr_server_v2.0_rec_infer.tar /PaddleOCR/inference/
# RUN tar xf /PaddleOCR/inference/ch_ppocr_server_v2.0_rec_infer.tar -C /PaddleOCR/inference/

RUN hub install deploy/hubserving/ocr_system/
RUN hub install deploy/hubserving/ocr_cls/
RUN hub install deploy/hubserving/ocr_det/
RUN hub install deploy/hubserving/ocr_rec/

EXPOSE 9000
CMD ["/bin/bash","-c","hub serving start --modules ocr_system ocr_cls ocr_det ocr_rec -p 9000"]
```



























