# Docker 學習筆記
## Container
[ref](https://docs.docker.com/reference/cli/docker/container/)
### 列出所有container
```shell=
$ docker ps
```
> `-a` 列出未開啟的container
> `--size` 列出size

---

### 啟動container
```shell=
$ docker start {container name or id}
```

---

### 進入指定container
```shell=
$ docker exec -it {container name or id} bash
```

---

### 退出container
> `Ctrl + D` or `exit`

---

### 停止container
```shell=
$ docker stop {container name or id}
```

---

### 建立container
```shell=
$ docker create -it {imaage name}
```
```shell=
$ docker run -it {imaage name}
```
* `docker run` = `docker create` + `docker start`

> `-it` 讓terminal可以正常使用的東西，打就對了
> `--name <container name>` 幫 container 取名
> `-v <target path or volume name>:<container path>` 使用volume，先打已創建的volume名稱或本地(linux/wsl)路徑，冒號後面打創建container後要放的路徑
> `-p <host port>:<container port>` 將本機的port對應到container的port
> `--rm` 執行完成後自行從硬碟刪除 container (酷東西列爽的可能用不到)

---

### 刪除container
一定要確定不用再刪，linux沒有資源回收桶
```shell=
$ docker stop {container name or id} # 停止container
$ docker rm {container name or id} # 移除指定container
$ docker volume rm {volume name or id} # 移除volume
```

## Image
[ref](https://docs.docker.com/reference/cli/docker/image/)
### 列出所有image
```shell=
$ docker image ls
```

---

### 建立image (with Dockerfile)
```shell=
$ docker build -t {name}:{tag} .
```
如果不需要tag可以不用打`:`
`.`代表當前目錄，Docker 會在這裡尋找 Dockerfile

## 將image存成.tar檔
```shell=
$ docker save -o {filename}.tar {repo}:{tag}
```
* 範例:
    > filename:  `ml-dev_cu12.4-py311-torch2.4.0.tar`
    > repo: `nchunlplab/ml-dev`
    > tag: `cu12.4-py311-torch2.4.0-jupyterlab-vscode`

## 將.tar檔讀進docker images
```shell=
$ docker load --input {filename}.tar
```


## Dockerfile
> 一種建置Docker image的腳本
* 範例:
    ```Dockerfile=
    # 使用官方的 Python 3.9 基礎映像
    FROM python:3.9-slim

    # 設定工作目錄
    WORKDIR /app

    # 複製當前目錄的所有文件到容器內的 /app 目錄
    COPY . /app

    # 安裝需求（如果有 requirements.txt）
    RUN pip install --no-cache-dir -r requirements.txt

    # 設定啟動應用的指令
    CMD ["python", "app.py"]
    ```
> `FROM` 指定base image
> `WORKDIR` 指定docker執行起來時候的預設目錄位置
> `COPY` 複製文件至container中 (若要直接使用volume來串資料則不需要這個)
> `RUN` build過程中需要執行的指令或安裝
> `CMD` Container 啟動後執行的指令
> :::info
> `CMD`不能空白或不打，否則 Container 一開起來就會馬上關閉
> `CMD ["tail", "-f", "/dev/null"]` 可以讓 Container 保持開起但不執行任何命令
> `CMD ["/usr/sbin/sshd", "-D"]` 啟動SSH服務 (也可以達到保持開起的效果)
> :::
> `EXPOSE` 列出服務會用到哪些port，沒打其實也沒差(但有比較嚴謹)，重點在 `docker create/run` 時要 `-p` 這些port
> `ENV` 先定義好一些環境變數，例如 `ENV PATH=$PATH:/user_data/.local/bin`
> `ARG` 在 `build` 時，提供一個可以用 `--build-arg` 帶入的環境變數。


## Volume
[ref](https://docs.docker.com/engine/storage/volumes/)
### 建立volume
```shell=
$ docker volume create {volume name}
```

### 刪除volume
```shell=
$ docker volume rm {volume name}
```

### 列出所有volume
```shell=
$ docker volume ls
```

# Docker Compose
[ref](https://docs.docker.com/compose/)
## Dockerfile vs Docker-compose
* **Dockerfile:** 描述image，設定客製化image (類似於一個建立image的)
* **Docker-compose:** 描述container，管理一個以上的container，彼此串聯，把架構寫在一起，設定container參數
    > 儘管只有一個 container，也建議使用 `docker-compose.yml`

---

## 建立並啟動 Container
```shell=
$ docker compose -f {filename}.yml -d --build
```
> `-d` Detached mode: Run containers in the background
> `--build` 建立Container之前先build image，如果已經有該image，可以直接在.yml中指定image

##  YAML檔 (.yml)
> Docker Compose所用的檔案
* 範例:
    ```YAML=
    version: '2'

    services:
      {service name}:
        build: "."
        # 如果已有image，則 `build` 可改成 `image: {image name}:{image tag}`
        container_name: "{container_name}"
        restart: always
        ports:
          - "27720:3000"
        volumes:
          - {target path or volume name}:{container path}
    ```

---

## License

docker-learn © 2024 by Chen-Jui, Yu is licensed under CC BY 4.0.  
Shield: [![CC BY 4.0][cc-by-shield]][cc-by]

This work is licensed under a  
[Creative Commons Attribution 4.0 International License][cc-by].

[![CC BY 4.0][cc-by-image]][cc-by]

[cc-by]: http://creativecommons.org/licenses/by/4.0/  
[cc-by-image]: https://i.creativecommons.org/l/by/4.0/88x31.png  
[cc-by-shield]: https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg



