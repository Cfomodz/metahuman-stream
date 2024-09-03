Real-time interactive streaming digital human, achieving audio-video synchronized dialogue. It can basically achieve commercial-grade effects.

[ernerf demo](https://www.bilibili.com/video/BV1PM4m1y7Q2/)  [musetalk demo](https://www.bilibili.com/video/BV1gm421N7vQ/)  [wav2lip demo](https://www.bilibili.com/video/BV1Bw4m1e74P/)

## Features
1. Supports multiple digital human models: ernerf, musetalk, wav2lip
2. Supports voice cloning
3. Supports interrupting the digital human while speaking
4. Supports full-body video stitching
5. Supports RTMP and WebRTC
6. Supports video orchestration: plays custom video when not speaking

## 1. Installation

Tested on Ubuntu 20.04, Python 3.10, PyTorch 1.12, and CUDA 11.3

### 1.1 Install dependencies

```bash
conda create -n nerfstream python=3.10
conda activate nerfstream
conda install pytorch==1.12.1 torchvision==0.13.1 cudatoolkit=11.3 -c pytorch
pip install -r requirements.txt
If only using musetalk or wav2lip models, the following libraries are not needed
pip install "git+https://github.com/facebookresearch/pytorch3d.git"
pip install tensorflow-gpu==2.8.0
pip install --upgrade "protobuf<=3.20.1"
```
If using PyTorch 2.1, use torchvision 0.16 (check the torchvision website for matching versions based on PyTorch version), cudatoolkit installation can be skipped.  
For common installation issues, see [FAQ](/assets/faq.md)  
For setting up CUDA environment on Linux, refer to this article: https://zhuanlan.zhihu.com/p/674972886


## 2. Quick Start
Default model is ernerf, using WebRTC to stream to SRS
### 2.1 Run SRS
```
export CANDIDATE='<server_public_ip>'
docker run --rm --env CANDIDATE=$CANDIDATE \
-p 1935:1935 -p 8080:8080 -p 1985:1985 -p 8000:8000/udp \
registry.cn-hangzhou.aliyuncs.com/ossrs/srs:5 \
objs/srs -c conf/rtc.conf
```

### 2.2 Start the digital human:

```python
python app.py
```

If you can't access Hugging Face, run this before starting:
```
export HF_ENDPOINT=https://hf-mirror.com
```

Open http://serverip:8010/rtcpushapi.html in a browser, enter any text in the input box and submit. The digital human will speak the text.  
Note: The server needs to open ports TCP: 8000, 8010, 1985; UDP: 8000

## 3. More Usage
User guide: <https://livetalking-doc.readthedocs.io/>

## 4. Docker Run  
No need for previous installation steps, just run directly.
```
docker run --gpus all -it --network=host --rm registry.cn-beijing.aliyuncs.com/codewithgpu2/lipku-metahuman-stream:vjo1Y6NJ3N
```
The code is in /root/metahuman-stream, first `git pull` to get the latest code, then execute commands as in steps 2 and 3.

The following images are provided:
- AutoDL image: <https://www.codewithgpu.com/i/lipku/metahuman-stream/base>   
[AutoDL tutorial](autodl/README.md)
- Haoee AI Computing image (no need for separate SRS server): <https://bbs.haoee.com/postDetail/657>  
Run the following commands in the background:
```
nginx
cd ~/srs/trunk
./objs/srs -c conf/rtc.tcp.udp.conf
cd ~/metahuman-stream
python app.py
```

Open http:<haoee_public_access_address>/rtcpushapi.html in a browser, e.g., http://www.haoee.com:25232/rtcpushapi.html


## 5. Performance Analysis
1. Frame rate  
Tested on Tesla T4 GPU, overall FPS is around 18. Without audio/video encoding and streaming, frame rate is around 20. With an RTX 4090 GPU, it can reach over 40 frames/second.  
Optimization: Run audio/video encoding and streaming in a separate thread.
2. Latency  
Overall latency is about 3s  
(1) TTS latency is about 1.7s, currently using EdgeTTS, which requires inputting each sentence after it's fully converted. Can be optimized by changing TTS to streaming input.  
(2) Wav2vec latency is 0.4s, requires caching 18 frames of audio for calculation. 
(3) SRS forwarding latency, configure SRS server to reduce buffer delay. See https://ossrs.net/lts/zh-cn/docs/v5/doc/low-latency for specific configuration.


## 6. TODO
- [x] Add ChatGPT for digital human dialogue
- [x] Voice cloning
- [x] Play a video clip when the digital human is silent
- [x] MuseTalk
- [x] Wav2Lip
- [ ] TalkingGaussian

---
If this project is helpful to you, please give it a star. We also welcome interested friends to help improve this project.
* Knowledge Planet: https://t.zsxq.com/7NMyO for high-quality FAQs, best practices, and problem-solving
* WeChat Official Account: Digital Human Technology  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/l3ZibgueFiaeyfaiaLZGuMGQXnhLWxibpJUS2gfs8Dje6JuMY8zu2tVyU9n8Zx1yaNncvKHBMibX0ocehoITy5qQEZg/640?wxfrom=12&tp=wxpic&usePicPrefetch=1&wx_fmt=jpeg&amp;from=appmsg)
