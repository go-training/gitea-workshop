# gitea-workshop

Starting with Gitea 1.19,  Gitea Actions are available as a built-in CI/CD solution.

## 事前準備

1. 註冊 [Docker Hub][1] 帳號
2. 登入 [Play With Docker][2] 平台

也可以在 [DigitalOcean](https://cloud.digitalocean.com/) 上建立 Droplet，並安裝 [Docker](https://docs.docker.com/engine/install/ubuntu/).

[1]:https://hub.docker.com/
[2]:https://labs.play-with-docker.com/

## 安裝 Gitea 服務

多種安裝方式可以參考[線上文件][3]，我們選擇使用 [Installation with Docker (rootless)][4] 方式安裝。

```bash
mkdir -p gitea/{data,config}
cd gitea
touch docker-compose.yml
sudo chown 1000:1000 config/ data/
```

打開 `docker-compose.yml` 檔案，輸入以下內容：

```yaml
version: "2"

services:
  server:
    image: gitea/gitea:1.20-rootless
    restart: always
    volumes:
      - ./data:/var/lib/gitea
      - ./config:/etc/gitea
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "3000:3000"
      - "2222:2222"
```

[3]:https://docs.gitea.com/next/category/installation
[4]:https://docs.gitea.com/next/installation/install-with-docker-rootless

## 啟動 Gitea 服務

```bash
docker-compose up -d
```

![install gitea](./images/install-gitea-from-docker.png)

請點選 3000 port 的連結就可以看到首頁了，請參考底下畫面

![gitea install page](./images/install-gitea-home.png)

設定 Administrator 帳號，請參考底下畫面

![install administrator](./images/install-gitea-administrator.png)

按下送出後，請等待 Install 過程

![install process](./images/install-gitea-loading.png)

## 安裝 Gitea Actions

從 Gitea 1.19 版本開始，[Gitea Actions][11] 就已經內建在 Gitea 服務中。請打開 `config/app.ini` 檔案，並增加底下設定

```yaml
[actions]
ENABLED=true
```

請重新啟動 Gitea 服務

```bash
docker-compose restart
```

請點選 `Site Administration`

![click administration link](./images/gitea-administration.png)

點選 Runner Tab 選單

![create runner](./images/create-runner-page.png)

[11]:https://docs.gitea.com/next/usage/actions/overview

接著透過 docker compose 來啟動 runner 服務，先建立 `gitea` 資料夾，並建立 `docker-compose.yml` 檔案

```bash
mkdir -p gitea/data
cd gitea
touch docker-compose.yml
sudo chown 1000:1000 data/
```

打開 `docker-compose.yml` 檔案，輸入以下內容：

```yaml
version: "2"

services:
  runner:
    image: gitea/act_runner:0.2.3
    restart: always
    volumes:
      - ./data/act_runner:/data
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - GITEA_INSTANCE_URL=<instance url>
      - GITEA_RUNNER_REGISTRATION_TOKEN=<registration token>
```

請將 `<instance url>` 替換成你的 Gitea 服務網址，`<registration token>` 替換成你的 Runner Token。啟動後可以在後台看到底下畫面

![gitea runner list](./images/gitea-runner-list.png)
