# 适用于DeepSeek-VL的LMDeploy镜像


## Build
```
sudo docker build . -f docker/Dockerfile -t hello2mao/lmdeploy-deepseek-vl:latest
```

## Run
```shell
sudo docker run -d --restart=always --runtime nvidia --gpus all \
    -v ~/.cache/huggingface:/root/.cache/huggingface \
    -p 23333:23333 \
    --ipc=host \
    hello2mao/lmdeploy-deepseek-vl:latest \
    lmdeploy serve api_server deepseek-ai/deepseek-vl-7b-chat \
    --tp 8 \
    --session-len 8192 \
    --cache-max-entry-count 0.1 \
    --model-name deepseek-vl \
    --quant-policy 8 \
    --backend turbomind \
    --log-level ERROR
```