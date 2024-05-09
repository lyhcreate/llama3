```
# 如果你是InternStudio 可以直接使用
# studio-conda -t lmdeploy -o pytorch-2.1.2
# 初始化环境
conda create -n lmdeploy python=3.10
conda activate lmdeploy
conda install pytorch==2.1.2 torchvision==0.16.2 torchaudio==2.1.2 pytorch-cuda=12.1 -c pytorch -c nvidia

#安装lmdeploy最新版
pip install -U lmdeploy[all]
```
运行模型
```
conda activate lmdeploy
lmdeploy chat /root/model/Meta-Llama-3-8B-Instruct
```

![image](https://github.com/lyhcreate/llama3/assets/93357834/fe44b8a9-f539-442a-bcae-1148fb7587e4)

```
# 如果你是InternStudio 就使用
# studio-smi
nvidia-smi 
```

0.8占的内存
![image](https://github.com/lyhcreate/llama3/assets/93357834/88a90f44-7b97-40f0-b84b-c3fdeacc95ac)

```
lmdeploy chat /root/model/Meta-Llama-3-8B-Instruct/ --cache-max-entry-count 0.5
```
0.5占的内存
![image](https://github.com/lyhcreate/llama3/assets/93357834/9587217a-64ae-4a6d-a0b0-e69642f5e0c6)

```
lmdeploy chat /root/model/Meta-Llama-3-8B-Instruct/ --cache-max-entry-count 0.01
```
0.01占的内存
![image](https://github.com/lyhcreate/llama3/assets/93357834/2ad559bd-ac30-4eaa-b464-7b1cbedb1de3)

使用W4A16量化
```
lmdeploy lite auto_awq \
   /root/model/Meta-Llama-3-8B-Instruct \
  --calib-dataset 'ptb' \
  --calib-samples 128 \
  --calib-seqlen 1024 \
  --w-bits 4 \
  --w-group-size 128 \
  --work-dir /root/model/Meta-Llama-3-8B-Instruct_4bit


lmdeploy chat /root/model/Meta-Llama-3-8B-Instruct_4bit --model-format awq

lmdeploy chat /root/model/Meta-Llama-3-8B-Instruct_4bit --model-format awq --cache-max-entry-count 0.01
```
0.01占用内存明显减少
![image](https://github.com/lyhcreate/llama3/assets/93357834/6ad036e5-be28-48fd-9d0f-6f0bf0fda69f)



启动api服务器
```
lmdeploy serve api_server \
    /root/model/Meta-Llama-3-8B-Instruct \
    --model-format hf \
    --quant-policy 0 \
    --server-name 0.0.0.0 \
    --server-port 23333 \
    --tp 1
```
新窗口运行命令行客户端：
```
conda activate lmdeploy
lmdeploy serve api_client http://localhost:23333
```
![image](https://github.com/lyhcreate/llama3/assets/93357834/dac0ceaf-78ff-4638-80a9-b8bd565d56db)

```
pip install gradio==3.50.2
conda activate lmdeploy

#启动网页客户端
lmdeploy serve gradio http://localhost:23333 \
    --server-name 0.0.0.0 \
    --server-port 6006
```
打开浏览器，访问地址http://127.0.0.1:6006 然后就可以与模型进行对话了！

![image](https://github.com/lyhcreate/llama3/assets/93357834/75f6ac52-d979-4008-a770-ec45f3c75442)

```
pip install git+https://github.com/haotian-liu/LLaVA.git
#新建一个py文件
touch /root/pipeline_llava.py

from lmdeploy import pipeline, ChatTemplateConfig
from lmdeploy.vl import load_image
pipe = pipeline('xtuner/llava-llama-3-8b-v1_1-hf',
                chat_template_config=ChatTemplateConfig(model_name='llama3'))

image = load_image('https://raw.githubusercontent.com/open-mmlab/mmdeploy/main/tests/data/tiger.jpeg')
response = pipe(('describe this image', image))
print(response.text)
```

```

```
