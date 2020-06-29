## 進める上で参考になった本、サイトなど
- [Webを支える技術](https://www.amazon.co.jp/Web%E3%82%92%E6%94%AF%E3%81%88%E3%82%8B%E6%8A%80%E8%A1%93-HTTP%E3%80%81URI%E3%80%81HTML%E3%80%81%E3%81%9D%E3%81%97%E3%81%A6REST-WEB-PRESS-plus/dp/4774142042)

## 全体の流れ
第1部 Web概論
- 第1 ~ 3章  

第2部 URL
- 第4 ~ 5章  

第3部 HTTP
- 第6 ~ 9章  

第4部 ハイパーメディアフォーマット
- 第10 ~ 14章  

第5部 Webサービスの設計
- 第15 ~ 17章

## 読書メモ
### 第1章(Webとは何か)
- Webの3つの用途
    - Webサイト
    - ユーザーインターフェースとしてのWeb
    - プログラム用APIとしてのWeb
- Webを支える基本的な技術
    - HTTP
    - URI
    - HTML
- ハイパーメディア
    - ユーザーが自分でリンクを選択して情報を取得するメディア
### 2章(Webの歴史)
- Webはインターネットを使ったハイパーメディア
- 初期のWebではHTMLが唯一のハイパーメディアフォーマット
    - HTMLやAtomはXMLをベースにした構造化文書のためのマークアップ言語
    - データを記述するデータフォーマットはJSON
- RESTとはWeb APIが提供するリソースをHTTPやURIで簡単に操作するスタイル
    - RESTはRepresentational State Transfer。「リソースの状態」(Resource State)の「表現」(Representation)
### 3章(REST -Webのアーキテクチャスタイル)
- RESTはネットワークシステムのアーキテクチャスタイル
- 実装から一つ抽象度を上げたものをアーキテクチャ、アーキテクチャから一つ抽象度を上げたものをアーキテクチャスタイル
- RESTにおける重要な概念のリソースとはURIのこと
- RESTを構成する6つのアーキテクチャスタイル
    - クライアント/サーバー
    - ステートレスサーバー
    - キャッシュ
    - 統一インターフェイス
    - 階層化システム
    - コードオンデマンド
- RESTの制約に従っていてRESTらしいことをRESTful
### 4章(URIの仕様)
- URIはUniform Resource Identifierの略。統一リソース識別子。つまりURIはリソースを統一的に識別するID
- URIを構成するパーツは(http://blog.example.jp/entries/1)
    - URIスキーム: http
    - ホスト名: blog.example.jp
    - パス: /entries/1
- 例2:http://yohei:pass@blog.example.jp:8000/search?q=test&debug=true#n10
    - URIスキーム: http
    - ユーザー情報: yohei:pass
    - ホスト名: blog.example.jp
    - ポート番号: 8000
    - パス: /search
    - クエリパラメータ: q=test&debug=true
    - URIフラグメント: #n10
- Web APIを実装する際はなるべく絶対URIで実装
- URIにASCII文字以外を入れる場合、文字エンコーディングとしてUTF-8がいい
### 5章(URIの設計)
- プログラミング言語に依存した拡張子やパスを含めない
    - その言語を変更したとたんにそのURIは使えなくなるため
- URIはリソースの名前、名詞が好ましい。
- URIを変更したとき、リダイレクト(古いURIから新しいURIへ転送)する仕組みがHTTPにある
### 6章(HTTPの基本)
- HTTPはTCP/IPをベースとしたプロトコル
    - TCPはTransmission Control Protocolの略。
    - IPはInternet Protocolの略。
- インターネット層のプロトコル、IPはデータを送り出すことだけを保証。最終的な送り先に届くかは保証しない。
- トランスポート層のプロトコル、TCPはIPが保証しないデータの転送を保証する。
- ステートレスとは。クライアントがサーバに対して毎回すべての情報を送信すること。
### 7章(HTTPメソッド)
- メソッドは8つしかない
    - GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE, CONNECT
- GET(リソースの取得)
- POST(リソースの作成、追加)
- PUT(リソースの更新、作成)
- POSTとPUTの違い
    - リソースを作成する決定権が違う、POSTではURIの決定権はサーバ側、PUTではクライアント側。
### 8章(ステータスコード)
- 3桁の数字で、先頭の数字によって5つに分類される
    - 1xx: 処理中
    - 2xx: 成功
    - 3xx: リダイレクト
    - 4xx: クライアントエラー
    - 5xx: サーバエラー
- よく使われるもの
    - 200 OK: リクエスト成功
    - 201 Created: リソースの作成成功
    - 301 Moved Permanently: リソースの恒久的な移動
    - 303 See Other: 別URIの参照
    - 400 Bad Request: リクエストの間違い
    - 401 Unauthorized: アクセス権不正
    - 404 Not Found: リソースの不在
    - 500 Internal Server Error: サーバ内部エラー
    - 503 Service Unavailable: サービス停止
### 9章(HTTPヘッダ)
- MIMEメディアタイプ
    - メッセージでやりとりするリソースの表現の種類を指定する
- HTTPSとはHTTPとSSL/TLSを組み合わせた通信
    - 暗号化
    - 認証
    - 改ざん検知
- キャッシュ用ヘッダ
    - Prama: キャッシュを抑制する
    - Expires: キャッシュの有効期限を示す
    - Cache-Control: 詳細なキャッシュ方法を指定する
### 10章(HTML)
- HTMLの構文について
### 11章(microformats)
- HTMLの中で更に意味のあるデータを表現するための技術
- HTMLそのもにメタデータを埋め込む技術
- 大きく２つに分類
    - elemental(単純) microformats
        - リンク関係(<a>要素や<link>要素のrel属性)を使ってメタデータを表現する
    - compound(複合) microformats
        - 主にclass属性を使って階層構造のあるメタデータを表現する
### 12章(Atom)
- ブログなどの更新情報を配信するためのフィードとして知られる
    - 汎用XMLフォーマット
    - もう一つRSSというフィードがある
    - 検索エンジンや写真管理など様々なWebAPIとして利用できる
- 最小のリソースの単位はメンバリソース
- メンバリソースが複数集まったリソースをコレクションリソース
### 13章(Atom Publishing Protocol)
- Atomが出来ること
    - CRUDなど

