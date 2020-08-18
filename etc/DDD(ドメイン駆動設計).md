# ドメイン駆動設計(DDD)とは
https://codezine.jp/article/detail/11968
- ドメインの知識に焦点をあてた設計手法
- ドメイン駆動設計はいわば当たり前を当たり前に実践するための開発手法
- DDDでよく使われるアーキテクチャ
    - レイヤードアーキテクチャ
    - ヘキサゴナルアーキテクチャ
    - オニオンアーキテクチャ
    - クリーンアーキテクチャ
- 「ドメインとは何か」
    - ドメインとは「領域」という意味。
    - ソフトウェア開発におけるドメインは、「プログラムを適用する対象となる領域」のこと。重要なのはドメインが何かではなく、ドメインに含まれるものが何か。
- 例:
    - 会計システム
        - 会計の世界には金銭や帳票といった概念あり、これらが会計システムのドメインになる。
    - 物流システム
        - 貨物や倉庫、輸送手段などの概念があり、これらが物流システムのドメインになる。
- 「ドメインモデルとは何か」
    - そもそものモデルとは現実の事象あるいは概念を抽象化した概念
    - 概念を抽象化する作業がモデリング
        - その結果がモデル
    - ドメイン駆動設計では、ドメインの概念をモデリングして得られたモデルをドメインモデル

## 解説パターン
- 知識を表現するパターン(ドメインの知識をオブジェクトとして表現するドメインオブジェクト)
    - 値オブジェクト
        - ドメイン固有の概念（金銭や製造番号など）を値として表現するパターン
    - エンティティ
        - 値オブジェクトと対を為すような性質
    - ドメインサービス
        - 値オブジェクトやエンティティではうまく表現できない知識を取り扱うためのパターン
- アプリケーションを実現するためのパターン(利用者の必要を満たすアプリケーションを構築するための手法)
    - リポジトリ
        - データの保存や復元といった永続化や再構築を担当するオブジェクト
        - データの永続化というとリレーショナルデータベースなどの具体的なデータストアが思い浮かびますが、リポジトリはそれらを抽象化するもの
    - アプリケーションサービス
        - 値オブジェクト・エンティティ・ドメインサービス・リポジトリの4つの要素を協調させ、アプリケーションとして成り立たせる場
    - ファクトリ
        - オブジェクトを作る知識に特化したオブジェクト
        - オブジェクトの生成は至るところで発生するので、ファクトリを利用して生成に関する知識を一箇所にまとめあげることで処理の趣旨を明確に出来る
- 知識を表現する、より発展的なパターン
    - 集約
        - 整合性を保つ境界
        - 値オブジェクトやエンティティといったドメインオブジェクトを束ねて複雑なドメインの概念を表現
    - 仕様
        - オブジェクトの評価

### 値オブジェクト
- 値オブジェクトの考え方は「システム固有の値を作ろう」
- ドメインにはさまざまなルールがあり、値オブジェクトを定義するとそういったルールは値オブジェクトに記述され、コードがドキュメントとして機能する

    class FullName
    {
        public FullName(string firstName, string lastName)
        {
            FirstName = firstName;
            LastName = lastName;
        }

        public string FirstName { get; }
        public string LastName { get; }
    }

    var fullName = new FullName("ryo", "sato");
    Console.WriteLine(fullName.LastName); // satoが表示される

    var fullName = new FullName("john", "smith");
    Console.WriteLine(fullName.LastName); // smithが表示される

- FullNameクラスはコンストラクタで第1引数に名、第２引数に性を指定するようになっている
- FuLLNameは名前の通り氏名を表現したオブジェクトで、値の表現である
- システムの必要な処理にしたがって、そのシステムならではの値の表現が値オブジェクト
- オブジェクトであり、値でもある、ゆえに値オブジェクト
- 値オブジェクトはデータを保持するコンテナではなく、ふるまいを持つことができるオブジェクト
- お金を例に↓お金の加算処理を説明

        class Money
        {
            private readonly decimal amount;
            privete readonly string currency;

            (...略...)

            public Money Add(Money arg)
            {
                if (arg == null) throw new ArgumentNullException(nameof(arg));
                if (currency != arg.currency) throw new ArgumentException($"通貨単位が異なります (this:{currency}, arg:{arg.currency}) ");

                return new Money(amount + arg.amount, currency);
            }
        }
- 行っていること
    - お金を加算するときは通貨単位を揃える必要があるので加算する通貨が同一かの確認
    - 値オブジェクトは不変なため、計算を行った結果は新たなインスタンスとして返却される

- 加算した結果を受け取る

        var myMoney = new Money(1000, "JPY");
        var allowance = new Money(1000, "JPY");
        var result = myMoney.Add(allowance);

- 値オブジェクトはオブジェクトに対する操作をふるまいとして一処にまとめることで、値オブジェクトは自身に関するルールを語るドメインオブジェクトらしさを帯びる
    - 例: 値オブジェクト内でガード節(処理の対象外とする条件を関数の初期に確認すること)を入れることで制限すること

            class UserName
            {
                private readonly string value;

                public UserName(string value)
                {
                    if (value == null) throw new ArgumentNullException(nameof(value));
                    if (value.length < 3) throw new ArgumentException("ユーザー名は3文字以上です", nameof(value));

                    this.value = value;
                }
            }

### エンティティ


### その他のキーワード
- コンストラクタ
    - オブジェクト指向のプログラミング言語で新たなオブジェクトを生成する際に呼び出されて内容の初期化などを行なう関数あるいはメソッドのこと
    - クラスのインスタンス生成時に実行されるメソッド

            class Sample():

            def __init__(self, item_domain_service):
                '''
                コンストラクタ
                '''
