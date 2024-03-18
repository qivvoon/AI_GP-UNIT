# GP-UNIT을 이용한 명화 속 인물을 사람 얼굴로 변환
Content Encoder, Style Encoder, 생성자(Generator)를 이용해 명화 속 인물을 여자 사람 얼굴로 변환시킬 수 있다. </br>
Pretrained Content encoder와 Style Encoder, 생성자모델 학습을 위한 코드는 GP-UNIT에서 제공해준다. <br></br>

## Datasets
* [MetFaces](https://github.com/NVlabs/metfaces-dataset)
* [CelebA-HQ](https://github.com/clovaai/stargan-v2#datasets-and-pre-trained-networks)

## 모델 학습 과정
1. fork한 repository([GP-UNIT](https://github.com/williamyang1991/GP-UNIT))를 clone한다.
2. GP-UNIT repository를 클론한 폴더에 `data` 폴더를 생성한 후(`GP-UNIT/data`), 위의 데이터셋을 다운로드한다.
3. `GP-UNIT/checkpoint` 폴더에 `content_encoder.pt`와 `model_ir_se50.pth`를 다운로드한다.
* [content_encoder.pt](https://drive.google.com/file/d/1I7_IMMheihcIR57vInof5g6R0j8wEonx/view?usp=sharing) | pretrained content encoder
* [model_ir_se50.pth](https://drive.google.com/file/d/1KW7bjndL3QG3sxBbZxreGHigcCCpsDgn/view?usp=sharing) | pretrained IR-SE50 model for ID loss
4. GP-UNIT에서 제공해주는 `train.py`를 통해 생성자모델 학습을 진행한다.
  * `--task`: 생성자모델의 이름을 지정할 때 사용한다.
  * `--source_paths`: content image dataset의 경로를 지정할 때 사용한다.
  * `--target_paths`: style image dataset의 경로를 지정할 때 사용한다.
  * `--iter` (default=75000): epoch 수를 지정해주는 옵션이다.
  * `--style_layer` (default=4): `--style_layer=5`로 설정 시, 사람 얼굴 변환에서 더 좋은 성능을 얻을 수 있다.
  * `--use_allskip` (default=False): `Dynamic Skip Connection`을 진행시켜 content image의 특징을 더 반영한 result를 생성할 수 있다.
  * `--use_idloss` (default=False): GP-UNIT 문서에 따르면 cat/dog → face, face → metface 변환 시, --use_idloss를 사용하여 더 좋은 성능을 얻었다. 또한, `ID Loss`는 `Identity loss`를 의미하므로, 이를 사용하면 style image의 특징을 더 반영하고, 더 자연스러운 이미지를 생성할 것이다.

  ```
  python train.py --task paint2female --batch 4 --style_layer=5 --use_allskip --use_idloss
                  --source_paths data/images --target_paths data/celeba_hq/train/female
  ```
5. 학습이 완료되면 `GP-UNIT/checkpoint` 폴더에 `paint2female` 모델이 생성된다.

## 결과
```
python inference.py --content [conten image 경로] --style [style image 경로]
                    --name [생성할 이미지 이름]
                    --generator_path [이전에 학습시킨 생성자모델 경로]
```
<img width="457" alt="스크린샷 2023-12-26 오후 3 00 18" src="https://github.com/qivvoon/GP-UNIT/assets/90748096/6b2d98a9-ef43-476b-809e-95471a3d2a6f">
<br></br>
그러나 content image와 style image의 시선 방향이 다를 경우, 생성된 이미지가 자연스럽지 못하다.
<img width="457" alt="image" src="https://github.com/qivvoon/GP-UNIT/assets/90748096/7d0fad9c-fdec-4915-93e9-e42537610577">

## 또 다른 실험들
Dynamic Skip Connection 없이 모델 학습 </br>
<img width="457" alt="스크린샷 2024-01-02 오후 3 54 47" src="https://github.com/qivvoon/GP-UNIT/assets/90748096/a41b69ac-64fe-4406-bbb3-56d47f5d894d">
<br></br>
Identity loss 없이 모델 학습 </br>
<img width="457" alt="스크린샷 2024-01-02 오후 3 55 24" src="https://github.com/qivvoon/GP-UNIT/assets/90748096/0a6f8d87-8008-4093-be52-b5c00c4eab4b">

## 논문
[GP-UNIT을 이용한 명화 속 인물을 여자 사람 얼굴로 변환.pdf](https://github.com/qivvoon/AI_GP-UNIT/files/14640703/GP-UNIT.pdf)

