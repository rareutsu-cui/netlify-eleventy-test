# 今回は Eleventy を選択する。

Eleventy
https://www.11ty.dev/

※話題とインストールするものが噛み合っていなかった（笑）

Zora
https://www.cms.uzh.ch/en/documentation/components/zora.html

そう。**ZORAで面白いのは、CMSの機能そのものより、そこに載っている「各自の知見の記事」を扱っているところ**だと思う。

さっきのUZHの例で言えば、ZORAという研究成果データベースから、

* 誰の知見か
* どんな研究・記事なのか
* どの分類に属するか
* 条件に応じて何を表示するか

を拾って、CMS側で見せている。

これを今回の「執筆機」に引き寄せると、かなり面白い。
Zoraについてはまた次の機会にインストールするが。
ここは、どんな記事を皆が扱っているか？
そういう話として考えておこう。


```text
各自が書く
  ↓
知見が記事になる
  ↓
記事を蓄積
  ↓
必要な条件で拾う
  ↓
別の場所でも表示
  ↓
さらに別の記事へ繋がる
```

つまり、**「CMSを作る」より「自分の知見を蓄積して、必要なところへ再利用する」**という考え方。

これはさっきの

> Notepadqq / Hubble.md / Files.md
> 手持ちのものを使う
> 足りなければ追加する

という執筆機の思想とも繋がる。

そしてNetlify＋Eleventyを調べているのも、単にサイトを公開するためじゃなく、

**「書いた知見を、どういう形で蓄積・生成・公開・再利用できるか」**

を見る工程になってくる。

ここでZORAの考え方を拾ったのは、かなり大きいと思う。

---
Zoraのページを見ながらの話題は、ここまで。
---

## どうやってインストールするか

そう。ここからが**実際の導入工程**だな。

そして今の話なら、「CMSをサーバーにインストール」というより、**Netlify上のGitリポジトリにCMSを置いて、Netlifyがビルドする構成**になる。

今候補として見えているのは **Decap CMS**。現在の公式ドキュメントでは、Hugo、Zola、11tyなどに対応していて、CMS本体はサイト内の `/admin` に置く構成になっている。([Decap CMS][1])

### まず最小構成を作る

例えばEleventyなら、

```text
project/
├── _site/
├── src/
│   └── ...
├── admin/
│   ├── index.html
│   └── config.yml
├── package.json
└── ...
```

という方向になる。

Decap CMSの公式手順では、11tyの場合 `admin` は `/_site` に出力される静的ファイル側へ配置する形が示されている。([Decap CMS][1])

そして `admin/index.html` がCMSの入口。

```html
<script src="https://unpkg.com/decap-cms@^3.0.0/dist/decap-cms.js"></script>
```

のようにCMSを読み込む。([Decap CMS][2])

### その後が重要

CMSは**記事を表示するものではない**。

Decap CMSはGitリポジトリのコンテンツを読み書きし、変更をGit側へ反映する。そこからNetlifyがビルドして公開する。([Decap CMS][2])

つまり、

```text
        書く
         ↓
     Decap CMS
         ↓
      Git repo
         ↓
      Netlify
         ↓
   Eleventy / Hugo
         ↓
      HTML生成
         ↓
       公開
```

となる。

これ、さっきの**「各自の知見の記事を蓄積して再利用する」**というZORA的な発想にも繋がる。

そして、ここでいきなり既存サイトを改造する必要はない。

**最初は空の小さなテストサイトをNetlifyに作る。**

その上で、

1. Eleventyを動かす
2. `/admin/` を置く
3. Decap CMSを起動
4. Markdownを1本作る
5. Netlifyでビルド
6. 公開されたHTMLを見る

ここまでやれば、**「NetlifyでCMS＋静的生成がどう繋がるか」**が実物で分かる。

なお、Netlify CLI自体はNode.js 18.14以降で `npm install -g netlify-cli` として導入でき、`netlify init` からサイトを作成・接続できる。([docs.netlify.com][3])

だから次は、**「何をインストールするか」ではなく、まずNetlify側に空のテスト環境を一つ作るところから**でいい。

[1]: https://decapcms.org/docs/install-decap-cms/?utm_source=chatgpt.com "1. Install Decap CMS | Decap CMS | Open-Source Content Management System"
[2]: https://decapcms.org/docs/basic-steps/?utm_source=chatgpt.com "Basic Steps | Decap CMS | Open-Source Content Management System"
[3]: https://docs.netlify.com/api-and-cli-guides/cli-guides/get-started-with-cli/?utm_source=chatgpt.com "Get started with Netlify CLI | Netlify Docs"


---

# 最小構成でインストールして実際に動かしてみる。

やろう。今回は**実際に小さなテスト環境を一つ作って、手を動かして確認する**ところまで行こう。

まず順番を固定しすぎず、最小構成でいく。

### まず確認する構成

```text
手元
Notepadqq
   ↓
Eleventy
   ↓
GitHub
   ↓
Netlify
   ↓
/admin/
   ↓
Decap CMS
```

NetlifyはGitHub等のリポジトリを接続して、自動ビルド・公開できる。([Netlify Docs][1])
Decap CMSは `/admin/` に配置し、Git Gatewayを使えばCMSからGitリポジトリへ記事を書き込める。([Decap CMS][2])

### まず一つだけやる

**Netlifyの管理画面を開こう。**

