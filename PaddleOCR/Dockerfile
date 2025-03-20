FROM python:3.7.10-slim

RUN apt update && apt install g++ libglib2.0-dev libgl1-mesa-glx libsm6 libxrender1 libgl1 -y \
     && apt-get clean && rm -rf /root/.cache/* && rm -rf /var/lib/apt/lists/*

RUN pip3 install --no-cache-dir shapely pyclipper scikit-image imgaug lmdb tqdm numpy visualdl \
protobuf==3.20.0 python-Levenshtein opencv-contrib-python opencv-python \
paddlenlp==2.2.6 paddlepaddle==2.2.2 paddle2onnx==0.9.4 paddlehub \
-i https://mirror.baidu.com/pypi/simple

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