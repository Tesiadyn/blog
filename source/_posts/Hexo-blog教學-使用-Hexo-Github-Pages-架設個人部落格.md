---
title: Hexo blog教學 - 使用 Hexo + Github Pages 架設個人部落格
date: 2024-08-12 15:17:43
tags:
---

## 前言 - Hexo 是什麼?

今天來整理使用 Hexo 架設個人部落格的過程，希望能夠幫助到正在考慮要不要使用的人。

Hexo 是一個基於 Node.js 的網誌框架，雖然剛開始可能會需要一些學習時間，但它有以下優點:

- 基本上免費
- 編譯速度很快
- 支援 MarkDown 撰寫
- deploy 自動化
- 數百個社群提供的主題供使用

## 前置需求

- [Node.js](https://nodejs.org/en/download/package-manager)
  官網提到: Node.js 的版本需要不低於 8.10, 建議使用 10.0 以及以上版本
  因為 Hexo 官方不會對以往版本的 Hexo 進行錯誤修復，所以強烈建議安裝最新版本的 Hexo 以及推薦版本的 Node.js。

- [Git](https://git-scm.com/downloads)
  版本控制工具，我們用它來把部落格的檔案送到 Github 上。

安裝完以上兩個東西後可以在 terminal 中使用以下兩個指令來查看現在安裝的版本是多少(有顯示代表有安裝成功)。

```
git -v
```

```
node -v
```

![testing-for-version-of-node-and-git](https://imgur.com/XLB85B5.png)

## 安裝 Hexo

1. 在 terminal 使用以下指令安裝 Hexo

```
$ npm install hexo-cli -g
```

使用以下指令來確定 Hexo 已經安裝完成，如果沒有問題應該會出現類似以下畫面:

```
$ hexo -v
```

![testing-for-version-of-hexo](https://imgur.com/qu3GXVW.png)

2. 安裝完後再使用 CLI 指令來初始化資料夾

```
$ hexo init <folder name> // 初始化資料夾，如果沒有相符名稱的資料夾會自己建立
$ cd <folder name>        // 進入上一步的資料夾
$ npm install             // 安裝相依套件
```

經過以上步驟後，應該會看到一個像是下面這樣的資料夾結構:

```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

### 主要檔案結構介紹

#### \_config.yml

設定用檔案，大部分關於部落格的設定都可以在這邊找到。像是網頁標題以及作者名稱等等，詳細可以參考[官方文件](https://hexo.io/zh-tw/docs/configuration)

#### package.json

配置文件，預設已經有安裝 EJS, Stylus 和 Markdown renderer，也可以手動移除。

#### scaffolds

scaffolds 資料夾，當建立新文章時，Hexo 會根據 scaffold 來建立檔案(類似範本)。
當我們使用 $ hexo new <類型> <文章名稱>，就會依照 scaffolds 內的對應類型產生文章檔案。

#### source

資源資料夾，檔案 / 資料夾名稱開頭為 \_ (底線) 和隱藏的檔案會被忽略，除了 \_posts 資料夾以外。 Markdown 和 HTML 檔案會被處理並放到 public 資料夾，而其他檔案會被拷貝過去。

#### themes

存放主題的資料夾。 Hexo 會根據設定的主題來產生靜態檔案。

其實到這邊已經差不多安裝完成了，如果我們使用以下指令，應該可以在預設路徑看到部落格的預覽，預設路徑為 http://localhost:4000/

```
# hexo server  //    or  $ hexo s
```

## 常用操作指令

#### new

```
$ hexo new [layout] <title>
```

建立新的文章，如果沒有設定 layout 的話，則會使用 \_config.yml 中的 default_layout 設定代替。

#### generate

```
$ hexo generate
```

產生靜態檔案。

#### clean

```
$ hexo clean
```

清除快取檔案 db.json 以及 public 資料夾的內容。

#### server

```
$ hexo server
```

啟動本地伺服器，預設為 http://localhost:4000/。

#### deploy

```
$ hexo deploy
```

部屬網站。

## 更換主題

既然我們現在有自己的部落格了，當然不太會想要跟其他人一樣用預設的主題，這時候我們就可以到 Hexo 官方的[主題頁面](https://hexo.io/themes/)來找到我們想要的主題，這邊我選用[Prince](https://github.com/yiliashaw/hexo-theme-prince)。

進到 Prince 主題頁面後可以參照 README 的內容來一步步完成設定:

![Prince-github-page](https://imgur.com/a8Z3nqX.png)

1. 安裝主題
   在 Hexo 的根資料夾下使用以下指令將檔案 pull 下來。

```
$ npm install hexo-renderer-jade --save
$ git clone https://github.com/yiliashaw/hexo-theme-prince.git themes/prince
```

2. 更改主題為 Prince
   在根目錄的\_config.yml(不是 themes/prince 底下的\_config.yml)找到 theme(預設為 landscape)，將其更改為 prince。
   未來如果主題的 Github Repository 有更新的話，也只需要執行以下的指令讓其保持最新版本。

```
cd themes/prince
git pull
```

3. 主題相關設定
   主題本身會提供一些自訂的選項，可以參照你選的主題的 Github 頁面以及 theme 資料夾中的\_config.yml 來看看作者提供了哪些選項讓你調整。

## 部屬到 Github Pages

1. 先到 Github 創建一個新的 Repository，名稱為"<你的 Github 使用者名稱>.github.io"。
   以我的為舉例，會是 tesiadyn.github.io。

2. 接下來把檔案推送到預設的 branch 上，一般預設會是 main 或是 master。

3. 到 Github repository 的設定中，找到 Setting -> Pages -> Source 改為 Github Actions。

4. 到 repository 頁面 -> Add file -> Create new file -> 命名為.github/workflows/pages.yml。

![repo-create-new-file](https://imgur.com/1n96NV0.png)

5. 將檔案改為以下內容，並且根據 node -v 指令所顯示的版本更改 Use Node.js <你用的版本數字> 更改完後按下右上綠色按鈕"Commit changes..."。

```
name: Pages

on:
  push:
    branches:
      - main # default branch

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          # If your repository depends on submodule, please see: https://github.com/actions/checkout
          submodules: recursive
      - name: Use Node.js 20
        uses: actions/setup-node@v4
        with:
          # Examples: 20, 18.19, >=16.20.2, lts/Iron, lts/Hydrogen, *, latest, current, node
          # Ref: https://github.com/actions/setup-node#supported-version-syntax
          node-version: "20"
      - name: Cache NPM dependencies
        uses: actions/cache@v4
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    needs: build
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

6. 設定 submodule
   接下來我們到部落格的根目錄，新建一個名稱為.gitmodules 的檔案，並填入以下內容(以我的為例，請更改為您選擇的主題)。

```
[submodule "themes/prince"]
path = themes/prince                                      //  主題資料夾的路徑
url = https://github.com/yiliashaw/hexo-theme-prince.git  // 主題repository
```

7. 設定 url
   到根目錄的\_config.yml 找到 url，並且設定為 https://<你的使用者名稱>.github.io/。

8. 完成
   這時候應該可以在 https://<你的使用者名稱>.github.io/看到你的部落格網頁了!
   Github 會在你每次推送新 commit 時自動嘗試部屬，如果像以下出現綠勾勾的 successful checks，就是代表成功了。

![repo-page-successful-checks](https://imgur.com/DNzuHs7.png)
