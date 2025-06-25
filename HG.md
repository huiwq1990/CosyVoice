


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