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
### 5章()
- 
### 6章()
- 
### 7章( )
- 
### 8章()
-
### 9章()
- 
### 10章()
- 
### 11章()
- 
### 12章()
- 
