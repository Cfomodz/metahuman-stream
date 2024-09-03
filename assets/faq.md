1. pytorch3d installation fails\
   Download the source code and compile

```bash
git clone https://github.com/facebookresearch/pytorch3d.git
python setup.py install
```

2. websocket connection error\
   Modify python/site-packages/flask_sockets.py


```python
# Change this line:
self.url_map.add(Rule(rule, endpoint=f))
# To:
self.url_map.add(Rule(rule, endpoint=f, websocket=True))
```

3. protobuf version too high

```bash
pip uninstall protobuf
pip install protobuf==3.20.1
```

4. Digital human doesn't blink\
   Add the following steps when training the model

> Obtain AU45 for eyes blinking.\
> Run FeatureExtraction in OpenFace, rename and move the output CSV file to data/<ID>/au.csv.

Copy au.csv to the data directory of this project

5. Add background image to digital human

```bash
python app.py --bg_img bc.jpg
```

6. Dimension mismatch error when using self-trained model\
   Use wav2vec to extract audio features when training the model

```bash
python main.py data/ --workspace workspace/ -O --iters 100000 --asr_model cpierse/wav2vec2-large-xlsr-53-esperanto
```

7. Incorrect ffmpeg version for rtmp streaming
Online users reported that version 4.2.2 is needed. I'm not sure which versions exactly don't work. The principle is to run ffmpeg, and the printed information should include libx264. If it's not there, it definitely won't work.
```
--enable-libx264
```
8. Replace with your own trained model
```python
.
├── data
│ ├── data_kf.json (corresponds to transforms_train.json in the training data)
│ ├── au.csv
│ ├── pretrained
│ └── └── ngp_kf.pth (corresponds to the trained model ngp_ep00xx.pth)

```

For other references, see:
https://github.com/lipku/metahuman-stream/issues/43#issuecomment-2008930101
