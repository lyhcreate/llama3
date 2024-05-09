环境配置
```
conda create -n llama3 python=3.10
conda activate llama3
conda install pytorch==2.1.2 torchvision==0.16.2 torchaudio==2.1.2 pytorch-cuda=12.1 -c pytorch -c nvidia
```
下载模型
```
mkdir -p ~/model
cd ~/model

ln -s /root/share/new_models/meta-llama/Meta-Llama-3-8B-Instruct ~/model/Meta-Llama-3-8B-Instruct
```


web demo部署
```
cd ~
git clone https://github.com/SmartFlowAI/Llama3-Tutorial

cd ~
git clone -b v0.1.18 https://github.com/InternLM/XTuner
cd XTuner
pip install -e .
```
运行 web_demo.py
```
streamlit run ~/Llama3-Tutorial/tools/internstudio_web_demo.py \
  ~/model/Meta-Llama-3-8B-Instruct
```
自我认知训练数据集准备
```
cd ~/Llama3-Tutorial
python tools/gdata.py 
```

训练模型
```
cd ~/Llama3-Tutorial

# 开始训练,使用 deepspeed 加速，A100 40G显存 耗时24分钟
xtuner train configs/assistant/llama3_8b_instruct_qlora_assistant.py --work-dir /root/llama3_pth

# Adapter PTH 转 HF 格式
xtuner convert pth_to_hf /root/llama3_pth/llama3_8b_instruct_qlora_assistant.py \
  /root/llama3_pth/iter_500.pth \
  /root/llama3_hf_adapter

# 模型合并
export MKL_SERVICE_FORCE_INTEL=1
xtuner convert merge /root/model/Meta-Llama-3-8B-Instruct \
  /root/llama3_hf_adapter\
  /root/llama3_hf_merged
```


推理验证
```
streamlit run ~/Llama3-Tutorial/tools/internstudio_web_demo.py \
  /root/llama3_hf_merged
```
![549377cb92f9ddb5d3332b87cb1851fe](https://github.com/lyhcreate/llama3/assets/93357834/f1d69463-47ce-4871-a8b8-6296d8e11a98)

