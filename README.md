# konlpy-fastapi

[기존 이미지 메인테이너 왈](https://hub.docker.com/r/roseline124/konlpy-fastapi)
> konlpy에서 자바를 사용하기때문에 우분투 이미지를 베이스로, java와 python이 함께 깔려있어야 한다.
> 첫 설정에 어려움이 많아 docker image로 만들어 Hub에 올려놓습니다.

필요에 따라 다른 파이썬 버전을 사용해야하는 상황에서도 사용이 가능하도록 버전 별로 이미지를 빌드했습니다.

## 필요 조건

 - requirements.txt (docker 내에서 uvicorn으로 실행하므로 uvicorn은 꼭 있어야 한다)

## 실행

 - 도커 이미지 가져오기

```bash
docker pull mineru/konlpy-fastapi:py37
docker pull mineru/konlpy-fastapi:py38
docker pull mineru/konlpy-fastapi:py39
docker pull mineru/konlpy-fastapi:py310

docker pull mineru/konlpy-fastapi:py37-mecab
docker pull mineru/konlpy-fastapi:py38-mecab
docker pull mineru/konlpy-fastapi:py39-mecab
docker pull mineru/konlpy-fastapi:py310-mecab
```

- 컨테이너 실행

```bash
docker run -d --name <컨테이너이름> -p 80:80 mineru/konlpy-fastapi:py38
```

## dockerfile
```
FROM ubuntu:latest
LABEL maintainer="mineru98 <mineru664500@gmail.com>"
ARG PYTHON_VERSION=3.7

ENV LANG=C.UTF-8
ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHON_VERSION=python${PYTHON_VERSION}
RUN apt-get update && \
  apt-get install -y --no-install-recommends tzdata g++ curl git

# install java
RUN apt-get install -y openjdk-8-jdk
ENV JAVA_HOME="/usr/lib/jvm/java-1.8-openjdk"

# install python
RUN apt-get install -y python3-pip python3-dev
RUN apt-get install -y ${PYTHON_VERSION}
RUN cd /usr/local/bin && \
  ln -s /usr/bin/python3 python && \
  ln -s /usr/bin/pip3 pip && \
  pip install --upgrade pip

# apt clean
RUN apt-get clean && \
  rm -rf /var/lib/apt/lists/*

# copy resources
COPY . .

# install python package
RUN pip install -r requirements.txt
```