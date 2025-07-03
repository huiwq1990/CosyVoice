


## colab使用

```shell

uv python install 3.10

uv venv --python 3.10

source .venv/bin/activate
export UV_SYSTEM_PYTHON=false

uv pip install pip

python --version

cd CosyVoice/
python -m pip install -r requirements.txt

# 有问题，暂时无法解决
#uv pip install -r requirements.txt --index-strategy unsafe-best-match -i https://mirrors.aliyun.com/pypi/simple/ --trusted-host=mirrors.aliyun.com

wget https://github.com/user-attachments/files/18149385/spk2info.zip 

unzip spk2info.zip

python webui.py --model_dir=pretrained_models/CosyVoice2-0.5B

```

## Docker

```shell

cat<<EOF > Dockerfile.tmp
FROM modelscope-registry.cn-beijing.cr.aliyuncs.com/modelscope-repo/modelscope:ubuntu22.04-py311-torch2.3.1-1.26.0 as ms
RUN modelscope download --cache_dir /root/pretrained_models/ --model iic/CosyVoice2-0.5B
RUN modelscope download --cache_dir /root/pretrained_models/ --model iic/CosyVoice-ttsfrd
#RUN cd /tmp && wget https://ghfast.top/https://github.com/user-attachments/files/18149385/spk2info.zip && unzip spk2info.zip
RUN cd /tmp && wget https://hgtemp.oss-rg-china-mainland.aliyuncs.com/spk2info.pt

FROM registry.cn-beijing.aliyuncs.com/huiwq1990/public:cosyvoice-v0.0.3-amd64
WORKDIR /workspace/CosyVoice/

COPY --from=ms /root/pretrained_models/iic/ ./pretrained_models/

RUN cd pretrained_models && rm -rf CosyVoice2-0.5B && ln -s CosyVoice2-0___5B/ CosyVoice2-0.5B

COPY --from=ms /tmp/spk2info.pt ./pretrained_models/CosyVoice2-0.5B
RUN cd pretrained_models/CosyVoice-ttsfrd/ && unzip resource.zip -d .
RUN cd pretrained_models/CosyVoice-ttsfrd/ && \
  conda activate cosyvoice && \
  pip install ttsfrd_dependency-0.1-py3-none-any.whl && \
  pip install ttsfrd-0.4.2-cp310-cp310-linux_x86_64.whl

EOF

docker buildx build --provenance=false --platform=linux/amd64 --push -t hub.jdcloud.com/jdos-edge/cosyvoice-amd64:v0.0.4 -f Dockerfile.tmp .

# https://github.com/FunAudioLLM/CosyVoice/issues/729
```

```shell

docker rm -f cosyvoice2
docker run -d --name cosyvoice2 -p 8000:8000 hub.jdcloud.com/jdos-edge/cosyvoice-amd64:v0.0.4 /bin/bash -c "cd /workspace/CosyVoice/ && python3 webui.py --port 8000 --model_dir pretrained_models/CosyVoice2-0.5B && sleep infinity"
docker logs -f cosyvoice2

```