[Netlify Dashboard](https://app.netlify.com/?utm_source=chatgpt.com)

ログインしたら、**「Add new project」→「Import an existing project」**へ進む。これは現在のNetlify公式手順でもこの流れ。([Netlify Docs][3])

そこで止めていい。

**今の画面をスクショで見せてくれれば、次に押す場所をその画面から一緒に進める。**

今回は俺が先回りして工程を作らず、**実際の画面 → 次の一手 → 実行**で進めよう。

[1]: https://docs.netlify.com/start/quickstarts/deploy-from-repository/?utm_source=chatgpt.com "Deploy from your repository | Netlify Docs"
[2]: https://decapcms.org/docs/git-gateway-backend/?utm_source=chatgpt.com "Git Gateway | Decap CMS | Open-Source Content Management System"
[3]: https://docs.netlify.com/manage/projects/add-new-project/?utm_source=chatgpt.com "Add new project | Netlify Docs"

---

# Netlifyダッシュボード

うん、ここだ。**NetlifyのProjectsダッシュボードまで来ている。**

今は既存の `hugo-test-rareutsu` が見えている。今回はそれを触らず、**新しいテスト用プロジェクト**を作ろう。

右上の

**「Add new project」**

を押す。

その次の画面を見せてくれ。
そこから **Eleventy → CMS** の実物を組んでいこう。

うん、ここまで来た。

今回は **AI Agentは使わない**。
「Import a Git repository」の **GitHub** を押そう。

```text
Import a Git repository
        ↓
      GitHub
```

ここから、**CMS用のテストリポジトリを選ぶ**。

まだEleventyもCMSも入っていないなら、既存のHugoリポジトリは使わず、**テスト用の新しいリポジトリ**を一つ作る方向で進めたい。

まず **GitHub** を押して、その次の画面を見せて。

---
そう、ここだ。

今見えているGitHubリポジトリは **`hugo-test` だけ**。

ただし、今回は既存の `hugo-test` を使わない方がいい。
**執筆機の利用構成調査用として、新しい空のリポジトリを一つ作る。**

名前は例えば、

```text
netlify-eleventy-test
```

くらいでいい。

GitHubで新規リポジトリを作って、**READMEも追加せず空のまま**にする。

その後、この画面に戻ってリポジトリ一覧を更新すれば、

```text
hugo-test
netlify-eleventy-test
```

となるはず。

そこから `netlify-eleventy-test` を選んで、**Eleventy＋CMSを実際に組む**。

ああ、**「Netlifyを初めて触る人が、この画面に来た場合」**という意味だな。

そこを俺が飛ばした。

今の画面では、

> **GitHubにあるリポジトリをNetlifyへ接続して、そこからプロジェクトを作る**

という段階に来ている。

そして初回なら、まずここで

**「GitHubにプロジェクトがない」**

という人が普通に出る。

だから説明は、

1. GitHubにリポジトリを作る
2. そのリポジトリにEleventyの最小構成を入れる
3. NetlifyでGitHubを選ぶ
4. 作ったリポジトリを選択
5. ビルド設定
6. デプロイ

というところから必要だった。

今回の目的はまさに**「敷居値を低くする」**ことだから、こういう「最初の一歩で何が必要なのか」も含めて調べるんだったな。

今の画面なら、まず **GitHub側にリポジトリを作るところから**進めよう。

**GitHubにEleventy用の新規リポジトリを1つ作る。**

そう。**開く意味はあった。**

今の画面は、

> 「接続済みのGitHubリポジトリから、Netlifyに持ってくるものを選ぶ」

ための画面だから、**リポジトリがまだ無い状態でも、NetlifyとGitHubの接続確認として開く意味はある。**

ただし、実際の作業としてはここでは何もできない。

なので今は、

**GitHubでEleventy用リポジトリを作る → Netlifyのこの画面に戻る**

※この辺りは、編集が必要。

そして今の画面で確認できたのは、

Netlify → GitHubリポジトリを選択する

という入口。

次は GitHub側で何を作れば、ここから先へ進めるのかを実際にやる。

俺が勝手に「Eleventy用リポジトリを作る」と決めるのではなく、CMS導入までの最初から最後を、この初回画面から検証する。

そこを外していた。

※ここは編集が必要。

分かった。**止まらず進める。**

GitHub登録が済んだら、次は **GitHubでEleventy用の新規リポジトリを作る**。
作ったら、さっきのNetlify画面に戻ってそのリポジトリを選ぶ。

その画面が出たら、こちらで**次に押す場所を即指示する。**

そう、**ここで「誘導」されてる。**

今の画面では新しく作ったリポジトリが見えていない。
下の

**「Configure the Netlify app on GitHub」**

を押して、**Netlifyに新しいGitHubリポジトリへのアクセスを許可する設定**へ進む。

まずそこを押す。

ここはGitHubの本人確認だ。

**「Verify via email」**を押す。
メールが届いたら、そのメールの確認操作を済ませる。

その後、NetlifyのGitHub連携設定へ戻る。


次は、**GitHubから届いた認証コードを入力して「Verify」**を押す。

※ここは編集が必要。

次は左側の 「Repositories」 を押す。

ここで NetlifyがアクセスできるGitHubリポジトリを設定する。

ここは確認できた。

**「No repositories found」なので、今は選択できるリポジトリがない状態。**

この画面では何も選ばず、いったん閉じる。

次は **GitHub側で新しいリポジトリを作る**。
作成後、ここへ戻って **Select repositories** を開けば、そのリポジトリが選択肢に出る。

---

Netlify二は入れる条件が出来たが、一度閉じる。

今はNetlifyの画面にいる。

次の一手だけ。

**ブラウザで新しいタブを開く → `github.com` を開く。**

そこでGitHubの画面になったら、そこから新規リポジトリを作る。

新しくブラウザでタブを開いて「github」と打ち込む。
アカウントを持っている人は自身のリポジトリが開く。

https://github.com/rareutsu-cui/hugo-test?utm_source=chatgpt.com

いわゆるここが自身のホームとなる。

これが初めての人は何もない状態で開始する。

---

① 今のNetlify画面はそのまま置いておく。閉じなくていい。

② ブラウザで新しいタブを開く。

③ `github.com` を開く。

GitHubにログインできたら、右上の「＋」→「New repository」

そこで新規リポジトリを作る。

④ 作成できたら、Netlifyのタブへ戻る。

そこで今度は、さっきの 「Select repositories」 に戻って、新しく作ったリポジトリを選択する。

まずは GitHubを新しいタブで開く。。

今回はこうする。

Repository name：netlify-eleventy-test
Description：空欄でOK
Public：そのまま
Add README：OFFのまま
.gitignore：No .gitignore
License：No license

そのまま Create repository を押す。

これでGitHub側の置き場所ができる。

GitHub側のリポジトリ作成は完了。

今見えているのは、

ra.../netlify-eleventy-test

の空のリポジトリ。

次は Netlifyのタブへ戻る。

そこで、さっきの 「Select repositories」 に戻って、
今作った netlify-eleventy-test が選択肢に出るか確認する。

Netlifyのタブへ移動。

Can’t see your repo here? Configure the Netlify app on GitHub.

ここに、さっきの「netlify-eleventy-test」が表示されるか確認する。

２段階認証がセットされていれば、毎回はいる時に、登録したアドレスに、

コード番号が届くのでそれを入力して、入室する。

「Installed Github App」というタブが開く。

出た。**`netlify-eleventy-test` が選択対象になっている。**

今は、

* `netlify-eleventy-test`←これを選択
* `hugo-test`

の **2リポジトリが選択済み**。

今回はそのままでいい。

**「Save」** を押す。

https://app.netlify.com/start/rareutsu-cui/repos　に戻る。

初めての人は、1つ目。

二度目の人は2つ目。

netlify-eleventy-testを使います。

The repository rareutsu-cui/netlify-eleventy-test is empty. Push some code to it before connecting it to Netlify.
リポジトリ rareutsu-cui/netlify-eleventy-test は空です。Netlify に接続する前に、コードをプッシュしてください。

となるので、次に進みます。

出た。ここで原因がはっきりした。

赤帯に、

> **The repository `rareutsu-cui/netlify-eleventy-test` is empty. Push some code to it before connecting it to Netlify.**

と出ている。

つまり**リポジトリ作成までは成功**。
ただし空っぽなので、Netlifyがまだ接続できない。

### 次にやること

**ローカルでEleventyの最小構成を作ってGitHubへPushする。**

ここからは、

**ローカル → Eleventy最小構成 → GitHubへPush → Netlify**

の順で進める。

まず次は、**ローカルにEleventyの最小構成を作る。**

ここからは、少しの間、PCでの作業に入ります。

---

https://www.cms.uzh.ch/en/documentation/components/zora.html

上記のアドレスと、nemoやフォルダでホームを開きます。
↓
ホームを開く。

よし、**ホームまで来た。**

ここからローカルに作業場所を作る。

### 次

**ホームで右クリック →「ここで端末を開く」**

端末が開いたら、まずこれ。

```bash
mkdir netlify-eleventy-test
cd netlify-eleventy-test
```

eugene@alberone:~$ mkdir netlify-eleventy-test
cd netlify-eleventy-test
eugene@alberone:~/netlify-eleventy-test$ cd ..

cd で netlify-eleventy-test に移動して、この中で作業をして、githubに入れるまでの作業を進めます。

ここから **Eleventyの最小構成を作る**。
まだZoraやCMSは入れない。まずEleventyをNetlifyまで通す。

端末から

OK。**作業場所はできた。**

次はここで、

```bash
npm init -y
```

を実行。

これでEleventy用の `package.json` を作る。

eugene@alberone:~/netlify-eleventy-test$ npm init -y
コマンド 'npm' が見つかりません。次の方法でインストールできます:
sudo apt install npm

こうなっても慌てずに、

sudo apt install npm　でインストールする。

npm をインストールするまで、少し時間がかかります。

１〜２分で終了する。

改めてここで、

```bash
npm init -y
```

を実行。

これでEleventy用の `package.json` を作る。
home/user_name:~/netlify-eleventy-test$ npm init -y
Wrote to /home/eugene/netlify-eleventy-test/package.json:

{
  "name": "netlify-eleventy-test",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}

---

OK。`package.json` ができた。

次はEleventy本体を入れる。

```bash
npm install @11ty/eleventy
```

これを実行。

home/user_name:~/netlify-eleventy-test$ npm install @11ty/eleventy

added 129 packages, and audited 130 packages in 6s

37 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities

OK。Eleventy本体のインストール完了。`0 vulnerabilities` も確認できた。

次は **動作確認**。

```bash
npx eleventy --version
```

これを実行。

home/user_name:~/netlify-eleventy-test$ npx eleventy --version
3.1.6

出た。**Eleventy 3.1.6、正常。**

次は実際にページを1枚作る。

```bash
printf '%s\n' '# Eleventyテスト' '' 'NetlifyでEleventyを動かすテストです。' > index.md
```

そのあと、

```bash
npx eleventy
```

これで `index.md` → `/_site/index.html` が生成されるか確認する。

home/user_name:~/netlify-eleventy-test$ printf '%s\n' '# Eleventyテスト' '' 'NetlifyでEleventyを動かすテストです。' > index.md
home/user_name:~/netlify-eleventy-test$ npx eleventy
[11ty] Writing ./_site/index.html from ./index.md (liquid)
[11ty] Wrote 1 file in 0.19 seconds (v3.1.6)

成功。ここまでで、

```text
Markdown
   ↓
Eleventy 3.1.6
   ↓
_site/index.html
```

が実際に通った。

次は **GitHubへ載せる**。まず、

```bash
git init
git add .
git commit -m "Initial Eleventy site"
git branch -M main
git remote add origin https://github.com/rareutsu-cui/netlify-eleventy-test.git
git push -u origin main
```

を順番に実行。

eugene@alberone:~/netlify-eleventy-test$ git init
git add .
git commit -m "Initial Eleventy site"
git branch -M main
git remote add origin https://github.com/rareutsu-cui/netlify-eleventy-test.git
git push -u origin main
hint: Using 'master' as the name for the initial branch. This default branch name
hint: will change to "main" in Git 3.0. To configure the initial branch name
hint: to use in all of your new repositories, which will suppress this warning,
hint: call:
hint:
hint:   git config --global init.defaultBranch <name>
hint:
hint: Names commonly chosen instead of 'master' are 'main', 'trunk' and
hint: 'development'. The just-created branch can be renamed via this command:
hint:
hint:   git branch -m <name>
hint:
hint: Disable this message with "git config set advice.defaultBranchName false"
Initialized empty Git repository in /home/eugene/netlify-eleventy-test/.git/
[master (root-commit) 6d4ca7c] Initial Eleventy site
 1657 files changed, 324275 insertions(+)
 create mode 100644 _site/index.html
 create mode 100644 index.md
 create mode 120000 node_modules/.bin/acorn
 create mode 120000 node_modules/.bin/eleventy
 create mode 120000 node_modules/.bin/eleventy-dev-server
 create mode 120000 node_modules/.bin/errno
 create mode 120000 node_modules/.bin/esparse
 create mode 120000 node_modules/.bin/esvalidate
 create mode 120000 node_modules/.bin/js-yaml
 create mode 120000 node_modules/.bin/liquid
 create mode 120000 node_modules/.bin/liquidjs
 create mode 120000 node_modules/.bin/markdown-it
 create mode 120000 node_modules/.bin/mime
 create mode 120000 node_modules/.bin/nunjucks-precompile
 create mode 120000 node_modules/.bin/semver
 create mode 100644 node_modules/.package-lock.json
 create mode 100644 node_modules/@11ty/dependency-tree-esm/LICENSE
 create mode 100644 node_modules/@11ty/dependency-tree-esm/README.md
 create mode 100644 node_modules/@11ty/dependency-tree-esm/main.js
 create mode 100644 node_modules/@11ty/dependency-tree-esm/package.json
 create mode 100644 node_modules/@11ty/dependency-tree/LICENSE
 create mode 100644 node_modules/@11ty/dependency-tree/README.md
 create mode 100644 node_modules/@11ty/dependency-tree/main.js
 create mode 100644 node_modules/@11ty/dependency-tree/package.json
 create mode 100644 node_modules/@11ty/eleventy-dev-server/README.md
 create mode 100644 node_modules/@11ty/eleventy-dev-server/cli.js
 create mode 100644 node_modules/@11ty/eleventy-dev-server/client/reload-client.js
 create mode 100755 node_modules/@11ty/eleventy-dev-server/cmd.js
 create mode 100644 node_modules/@11ty/eleventy-dev-server/package.json
 create mode 100644 node_modules/@11ty/eleventy-dev-server/server.js
 create mode 100644 node_modules/@11ty/eleventy-dev-server/server/ipAddress.js
 create mode 100644 node_modules/@11ty/eleventy-dev-server/server/wrapResponse.js
 create mode 100644 node_modules/@11ty/eleventy-plugin-bundle/README.md
 create mode 100644 node_modules/@11ty/eleventy-plugin-bundle/eleventy.bundle.js
 create mode 100644 node_modules/@11ty/eleventy-plugin-bundle/package.json
 create mode 100644 node_modules/@11ty/eleventy-plugin-bundle/src/BundleFileOutput.js
 create mode 100644 node_modules/@11ty/eleventy-plugin-bundle/src/CodeManager.js
 create mode 100644 node_modules/@11ty/eleventy-plugin-bundle/src/OutOfOrderRender.js
 create mode 100644 node_modules/@11ty/eleventy-plugin-bundle/src/bundlePlucker.js
 create mode 100644 node_modules/@11ty/eleventy-plugin-bundle/src/eleventy.bundleManagers.js
 create mode 100644 node_modules/@11ty/eleventy-plugin-bundle/src/eleventy.pruneEmptyBundles.js
 create mode 100644 node_modules/@11ty/eleventy-plugin-bundle/src/eleventy.shortcodes.js
 create mode 100644 node_modules/@11ty/eleventy-utils/LICENSE
 create mode 100644 node_modules/@11ty/eleventy-utils/README.md
 create mode 100644 node_modules/@11ty/eleventy-utils/index.js
 create mode 100644 node_modules/@11ty/eleventy-utils/package.json
 create mode 100644 node_modules/@11ty/eleventy-utils/src/Buffer.js
 create mode 100644 node_modules/@11ty/eleventy-utils/src/CreateHash.js
 create mode 100644 node_modules/@11ty/eleventy-utils/src/DateCompare.js
 create mode 100644 node_modules/@11ty/eleventy-utils/src/HashTypes.js
 create mode 100644 node_modules/@11ty/eleventy-utils/src/IsPlainObject.js
 create mode 100644 node_modules/@11ty/eleventy-utils/src/Merge.js
 create mode 100644 node_modules/@11ty/eleventy-utils/src/TemplatePath.js
 create mode 100644 node_modules/@11ty/eleventy-utils/src/Url.js
 create mode 100644 node_modules/@11ty/eleventy-utils/src/lib-sha256.js
 create mode 100644 node_modules/@11ty/eleventy/CODE_OF_CONDUCT.md
 create mode 100644 node_modules/@11ty/eleventy/LICENSE
 create mode 100644 node_modules/@11ty/eleventy/README.md
 create mode 100644 node_modules/@11ty/eleventy/SECURITY.md
 create mode 100755 node_modules/@11ty/eleventy/cmd.cjs
 create mode 100644 node_modules/@11ty/eleventy/package.json
 create mode 100644 node_modules/@11ty/eleventy/src/Benchmark/Benchmark.js
 create mode 100644 node_modules/@11ty/eleventy/src/Benchmark/BenchmarkGroup.js
 create mode 100644 node_modules/@11ty/eleventy/src/Benchmark/BenchmarkManager.js
 create mode 100644 node_modules/@11ty/eleventy/src/Data/ComputedData.js
 create mode 100644 node_modules/@11ty/eleventy/src/Data/ComputedDataProxy.js
 create mode 100644 node_modules/@11ty/eleventy/src/Data/ComputedDataQueue.js
 create mode 100644 node_modules/@11ty/eleventy/src/Data/ComputedDataTemplateString.js
 create mode 100644 node_modules/@11ty/eleventy/src/Data/TemplateData.js
 create mode 100644 node_modules/@11ty/eleventy/src/Data/TemplateDataInitialGlobalData.js
 create mode 100644 node_modules/@11ty/eleventy/src/Eleventy.js
 create mode 100644 node_modules/@11ty/eleventy/src/EleventyCommonJs.cjs
 create mode 100644 node_modules/@11ty/eleventy/src/EleventyExtensionMap.js
 create mode 100644 node_modules/@11ty/eleventy/src/EleventyFiles.js
 create mode 100644 node_modules/@11ty/eleventy/src/EleventyServe.js
 create mode 100755 node_modules/@11ty/eleventy/src/EleventyWatch.js
 create mode 100644 node_modules/@11ty/eleventy/src/EleventyWatchTargets.js
 create mode 100644 node_modules/@11ty/eleventy/src/Engines/Custom.js
 create mode 100644 node_modules/@11ty/eleventy/src/Engines/FrontMatter/JavaScript.js
 create mode 100644 node_modules/@11ty/eleventy/src/Engines/Html.js
 create mode 100644 node_modules/@11ty/eleventy/src/Engines/JavaScript.js
 create mode 100644 node_modules/@11ty/eleventy/src/Engines/Liquid.js
 create mode 100644 node_modules/@11ty/eleventy/src/Engines/Markdown.js
 create mode 100755 node_modules/@11ty/eleventy/src/Engines/Nunjucks.js
 create mode 100644 node_modules/@11ty/eleventy/src/Engines/TemplateEngine.js
 create mode 100644 node_modules/@11ty/eleventy/src/Engines/TemplateEngineManager.js
 create mode 100644 node_modules/@11ty/eleventy/src/Engines/Util/ContextAugmenter.js
 create mode 100644 node_modules/@11ty/eleventy/src/Errors/DuplicatePermalinkOutputError.js
 create mode 100644 node_modules/@11ty/eleventy/src/Errors/EleventyBaseError.js
 create mode 100644 node_modules/@11ty/eleventy/src/Errors/EleventyErrorHandler.js
 create mode 100644 node_modules/@11ty/eleventy/src/Errors/EleventyErrorUtil.js
 create mode 100644 node_modules/@11ty/eleventy/src/Errors/TemplateContentPrematureUseError.js
 create mode 100644 node_modules/@11ty/eleventy/src/Errors/TemplateContentUnrenderedTemplateError.js
 create mode 100644 node_modules/@11ty/eleventy/src/Errors/UsingCircularTemplateContentReferenceError.js
 create mode 100644 node_modules/@11ty/eleventy/src/EventBus.js
 create mode 100644 node_modules/@11ty/eleventy/src/FileSystemSearch.js
 create mode 100644 node_modules/@11ty/eleventy/src/Filters/GetCollectionItem.js
 create mode 100644 node_modules/@11ty/eleventy/src/Filters/GetCollectionItemIndex.js
 create mode 100644 node_modules/@11ty/eleventy/src/Filters/GetLocaleCollectionItem.js
 create mode 100644 node_modules/@11ty/eleventy/src/Filters/Slug.js
 create mode 100644 node_modules/@11ty/eleventy/src/Filters/Slugify.js
 create mode 100644 node_modules/@11ty/eleventy/src/Filters/Url.js
 create mode 100644 node_modules/@11ty/eleventy/src/GlobalDependencyMap.js
 create mode 100644 node_modules/@11ty/eleventy/src/LayoutCache.js
 create mode 100644 node_modules/@11ty/eleventy/src/Plugins/HtmlBasePlugin.js
 create mode 100644 node_modules/@11ty/eleventy/src/Plugins/HtmlRelativeCopyPlugin.js
 create mode 100644 node_modules/@11ty/eleventy/src/Plugins/I18nPlugin.js
 create mode 100644 node_modules/@11ty/eleventy/src/Plugins/IdAttributePlugin.js
 create mode 100644 node_modules/@11ty/eleventy/src/Plugins/InputPathToUrl.js
 create mode 100755 node_modules/@11ty/eleventy/src/Plugins/Pagination.js
 create mode 100644 node_modules/@11ty/eleventy/src/Plugins/RenderPlugin.js
 create mode 100755 node_modules/@11ty/eleventy/src/Template.js
 create mode 100644 node_modules/@11ty/eleventy/src/TemplateBehavior.js
 create mode 100755 node_modules/@11ty/eleventy/src/TemplateCollection.js
 create mode 100644 node_modules/@11ty/eleventy/src/TemplateConfig.js
 create mode 100644 node_modules/@11ty/eleventy/src/TemplateContent.js
 create mode 100644 node_modules/@11ty/eleventy/src/TemplateFileSlug.js
 create mode 100644 node_modules/@11ty/eleventy/src/TemplateGlob.js
 create mode 100644 node_modules/@11ty/eleventy/src/TemplateLayout.js
 create mode 100644 node_modules/@11ty/eleventy/src/TemplateLayoutPathResolver.js
 create mode 100644 node_modules/@11ty/eleventy/src/TemplateMap.js
 create mode 100644 node_modules/@11ty/eleventy/src/TemplatePassthrough.js
 create mode 100644 node_modules/@11ty/eleventy/src/TemplatePassthroughManager.js
 create mode 100644 node_modules/@11ty/eleventy/src/TemplatePermalink.js
 create mode 100644 node_modules/@11ty/eleventy/src/TemplateRender.js
 create mode 100755 node_modules/@11ty/eleventy/src/TemplateWriter.js
 create mode 100644 node_modules/@11ty/eleventy/src/UserConfig.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/ArrayUtil.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/AsyncEventEmitter.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/Compatibility.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/ConsoleLogger.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/DateGitFirstAdded.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/DateGitLastUpdated.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/DirContains.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/EsmResolver.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/EsmResolverPortAdapter.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/EventBusUtil.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/ExistsCache.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/FilePathUtil.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/FileSystemManager.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/GetJavaScriptData.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/GlobMatcher.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/GlobRemap.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/HtmlRelativeCopy.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/HtmlTransformer.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/ImportJsonSync.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/IsAsyncFunction.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/JavaScriptDependencies.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/MemoizeFunction.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/Objects/DeepFreeze.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/Objects/ObjectFilter.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/Objects/ProxyWrap.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/Objects/SampleModule.mjs
 create mode 100644 node_modules/@11ty/eleventy/src/Util/Objects/Sortable.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/Objects/Unique.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/PassthroughCopyBehaviorCheck.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/PathNormalizer.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/PathPrefixer.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/Pluralize.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/ProjectDirectories.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/ProjectTemplateFormats.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/PromiseUtil.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/Require.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/ReservedData.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/SetUnion.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/SpawnAsync.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/TemplateDepGraph.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/TransformsUtil.js
 create mode 100644 node_modules/@11ty/eleventy/src/Util/ValidUrl.js
 create mode 100644 node_modules/@11ty/eleventy/src/defaultConfig.js
 create mode 100644 node_modules/@11ty/eleventy/tsconfig.json
 create mode 100644 node_modules/@11ty/lodash-custom/README.md
 create mode 100644 node_modules/@11ty/lodash-custom/lodash.custom.js
 create mode 100644 node_modules/@11ty/lodash-custom/package.json
 create mode 100644 node_modules/@11ty/posthtml-urls/LICENSE
 create mode 100644 node_modules/@11ty/posthtml-urls/README.md
 create mode 100644 node_modules/@11ty/posthtml-urls/lib/defaultOptions.js
 create mode 100644 node_modules/@11ty/posthtml-urls/lib/index.js
 create mode 100644 node_modules/@11ty/posthtml-urls/package.json
 create mode 100644 node_modules/@11ty/recursive-copy/README.md
 create mode 100644 node_modules/@11ty/recursive-copy/index.d.ts
 create mode 100644 node_modules/@11ty/recursive-copy/index.js
 create mode 100644 node_modules/@11ty/recursive-copy/lib/copy.js
 create mode 100644 node_modules/@11ty/recursive-copy/lib/maximatch.js
 create mode 100644 node_modules/@11ty/recursive-copy/package.json
 create mode 100644 node_modules/@sindresorhus/slugify/index.d.ts
 create mode 100644 node_modules/@sindresorhus/slugify/index.js
 create mode 100644 node_modules/@sindresorhus/slugify/license
 create mode 100644 node_modules/@sindresorhus/slugify/overridable-replacements.js
 create mode 100644 node_modules/@sindresorhus/slugify/package.json
 create mode 100644 node_modules/@sindresorhus/slugify/readme.md
 create mode 100644 node_modules/@sindresorhus/transliterate/index.d.ts
 create mode 100644 node_modules/@sindresorhus/transliterate/index.js
 create mode 100644 node_modules/@sindresorhus/transliterate/license
 create mode 100644 node_modules/@sindresorhus/transliterate/package.json
 create mode 100644 node_modules/@sindresorhus/transliterate/readme.md
 create mode 100644 node_modules/@sindresorhus/transliterate/replacements.js
 create mode 100644 node_modules/a-sync-waterfall/LICENSE
 create mode 100644 node_modules/a-sync-waterfall/README.md
 create mode 100644 node_modules/a-sync-waterfall/index.js
 create mode 100644 node_modules/a-sync-waterfall/package.json
 create mode 100644 node_modules/a-sync-waterfall/test.js
 create mode 100644 node_modules/acorn-walk/CHANGELOG.md
 create mode 100644 node_modules/acorn-walk/LICENSE
 create mode 100644 node_modules/acorn-walk/README.md
 create mode 100644 node_modules/acorn-walk/dist/walk.d.mts
 create mode 100644 node_modules/acorn-walk/dist/walk.d.ts
 create mode 100644 node_modules/acorn-walk/dist/walk.js
 create mode 100644 node_modules/acorn-walk/dist/walk.mjs
 create mode 100644 node_modules/acorn-walk/package.json
 create mode 100644 node_modules/acorn/CHANGELOG.md
 create mode 100644 node_modules/acorn/LICENSE
 create mode 100644 node_modules/acorn/README.md
 create mode 100755 node_modules/acorn/bin/acorn
 create mode 100644 node_modules/acorn/dist/acorn.d.mts
 create mode 100644 node_modules/acorn/dist/acorn.d.ts
 create mode 100644 node_modules/acorn/dist/acorn.js
 create mode 100644 node_modules/acorn/dist/acorn.mjs
 create mode 100644 node_modules/acorn/dist/bin.js
 create mode 100644 node_modules/acorn/package.json
 create mode 100644 node_modules/anymatch/LICENSE
 create mode 100644 node_modules/anymatch/README.md
 create mode 100644 node_modules/anymatch/index.d.ts
 create mode 100644 node_modules/anymatch/index.js
 create mode 100644 node_modules/anymatch/node_modules/picomatch/LICENSE
 create mode 100644 node_modules/anymatch/node_modules/picomatch/README.md
 create mode 100644 node_modules/anymatch/node_modules/picomatch/index.js
 create mode 100644 node_modules/anymatch/node_modules/picomatch/lib/constants.js
 create mode 100644 node_modules/anymatch/node_modules/picomatch/lib/parse.js
 create mode 100644 node_modules/anymatch/node_modules/picomatch/lib/picomatch.js
 create mode 100644 node_modules/anymatch/node_modules/picomatch/lib/scan.js
 create mode 100644 node_modules/anymatch/node_modules/picomatch/lib/utils.js
 create mode 100644 node_modules/anymatch/node_modules/picomatch/package.json
 create mode 100644 node_modules/anymatch/package.json
 create mode 100644 node_modules/argparse/CHANGELOG.md
 create mode 100644 node_modules/argparse/LICENSE
 create mode 100644 node_modules/argparse/README.md
 create mode 100644 node_modules/argparse/argparse.js
 create mode 100644 node_modules/argparse/lib/sub.js
 create mode 100644 node_modules/argparse/lib/textwrap.js
 create mode 100644 node_modules/argparse/package.json
 create mode 100644 node_modules/asap/CHANGES.md
 create mode 100644 node_modules/asap/LICENSE.md
 create mode 100644 node_modules/asap/README.md
 create mode 100644 node_modules/asap/asap.js
 create mode 100644 node_modules/asap/browser-asap.js
 create mode 100644 node_modules/asap/browser-raw.js
 create mode 100644 node_modules/asap/package.json
 create mode 100644 node_modules/asap/raw.js
 create mode 100644 node_modules/balanced-match/.github/FUNDING.yml
 create mode 100644 node_modules/balanced-match/LICENSE.md
 create mode 100644 node_modules/balanced-match/README.md
 create mode 100644 node_modules/balanced-match/index.js
 create mode 100644 node_modules/balanced-match/package.json
 create mode 100644 node_modules/bcp-47-match/index.d.ts
 create mode 100644 node_modules/bcp-47-match/index.js
 create mode 100644 node_modules/bcp-47-match/license
 create mode 100644 node_modules/bcp-47-match/package.json
 create mode 100644 node_modules/bcp-47-match/readme.md
 create mode 100644 node_modules/bcp-47-normalize/index.d.ts
 create mode 100644 node_modules/bcp-47-normalize/index.js
 create mode 100644 node_modules/bcp-47-normalize/lib/fields.d.ts
 create mode 100644 node_modules/bcp-47-normalize/lib/fields.js
 create mode 100644 node_modules/bcp-47-normalize/lib/index.d.ts
 create mode 100644 node_modules/bcp-47-normalize/lib/index.js
 create mode 100644 node_modules/bcp-47-normalize/lib/likely.d.ts
 create mode 100644 node_modules/bcp-47-normalize/lib/likely.js
 create mode 100644 node_modules/bcp-47-normalize/lib/many.d.ts
 create mode 100644 node_modules/bcp-47-normalize/lib/many.js
 create mode 100644 node_modules/bcp-47-normalize/lib/matches.d.ts
 create mode 100644 node_modules/bcp-47-normalize/lib/matches.js
 create mode 100644 node_modules/bcp-47-normalize/license
 create mode 100644 node_modules/bcp-47-normalize/package.json
 create mode 100644 node_modules/bcp-47-normalize/readme.md
 create mode 100644 node_modules/bcp-47/index.d.ts
 create mode 100644 node_modules/bcp-47/index.d.ts.map
 create mode 100644 node_modules/bcp-47/index.js
 create mode 100644 node_modules/bcp-47/lib/normal.d.ts
 create mode 100644 node_modules/bcp-47/lib/normal.d.ts.map
 create mode 100644 node_modules/bcp-47/lib/normal.js
 create mode 100644 node_modules/bcp-47/lib/parse.d.ts
 create mode 100644 node_modules/bcp-47/lib/parse.d.ts.map
 create mode 100644 node_modules/bcp-47/lib/parse.js
 create mode 100644 node_modules/bcp-47/lib/regular.d.ts
 create mode 100644 node_modules/bcp-47/lib/regular.d.ts.map
 create mode 100644 node_modules/bcp-47/lib/regular.js
 create mode 100644 node_modules/bcp-47/lib/stringify.d.ts
 create mode 100644 node_modules/bcp-47/lib/stringify.d.ts.map
 create mode 100644 node_modules/bcp-47/lib/stringify.js
 create mode 100644 node_modules/bcp-47/license
 create mode 100644 node_modules/bcp-47/package.json
 create mode 100644 node_modules/bcp-47/readme.md
 create mode 100644 node_modules/binary-extensions/binary-extensions.json
 create mode 100644 node_modules/binary-extensions/binary-extensions.json.d.ts
 create mode 100644 node_modules/binary-extensions/index.d.ts
 create mode 100644 node_modules/binary-extensions/index.js
 create mode 100644 node_modules/binary-extensions/license
 create mode 100644 node_modules/binary-extensions/package.json
 create mode 100644 node_modules/binary-extensions/readme.md
 create mode 100644 node_modules/brace-expansion/LICENSE
 create mode 100644 node_modules/brace-expansion/README.md
 create mode 100644 node_modules/brace-expansion/index.js
 create mode 100644 node_modules/brace-expansion/package.json
 create mode 100644 node_modules/braces/LICENSE
 create mode 100644 node_modules/braces/README.md
 create mode 100644 node_modules/braces/index.js
 create mode 100644 node_modules/braces/lib/compile.js
 create mode 100644 node_modules/braces/lib/constants.js
 create mode 100644 node_modules/braces/lib/expand.js
 create mode 100644 node_modules/braces/lib/parse.js
 create mode 100644 node_modules/braces/lib/stringify.js
 create mode 100644 node_modules/braces/lib/utils.js
 create mode 100644 node_modules/braces/package.json
 create mode 100644 node_modules/chokidar/LICENSE
 create mode 100644 node_modules/chokidar/README.md
 create mode 100644 node_modules/chokidar/index.js
 create mode 100644 node_modules/chokidar/lib/constants.js
 create mode 100644 node_modules/chokidar/lib/fsevents-handler.js
 create mode 100644 node_modules/chokidar/lib/nodefs-handler.js
 create mode 100644 node_modules/chokidar/package.json
 create mode 100644 node_modules/chokidar/types/index.d.ts
 create mode 100644 node_modules/commander/LICENSE
 create mode 100644 node_modules/commander/Readme.md
 create mode 100644 node_modules/commander/esm.mjs
 create mode 100644 node_modules/commander/index.js
 create mode 100644 node_modules/commander/lib/argument.js
 create mode 100644 node_modules/commander/lib/command.js
 create mode 100644 node_modules/commander/lib/error.js
 create mode 100644 node_modules/commander/lib/help.js
 create mode 100644 node_modules/commander/lib/option.js
 create mode 100644 node_modules/commander/lib/suggestSimilar.js
 create mode 100644 node_modules/commander/package-support.json
 create mode 100644 node_modules/commander/package.json
 create mode 100644 node_modules/commander/typings/index.d.ts
 create mode 100644 node_modules/concat-map/.travis.yml
 create mode 100644 node_modules/concat-map/LICENSE
 create mode 100644 node_modules/concat-map/README.markdown
 create mode 100644 node_modules/concat-map/example/map.js
 create mode 100644 node_modules/concat-map/index.js
 create mode 100644 node_modules/concat-map/package.json
 create mode 100644 node_modules/concat-map/test/map.js
 create mode 100644 node_modules/debug/LICENSE
 create mode 100644 node_modules/debug/README.md
 create mode 100644 node_modules/debug/package.json
 create mode 100644 node_modules/debug/src/browser.js
 create mode 100644 node_modules/debug/src/common.js
 create mode 100644 node_modules/debug/src/index.js
 create mode 100644 node_modules/debug/src/node.js
 create mode 100644 node_modules/depd/History.md
 create mode 100644 node_modules/depd/LICENSE
 create mode 100644 node_modules/depd/Readme.md
 create mode 100644 node_modules/depd/index.js
 create mode 100644 node_modules/depd/lib/browser/index.js
 create mode 100644 node_modules/depd/package.json
 create mode 100755 node_modules/dependency-graph/.github/workflows/node.js.yml
 create mode 100755 node_modules/dependency-graph/CHANGELOG.md
 create mode 100755 node_modules/dependency-graph/LICENSE
 create mode 100755 node_modules/dependency-graph/README.md
 create mode 100755 node_modules/dependency-graph/lib/dep_graph.js
 create mode 100755 node_modules/dependency-graph/lib/index.d.ts
 create mode 100755 node_modules/dependency-graph/package.json
 create mode 100755 node_modules/dependency-graph/specs/dep_graph_spec.js
 create mode 100644 node_modules/dom-serializer/LICENSE
 create mode 100644 node_modules/dom-serializer/README.md
 create mode 100644 node_modules/dom-serializer/lib/esm/foreignNames.d.ts
 create mode 100644 node_modules/dom-serializer/lib/esm/foreignNames.d.ts.map
 create mode 100644 node_modules/dom-serializer/lib/esm/foreignNames.js
 create mode 100644 node_modules/dom-serializer/lib/esm/index.d.ts
 create mode 100644 node_modules/dom-serializer/lib/esm/index.d.ts.map
 create mode 100644 node_modules/dom-serializer/lib/esm/index.js
 create mode 100644 node_modules/dom-serializer/lib/esm/package.json
 create mode 100644 node_modules/dom-serializer/lib/foreignNames.d.ts
 create mode 100644 node_modules/dom-serializer/lib/foreignNames.d.ts.map
 create mode 100644 node_modules/dom-serializer/lib/foreignNames.js
 create mode 100644 node_modules/dom-serializer/lib/index.d.ts
 create mode 100644 node_modules/dom-serializer/lib/index.d.ts.map
 create mode 100644 node_modules/dom-serializer/lib/index.js
 create mode 100644 node_modules/dom-serializer/node_modules/entities/LICENSE
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/decode.d.ts
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/decode.d.ts.map
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/decode.js
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/decode_codepoint.d.ts
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/decode_codepoint.d.ts.map
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/decode_codepoint.js
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/encode.d.ts
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/encode.d.ts.map
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/encode.js
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/index.d.ts
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/index.d.ts.map
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/index.js
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/maps/decode.json
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/maps/entities.json
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/maps/legacy.json
 create mode 100644 node_modules/dom-serializer/node_modules/entities/lib/maps/xml.json
 create mode 100644 node_modules/dom-serializer/node_modules/entities/package.json
 create mode 100644 node_modules/dom-serializer/node_modules/entities/readme.md
 create mode 100644 node_modules/dom-serializer/package.json
 create mode 100644 node_modules/domelementtype/LICENSE
 create mode 100644 node_modules/domelementtype/lib/esm/index.d.ts
 create mode 100644 node_modules/domelementtype/lib/esm/index.d.ts.map
 create mode 100644 node_modules/domelementtype/lib/esm/index.js
 create mode 100644 node_modules/domelementtype/lib/esm/package.json
 create mode 100644 node_modules/domelementtype/lib/index.d.ts
 create mode 100644 node_modules/domelementtype/lib/index.d.ts.map
 create mode 100644 node_modules/domelementtype/lib/index.js
 create mode 100644 node_modules/domelementtype/package.json
 create mode 100644 node_modules/domelementtype/readme.md
 create mode 100644 node_modules/domhandler/LICENSE
 create mode 100644 node_modules/domhandler/lib/index.d.ts
 create mode 100644 node_modules/domhandler/lib/index.d.ts.map
 create mode 100644 node_modules/domhandler/lib/index.js
 create mode 100644 node_modules/domhandler/lib/node.d.ts
 create mode 100644 node_modules/domhandler/lib/node.d.ts.map
 create mode 100644 node_modules/domhandler/lib/node.js
 create mode 100644 node_modules/domhandler/package.json
 create mode 100644 node_modules/domhandler/readme.md
 create mode 100644 node_modules/domutils/LICENSE
 create mode 100644 node_modules/domutils/lib/feeds.d.ts
 create mode 100644 node_modules/domutils/lib/feeds.d.ts.map
 create mode 100644 node_modules/domutils/lib/feeds.js
 create mode 100644 node_modules/domutils/lib/helpers.d.ts
 create mode 100644 node_modules/domutils/lib/helpers.d.ts.map
 create mode 100644 node_modules/domutils/lib/helpers.js
 create mode 100644 node_modules/domutils/lib/index.d.ts
 create mode 100644 node_modules/domutils/lib/index.d.ts.map
 create mode 100644 node_modules/domutils/lib/index.js
 create mode 100644 node_modules/domutils/lib/legacy.d.ts
 create mode 100644 node_modules/domutils/lib/legacy.d.ts.map
 create mode 100644 node_modules/domutils/lib/legacy.js
 create mode 100644 node_modules/domutils/lib/manipulation.d.ts
 create mode 100644 node_modules/domutils/lib/manipulation.d.ts.map
 create mode 100644 node_modules/domutils/lib/manipulation.js
 create mode 100644 node_modules/domutils/lib/querying.d.ts
 create mode 100644 node_modules/domutils/lib/querying.d.ts.map
 create mode 100644 node_modules/domutils/lib/querying.js
 create mode 100644 node_modules/domutils/lib/stringify.d.ts
 create mode 100644 node_modules/domutils/lib/stringify.d.ts.map
 create mode 100644 node_modules/domutils/lib/stringify.js
 create mode 100644 node_modules/domutils/lib/traversal.d.ts
 create mode 100644 node_modules/domutils/lib/traversal.d.ts.map
 create mode 100644 node_modules/domutils/lib/traversal.js
 create mode 100644 node_modules/domutils/package.json
 create mode 100644 node_modules/domutils/readme.md
 create mode 100644 node_modules/ee-first/LICENSE
 create mode 100644 node_modules/ee-first/README.md
 create mode 100644 node_modules/ee-first/index.js
 create mode 100644 node_modules/ee-first/package.json
 create mode 100644 node_modules/encodeurl/LICENSE
 create mode 100644 node_modules/encodeurl/README.md
 create mode 100644 node_modules/encodeurl/index.js
 create mode 100644 node_modules/encodeurl/package.json
 create mode 100644 node_modules/entities/LICENSE
 create mode 100644 node_modules/entities/decode.d.ts
 create mode 100644 node_modules/entities/decode.js
 create mode 100644 node_modules/entities/dist/commonjs/decode-codepoint.d.ts
 create mode 100644 node_modules/entities/dist/commonjs/decode-codepoint.d.ts.map
 create mode 100644 node_modules/entities/dist/commonjs/decode-codepoint.js
 create mode 100644 node_modules/entities/dist/commonjs/decode-codepoint.js.map
 create mode 100644 node_modules/entities/dist/commonjs/decode.d.ts
 create mode 100644 node_modules/entities/dist/commonjs/decode.d.ts.map
 create mode 100644 node_modules/entities/dist/commonjs/decode.js
 create mode 100644 node_modules/entities/dist/commonjs/decode.js.map
 create mode 100644 node_modules/entities/dist/commonjs/encode.d.ts
 create mode 100644 node_modules/entities/dist/commonjs/encode.d.ts.map
 create mode 100644 node_modules/entities/dist/commonjs/encode.js
 create mode 100644 node_modules/entities/dist/commonjs/encode.js.map
 create mode 100644 node_modules/entities/dist/commonjs/escape.d.ts
 create mode 100644 node_modules/entities/dist/commonjs/escape.d.ts.map
 create mode 100644 node_modules/entities/dist/commonjs/escape.js
 create mode 100644 node_modules/entities/dist/commonjs/escape.js.map
 create mode 100644 node_modules/entities/dist/commonjs/generated/decode-data-html.d.ts
 create mode 100644 node_modules/entities/dist/commonjs/generated/decode-data-html.d.ts.map
 create mode 100644 node_modules/entities/dist/commonjs/generated/decode-data-html.js
 create mode 100644 node_modules/entities/dist/commonjs/generated/decode-data-html.js.map
 create mode 100644 node_modules/entities/dist/commonjs/generated/decode-data-xml.d.ts
 create mode 100644 node_modules/entities/dist/commonjs/generated/decode-data-xml.d.ts.map
 create mode 100644 node_modules/entities/dist/commonjs/generated/decode-data-xml.js
 create mode 100644 node_modules/entities/dist/commonjs/generated/decode-data-xml.js.map
 create mode 100644 node_modules/entities/dist/commonjs/generated/encode-html.d.ts
 create mode 100644 node_modules/entities/dist/commonjs/generated/encode-html.d.ts.map
 create mode 100644 node_modules/entities/dist/commonjs/generated/encode-html.js
 create mode 100644 node_modules/entities/dist/commonjs/generated/encode-html.js.map
 create mode 100644 node_modules/entities/dist/commonjs/index.d.ts
 create mode 100644 node_modules/entities/dist/commonjs/index.d.ts.map
 create mode 100644 node_modules/entities/dist/commonjs/index.js
 create mode 100644 node_modules/entities/dist/commonjs/index.js.map
 create mode 100644 node_modules/entities/dist/commonjs/package.json
 create mode 100644 node_modules/entities/dist/esm/decode-codepoint.d.ts
 create mode 100644 node_modules/entities/dist/esm/decode-codepoint.d.ts.map
 create mode 100644 node_modules/entities/dist/esm/decode-codepoint.js
 create mode 100644 node_modules/entities/dist/esm/decode-codepoint.js.map
 create mode 100644 node_modules/entities/dist/esm/decode.d.ts
 create mode 100644 node_modules/entities/dist/esm/decode.d.ts.map
 create mode 100644 node_modules/entities/dist/esm/decode.js
 create mode 100644 node_modules/entities/dist/esm/decode.js.map
 create mode 100644 node_modules/entities/dist/esm/encode.d.ts
 create mode 100644 node_modules/entities/dist/esm/encode.d.ts.map
 create mode 100644 node_modules/entities/dist/esm/encode.js
 create mode 100644 node_modules/entities/dist/esm/encode.js.map
 create mode 100644 node_modules/entities/dist/esm/escape.d.ts
 create mode 100644 node_modules/entities/dist/esm/escape.d.ts.map
 create mode 100644 node_modules/entities/dist/esm/escape.js
 create mode 100644 node_modules/entities/dist/esm/escape.js.map
 create mode 100644 node_modules/entities/dist/esm/generated/decode-data-html.d.ts
 create mode 100644 node_modules/entities/dist/esm/generated/decode-data-html.d.ts.map
 create mode 100644 node_modules/entities/dist/esm/generated/decode-data-html.js
 create mode 100644 node_modules/entities/dist/esm/generated/decode-data-html.js.map
 create mode 100644 node_modules/entities/dist/esm/generated/decode-data-xml.d.ts
 create mode 100644 node_modules/entities/dist/esm/generated/decode-data-xml.d.ts.map
 create mode 100644 node_modules/entities/dist/esm/generated/decode-data-xml.js
 create mode 100644 node_modules/entities/dist/esm/generated/decode-data-xml.js.map
 create mode 100644 node_modules/entities/dist/esm/generated/encode-html.d.ts
 create mode 100644 node_modules/entities/dist/esm/generated/encode-html.d.ts.map
 create mode 100644 node_modules/entities/dist/esm/generated/encode-html.js
 create mode 100644 node_modules/entities/dist/esm/generated/encode-html.js.map
 create mode 100644 node_modules/entities/dist/esm/index.d.ts
 create mode 100644 node_modules/entities/dist/esm/index.d.ts.map
 create mode 100644 node_modules/entities/dist/esm/index.js
 create mode 100644 node_modules/entities/dist/esm/index.js.map
 create mode 100644 node_modules/entities/dist/esm/package.json
 create mode 100644 node_modules/entities/escape.d.ts
 create mode 100644 node_modules/entities/escape.js
 create mode 100644 node_modules/entities/package.json
 create mode 100644 node_modules/entities/readme.md
 create mode 100644 node_modules/entities/src/decode-codepoint.ts
 create mode 100644 node_modules/entities/src/decode.spec.ts
 create mode 100644 node_modules/entities/src/decode.ts
 create mode 100644 node_modules/entities/src/encode.spec.ts
 create mode 100644 node_modules/entities/src/encode.ts
 create mode 100644 node_modules/entities/src/escape.spec.ts
 create mode 100644 node_modules/entities/src/escape.ts
 create mode 100644 node_modules/entities/src/generated/.eslintrc.json
 create mode 100644 node_modules/entities/src/generated/decode-data-html.ts
 create mode 100644 node_modules/entities/src/generated/decode-data-xml.ts
 create mode 100644 node_modules/entities/src/generated/encode-html.ts
 create mode 100644 node_modules/entities/src/index.spec.ts
 create mode 100644 node_modules/entities/src/index.ts
 create mode 100644 node_modules/errno/.jshintrc
 create mode 100644 node_modules/errno/.travis.yml
 create mode 100644 node_modules/errno/README.md
 create mode 100755 node_modules/errno/build.js
 create mode 100755 node_modules/errno/cli.js
 create mode 100644 node_modules/errno/custom.js
 create mode 100644 node_modules/errno/errno.js
 create mode 100644 node_modules/errno/package.json
 create mode 100644 node_modules/errno/test.js
 create mode 100644 node_modules/escape-html/LICENSE
 create mode 100644 node_modules/escape-html/Readme.md
 create mode 100644 node_modules/escape-html/index.js
 create mode 100644 node_modules/escape-html/package.json
 create mode 100644 node_modules/escape-string-regexp/index.d.ts
 create mode 100644 node_modules/escape-string-regexp/index.js
 create mode 100644 node_modules/escape-string-regexp/license
 create mode 100644 node_modules/escape-string-regexp/package.json
 create mode 100644 node_modules/escape-string-regexp/readme.md
 create mode 100644 node_modules/esm-import-transformer/README.md
 create mode 100644 node_modules/esm-import-transformer/import-transformer.js
 create mode 100644 node_modules/esm-import-transformer/package.json
 create mode 100644 node_modules/esprima/ChangeLog
 create mode 100644 node_modules/esprima/LICENSE.BSD
 create mode 100644 node_modules/esprima/README.md
 create mode 100755 node_modules/esprima/bin/esparse.js
 create mode 100755 node_modules/esprima/bin/esvalidate.js
 create mode 100644 node_modules/esprima/dist/esprima.js
 create mode 100644 node_modules/esprima/package.json
 create mode 100644 node_modules/etag/HISTORY.md
 create mode 100644 node_modules/etag/LICENSE
 create mode 100644 node_modules/etag/README.md
 create mode 100644 node_modules/etag/index.js
 create mode 100644 node_modules/etag/package.json
 create mode 100644 node_modules/evaluate-value/LICENSE
 create mode 100644 node_modules/evaluate-value/README.md
 create mode 100644 node_modules/evaluate-value/index-es5.js
 create mode 100644 node_modules/evaluate-value/index-es5.js.map
 create mode 100644 node_modules/evaluate-value/index.js
 create mode 100644 node_modules/evaluate-value/package.json
 create mode 100644 node_modules/extend-shallow/LICENSE
 create mode 100644 node_modules/extend-shallow/README.md
 create mode 100644 node_modules/extend-shallow/index.js
 create mode 100644 node_modules/extend-shallow/package.json
 create mode 100644 node_modules/fdir/LICENSE
 create mode 100644 node_modules/fdir/README.md
 create mode 100644 node_modules/fdir/dist/index.cjs
 create mode 100644 node_modules/fdir/dist/index.d.cts
 create mode 100644 node_modules/fdir/dist/index.d.mts
 create mode 100644 node_modules/fdir/dist/index.mjs
 create mode 100644 node_modules/fdir/package.json
 create mode 100644 node_modules/filesize/LICENSE
 create mode 100644 node_modules/filesize/README.md
 create mode 100644 node_modules/filesize/dist/filesize.cjs
 create mode 100644 node_modules/filesize/dist/filesize.esm.js
 create mode 100644 node_modules/filesize/package.json
 create mode 100644 node_modules/filesize/types/filesize.d.ts
 create mode 100644 node_modules/fill-range/LICENSE
 create mode 100644 node_modules/fill-range/README.md
 create mode 100644 node_modules/fill-range/index.js
 create mode 100644 node_modules/fill-range/package.json
 create mode 100644 node_modules/finalhandler/HISTORY.md
 create mode 100644 node_modules/finalhandler/LICENSE
 create mode 100644 node_modules/finalhandler/README.md
 create mode 100644 node_modules/finalhandler/SECURITY.md
 create mode 100644 node_modules/finalhandler/index.js
 create mode 100644 node_modules/finalhandler/node_modules/debug/.coveralls.yml
 create mode 100644 node_modules/finalhandler/node_modules/debug/.eslintrc
 create mode 100644 node_modules/finalhandler/node_modules/debug/.npmignore
 create mode 100644 node_modules/finalhandler/node_modules/debug/.travis.yml
 create mode 100644 node_modules/finalhandler/node_modules/debug/CHANGELOG.md
 create mode 100644 node_modules/finalhandler/node_modules/debug/LICENSE
 create mode 100644 node_modules/finalhandler/node_modules/debug/Makefile
 create mode 100644 node_modules/finalhandler/node_modules/debug/README.md
 create mode 100644 node_modules/finalhandler/node_modules/debug/component.json
 create mode 100644 node_modules/finalhandler/node_modules/debug/karma.conf.js
 create mode 100644 node_modules/finalhandler/node_modules/debug/node.js
 create mode 100644 node_modules/finalhandler/node_modules/debug/package.json
 create mode 100644 node_modules/finalhandler/node_modules/debug/src/browser.js
 create mode 100644 node_modules/finalhandler/node_modules/debug/src/debug.js
 create mode 100644 node_modules/finalhandler/node_modules/debug/src/index.js
 create mode 100644 node_modules/finalhandler/node_modules/debug/src/inspector-log.js
 create mode 100644 node_modules/finalhandler/node_modules/debug/src/node.js
 create mode 100644 node_modules/finalhandler/node_modules/ms/index.js
 create mode 100644 node_modules/finalhandler/node_modules/ms/license.md
 create mode 100644 node_modules/finalhandler/node_modules/ms/package.json
 create mode 100644 node_modules/finalhandler/node_modules/ms/readme.md
 create mode 100644 node_modules/finalhandler/package.json
 create mode 100644 node_modules/fresh/HISTORY.md
 create mode 100644 node_modules/fresh/LICENSE
 create mode 100644 node_modules/fresh/README.md
 create mode 100644 node_modules/fresh/index.js
 create mode 100644 node_modules/fresh/package.json
 create mode 100644 node_modules/glob-parent/CHANGELOG.md
 create mode 100644 node_modules/glob-parent/LICENSE
 create mode 100644 node_modules/glob-parent/README.md
 create mode 100644 node_modules/glob-parent/index.js
 create mode 100644 node_modules/glob-parent/package.json
 create mode 100644 node_modules/gray-matter/CHANGELOG.md
 create mode 100644 node_modules/gray-matter/LICENSE
 create mode 100644 node_modules/gray-matter/README.md
 create mode 100644 node_modules/gray-matter/gray-matter.d.ts
 create mode 100644 node_modules/gray-matter/index.js
 create mode 100644 node_modules/gray-matter/lib/defaults.js
 create mode 100644 node_modules/gray-matter/lib/engine.js
 create mode 100644 node_modules/gray-matter/lib/engines.js
 create mode 100644 node_modules/gray-matter/lib/excerpt.js
 create mode 100644 node_modules/gray-matter/lib/parse.js
 create mode 100644 node_modules/gray-matter/lib/stringify.js
 create mode 100644 node_modules/gray-matter/lib/to-file.js
 create mode 100644 node_modules/gray-matter/lib/utils.js
 create mode 120000 node_modules/gray-matter/node_modules/.bin/js-yaml
 create mode 100644 node_modules/gray-matter/node_modules/argparse/CHANGELOG.md
 create mode 100644 node_modules/gray-matter/node_modules/argparse/LICENSE
 create mode 100644 node_modules/gray-matter/node_modules/argparse/README.md
 create mode 100644 node_modules/gray-matter/node_modules/argparse/index.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/action.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/action/append.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/action/append/constant.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/action/count.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/action/help.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/action/store.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/action/store/constant.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/action/store/false.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/action/store/true.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/action/subparsers.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/action/version.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/action_container.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/argparse.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/argument/error.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/argument/exclusive.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/argument/group.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/argument_parser.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/const.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/help/added_formatters.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/help/formatter.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/namespace.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/lib/utils.js
 create mode 100644 node_modules/gray-matter/node_modules/argparse/package.json
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/LICENSE
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/README.md
 create mode 100755 node_modules/gray-matter/node_modules/js-yaml/bin/js-yaml.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/dist/js-yaml.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/dist/js-yaml.min.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/index.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/common.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/dumper.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/exception.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/loader.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/mark.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/schema.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/schema/core.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/schema/default_full.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/schema/default_safe.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/schema/failsafe.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/schema/json.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/binary.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/bool.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/float.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/int.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/js/function.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/js/regexp.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/js/undefined.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/map.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/merge.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/null.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/omap.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/pairs.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/seq.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/set.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/str.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/lib/js-yaml/type/timestamp.js
 create mode 100644 node_modules/gray-matter/node_modules/js-yaml/package.json
 create mode 100644 node_modules/gray-matter/package.json
 create mode 100644 node_modules/htmlparser2/LICENSE
 create mode 100644 node_modules/htmlparser2/README.md
 create mode 100644 node_modules/htmlparser2/lib/FeedHandler.d.ts
 create mode 100644 node_modules/htmlparser2/lib/FeedHandler.d.ts.map
 create mode 100644 node_modules/htmlparser2/lib/FeedHandler.js
 create mode 100644 node_modules/htmlparser2/lib/Parser.d.ts
 create mode 100644 node_modules/htmlparser2/lib/Parser.d.ts.map
 create mode 100644 node_modules/htmlparser2/lib/Parser.js
 create mode 100644 node_modules/htmlparser2/lib/Tokenizer.d.ts
 create mode 100644 node_modules/htmlparser2/lib/Tokenizer.d.ts.map
 create mode 100644 node_modules/htmlparser2/lib/Tokenizer.js
 create mode 100644 node_modules/htmlparser2/lib/WritableStream.d.ts
 create mode 100644 node_modules/htmlparser2/lib/WritableStream.d.ts.map
 create mode 100644 node_modules/htmlparser2/lib/WritableStream.js
 create mode 100644 node_modules/htmlparser2/lib/index.d.ts
 create mode 100644 node_modules/htmlparser2/lib/index.d.ts.map
 create mode 100644 node_modules/htmlparser2/lib/index.js
 create mode 100644 node_modules/htmlparser2/node_modules/entities/LICENSE
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/decode.d.ts
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/decode.d.ts.map
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/decode.js
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/decode_codepoint.d.ts
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/decode_codepoint.d.ts.map
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/decode_codepoint.js
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/encode-trie.d.ts
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/encode-trie.d.ts.map
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/encode-trie.js
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/encode.d.ts
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/encode.d.ts.map
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/encode.js
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/generated/decode-data-html.d.ts
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/generated/decode-data-html.d.ts.map
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/generated/decode-data-html.js
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/generated/decode-data-xml.d.ts
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/generated/decode-data-xml.d.ts.map
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/generated/decode-data-xml.js
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/index.d.ts
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/index.d.ts.map
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/index.js
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/maps/entities.json
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/maps/legacy.json
 create mode 100644 node_modules/htmlparser2/node_modules/entities/lib/maps/xml.json
 create mode 100644 node_modules/htmlparser2/node_modules/entities/package.json
 create mode 100644 node_modules/htmlparser2/node_modules/entities/readme.md
 create mode 100644 node_modules/htmlparser2/package.json
 create mode 100644 node_modules/http-equiv-refresh/README.md
 create mode 100644 node_modules/http-equiv-refresh/index-es5.js
 create mode 100644 node_modules/http-equiv-refresh/index-es5.js.map
 create mode 100644 node_modules/http-equiv-refresh/index.js
 create mode 100644 node_modules/http-equiv-refresh/license
 create mode 100644 node_modules/http-equiv-refresh/package.json
 create mode 100644 node_modules/http-errors/HISTORY.md
 create mode 100644 node_modules/http-errors/LICENSE
 create mode 100644 node_modules/http-errors/README.md
 create mode 100644 node_modules/http-errors/index.js
 create mode 100644 node_modules/http-errors/package.json
 create mode 100644 node_modules/inherits/LICENSE
 create mode 100644 node_modules/inherits/README.md
 create mode 100644 node_modules/inherits/inherits.js
 create mode 100644 node_modules/inherits/inherits_browser.js
 create mode 100644 node_modules/inherits/package.json
 create mode 100644 node_modules/is-alphabetical/index.d.ts
 create mode 100644 node_modules/is-alphabetical/index.js
 create mode 100644 node_modules/is-alphabetical/license
 create mode 100644 node_modules/is-alphabetical/package.json
 create mode 100644 node_modules/is-alphabetical/readme.md
 create mode 100644 node_modules/is-alphanumerical/index.d.ts
 create mode 100644 node_modules/is-alphanumerical/index.js
 create mode 100644 node_modules/is-alphanumerical/license
 create mode 100644 node_modules/is-alphanumerical/package.json
 create mode 100644 node_modules/is-alphanumerical/readme.md
 create mode 100644 node_modules/is-binary-path/index.d.ts
 create mode 100644 node_modules/is-binary-path/index.js
 create mode 100644 node_modules/is-binary-path/license
 create mode 100644 node_modules/is-binary-path/package.json
 create mode 100644 node_modules/is-binary-path/readme.md
 create mode 100644 node_modules/is-decimal/index.d.ts
 create mode 100644 node_modules/is-decimal/index.js
 create mode 100644 node_modules/is-decimal/license
 create mode 100644 node_modules/is-decimal/package.json
 create mode 100644 node_modules/is-decimal/readme.md
 create mode 100644 node_modules/is-extendable/LICENSE
 create mode 100644 node_modules/is-extendable/README.md
 create mode 100644 node_modules/is-extendable/index.js
 create mode 100644 node_modules/is-extendable/package.json
 create mode 100644 node_modules/is-extglob/LICENSE
 create mode 100644 node_modules/is-extglob/README.md
 create mode 100644 node_modules/is-extglob/index.js
 create mode 100644 node_modules/is-extglob/package.json
 create mode 100644 node_modules/is-glob/LICENSE
 create mode 100644 node_modules/is-glob/README.md
 create mode 100644 node_modules/is-glob/index.js
 create mode 100644 node_modules/is-glob/package.json
 create mode 100644 node_modules/is-json/.npmignore
 create mode 100644 node_modules/is-json/.travis.yml
 create mode 100644 node_modules/is-json/LICENSE
 create mode 100644 node_modules/is-json/README.md
 create mode 100644 node_modules/is-json/index.js
 create mode 100644 node_modules/is-json/package.json
 create mode 100644 node_modules/is-json/test/index.js
 create mode 100644 node_modules/is-number/LICENSE
 create mode 100644 node_modules/is-number/README.md
 create mode 100644 node_modules/is-number/index.js
 create mode 100644 node_modules/is-number/package.json
 create mode 100644 node_modules/iso-639-1/.eslintrc
 create mode 100644 node_modules/iso-639-1/.nvmrc
 create mode 100644 node_modules/iso-639-1/.prettierrc
 create mode 100644 node_modules/iso-639-1/.travis.yml
 create mode 100644 node_modules/iso-639-1/.vscode/settings.json
 create mode 100644 node_modules/iso-639-1/CHANGELOG.md
 create mode 100644 node_modules/iso-639-1/LICENSE
 create mode 100644 node_modules/iso-639-1/build/index.js
 create mode 100644 node_modules/iso-639-1/index.d.ts
 create mode 100644 node_modules/iso-639-1/package.json
 create mode 100644 node_modules/iso-639-1/readme.md
 create mode 100644 node_modules/iso-639-1/src/data.js
 create mode 100644 node_modules/iso-639-1/src/index.js
 create mode 100644 node_modules/iso-639-1/test/test.js
 create mode 100644 node_modules/iso-639-1/webpack.config.js
 create mode 100644 node_modules/js-yaml/LICENSE
 create mode 100644 node_modules/js-yaml/README.md
 create mode 100755 node_modules/js-yaml/bin/js-yaml.js
 create mode 100644 node_modules/js-yaml/dist/js-yaml.js
 create mode 100644 node_modules/js-yaml/dist/js-yaml.js.map
 create mode 100644 node_modules/js-yaml/dist/js-yaml.min.js
 create mode 100644 node_modules/js-yaml/dist/js-yaml.min.js.map
 create mode 100644 node_modules/js-yaml/dist/js-yaml.mjs
 create mode 100644 node_modules/js-yaml/dist/js-yaml.mjs.map
 create mode 100644 node_modules/js-yaml/index.js
 create mode 100644 node_modules/js-yaml/lib/common.js
 create mode 100644 node_modules/js-yaml/lib/dumper.js
 create mode 100644 node_modules/js-yaml/lib/exception.js
 create mode 100644 node_modules/js-yaml/lib/index_vite_proxy.tmp.mjs
 create mode 100644 node_modules/js-yaml/lib/loader.js
 create mode 100644 node_modules/js-yaml/lib/schema.js
 create mode 100644 node_modules/js-yaml/lib/schema/core.js
 create mode 100644 node_modules/js-yaml/lib/schema/default.js
 create mode 100644 node_modules/js-yaml/lib/schema/failsafe.js
 create mode 100644 node_modules/js-yaml/lib/schema/json.js
 create mode 100644 node_modules/js-yaml/lib/snippet.js
 create mode 100644 node_modules/js-yaml/lib/type.js
 create mode 100644 node_modules/js-yaml/lib/type/binary.js
 create mode 100644 node_modules/js-yaml/lib/type/bool.js
 create mode 100644 node_modules/js-yaml/lib/type/float.js
 create mode 100644 node_modules/js-yaml/lib/type/int.js
 create mode 100644 node_modules/js-yaml/lib/type/map.js
 create mode 100644 node_modules/js-yaml/lib/type/merge.js
 create mode 100644 node_modules/js-yaml/lib/type/null.js
 create mode 100644 node_modules/js-yaml/lib/type/omap.js
 create mode 100644 node_modules/js-yaml/lib/type/pairs.js
 create mode 100644 node_modules/js-yaml/lib/type/seq.js
 create mode 100644 node_modules/js-yaml/lib/type/set.js
 create mode 100644 node_modules/js-yaml/lib/type/str.js
 create mode 100644 node_modules/js-yaml/lib/type/timestamp.js
 create mode 100644 node_modules/js-yaml/package.json
 create mode 100644 node_modules/junk/index.d.ts
 create mode 100644 node_modules/junk/index.js
 create mode 100644 node_modules/junk/license
 create mode 100644 node_modules/junk/package.json
 create mode 100644 node_modules/junk/readme.md
 create mode 100644 node_modules/kind-of/CHANGELOG.md
 create mode 100644 node_modules/kind-of/LICENSE
 create mode 100644 node_modules/kind-of/README.md
 create mode 100644 node_modules/kind-of/index.js
 create mode 100644 node_modules/kind-of/package.json
 create mode 100644 node_modules/kleur/colors.d.ts
 create mode 100644 node_modules/kleur/colors.js
 create mode 100644 node_modules/kleur/colors.mjs
 create mode 100644 node_modules/kleur/index.d.ts
 create mode 100644 node_modules/kleur/index.js
 create mode 100644 node_modules/kleur/index.mjs
 create mode 100644 node_modules/kleur/license
 create mode 100644 node_modules/kleur/package.json
 create mode 100644 node_modules/kleur/readme.md
 create mode 100644 node_modules/linkify-it/LICENSE
 create mode 100644 node_modules/linkify-it/README.md
 create mode 100644 node_modules/linkify-it/build/index.cjs.js
 create mode 100644 node_modules/linkify-it/index.mjs
 create mode 100644 node_modules/linkify-it/lib/re.mjs
 create mode 100644 node_modules/linkify-it/package.json
 create mode 100644 node_modules/liquidjs/LICENSE
 create mode 100644 node_modules/liquidjs/README.md
 create mode 100755 node_modules/liquidjs/bin/liquid.js
 create mode 100644 node_modules/liquidjs/dist/build/base64-impl-browser.d.ts
 create mode 100644 node_modules/liquidjs/dist/build/base64-impl-browser.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/build/crypto-impl-browser.d.ts
 create mode 100644 node_modules/liquidjs/dist/build/crypto-impl-browser.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/build/fs-impl-browser.d.ts
 create mode 100644 node_modules/liquidjs/dist/build/fs-impl-browser.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/build/streamed-emitter-browser.d.ts
 create mode 100644 node_modules/liquidjs/dist/build/streamed-emitter-browser.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/cache/cache.d.ts
 create mode 100644 node_modules/liquidjs/dist/cache/index.d.ts
 create mode 100644 node_modules/liquidjs/dist/cache/lru.d.ts
 create mode 100644 node_modules/liquidjs/dist/cache/lru.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/context/block-mode.d.ts
 create mode 100644 node_modules/liquidjs/dist/context/context.d.ts
 create mode 100644 node_modules/liquidjs/dist/context/context.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/context/index.d.ts
 create mode 100644 node_modules/liquidjs/dist/context/scope.d.ts
 create mode 100644 node_modules/liquidjs/dist/drop/blank-drop.d.ts
 create mode 100644 node_modules/liquidjs/dist/drop/block-drop.d.ts
 create mode 100644 node_modules/liquidjs/dist/drop/comparable.d.ts
 create mode 100644 node_modules/liquidjs/dist/drop/drop.d.ts
 create mode 100644 node_modules/liquidjs/dist/drop/empty-drop.d.ts
 create mode 100644 node_modules/liquidjs/dist/drop/forloop-drop.d.ts
 create mode 100644 node_modules/liquidjs/dist/drop/index.d.ts
 create mode 100644 node_modules/liquidjs/dist/drop/null-drop.d.ts
 create mode 100644 node_modules/liquidjs/dist/drop/tablerowloop-drop.d.ts
 create mode 100644 node_modules/liquidjs/dist/emitters/emitter.d.ts
 create mode 100644 node_modules/liquidjs/dist/emitters/index.d.ts
 create mode 100644 node_modules/liquidjs/dist/emitters/keeping-type-emitter.d.ts
 create mode 100644 node_modules/liquidjs/dist/emitters/simple-emitter.d.ts
 create mode 100644 node_modules/liquidjs/dist/emitters/streamed-emitter.d.ts
 create mode 100644 node_modules/liquidjs/dist/filters/array.d.ts
 create mode 100644 node_modules/liquidjs/dist/filters/base64-impl.d.ts
 create mode 100644 node_modules/liquidjs/dist/filters/base64.d.ts
 create mode 100644 node_modules/liquidjs/dist/filters/crypto-impl.d.ts
 create mode 100644 node_modules/liquidjs/dist/filters/crypto.d.ts
 create mode 100644 node_modules/liquidjs/dist/filters/date.d.ts
 create mode 100644 node_modules/liquidjs/dist/filters/html.d.ts
 create mode 100644 node_modules/liquidjs/dist/filters/index.d.ts
 create mode 100644 node_modules/liquidjs/dist/filters/math.d.ts
 create mode 100644 node_modules/liquidjs/dist/filters/misc.d.ts
 create mode 100644 node_modules/liquidjs/dist/filters/string.d.ts
 create mode 100644 node_modules/liquidjs/dist/filters/url.d.ts
 create mode 100644 node_modules/liquidjs/dist/fs/fs-impl.d.ts
 create mode 100644 node_modules/liquidjs/dist/fs/fs-impl.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/fs/fs.d.ts
 create mode 100644 node_modules/liquidjs/dist/fs/index.d.ts
 create mode 100644 node_modules/liquidjs/dist/fs/loader.d.ts
 create mode 100644 node_modules/liquidjs/dist/fs/loader.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/fs/map-fs.d.ts
 create mode 100644 node_modules/liquidjs/dist/fs/map-fs.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/fs/node-require.d.ts
 create mode 100644 node_modules/liquidjs/dist/index.d.ts
 create mode 100644 node_modules/liquidjs/dist/liquid-options.d.ts
 create mode 100644 node_modules/liquidjs/dist/liquid-options.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/liquid.browser.min.js
 create mode 100644 node_modules/liquidjs/dist/liquid.browser.min.js.map
 create mode 100644 node_modules/liquidjs/dist/liquid.browser.mjs
 create mode 100644 node_modules/liquidjs/dist/liquid.browser.umd.js
 create mode 100644 node_modules/liquidjs/dist/liquid.browser.umd.js.map
 create mode 100644 node_modules/liquidjs/dist/liquid.d.ts
 create mode 100644 node_modules/liquidjs/dist/liquid.node.js
 create mode 100644 node_modules/liquidjs/dist/liquid.node.mjs
 create mode 100644 node_modules/liquidjs/dist/parser/filter-arg.d.ts
 create mode 100644 node_modules/liquidjs/dist/parser/index.d.ts
 create mode 100644 node_modules/liquidjs/dist/parser/parse-stream.d.ts
 create mode 100644 node_modules/liquidjs/dist/parser/parse-stream.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/parser/parser.d.ts
 create mode 100644 node_modules/liquidjs/dist/parser/parser.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/parser/token-kind.d.ts
 create mode 100644 node_modules/liquidjs/dist/parser/tokenizer.d.ts
 create mode 100644 node_modules/liquidjs/dist/parser/tokenizer.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/parser/whitespace-ctrl.d.ts
 create mode 100644 node_modules/liquidjs/dist/render/boolean.d.ts
 create mode 100644 node_modules/liquidjs/dist/render/boolean.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/render/expression.d.ts
 create mode 100644 node_modules/liquidjs/dist/render/expression.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/render/index.d.ts
 create mode 100644 node_modules/liquidjs/dist/render/operator.d.ts
 create mode 100644 node_modules/liquidjs/dist/render/render.d.ts
 create mode 100644 node_modules/liquidjs/dist/render/render.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/render/string.d.ts
 create mode 100644 node_modules/liquidjs/dist/render/string.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/assign.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/block.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/break.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/capture.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/case.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/comment.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/continue.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/cycle.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/decrement.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/echo.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/for.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/if.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/include.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/increment.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/index.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/inline-comment.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/layout.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/liquid.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/raw.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/render.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/tablerow.d.ts
 create mode 100644 node_modules/liquidjs/dist/tags/unless.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/analysis.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/analysis.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/filter-impl-options.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/filter.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/filter.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/hash.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/hash.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/html.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/index.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/output.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/output.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/tag-options-adapter.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/tag.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/template-impl.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/template.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/value.d.ts
 create mode 100644 node_modules/liquidjs/dist/template/value.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/delimited-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/filter-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/filtered-value-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/hash-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/html-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/identifier-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/index.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/liquid-tag-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/literal-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/number-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/operator-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/output-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/property-access-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/quoted-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/range-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/tag-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/top-level-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/tokens/value-token.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/assert.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/assert.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/async.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/async.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/character.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/error.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/error.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/index.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/intl.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/limiter.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/liquid-date.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/liquid-date.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/literal.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/operator-trie.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/performance.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/performance.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/strftime.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/strftime.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/type-guards.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/type-guards.spec.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/underscore.d.ts
 create mode 100644 node_modules/liquidjs/dist/util/underscore.spec.d.ts
 create mode 100644 node_modules/liquidjs/package.json
 create mode 100644 node_modules/list-to-array/.npmignore
 create mode 100644 node_modules/list-to-array/LICENSE
 create mode 100644 node_modules/list-to-array/README.md
 create mode 100644 node_modules/list-to-array/index.js
 create mode 100644 node_modules/list-to-array/package.json
 create mode 100644 node_modules/list-to-array/tests.js
 create mode 100644 node_modules/luxon/LICENSE.md
 create mode 100644 node_modules/luxon/README.md
 create mode 100644 node_modules/luxon/build/amd/luxon.js
 create mode 100644 node_modules/luxon/build/amd/luxon.js.map
 create mode 100644 node_modules/luxon/build/cjs-browser/luxon.js
 create mode 100644 node_modules/luxon/build/cjs-browser/luxon.js.map
 create mode 100644 node_modules/luxon/build/es6/luxon.mjs
 create mode 100644 node_modules/luxon/build/es6/luxon.mjs.map
 create mode 100644 node_modules/luxon/build/global/luxon.js
 create mode 100644 node_modules/luxon/build/global/luxon.js.map
 create mode 100644 node_modules/luxon/build/global/luxon.min.js
 create mode 100644 node_modules/luxon/build/global/luxon.min.js.map
 create mode 100644 node_modules/luxon/build/node/luxon.js
 create mode 100644 node_modules/luxon/build/node/luxon.js.map
 create mode 100644 node_modules/luxon/package.json
 create mode 100644 node_modules/luxon/src/datetime.js
 create mode 100644 node_modules/luxon/src/duration.js
 create mode 100644 node_modules/luxon/src/errors.js
 create mode 100644 node_modules/luxon/src/impl/conversions.js
 create mode 100644 node_modules/luxon/src/impl/diff.js
 create mode 100644 node_modules/luxon/src/impl/digits.js
 create mode 100644 node_modules/luxon/src/impl/english.js
 create mode 100644 node_modules/luxon/src/impl/formats.js
 create mode 100644 node_modules/luxon/src/impl/formatter.js
 create mode 100644 node_modules/luxon/src/impl/invalid.js
 create mode 100644 node_modules/luxon/src/impl/locale.js
 create mode 100644 node_modules/luxon/src/impl/regexParser.js
 create mode 100644 node_modules/luxon/src/impl/tokenParser.js
 create mode 100644 node_modules/luxon/src/impl/util.js
 create mode 100644 node_modules/luxon/src/impl/zoneUtil.js
 create mode 100644 node_modules/luxon/src/info.js
 create mode 100644 node_modules/luxon/src/interval.js
 create mode 100644 node_modules/luxon/src/luxon.js
 create mode 100644 node_modules/luxon/src/package.json
 create mode 100644 node_modules/luxon/src/settings.js
 create mode 100644 node_modules/luxon/src/zone.js
 create mode 100644 node_modules/luxon/src/zones/IANAZone.js
 create mode 100644 node_modules/luxon/src/zones/fixedOffsetZone.js
 create mode 100644 node_modules/luxon/src/zones/invalidZone.js
 create mode 100644 node_modules/luxon/src/zones/systemZone.js
 create mode 100644 node_modules/markdown-it/LICENSE
 create mode 100644 node_modules/markdown-it/README.md
 create mode 100755 node_modules/markdown-it/bin/markdown-it.mjs
 create mode 100644 node_modules/markdown-it/dist/index.cjs.js
 create mode 100644 node_modules/markdown-it/dist/index.cjs.js.map
 create mode 100644 node_modules/markdown-it/dist/markdown-it.js
 create mode 100644 node_modules/markdown-it/dist/markdown-it.js.map
 create mode 100644 node_modules/markdown-it/dist/markdown-it.min.js
 create mode 100644 node_modules/markdown-it/dist/markdown-it.min.js.map
 create mode 100644 node_modules/markdown-it/index.mjs
 create mode 100644 node_modules/markdown-it/lib/common/html_blocks.mjs
 create mode 100644 node_modules/markdown-it/lib/common/html_re.mjs
 create mode 100644 node_modules/markdown-it/lib/common/utils.mjs
 create mode 100644 node_modules/markdown-it/lib/helpers/index.mjs
 create mode 100644 node_modules/markdown-it/lib/helpers/parse_link_destination.mjs
 create mode 100644 node_modules/markdown-it/lib/helpers/parse_link_label.mjs
 create mode 100644 node_modules/markdown-it/lib/helpers/parse_link_title.mjs
 create mode 100644 node_modules/markdown-it/lib/index.mjs
 create mode 100644 node_modules/markdown-it/lib/parser_block.mjs
 create mode 100644 node_modules/markdown-it/lib/parser_core.mjs
 create mode 100644 node_modules/markdown-it/lib/parser_inline.mjs
 create mode 100644 node_modules/markdown-it/lib/presets/commonmark.mjs
 create mode 100644 node_modules/markdown-it/lib/presets/default.mjs
 create mode 100644 node_modules/markdown-it/lib/presets/zero.mjs
 create mode 100644 node_modules/markdown-it/lib/renderer.mjs
 create mode 100644 node_modules/markdown-it/lib/ruler.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_block/blockquote.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_block/code.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_block/fence.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_block/heading.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_block/hr.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_block/html_block.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_block/lheading.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_block/list.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_block/paragraph.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_block/reference.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_block/state_block.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_block/table.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_core/block.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_core/inline.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_core/linkify.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_core/normalize.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_core/replacements.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_core/smartquotes.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_core/state_core.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_core/text_join.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/autolink.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/backticks.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/balance_pairs.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/emphasis.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/entity.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/escape.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/fragments_join.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/html_inline.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/image.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/link.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/linkify.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/newline.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/state_inline.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/strikethrough.mjs
 create mode 100644 node_modules/markdown-it/lib/rules_inline/text.mjs
 create mode 100644 node_modules/markdown-it/lib/token.mjs
 create mode 100644 node_modules/markdown-it/node_modules/entities/LICENSE
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/decode.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/decode.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/decode.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/decode.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/decode_codepoint.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/decode_codepoint.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/decode_codepoint.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/decode_codepoint.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/encode.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/encode.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/encode.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/encode.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/escape.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/escape.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/escape.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/escape.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/decode.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/decode.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/decode.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/decode.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/decode_codepoint.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/decode_codepoint.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/decode_codepoint.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/decode_codepoint.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/encode.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/encode.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/encode.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/encode.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/escape.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/escape.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/escape.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/escape.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/generated/decode-data-html.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/generated/decode-data-html.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/generated/decode-data-html.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/generated/decode-data-html.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/generated/decode-data-xml.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/generated/decode-data-xml.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/generated/decode-data-xml.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/generated/decode-data-xml.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/generated/encode-html.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/generated/encode-html.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/generated/encode-html.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/generated/encode-html.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/index.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/index.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/index.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/index.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/esm/package.json
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/generated/decode-data-html.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/generated/decode-data-html.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/generated/decode-data-html.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/generated/decode-data-html.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/generated/decode-data-xml.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/generated/decode-data-xml.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/generated/decode-data-xml.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/generated/decode-data-xml.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/generated/encode-html.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/generated/encode-html.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/generated/encode-html.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/generated/encode-html.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/index.d.ts
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/index.d.ts.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/index.js
 create mode 100644 node_modules/markdown-it/node_modules/entities/lib/index.js.map
 create mode 100644 node_modules/markdown-it/node_modules/entities/package.json
 create mode 100644 node_modules/markdown-it/node_modules/entities/readme.md
 create mode 100644 node_modules/markdown-it/package.json
 create mode 100644 node_modules/mdurl/LICENSE
 create mode 100644 node_modules/mdurl/README.md
 create mode 100644 node_modules/mdurl/build/index.cjs.js
 create mode 100644 node_modules/mdurl/index.mjs
 create mode 100644 node_modules/mdurl/lib/decode.mjs
 create mode 100644 node_modules/mdurl/lib/encode.mjs
 create mode 100644 node_modules/mdurl/lib/format.mjs
 create mode 100644 node_modules/mdurl/lib/index.d.ts
 create mode 100644 node_modules/mdurl/lib/parse.mjs
 create mode 100644 node_modules/mdurl/package.json
 create mode 100644 node_modules/mime-db/HISTORY.md
 create mode 100644 node_modules/mime-db/LICENSE
 create mode 100644 node_modules/mime-db/README.md
 create mode 100644 node_modules/mime-db/db.json
 create mode 100644 node_modules/mime-db/index.js
 create mode 100644 node_modules/mime-db/package.json
 create mode 100644 node_modules/mime-types/HISTORY.md
 create mode 100644 node_modules/mime-types/LICENSE
 create mode 100644 node_modules/mime-types/README.md
 create mode 100644 node_modules/mime-types/index.js
 create mode 100644 node_modules/mime-types/mimeScore.js
 create mode 100644 node_modules/mime-types/package.json
 create mode 100644 node_modules/mime/CHANGELOG.md
 create mode 100644 node_modules/mime/LICENSE
 create mode 100644 node_modules/mime/Mime.js
 create mode 100644 node_modules/mime/README.md
 create mode 100755 node_modules/mime/cli.js
 create mode 100644 node_modules/mime/index.js
 create mode 100644 node_modules/mime/lite.js
 create mode 100644 node_modules/mime/package.json
 create mode 100644 node_modules/mime/types/other.js
 create mode 100644 node_modules/mime/types/standard.js
 create mode 100644 node_modules/minimatch/LICENSE
 create mode 100644 node_modules/minimatch/README.md
 create mode 100644 node_modules/minimatch/minimatch.js
 create mode 100644 node_modules/minimatch/package.json
 create mode 100644 node_modules/minimist/.eslintrc
 create mode 100644 node_modules/minimist/.github/FUNDING.yml
 create mode 100644 node_modules/minimist/.nycrc
 create mode 100644 node_modules/minimist/CHANGELOG.md
 create mode 100644 node_modules/minimist/LICENSE
 create mode 100644 node_modules/minimist/README.md
 create mode 100644 node_modules/minimist/example/parse.js
 create mode 100644 node_modules/minimist/index.js
 create mode 100644 node_modules/minimist/package.json
 create mode 100644 node_modules/minimist/test/all_bool.js
 create mode 100644 node_modules/minimist/test/bool.js
 create mode 100644 node_modules/minimist/test/dash.js
 create mode 100644 node_modules/minimist/test/default_bool.js
 create mode 100644 node_modules/minimist/test/dotted.js
 create mode 100644 node_modules/minimist/test/kv_short.js
 create mode 100644 node_modules/minimist/test/long.js
 create mode 100644 node_modules/minimist/test/num.js
 create mode 100644 node_modules/minimist/test/parse.js
 create mode 100644 node_modules/minimist/test/parse_modified.js
 create mode 100644 node_modules/minimist/test/proto.js
 create mode 100644 node_modules/minimist/test/short.js
 create mode 100644 node_modules/minimist/test/stop_early.js
 create mode 100644 node_modules/minimist/test/unknown.js
 create mode 100644 node_modules/minimist/test/whitespace.js
 create mode 100644 node_modules/minipass/LICENSE.md
 create mode 100644 node_modules/minipass/README.md
 create mode 100644 node_modules/minipass/dist/commonjs/index.d.ts
 create mode 100644 node_modules/minipass/dist/commonjs/index.d.ts.map
 create mode 100644 node_modules/minipass/dist/commonjs/index.js
 create mode 100644 node_modules/minipass/dist/commonjs/index.js.map
 create mode 100644 node_modules/minipass/dist/commonjs/package.json
 create mode 100644 node_modules/minipass/dist/esm/index.d.ts
 create mode 100644 node_modules/minipass/dist/esm/index.d.ts.map
 create mode 100644 node_modules/minipass/dist/esm/index.js
 create mode 100644 node_modules/minipass/dist/esm/index.js.map
 create mode 100644 node_modules/minipass/dist/esm/package.json
 create mode 100644 node_modules/minipass/package.json
 create mode 100644 node_modules/moo/LICENSE
 create mode 100644 node_modules/moo/README.md
 create mode 100644 node_modules/moo/moo.js
 create mode 100644 node_modules/moo/package.json
 create mode 100644 node_modules/morphdom/CHANGELOG.md
 create mode 100644 node_modules/morphdom/LICENSE
 create mode 100644 node_modules/morphdom/README.md
 create mode 100644 node_modules/morphdom/dist/morphdom-esm.js
 create mode 100644 node_modules/morphdom/dist/morphdom-factory.js
 create mode 100644 node_modules/morphdom/dist/morphdom-umd.js
 create mode 100644 node_modules/morphdom/dist/morphdom-umd.min.js
 create mode 100644 node_modules/morphdom/dist/morphdom.js
 create mode 100644 node_modules/morphdom/docs/old-benchmark.md
 create mode 100644 node_modules/morphdom/docs/virtual-dom.md
 create mode 100644 node_modules/morphdom/factory.js
 create mode 100644 node_modules/morphdom/index.d.ts
 create mode 100644 node_modules/morphdom/package.json
 create mode 100644 node_modules/morphdom/src/index.js
 create mode 100644 node_modules/morphdom/src/morphAttrs.js
 create mode 100644 node_modules/morphdom/src/morphdom.js
 create mode 100644 node_modules/morphdom/src/specialElHandlers.js
 create mode 100644 node_modules/morphdom/src/util.js
 create mode 100644 node_modules/ms/index.js
 create mode 100644 node_modules/ms/license.md
 create mode 100644 node_modules/ms/package.json
 create mode 100644 node_modules/ms/readme.md
 create mode 100644 node_modules/node-retrieve-globals/LICENSE
 create mode 100644 node_modules/node-retrieve-globals/README.md
 create mode 100644 node_modules/node-retrieve-globals/package.json
 create mode 100644 node_modules/node-retrieve-globals/retrieveGlobals.js
 create mode 100644 node_modules/node-retrieve-globals/util/getWorkingDirectory.js
 create mode 100644 node_modules/node-retrieve-globals/util/vmModules.js
 create mode 100644 node_modules/normalize-path/LICENSE
 create mode 100644 node_modules/normalize-path/README.md
 create mode 100644 node_modules/normalize-path/index.js
 create mode 100644 node_modules/normalize-path/package.json
 create mode 100644 node_modules/nunjucks/LICENSE
 create mode 100644 node_modules/nunjucks/README.md
 create mode 100755 node_modules/nunjucks/bin/precompile
 create mode 100644 node_modules/nunjucks/bin/precompile.cmd
 create mode 100644 node_modules/nunjucks/browser/nunjucks-slim.js
 create mode 100644 node_modules/nunjucks/browser/nunjucks-slim.js.map
 create mode 100644 node_modules/nunjucks/browser/nunjucks-slim.min.js
 create mode 100644 node_modules/nunjucks/browser/nunjucks-slim.min.js.map
 create mode 100644 node_modules/nunjucks/browser/nunjucks.js
 create mode 100644 node_modules/nunjucks/browser/nunjucks.js.map
 create mode 100644 node_modules/nunjucks/browser/nunjucks.min.js
 create mode 100644 node_modules/nunjucks/browser/nunjucks.min.js.map
 create mode 100644 node_modules/nunjucks/index.js
 create mode 100644 node_modules/nunjucks/node_modules/commander/CHANGELOG.md
 create mode 100644 node_modules/nunjucks/node_modules/commander/LICENSE
 create mode 100644 node_modules/nunjucks/node_modules/commander/Readme.md
 create mode 100644 node_modules/nunjucks/node_modules/commander/index.js
 create mode 100644 node_modules/nunjucks/node_modules/commander/package.json
 create mode 100644 node_modules/nunjucks/node_modules/commander/typings/index.d.ts
 create mode 100644 node_modules/nunjucks/package.json
 create mode 100644 node_modules/nunjucks/src/compiler.js
 create mode 100644 node_modules/nunjucks/src/environment.js
 create mode 100644 node_modules/nunjucks/src/express-app.js
 create mode 100644 node_modules/nunjucks/src/filters.js
 create mode 100644 node_modules/nunjucks/src/globals.js
 create mode 100644 node_modules/nunjucks/src/jinja-compat.js
 create mode 100644 node_modules/nunjucks/src/lexer.js
 create mode 100644 node_modules/nunjucks/src/lib.js
 create mode 100644 node_modules/nunjucks/src/loader.js
 create mode 100644 node_modules/nunjucks/src/loaders.js
 create mode 100644 node_modules/nunjucks/src/node-loaders.js
 create mode 100644 node_modules/nunjucks/src/nodes.js
 create mode 100644 node_modules/nunjucks/src/object.js
 create mode 100644 node_modules/nunjucks/src/parser.js
 create mode 100644 node_modules/nunjucks/src/precompile-global.js
 create mode 100644 node_modules/nunjucks/src/precompile.js
 create mode 100644 node_modules/nunjucks/src/precompiled-loader.js
 create mode 100644 node_modules/nunjucks/src/runtime.js
 create mode 100644 node_modules/nunjucks/src/tests.js
 create mode 100644 node_modules/nunjucks/src/transformer.js
 create mode 100644 node_modules/nunjucks/src/web-loaders.js
 create mode 100644 node_modules/on-finished/HISTORY.md
 create mode 100644 node_modules/on-finished/LICENSE
 create mode 100644 node_modules/on-finished/README.md
 create mode 100644 node_modules/on-finished/index.js
 create mode 100644 node_modules/on-finished/package.json
 create mode 100644 node_modules/parse-srcset/.jscs.json
 create mode 100644 node_modules/parse-srcset/.jshintrc
 create mode 100644 node_modules/parse-srcset/.npmignore
 create mode 100644 node_modules/parse-srcset/LICENSE
 create mode 100644 node_modules/parse-srcset/README.md
 create mode 100644 node_modules/parse-srcset/package.json
 create mode 100644 node_modules/parse-srcset/src/parse-srcset.js
 create mode 100755 node_modules/parse-srcset/tests/he.js
 create mode 100644 node_modules/parse-srcset/tests/intern.js
 create mode 100644 node_modules/parse-srcset/tests/unit/ps.js
 create mode 100644 node_modules/parseurl/HISTORY.md
 create mode 100644 node_modules/parseurl/LICENSE
 create mode 100644 node_modules/parseurl/README.md
 create mode 100644 node_modules/parseurl/index.js
 create mode 100644 node_modules/parseurl/package.json
 create mode 100644 node_modules/picomatch/LICENSE
 create mode 100644 node_modules/picomatch/README.md
 create mode 100644 node_modules/picomatch/index.js
 create mode 100644 node_modules/picomatch/lib/constants.js
 create mode 100644 node_modules/picomatch/lib/parse.js
 create mode 100644 node_modules/picomatch/lib/picomatch.js
 create mode 100644 node_modules/picomatch/lib/scan.js
 create mode 100644 node_modules/picomatch/lib/utils.js
 create mode 100644 node_modules/picomatch/package.json
 create mode 100644 node_modules/picomatch/posix.js
 create mode 100644 node_modules/please-upgrade-node/.eslintrc.js
 create mode 100644 node_modules/please-upgrade-node/.github/FUNDING.yml
 create mode 100644 node_modules/please-upgrade-node/LICENSE
 create mode 100644 node_modules/please-upgrade-node/README.md
 create mode 100644 node_modules/please-upgrade-node/index.d.ts
 create mode 100644 node_modules/please-upgrade-node/index.js
 create mode 100644 node_modules/please-upgrade-node/package.json
 create mode 100644 node_modules/posthtml-match-helper/LICENSE
 create mode 100644 node_modules/posthtml-match-helper/README.md
 create mode 100644 node_modules/posthtml-match-helper/lib/index.d.ts
 create mode 100644 node_modules/posthtml-match-helper/lib/index.js
 create mode 100644 node_modules/posthtml-match-helper/package.json
 create mode 100644 node_modules/posthtml-parser/dist/chunk.2UQLUWPH.js
 create mode 100644 node_modules/posthtml-parser/dist/index.d.ts
 create mode 100644 node_modules/posthtml-parser/dist/index.js
 create mode 100644 node_modules/posthtml-parser/dist/location-tracker.d.ts
 create mode 100644 node_modules/posthtml-parser/dist/location-tracker.js
 create mode 100644 node_modules/posthtml-parser/license
 create mode 100644 node_modules/posthtml-parser/package.json
 create mode 100644 node_modules/posthtml-parser/readme.md
 create mode 100644 node_modules/posthtml-render/changelog.md
 create mode 100644 node_modules/posthtml-render/dist/index.d.ts
 create mode 100644 node_modules/posthtml-render/dist/index.js
 create mode 100644 node_modules/posthtml-render/license
 create mode 100644 node_modules/posthtml-render/package.json
 create mode 100644 node_modules/posthtml-render/readme.md
 create mode 100644 node_modules/posthtml/lib/api.js
 create mode 100644 node_modules/posthtml/lib/index.js
 create mode 100644 node_modules/posthtml/license
 create mode 100644 node_modules/posthtml/package.json
 create mode 100644 node_modules/posthtml/readme.md
 create mode 100644 node_modules/posthtml/types/posthtml.d.ts
 create mode 100644 node_modules/prr/.jshintrc
 create mode 100644 node_modules/prr/.npmignore
 create mode 100644 node_modules/prr/.travis.yml
 create mode 100644 node_modules/prr/LICENSE.md
 create mode 100644 node_modules/prr/README.md
 create mode 100644 node_modules/prr/package.json
 create mode 100644 node_modules/prr/prr.js
 create mode 100644 node_modules/prr/test.js
 create mode 100644 node_modules/punycode.js/LICENSE-MIT.txt
 create mode 100644 node_modules/punycode.js/README.md
 create mode 100644 node_modules/punycode.js/package.json
 create mode 100644 node_modules/punycode.js/punycode.es6.js
 create mode 100644 node_modules/punycode.js/punycode.js
 create mode 100644 node_modules/range-parser/HISTORY.md
 create mode 100644 node_modules/range-parser/LICENSE
 create mode 100644 node_modules/range-parser/README.md
 create mode 100644 node_modules/range-parser/index.js
 create mode 100644 node_modules/range-parser/package.json
 create mode 100644 node_modules/readdirp/LICENSE
 create mode 100644 node_modules/readdirp/README.md
 create mode 100644 node_modules/readdirp/index.d.ts
 create mode 100644 node_modules/readdirp/index.js
 create mode 100644 node_modules/readdirp/node_modules/picomatch/LICENSE
 create mode 100644 node_modules/readdirp/node_modules/picomatch/README.md
 create mode 100644 node_modules/readdirp/node_modules/picomatch/index.js
 create mode 100644 node_modules/readdirp/node_modules/picomatch/lib/constants.js
 create mode 100644 node_modules/readdirp/node_modules/picomatch/lib/parse.js
 create mode 100644 node_modules/readdirp/node_modules/picomatch/lib/picomatch.js
 create mode 100644 node_modules/readdirp/node_modules/picomatch/lib/scan.js
 create mode 100644 node_modules/readdirp/node_modules/picomatch/lib/utils.js
 create mode 100644 node_modules/readdirp/node_modules/picomatch/package.json
 create mode 100644 node_modules/readdirp/package.json
 create mode 100644 node_modules/section-matter/LICENSE
 create mode 100644 node_modules/section-matter/README.md
 create mode 100644 node_modules/section-matter/index.js
 create mode 100644 node_modules/section-matter/package.json
 create mode 100644 node_modules/semver-compare/.travis.yml
 create mode 100644 node_modules/semver-compare/LICENSE
 create mode 100644 node_modules/semver-compare/example/cmp.js
 create mode 100644 node_modules/semver-compare/example/lex.js
 create mode 100644 node_modules/semver-compare/index.js
 create mode 100644 node_modules/semver-compare/package.json
 create mode 100644 node_modules/semver-compare/readme.markdown
 create mode 100644 node_modules/semver-compare/test/cmp.js
 create mode 100644 node_modules/semver/LICENSE
 create mode 100644 node_modules/semver/README.md
 create mode 100755 node_modules/semver/bin/semver.js
 create mode 100644 node_modules/semver/classes/comparator.js
 create mode 100644 node_modules/semver/classes/index.js
 create mode 100644 node_modules/semver/classes/range.js
 create mode 100644 node_modules/semver/classes/semver.js
 create mode 100644 node_modules/semver/functions/clean.js
 create mode 100644 node_modules/semver/functions/cmp.js
 create mode 100644 node_modules/semver/functions/coerce.js
 create mode 100644 node_modules/semver/functions/compare-build.js
 create mode 100644 node_modules/semver/functions/compare-loose.js
 create mode 100644 node_modules/semver/functions/compare.js
 create mode 100644 node_modules/semver/functions/diff.js
 create mode 100644 node_modules/semver/functions/eq.js
 create mode 100644 node_modules/semver/functions/gt.js
 create mode 100644 node_modules/semver/functions/gte.js
 create mode 100644 node_modules/semver/functions/inc.js
 create mode 100644 node_modules/semver/functions/lt.js
 create mode 100644 node_modules/semver/functions/lte.js
 create mode 100644 node_modules/semver/functions/major.js
 create mode 100644 node_modules/semver/functions/minor.js
 create mode 100644 node_modules/semver/functions/neq.js
 create mode 100644 node_modules/semver/functions/parse.js
 create mode 100644 node_modules/semver/functions/patch.js
 create mode 100644 node_modules/semver/functions/prerelease.js
 create mode 100644 node_modules/semver/functions/rcompare.js
 create mode 100644 node_modules/semver/functions/rsort.js
 create mode 100644 node_modules/semver/functions/satisfies.js
 create mode 100644 node_modules/semver/functions/sort.js
 create mode 100644 node_modules/semver/functions/truncate.js
 create mode 100644 node_modules/semver/functions/valid.js
 create mode 100644 node_modules/semver/index.js
 create mode 100644 node_modules/semver/internal/constants.js
 create mode 100644 node_modules/semver/internal/debug.js
 create mode 100644 node_modules/semver/internal/identifiers.js
 create mode 100644 node_modules/semver/internal/lrucache.js
 create mode 100644 node_modules/semver/internal/parse-options.js
 create mode 100644 node_modules/semver/internal/re.js
 create mode 100644 node_modules/semver/package.json
 create mode 100644 node_modules/semver/preload.js
 create mode 100644 node_modules/semver/range.bnf
 create mode 100644 node_modules/semver/ranges/gtr.js
 create mode 100644 node_modules/semver/ranges/intersects.js
 create mode 100644 node_modules/semver/ranges/ltr.js
 create mode 100644 node_modules/semver/ranges/max-satisfying.js
 create mode 100644 node_modules/semver/ranges/min-satisfying.js
 create mode 100644 node_modules/semver/ranges/min-version.js
 create mode 100644 node_modules/semver/ranges/outside.js
 create mode 100644 node_modules/semver/ranges/simplify.js
 create mode 100644 node_modules/semver/ranges/subset.js
 create mode 100644 node_modules/semver/ranges/to-comparators.js
 create mode 100644 node_modules/semver/ranges/valid.js
 create mode 100644 node_modules/send/LICENSE
 create mode 100644 node_modules/send/README.md
 create mode 100644 node_modules/send/index.js
 create mode 100644 node_modules/send/package.json
 create mode 100644 node_modules/setprototypeof/LICENSE
 create mode 100644 node_modules/setprototypeof/README.md
 create mode 100644 node_modules/setprototypeof/index.d.ts
 create mode 100644 node_modules/setprototypeof/index.js
 create mode 100644 node_modules/setprototypeof/package.json
 create mode 100644 node_modules/setprototypeof/test/index.js
 create mode 100644 node_modules/slash/index.d.ts
 create mode 100644 node_modules/slash/index.js
 create mode 100644 node_modules/slash/license
 create mode 100644 node_modules/slash/package.json
 create mode 100644 node_modules/slash/readme.md
 create mode 100644 node_modules/slugify/LICENSE
 create mode 100644 node_modules/slugify/README.md
 create mode 100644 node_modules/slugify/package.json
 create mode 100644 node_modules/slugify/slugify.d.ts
 create mode 100644 node_modules/slugify/slugify.js
 create mode 100644 node_modules/sprintf-js/.npmignore
 create mode 100644 node_modules/sprintf-js/LICENSE
 create mode 100644 node_modules/sprintf-js/README.md
 create mode 100644 node_modules/sprintf-js/bower.json
 create mode 100644 node_modules/sprintf-js/demo/angular.html
 create mode 100644 node_modules/sprintf-js/dist/angular-sprintf.min.js
 create mode 100644 node_modules/sprintf-js/dist/angular-sprintf.min.js.map
 create mode 100644 node_modules/sprintf-js/dist/angular-sprintf.min.map
 create mode 100644 node_modules/sprintf-js/dist/sprintf.min.js
 create mode 100644 node_modules/sprintf-js/dist/sprintf.min.js.map
 create mode 100644 node_modules/sprintf-js/dist/sprintf.min.map
 create mode 100644 node_modules/sprintf-js/gruntfile.js
 create mode 100644 node_modules/sprintf-js/package.json
 create mode 100644 node_modules/sprintf-js/src/angular-sprintf.js
 create mode 100644 node_modules/sprintf-js/src/sprintf.js
 create mode 100644 node_modules/sprintf-js/test/test.js
 create mode 100644 node_modules/ssri/LICENSE.md
 create mode 100644 node_modules/ssri/README.md
 create mode 100644 node_modules/ssri/lib/index.js
 create mode 100644 node_modules/ssri/package.json
 create mode 100644 node_modules/statuses/HISTORY.md
 create mode 100644 node_modules/statuses/LICENSE
 create mode 100644 node_modules/statuses/README.md
 create mode 100644 node_modules/statuses/codes.json
 create mode 100644 node_modules/statuses/index.js
 create mode 100644 node_modules/statuses/package.json
 create mode 100644 node_modules/strip-bom-string/LICENSE
 create mode 100644 node_modules/strip-bom-string/README.md
 create mode 100644 node_modules/strip-bom-string/index.js
 create mode 100644 node_modules/strip-bom-string/package.json
 create mode 100644 node_modules/tinyglobby/LICENSE
 create mode 100644 node_modules/tinyglobby/README.md
 create mode 100644 node_modules/tinyglobby/dist/index.cjs
 create mode 100644 node_modules/tinyglobby/dist/index.d.cts
 create mode 100644 node_modules/tinyglobby/dist/index.d.mts
 create mode 100644 node_modules/tinyglobby/dist/index.mjs
 create mode 100644 node_modules/tinyglobby/package.json
 create mode 100644 node_modules/to-regex-range/LICENSE
 create mode 100644 node_modules/to-regex-range/README.md
 create mode 100644 node_modules/to-regex-range/index.js
 create mode 100644 node_modules/to-regex-range/package.json
 create mode 100644 node_modules/toidentifier/HISTORY.md
 create mode 100644 node_modules/toidentifier/LICENSE
 create mode 100644 node_modules/toidentifier/README.md
 create mode 100644 node_modules/toidentifier/index.js
 create mode 100644 node_modules/toidentifier/package.json
 create mode 100644 node_modules/uc.micro/LICENSE.txt
 create mode 100644 node_modules/uc.micro/README.md
 create mode 100644 node_modules/uc.micro/build/index.cjs.js
 create mode 100644 node_modules/uc.micro/categories/Cc/regex.mjs
 create mode 100644 node_modules/uc.micro/categories/Cf/regex.mjs
 create mode 100644 node_modules/uc.micro/categories/P/regex.mjs
 create mode 100644 node_modules/uc.micro/categories/S/regex.mjs
 create mode 100644 node_modules/uc.micro/categories/Z/regex.mjs
 create mode 100644 node_modules/uc.micro/index.mjs
 create mode 100644 node_modules/uc.micro/package.json
 create mode 100644 node_modules/uc.micro/properties/Any/regex.mjs
 create mode 100644 node_modules/unpipe/HISTORY.md
 create mode 100644 node_modules/unpipe/LICENSE
 create mode 100644 node_modules/unpipe/README.md
 create mode 100644 node_modules/unpipe/index.js
 create mode 100644 node_modules/unpipe/package.json
 create mode 100644 node_modules/urlpattern-polyfill/LICENSE
 create mode 100644 node_modules/urlpattern-polyfill/README.md
 create mode 100644 node_modules/urlpattern-polyfill/dist/index.d.ts
 create mode 100644 node_modules/urlpattern-polyfill/dist/types.d.ts
 create mode 100644 node_modules/urlpattern-polyfill/dist/urlpattern.cjs
 create mode 100644 node_modules/urlpattern-polyfill/dist/urlpattern.js
 create mode 100644 node_modules/urlpattern-polyfill/index.cjs
 create mode 100644 node_modules/urlpattern-polyfill/index.js
 create mode 100644 node_modules/urlpattern-polyfill/package.json
 create mode 100644 node_modules/ws/LICENSE
 create mode 100644 node_modules/ws/README.md
 create mode 100644 node_modules/ws/browser.js
 create mode 100644 node_modules/ws/index.js
 create mode 100644 node_modules/ws/lib/buffer-util.js
 create mode 100644 node_modules/ws/lib/constants.js
 create mode 100644 node_modules/ws/lib/event-target.js
 create mode 100644 node_modules/ws/lib/extension.js
 create mode 100644 node_modules/ws/lib/limiter.js
 create mode 100644 node_modules/ws/lib/permessage-deflate.js
 create mode 100644 node_modules/ws/lib/receiver.js
 create mode 100644 node_modules/ws/lib/sender.js
 create mode 100644 node_modules/ws/lib/stream.js
 create mode 100644 node_modules/ws/lib/subprotocol.js
 create mode 100644 node_modules/ws/lib/validation.js
 create mode 100644 node_modules/ws/lib/websocket-server.js
 create mode 100644 node_modules/ws/lib/websocket.js
 create mode 100644 node_modules/ws/package.json
 create mode 100644 node_modules/ws/wrapper.mjs
 create mode 100644 package-lock.json
 create mode 100644 package.json
Username for 'https://github.com': ^C

一旦ここで停止。

ただし、今回は先に修正したほうがいい。node_modules と _site までコミットされている。

いったん Ctrl+C で止めて、これを実行。

rm -rf .git
printf '%s\n' 'node_modules/' '_site/' > .gitignore

git init
git add .
git commit -m "Initial Eleventy site"
git branch -M main
git remote add origin https://github.com/rareutsu-cui/netlify-eleventy-test.git
git push -u origin main

今度はコミット対象が、

.gitignore
index.md
package.json
package-lock.json

あたりだけになる。

そして git push で認証を求められたら、そこでGitHub認証を通す。
GitHub側には、こちらのGitHub接続も使える。 netlify-eleventy-test の状態確認もできる。

ここで、サイドコマンドWo打ち込む

eugene@alberone:~/netlify-eleventy-test$ rm -rf .git
printf '%s\n' 'node_modules/' '_site/' > .gitignore

git init
git add .
git commit -m "Initial Eleventy site"
git branch -M main
git remote add origin https://github.com/rareutsu-cui/netlify-eleventy-test.git
git push -u origin main
hint: Using 'master' as the name for the initial branch. This default branch name
hint: will change to "main" in Git 3.0. To configure the initial branch name
hint: to use in all of your new repositories, which will suppress this warning,
hint: call:
hint:
hint:   git config --global init.defaultBranch <name>
hint:
hint: Names commonly chosen instead of 'master' are 'main', 'trunk' and
hint: 'development'. The just-created branch can be renamed via this command:
hint:
hint:   git branch -m <name>
hint:
hint: Disable this message with "git config set advice.defaultBranchName false"
Initialized empty Git repository in /home/eugene/netlify-eleventy-test/.git/
[master (root-commit) 0cbf172] Initial Eleventy site
 4 files changed, 1447 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 index.md
 create mode 100644 package-lock.json
 create mode 100644 package.json
Username for 'https://github.com': rareutsu-cui
Password for 'https://rareutsu-cui@github.com':
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 4 threads
Compressing objects: 100% (5/5), done.
Writing objects: 100% (6/6), 14.91 KiB | 1.66 MiB/s, done.
Total 6 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
To https://github.com/rareutsu-cui/netlify-eleventy-test.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
eugene@alberone:~/netlify-eleventy-test$

最後に、githubのユーザーネームとgithub_tokunを入力して完了。

今回の件は、単なるミスというより、

「EleventyをNetlifyに載せる初心者向け手順を実際に通してみたら、Gitの段階で説明不足が発見された」

という検証結果だな。

むしろ今ここで破綻したのは収穫がある。
この工程を「執筆機制作の知見」に入れるなら、

npmで依存関係を入れたら、Git管理前に .gitignore を作る。

これを最初から固定する。
---
※githubへの登録とgithub_tokunの取得は、今回省きましたが安全な方法でまず作成してください。
これにのぞむのはそれからでも遅くはありません。
---
ここは通った。

main → GitHub origin/main と同期済み
working tree clean
node_modules/ → Git管理対象なし

つまり、今回の修正は成功。

次

GitHub側の netlify-eleventy-test を確認してから、Netlifyへ戻る。

Netlifyで、

Add new project → Import an existing project → GitHub

から

rareutsu-cui/netlify-eleventy-test

を選択。

Build設定はまずこれ。

Build command:
npm install && npx eleventy

Publish directory:
_site

ここまでで Markdown → Eleventy → Netlify の最小経路を通す。

今回は余計なものをまだ足さない。

現在地はここ：

GitHub：rareutsu-cui/netlify-eleventy-test
ローカル：~/netlify-eleventy-test
Eleventy：3.1.6 インストール済み
index.md → _site/index.html の生成確認済み
node_modules はGit管理対象外
main → origin/main 同期済み
作業ツリー clean

次は Netlify側でこのGitHubリポジトリを接続してデプロイ。

Netlifyで：

Add new project → Import an existing project → GitHub → rareutsu-cui/netlify-eleventy-test

ビルド設定は、

Build command:
npm install && npx eleventy

Publish directory:
_site

ここから続行。

まず作成した。調べたところ、https://developer.mamezou-tech.com/11ty/11ty-intro/
では重要なのは、この記事では単なる index.md の一行テストで終わっていない。

実際に進める内容は、

_includes/base.liquid
Markdownコンテンツ
Front Matter
eleventy.config.js
src → public の構成
CSSのパススルーコピー
プラグイン
ディレクトリ単位の post.json

まで発展している。

「一杯書いた」は、こっちの流れを指していたんだな。

今はNetlifyのデプロイ設定へ飛ぶ前に、このEleventy入門のどこまで作った状態なのかを確認して、その続きから進めるのが正しい。

そう。**上げる前に、まずローカルで記事どおりの構成まで作って確認する段階**だ。

この記事の流れでは、

`index.liquid` の最小確認
→ `_includes/base.liquid`
→ `sample.md`
→ `eleventy.config.js`
→ `src/` / `public/` 構成
→ CSS・プラグイン
→ `post.json`

まで進めてから、`public`（この記事では `_site` から変更）をデプロイする流れになっている。([豆蔵デベロッパーサイト][1])

だから**今はNetlifyへ上げない。**

まず現在のローカルを確認する：

```bash
cd ~/netlify-eleventy-test
find . -maxdepth 3 -type f | sort
```

これで、**実際にどこまで作ってあるかを確認してから、その続きだけやる。**

[1]: https://developer.mamezou-tech.com/11ty/11ty-intro/ "Eleventy入門(第1回) - 11tyで手早く静的サイトを作成する | 豆蔵デベロッパーサイト"

eugene@alberone:~/netlify-eleventy-test$ cd ~/netlify-eleventy-test
find . -maxdepth 3 -type f | sort
./.git/COMMIT_EDITMSG
./.git/config
./.git/description
./.git/HEAD
./.git/hooks/applypatch-msg.sample
./.git/hooks/commit-msg.sample
./.git/hooks/fsmonitor-watchman.sample
./.git/hooks/post-update.sample
./.git/hooks/pre-applypatch.sample
./.git/hooks/pre-commit.sample
./.git/hooks/pre-merge-commit.sample
./.git/hooks/prepare-commit-msg.sample
./.git/hooks/pre-push.sample
./.git/hooks/pre-rebase.sample
./.git/hooks/pre-receive.sample
./.git/hooks/push-to-checkout.sample
./.git/hooks/sendemail-validate.sample
./.git/hooks/update.sample
./.gitignore
./.git/index
./.git/info/exclude
./.git/logs/HEAD
./index.md
./node_modules/acorn/CHANGELOG.md
./node_modules/acorn/LICENSE
./node_modules/acorn/package.json
./node_modules/acorn/README.md
./node_modules/acorn-walk/CHANGELOG.md
./node_modules/acorn-walk/LICENSE
./node_modules/acorn-walk/package.json
./node_modules/acorn-walk/README.md
./node_modules/anymatch/index.d.ts
./node_modules/anymatch/index.js
./node_modules/anymatch/LICENSE
./node_modules/anymatch/package.json
./node_modules/anymatch/README.md
./node_modules/argparse/argparse.js
./node_modules/argparse/CHANGELOG.md
./node_modules/argparse/LICENSE
./node_modules/argparse/package.json
./node_modules/argparse/README.md
./node_modules/asap/asap.js
./node_modules/asap/browser-asap.js
./node_modules/asap/browser-raw.js
./node_modules/asap/CHANGES.md
./node_modules/asap/LICENSE.md
./node_modules/asap/package.json
./node_modules/asap/raw.js
./node_modules/asap/README.md
./node_modules/a-sync-waterfall/index.js
./node_modules/a-sync-waterfall/LICENSE
./node_modules/a-sync-waterfall/package.json
./node_modules/a-sync-waterfall/README.md
./node_modules/a-sync-waterfall/test.js
./node_modules/balanced-match/index.js
./node_modules/balanced-match/LICENSE.md
./node_modules/balanced-match/package.json
./node_modules/balanced-match/README.md
./node_modules/bcp-47/index.d.ts
./node_modules/bcp-47/index.d.ts.map
./node_modules/bcp-47/index.js
./node_modules/bcp-47/license
./node_modules/bcp-47-match/index.d.ts
./node_modules/bcp-47-match/index.js
./node_modules/bcp-47-match/license
./node_modules/bcp-47-match/package.json
./node_modules/bcp-47-match/readme.md
./node_modules/bcp-47-normalize/index.d.ts
./node_modules/bcp-47-normalize/index.js
./node_modules/bcp-47-normalize/license
./node_modules/bcp-47-normalize/package.json
./node_modules/bcp-47-normalize/readme.md
./node_modules/bcp-47/package.json
./node_modules/bcp-47/readme.md
./node_modules/binary-extensions/binary-extensions.json
./node_modules/binary-extensions/binary-extensions.json.d.ts
./node_modules/binary-extensions/index.d.ts
./node_modules/binary-extensions/index.js
./node_modules/binary-extensions/license
./node_modules/binary-extensions/package.json
./node_modules/binary-extensions/readme.md
./node_modules/brace-expansion/index.js
./node_modules/brace-expansion/LICENSE
./node_modules/brace-expansion/package.json
./node_modules/brace-expansion/README.md
./node_modules/braces/index.js
./node_modules/braces/LICENSE
./node_modules/braces/package.json
./node_modules/braces/README.md
./node_modules/chokidar/index.js
./node_modules/chokidar/LICENSE
./node_modules/chokidar/package.json
./node_modules/chokidar/README.md
./node_modules/commander/esm.mjs
./node_modules/commander/index.js
./node_modules/commander/LICENSE
./node_modules/commander/package.json
./node_modules/commander/package-support.json
./node_modules/commander/Readme.md
./node_modules/concat-map/index.js
./node_modules/concat-map/LICENSE
./node_modules/concat-map/package.json
./node_modules/concat-map/README.markdown
./node_modules/concat-map/.travis.yml
./node_modules/debug/LICENSE
./node_modules/debug/package.json
./node_modules/debug/README.md
./node_modules/depd/History.md
./node_modules/depd/index.js
./node_modules/depd/LICENSE
./node_modules/depd/package.json
./node_modules/depd/Readme.md
./node_modules/dependency-graph/CHANGELOG.md
./node_modules/dependency-graph/LICENSE
./node_modules/dependency-graph/package.json
./node_modules/dependency-graph/README.md
./node_modules/domelementtype/LICENSE
./node_modules/domelementtype/package.json
./node_modules/domelementtype/readme.md
./node_modules/domhandler/LICENSE
./node_modules/domhandler/package.json
./node_modules/domhandler/readme.md
./node_modules/dom-serializer/LICENSE
./node_modules/dom-serializer/package.json
./node_modules/dom-serializer/README.md
./node_modules/domutils/LICENSE
./node_modules/domutils/package.json
./node_modules/domutils/readme.md
./node_modules/ee-first/index.js
./node_modules/ee-first/LICENSE
./node_modules/ee-first/package.json
./node_modules/ee-first/README.md
./node_modules/encodeurl/index.js
./node_modules/encodeurl/LICENSE
./node_modules/encodeurl/package.json
./node_modules/encodeurl/README.md
./node_modules/entities/decode.d.ts
./node_modules/entities/decode.js
./node_modules/entities/escape.d.ts
./node_modules/entities/escape.js
./node_modules/entities/LICENSE
./node_modules/entities/package.json
./node_modules/entities/readme.md
./node_modules/errno/build.js
./node_modules/errno/cli.js
./node_modules/errno/custom.js
./node_modules/errno/errno.js
./node_modules/errno/.jshintrc
./node_modules/errno/package.json
./node_modules/errno/README.md
./node_modules/errno/test.js
./node_modules/errno/.travis.yml
./node_modules/escape-html/index.js
./node_modules/escape-html/LICENSE
./node_modules/escape-html/package.json
./node_modules/escape-html/Readme.md
./node_modules/escape-string-regexp/index.d.ts
./node_modules/escape-string-regexp/index.js
./node_modules/escape-string-regexp/license
./node_modules/escape-string-regexp/package.json
./node_modules/escape-string-regexp/readme.md
./node_modules/esm-import-transformer/import-transformer.js
./node_modules/esm-import-transformer/package.json
./node_modules/esm-import-transformer/README.md
./node_modules/esprima/ChangeLog
./node_modules/esprima/LICENSE.BSD
./node_modules/esprima/package.json
./node_modules/esprima/README.md
./node_modules/etag/HISTORY.md
./node_modules/etag/index.js
./node_modules/etag/LICENSE
./node_modules/etag/package.json
./node_modules/etag/README.md
./node_modules/evaluate-value/index-es5.js
./node_modules/evaluate-value/index-es5.js.map
./node_modules/evaluate-value/index.js
./node_modules/evaluate-value/LICENSE
./node_modules/evaluate-value/package.json
./node_modules/evaluate-value/README.md
./node_modules/extend-shallow/index.js
./node_modules/extend-shallow/LICENSE
./node_modules/extend-shallow/package.json
./node_modules/extend-shallow/README.md
./node_modules/fdir/LICENSE
./node_modules/fdir/package.json
./node_modules/fdir/README.md
./node_modules/filesize/LICENSE
./node_modules/filesize/package.json
./node_modules/filesize/README.md
./node_modules/fill-range/index.js
./node_modules/fill-range/LICENSE
./node_modules/fill-range/package.json
./node_modules/fill-range/README.md
./node_modules/finalhandler/HISTORY.md
./node_modules/finalhandler/index.js
./node_modules/finalhandler/LICENSE
./node_modules/finalhandler/package.json
./node_modules/finalhandler/README.md
./node_modules/finalhandler/SECURITY.md
./node_modules/fresh/HISTORY.md
./node_modules/fresh/index.js
./node_modules/fresh/LICENSE
./node_modules/fresh/package.json
./node_modules/fresh/README.md
./node_modules/glob-parent/CHANGELOG.md
./node_modules/glob-parent/index.js
./node_modules/glob-parent/LICENSE
./node_modules/glob-parent/package.json
./node_modules/glob-parent/README.md
./node_modules/gray-matter/CHANGELOG.md
./node_modules/gray-matter/gray-matter.d.ts
./node_modules/gray-matter/index.js
./node_modules/gray-matter/LICENSE
./node_modules/gray-matter/package.json
./node_modules/gray-matter/README.md
./node_modules/htmlparser2/LICENSE
./node_modules/htmlparser2/package.json
./node_modules/htmlparser2/README.md
./node_modules/http-equiv-refresh/index-es5.js
./node_modules/http-equiv-refresh/index-es5.js.map
./node_modules/http-equiv-refresh/index.js
./node_modules/http-equiv-refresh/license
./node_modules/http-equiv-refresh/package.json
./node_modules/http-equiv-refresh/README.md
./node_modules/http-errors/HISTORY.md
./node_modules/http-errors/index.js
./node_modules/http-errors/LICENSE
./node_modules/http-errors/package.json
./node_modules/http-errors/README.md
./node_modules/inherits/inherits_browser.js
./node_modules/inherits/inherits.js
./node_modules/inherits/LICENSE
./node_modules/inherits/package.json
./node_modules/inherits/README.md
./node_modules/is-alphabetical/index.d.ts
./node_modules/is-alphabetical/index.js
./node_modules/is-alphabetical/license
./node_modules/is-alphabetical/package.json
./node_modules/is-alphabetical/readme.md
./node_modules/is-alphanumerical/index.d.ts
./node_modules/is-alphanumerical/index.js
./node_modules/is-alphanumerical/license
./node_modules/is-alphanumerical/package.json
./node_modules/is-alphanumerical/readme.md
./node_modules/is-binary-path/index.d.ts
./node_modules/is-binary-path/index.js
./node_modules/is-binary-path/license
./node_modules/is-binary-path/package.json
./node_modules/is-binary-path/readme.md
./node_modules/is-decimal/index.d.ts
./node_modules/is-decimal/index.js
./node_modules/is-decimal/license
./node_modules/is-decimal/package.json
./node_modules/is-decimal/readme.md
./node_modules/is-extendable/index.js
./node_modules/is-extendable/LICENSE
./node_modules/is-extendable/package.json
./node_modules/is-extendable/README.md
./node_modules/is-extglob/index.js
./node_modules/is-extglob/LICENSE
./node_modules/is-extglob/package.json
./node_modules/is-extglob/README.md
./node_modules/is-glob/index.js
./node_modules/is-glob/LICENSE
./node_modules/is-glob/package.json
./node_modules/is-glob/README.md
./node_modules/is-json/index.js
./node_modules/is-json/LICENSE
./node_modules/is-json/.npmignore
./node_modules/is-json/package.json
./node_modules/is-json/README.md
./node_modules/is-json/.travis.yml
./node_modules/is-number/index.js
./node_modules/is-number/LICENSE
./node_modules/is-number/package.json
./node_modules/is-number/README.md
./node_modules/iso-639-1/CHANGELOG.md
./node_modules/iso-639-1/.eslintrc
./node_modules/iso-639-1/index.d.ts
./node_modules/iso-639-1/LICENSE
./node_modules/iso-639-1/.nvmrc
./node_modules/iso-639-1/package.json
./node_modules/iso-639-1/.prettierrc
./node_modules/iso-639-1/readme.md
./node_modules/iso-639-1/.travis.yml
./node_modules/iso-639-1/webpack.config.js
./node_modules/js-yaml/index.js
./node_modules/js-yaml/LICENSE
./node_modules/js-yaml/package.json
./node_modules/js-yaml/README.md
./node_modules/junk/index.d.ts
./node_modules/junk/index.js
./node_modules/junk/license
./node_modules/junk/package.json
./node_modules/junk/readme.md
./node_modules/kind-of/CHANGELOG.md
./node_modules/kind-of/index.js
./node_modules/kind-of/LICENSE
./node_modules/kind-of/package.json
./node_modules/kind-of/README.md
./node_modules/kleur/colors.d.ts
./node_modules/kleur/colors.js
./node_modules/kleur/colors.mjs
./node_modules/kleur/index.d.ts
./node_modules/kleur/index.js
./node_modules/kleur/index.mjs
./node_modules/kleur/license
./node_modules/kleur/package.json
./node_modules/kleur/readme.md
./node_modules/linkify-it/index.mjs
./node_modules/linkify-it/LICENSE
./node_modules/linkify-it/package.json
./node_modules/linkify-it/README.md
./node_modules/liquidjs/LICENSE
./node_modules/liquidjs/package.json
./node_modules/liquidjs/README.md
./node_modules/list-to-array/index.js
./node_modules/list-to-array/LICENSE
./node_modules/list-to-array/.npmignore
./node_modules/list-to-array/package.json
./node_modules/list-to-array/README.md
./node_modules/list-to-array/tests.js
./node_modules/luxon/LICENSE.md
./node_modules/luxon/package.json
./node_modules/luxon/README.md
./node_modules/markdown-it/index.mjs
./node_modules/markdown-it/LICENSE
./node_modules/markdown-it/package.json
./node_modules/markdown-it/README.md
./node_modules/mdurl/index.mjs
./node_modules/mdurl/LICENSE
./node_modules/mdurl/package.json
./node_modules/mdurl/README.md
./node_modules/mime/CHANGELOG.md
./node_modules/mime/cli.js
./node_modules/mime-db/db.json
./node_modules/mime-db/HISTORY.md
./node_modules/mime-db/index.js
./node_modules/mime-db/LICENSE
./node_modules/mime-db/package.json
./node_modules/mime-db/README.md
./node_modules/mime/index.js
./node_modules/mime/LICENSE
./node_modules/mime/lite.js
./node_modules/mime/Mime.js
./node_modules/mime/package.json
./node_modules/mime/README.md
./node_modules/mime-types/HISTORY.md
./node_modules/mime-types/index.js
./node_modules/mime-types/LICENSE
./node_modules/mime-types/mimeScore.js
./node_modules/mime-types/package.json
./node_modules/mime-types/README.md
./node_modules/minimatch/LICENSE
./node_modules/minimatch/minimatch.js
./node_modules/minimatch/package.json
./node_modules/minimatch/README.md
./node_modules/minimist/CHANGELOG.md
./node_modules/minimist/.eslintrc
./node_modules/minimist/index.js
./node_modules/minimist/LICENSE
./node_modules/minimist/.nycrc
./node_modules/minimist/package.json
./node_modules/minimist/README.md
./node_modules/minipass/LICENSE.md
./node_modules/minipass/package.json
./node_modules/minipass/README.md
./node_modules/moo/LICENSE
./node_modules/moo/moo.js
./node_modules/moo/package.json
./node_modules/moo/README.md
./node_modules/morphdom/CHANGELOG.md
./node_modules/morphdom/factory.js
./node_modules/morphdom/index.d.ts
./node_modules/morphdom/LICENSE
./node_modules/morphdom/package.json
./node_modules/morphdom/README.md
./node_modules/ms/index.js
./node_modules/ms/license.md
./node_modules/ms/package.json
./node_modules/ms/readme.md
./node_modules/node-retrieve-globals/LICENSE
./node_modules/node-retrieve-globals/package.json
./node_modules/node-retrieve-globals/README.md
./node_modules/node-retrieve-globals/retrieveGlobals.js
./node_modules/normalize-path/index.js
./node_modules/normalize-path/LICENSE
./node_modules/normalize-path/package.json
./node_modules/normalize-path/README.md
./node_modules/nunjucks/index.js
./node_modules/nunjucks/LICENSE
./node_modules/nunjucks/package.json
./node_modules/nunjucks/README.md
./node_modules/on-finished/HISTORY.md
./node_modules/on-finished/index.js
./node_modules/on-finished/LICENSE
./node_modules/on-finished/package.json
./node_modules/on-finished/README.md
./node_modules/.package-lock.json
./node_modules/parse-srcset/.jscs.json
./node_modules/parse-srcset/.jshintrc
./node_modules/parse-srcset/LICENSE
./node_modules/parse-srcset/.npmignore
./node_modules/parse-srcset/package.json
./node_modules/parse-srcset/README.md
./node_modules/parseurl/HISTORY.md
./node_modules/parseurl/index.js
./node_modules/parseurl/LICENSE
./node_modules/parseurl/package.json
./node_modules/parseurl/README.md
./node_modules/picomatch/index.js
./node_modules/picomatch/LICENSE
./node_modules/picomatch/package.json
./node_modules/picomatch/posix.js
./node_modules/picomatch/README.md
./node_modules/please-upgrade-node/.eslintrc.js
./node_modules/please-upgrade-node/index.d.ts
./node_modules/please-upgrade-node/index.js
./node_modules/please-upgrade-node/LICENSE
./node_modules/please-upgrade-node/package.json
./node_modules/please-upgrade-node/README.md
./node_modules/posthtml/license
./node_modules/posthtml-match-helper/LICENSE
./node_modules/posthtml-match-helper/package.json
./node_modules/posthtml-match-helper/README.md
./node_modules/posthtml/package.json
./node_modules/posthtml-parser/license
./node_modules/posthtml-parser/package.json
./node_modules/posthtml-parser/readme.md
./node_modules/posthtml/readme.md
./node_modules/posthtml-render/changelog.md
./node_modules/posthtml-render/license
./node_modules/posthtml-render/package.json
./node_modules/posthtml-render/readme.md
./node_modules/prr/.jshintrc
./node_modules/prr/LICENSE.md
./node_modules/prr/.npmignore
./node_modules/prr/package.json
./node_modules/prr/prr.js
./node_modules/prr/README.md
./node_modules/prr/test.js
./node_modules/prr/.travis.yml
./node_modules/punycode.js/LICENSE-MIT.txt
./node_modules/punycode.js/package.json
./node_modules/punycode.js/punycode.es6.js
./node_modules/punycode.js/punycode.js
./node_modules/punycode.js/README.md
./node_modules/range-parser/HISTORY.md
./node_modules/range-parser/index.js
./node_modules/range-parser/LICENSE
./node_modules/range-parser/package.json
./node_modules/range-parser/README.md
./node_modules/readdirp/index.d.ts
./node_modules/readdirp/index.js
./node_modules/readdirp/LICENSE
./node_modules/readdirp/package.json
./node_modules/readdirp/README.md
./node_modules/section-matter/index.js
./node_modules/section-matter/LICENSE
./node_modules/section-matter/package.json
./node_modules/section-matter/README.md
./node_modules/semver-compare/index.js
./node_modules/semver-compare/LICENSE
./node_modules/semver-compare/package.json
./node_modules/semver-compare/readme.markdown
./node_modules/semver-compare/.travis.yml
./node_modules/semver/index.js
./node_modules/semver/LICENSE
./node_modules/semver/package.json
./node_modules/semver/preload.js
./node_modules/semver/range.bnf
./node_modules/semver/README.md
./node_modules/send/index.js
./node_modules/send/LICENSE
./node_modules/send/package.json
./node_modules/send/README.md
./node_modules/setprototypeof/index.d.ts
./node_modules/setprototypeof/index.js
./node_modules/setprototypeof/LICENSE
./node_modules/setprototypeof/package.json
./node_modules/setprototypeof/README.md
./node_modules/slash/index.d.ts
./node_modules/slash/index.js
./node_modules/slash/license
./node_modules/slash/package.json
./node_modules/slash/readme.md
./node_modules/slugify/LICENSE
./node_modules/slugify/package.json
./node_modules/slugify/README.md
./node_modules/slugify/slugify.d.ts
./node_modules/slugify/slugify.js
./node_modules/sprintf-js/bower.json
./node_modules/sprintf-js/gruntfile.js
./node_modules/sprintf-js/LICENSE
./node_modules/sprintf-js/.npmignore
./node_modules/sprintf-js/package.json
./node_modules/sprintf-js/README.md
./node_modules/ssri/LICENSE.md
./node_modules/ssri/package.json
./node_modules/ssri/README.md
./node_modules/statuses/codes.json
./node_modules/statuses/HISTORY.md
./node_modules/statuses/index.js
./node_modules/statuses/LICENSE
./node_modules/statuses/package.json
./node_modules/statuses/README.md
./node_modules/strip-bom-string/index.js
./node_modules/strip-bom-string/LICENSE
./node_modules/strip-bom-string/package.json
./node_modules/strip-bom-string/README.md
./node_modules/tinyglobby/LICENSE
./node_modules/tinyglobby/package.json
./node_modules/tinyglobby/README.md
./node_modules/toidentifier/HISTORY.md
./node_modules/toidentifier/index.js
./node_modules/toidentifier/LICENSE
./node_modules/toidentifier/package.json
./node_modules/toidentifier/README.md
./node_modules/to-regex-range/index.js
./node_modules/to-regex-range/LICENSE
./node_modules/to-regex-range/package.json
./node_modules/to-regex-range/README.md
./node_modules/uc.micro/index.mjs
./node_modules/uc.micro/LICENSE.txt
./node_modules/uc.micro/package.json
./node_modules/uc.micro/README.md
./node_modules/unpipe/HISTORY.md
./node_modules/unpipe/index.js
./node_modules/unpipe/LICENSE
./node_modules/unpipe/package.json
./node_modules/unpipe/README.md
./node_modules/urlpattern-polyfill/index.cjs
./node_modules/urlpattern-polyfill/index.js
./node_modules/urlpattern-polyfill/LICENSE
./node_modules/urlpattern-polyfill/package.json
./node_modules/urlpattern-polyfill/README.md
./node_modules/ws/browser.js
./node_modules/ws/index.js
./node_modules/ws/LICENSE
./node_modules/ws/package.json
./node_modules/ws/README.md
./node_modules/ws/wrapper.mjs
./package.json
./package-lock.json
./_site/index.html


確認できた。

**今あるのはまだ最小構成だけ。**

```text
index.md
package.json
package-lock.json
eleventy 3.1.6
_site/index.html
.git/
.gitignore
```

つまり、さっきの入門記事にある構成は**まだ作っていない**。

ここから記事の内容に沿って構成を作る段階。
**Netlifyへ上げるのはまだ先。**

次は `index.md` の現在内容を確認する。```bash
cat index.md

```
```

	
---
中身を確認する。
	
eugene@alberone:~/netlify-eleventy-test$ cat index.md
# Eleventyテスト

NetlifyでEleventyを動かすテストです。
	
確認した。現在の `index.md` はこれだけ。

```markdown
# Eleventyテスト

NetlifyでEleventyを動かすテストです。
```

**ここから記事どおりに組み立てていく。**

まず、いきなり書き換えずに、次の段階として `base.liquid` を作る。

`base.liquid` は、**Markdownの記事をHTMLとして出力するときの共通の「外枠」**にするためのもの。

たとえば、

```text
記事A.md ─┐
記事B.md ─┼→ base.liquid → HTML
記事C.md ─┘
```

という構造にして、

```html
<html>
<head>...</head>
<body>
  <!-- ここにMarkdownの記事本文 -->
</body>
</html>
```

のような共通部分を `base.liquid` にまとめる。

だから、**単にMarkdownをHTMLに変換するだけなら必須ではない。**

今回の記事で `base.liquid` を作るのは、Eleventyの

**「Markdown → 共通テンプレート → HTML」**

という仕組みを実際に確認するため。

なので、目的が「EleventyがMarkdownを変換できるか」だけなら、今の `index.md` で既に確認できている。

**「Eleventyでサイトを組むところまで試す」なら、ここから `base.liquid` が必要になる。**

次へ進む。

`base.liquid` を作る。

```bash
mkdir -p _includes
nano _includes/base.liquid
```

中身はまずこれだけ。

```liquid
<!doctype html>
<html lang="ja">
<head>
  <meta charset="utf-8">
  <title>{{ title }}</title>
</head>
<body>
  {{ content }}
</body>
</html>
```

保存したら、次に `index.md` をこのテンプレートを使う形に変更する。

eugene@alberone:~/netlify-eleventy-test$ mkdir -p _includes
nano _includes/base.liquid
	
実際に書き終えた。
	
次。

`index.md` をテンプレート指定にする。

```bash
nano index.md
```

先頭に Front Matter を追加して、こうする。

```markdown
---
layout: base.liquid
title: Eleventyテスト
---

# Eleventyテスト

NetlifyでEleventyを動かすテストです。
```

保存したら、

```bash
npx eleventy
```

これで `_site/index.html` を再生成する。

eugene@alberone:~/netlify-eleventy-test$ cat index.md
---
layout: base.liquid
title: Eleventyテスト
---

# Eleventyテスト

NetlifyでEleventyを動かすテストです。
↓
eugene@alberone:~/netlify-eleventy-test$ cat _site/index.html
<!doctype html>
<html lang="ja">
<head>
  <meta charset="utf-8">
  <title>Eleventyテスト</title>
</head>
<body>
  <h1>Eleventyテスト</h1>
<p>NetlifyでEleventyを動かすテストです。</p>

</body>
</html>
	生成された。
	
実際に**正常に動いている。**のが確認できた。

`index.md` の Front Matter が `base.liquid` に渡されて、

```text
title: Eleventyテスト
```

が `<title>` に入り、

```text
{{ content }}
```

がMarkdownからHTMLへ変換された本文に置き換わっている。

つまり現在、

**Markdown → Front Matter → Liquidテンプレート → HTML**

まで確認できた。

次は、記事をもう1本作って、**同じ `base.liquid` が複数ページで使えること**を確認する段階へ進める。

実際に作ってみる。
	
eugene@alberone:~/netlify-eleventy-test$ nano sample.md
eugene@alberone:~/netlify-eleventy-test$ cat sample.md
---
layout: base.liquid
title: サンプル記事
---

# サンプル記事

2ページ目のテストです。
	
これで2つ目の記事が作成された。

