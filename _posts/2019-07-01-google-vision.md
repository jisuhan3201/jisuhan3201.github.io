---
layout: post
title:  "Google Cloud Vision"
author: jisu
categories: [ gcp, cloud, python ]
image: assets/images/google-vision/google-vision-api.jpg
beforetoc: "Google Cloud Vision API (이미지 출처: https://image.slidesharecdn.com/googlevisionapi-160618133018/95/google-vision-api-1-638.jpg?cb=1466256779)"
toc: true
---
클라우드는 주로 AWS(Amazon Web Service)를 사용하지만 프로젝트에 OCR(Optical Character Recognition) 기능이 필요해 살펴보니 AWS Textract 같은 경우 한글을 지원하지 않았다. 또 구글 비전에 대한 평이 좋다는 소문을 듣고 사용해보았다.

## 시작하기

(계정 생성 및 결제 정보 입력 부분은 생략)

Cloud Vision API 빠른 시작 : 클라이언트 라이브러리 사용하여 시작하기 (<https://cloud.google.com/vision/docs/quickstart-client-libraries>)

![quickstart]({{ site.baseurl }}/assets/images/google-vision/quickstart-doc.png)

위 문서에서 설명하는 순서대로 따라가본다.

**1. 리소스 관리페이지 이동**

![quickstart1]({{ site.baseurl }}/assets/images/google-vision/1.png)
* 프로젝트 만들기 버튼 클릭 후 프로젝트 생성

<br>

**2. 결제 사용설정 방법 알아보기**
* 결제 계정이 하나인 경우 프로젝트에 자동으로 설정된다.

**3. API 사용 설정**

![quickstart3]({{ site.baseurl }}/assets/images/google-vision/3.png)
* 밑에 알림만 보고 왜 `요청한 API를 사용할 수 있는 권한이 없습니다.`라고 뜨지 했는데 위에 프로젝트 선택을 해줘야된다.

**4. 서비스 계정 키 만들기**

![quickstart4]({{ site.baseurl }}/assets/images/google-vision/4.png)
* 새 서비스 계정 선택
* 서비스 계정 이름 입력
* 역할 -> 프로젝트 -> 소유자 선택 (모든 권한 부여)
* 키유형 선택 

이렇게 하면 키가 다운 받아진다. (필자는 json 형식으로 받음)

**5. 환경 설정**

pip 과 virtualenv 를 사용해도 되지만 필자는 python 공식 홈페이지에서도 권장하는 pipenv를 선호한다.

> pipenv --three <br> pipenv shell <br> pipenv install google-cloud-vision

> export GOOGLE_APPLICATION_CREDENTIALS="/path/to/json/file.json"

* 환경 변수 설정시 절대 경로 지정 (/Users부터 시작)


## 구현

```
import io
import os
from google.cloud import vision
client = vision.ImageAnnotatorClient()

def main():

    detect_text("./feature.png")


def detect_text(path):

    with io.open(path, 'rb') as image_file:
        content = image_file.read()

    image = vision.types.Image(content=content)

    response = client.text_detection(image=image)
    texts = response.text_annotations
    print('Texts:')

    for text in texts:
        print('\n"{}"'.format(text.description))

        vertices = (['({},{})'.format(vertex.x, vertex.y) for vertex in text.bounding_poly.vertices])

        print('bounds: {}'.format(','.join(vertices)))


if __name__ == "__main__":
    main()
```

이 코드는 문서와 거의 동일하다.

## 마치며

![test-image]({{ site.baseurl }}/assets/images/google-vision/feature.png) <br>

![test-image]({{ site.baseurl }}/assets/images/google-vision/6.png)

사진이 선명해서 그런지 거의 100% 정확도로 사진에서 추출되었다. <br> 재밌던 것은 세로로 된 글자도 인식해서 뽑아진다는 것. 간단하게 테스트 마무리.

