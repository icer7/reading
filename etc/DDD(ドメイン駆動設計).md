# ドメイン駆動設計（DDD）とは
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
- 知識を表現するパターン（ドメインの知識をオブジェクトとして表現するドメインオブジェクト）
    - 値オブジェクト
        - ドメイン固有の概念（金銭や製造番号など）を値として表現するパターン
    - エンティティ
        - 値オブジェクトと対を為すような性質
    - ドメインサービス
        - 値オブジェクトやエンティティではうまく表現できない知識を取り扱うためのパターン
- アプリケーションを実現するためのパターン（利用者の必要を満たすアプリケーションを構築するための手法）
    - リポジトリ
        - データの保存や復元といった永続化や再構築を担当するオブジェクト
        - データの永続化というとリレーショナルデータベースなどの具体的なデータストアが思い浮かびますが、リポジトリはそれらを抽象化するもの
    - アプリケーションサービス
        - 値オブジェクト・エンティティ・ドメインサービス・リポジトリの4つの要素を協調させ、アプリケーションとして成り立たせる場
    - ファクトリ
        - オブジェクトを作る知識に特化したオブジェクト
        - オブジェクトの生成は至るところで発生するので、ファクトリを利用して生成に関する知識を一箇所にまとめあげることで処理の趣旨を明確にできる
- 知識を表現する、より発展的なパターン
    - 集約
        - 整合性を保つ境界
        - 値オブジェクトやエンティティといったドメインオブジェクトを束ねて複雑なドメインの概念を表現
    - 仕様
        - オブジェクトの評価

### 値オブジェクト
- 値オブジェクトの考え方は「システム固有の値を作ろう」
- ドメインにはさまざまなルールがあり、値オブジェクトを定義するとそういったルールは値オブジェクトに記述され、コードがドキュメントとして機能する
- 値オブジェクトの性質
    - 不変である
    - 交換が可能である
    - 等価性によって比較される

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

- FullNameクラスはコンストラクタで第1引数に名、第2引数に性を指定するようになっている
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
    - 例: 値オブジェクト内でガード節（処理の対象外とする条件を関数の初期に確認すること）を入れることで制限すること

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
- ドメイン駆動設計におけるエンティティはドメインモデルを実装したドメインオブジェクト
- 値オブジェクトとエンティティの違いは同一性によって識別されるか
- 同一性ではなく属性によって識別されるオブジェクト -> 値オブジェクト
    - 姓と名の属性からなる氏名は、そのいずれかの属性が変更されたらまったく異なるものになる
- エンティティの性質
    - 可変である
    - 同じ属性であっても区別される
    - 同一性により区別される
- 可変である
    - 値オブジェクトは不変な性質が存在するため交換（代入）によって変更を表現
    - エンティティはそのふるまいを通じて属性を変更する
    - 可変なオブジェクトは厄介なため、可能な限り不変にしておくことは良い習慣
- 同じ属性であっても区別される
    - 値オブジェクトでは同じ属性であれば同じものとして扱われる

            姓     名
            sato  sato
                =
            ryo   ryo

    - エンティティは同じ属性であっても区別される
    - 識別子（Identity）が利用される
    - まったく同姓同名のユーザーがいたとき、それが同一のユーザーか別のユーザーかどうかは識別子により区別される

            姓      名
            sato   sato
                !=
            ryo    ryo
- ライフサイクルをもち、連続性のある概念はエンティティとする
- システムによっては値オブジェクトにすべきときもあればエンティティにすべきときもある
    - 例）タイヤ
    - 値オブジェクト
        - 車にとってタイヤはパーツ。特性に細かい違いはあるものの交換可能であるので値オブジェクトとして表現する
    - エンティティ
        - タイヤを製造する工場では、タイヤにはロットがあり、それがいつ作られたかものか個体を識別することがあるのでエンティティとして表現する

### ドメインサービス
- ソフトウェア開発の文脈で語られる「サービス」とはクライアントのために何かを行うオブジェクトのこと
- ドメインのためのサービス -> ドメインサービス
- アプリケーションのためのサービス -> アプリケーションサービス
- 値オブジェクトやエンティティなどのドメインオブジェクトに記述すると不自然になるふるまいが存在しており、ドメインサービスはそういった不自然さを解決する
- 不自然なふるまいを確認する↓

        class User
        {
            private readonly UserId id;
            privete UserName name;

            public User(UserId id, UserName name)
            {
                if (id == null) throw new ArgumentNullException(nameof(id));
                if (name == null) throw new ArgumentNullException(nameof(name));

                this.id = id;
                this.name = name;
            }

            // 追加した重複確認のふるまい
            public bool Exists(User user)
            {
                // 重複を確認するコード
                (...略...)
            }
        }
- クラス内で定義された関数を使って重複確認を行う

        var userId = new UserId("id");
        var userName = new UserName("nrs");
        var user = ner User(userId, userName);

        // 生成したオブジェクト自身に問い合わせをすることになる
        var duplicateCheckResult = user.Exists(user);
        Console.WriteLine(duplicateCheckResult); // true? false?どっちかパット見で分からない
    - 重複を確認するふるまいはUserクラスに定義されているので、重複の有無を自身に対して問い合わせることになる
    - 生成したオブジェクト自身に問い合わせを行うことで混乱を招く
- 異なるアプローチとして、重複の確認のため専用のインスタンスを用意する

        var checkId = new UserId("check");
        var checkName = new UserName("checker");
        var checkObject = new User(checkId, checkName);

        var userId = new UserId("id");
        var userName = new UserName("nrs");
        var user = ner User(userId, userName);

        // 重複確認専用インスタンスに問い合わせ
        var duplicateCheckResult = checkObject.Exists(user);
        Console.WriteLine(duplicateCheckResult);
    - さきほどの例と違い自身に問い合わせをせずに済む点では、不自然さがいくばくか軽減されている
    - しかし、重複確認のために作成されたcheckObjectはユーザーを表すオブジェクトでありながらユーザーではない不自然なオブジェクト
- こういった不自然さを解決するのがドメインサービス

        // ユーザーのドメインサービスの定義
        class UserService
        {
            public bool Exists(User user)
            {
                // 重複を確認する処理
                (...略...)
            }
        }
    - ドメインサービスは値オブジェクトやエンティティと異なり、自身のふるまいを変更するようなインスタンス特有の状態を持たないオブジェクト

            // ドメインサービスを利用して重複確認を行う
            var userService = new UserService();

            var userId = new UserId("id");
            var userName = new UserName("sato");
            var user = new User(userId, userName);

            // ドメインサービスに問い合わせ
            var duplicateCheckResult = userService.Exists(user);
            Console.WriteLine(duplicateCheckResult);
        - ドメインサービスを用意することで、自身に問い合わせたり、チェック専用のインスタンスを用意する必要がなくなった
- 値オブジェクトやエンティティに定義すると不自然に感じる操作はドメインサービスに定義すると不自然さは解消される
- やろうと思えば、ふるまいのすべてをドメインサービスに移設することはできるが、そうするとドメインオブジェクトがデータを保持するただのオブジェクトになってしまう
- そもそもドメインオブジェクトはコードをドキュメントとして機能させる目的もあるので、目的からそれてしまうため
    - 可能な限りドメインサービスは利用しない
    - ふるまいを値オブジェクトやエンティティに定義するか、それともドメインサービスに定義するか迷った場合は可能な限り、値オブジェクトやエンティティに定義する

### リポジトリ
- ソフトウェア開発の文脈のリポジトリはデータの保管庫を表す
- データを永続化（保存）し、再構築（復元）するといった処理を抽象的に扱うためのオブジェクト
- オブジェクトのインスタンスを保存したいときは直接的にデータストアに書き込みを行う処理を行うのではなく、リポジトリにインスタンスの永続化を依頼する
- また永続化したデータからインスタンスを再構築したいときにもリポジトリにデータの再構築を依頼する
- データの永続化と再構築を直接行うのではなく、リポジトリを経由して行うことでソフトウェアに柔軟性をもたらす
- ユーザー作成処理を例にしてリポジトリを利用した実装

        class Program
        {
            private IUserRepository userRepository;

            public Program(IUserRepository userRepository)
            {
                this.userRepository = userRepository;
            }

            public void CreateUser(string userName)
            {
                var user = new User(
                    new UserName(userName)
                );

                var userService = new UserService(userRepository);
                if (userService.Exists(user))
                {
                    throw new Exception($"{userName}は既に存在しています");
                }

                userRepository.Save(user);
            }
        }
    - Userオブジェクトの永続化はリポジトリであるIUserRepositoryオブジェクトに対して依頼されるようになる
    - データストアに対する命令を抽象的に行うことで、ややこしい処理から開放される
- ドメインサービスに適用すると

        class UserService
        {
            private IUserRepository userRepository

            public UserService(IUserRepository userRepository)
            {
                this.userRepository = userRepository;
            }

            public bool Exists(User user)
            {
                var found = userRepository.Find(user.Name);

                return found != null;
            }
        }
- リポジトリを定義してみる

        public interface IUserRepository
        {
            void Save(User user);
            User Find(userName name);
            bool Exists(User user);
        }
    - こちらの定義を用いるとドメインサービスが↓のように書きかえることができる

            class UserService
            {
                private IUserRepository userRepository

                (...略...)

                public bool Exists(User user)
                {
                    // ユーザー名により重複確認を行うという知識は失われている
                    return userRepository.Exists(user);
                }
            }

### アプリケーションサービス
- ドメインサービスとアプリケーションサービスは対象となる領域が異なるだけ、、まずはサービスがあり、それの向いている方向がドメインなのかアプリケーションなのかの違い
- ユースケースを表現するオブジェクト
- たとえばユーザー登録の必要なシステムにおいて、ユーザー機能を実現するには「ユーザーを登録する」ユースケースや「ユーザー情報を変更する」ユースケースが必要
- CRUD処理もユースケース

        // ユーザー登録処理をアプリケーションサービスのふるまいとして実装
        public class UserApplicationService
        {
            private readonly IUserRepository userRepository;
            private readonly UserService userService;

            public UserApplicationService(IUserRepository userRepository, UserService userService)
            {
                this.userRepository = userRepository;
                this.userService = userService;
            }

            public void Register(string name)
            {
                vat user = new User(
                    new UserName(name)
                );
                if (userService.Exists(user))
                {
                    throw new CanNotRegisterUserException(user, "ユーザーは既に存在しています");
                }

                userRepository.Save(user);
            }
        }
    - Registerメソッドでは最初にUserオブジェクトを生成
    - 重複チェックをドメインサービスであるUserServiceに依頼
    - その結果としてユーザーが重複していないことを確認できた場合に、IUserRepositoryにインスタンスの永続化を依頼することでユーザーの登録を行っている
- アプリケーションサービスはあくまでもドメインオブジェクトのタスク調整に徹する
- アプリケーションサービスにはドメインのルールは記述しない
- ドメインのルールはドメインオブジェクトに記述し、アプリケーションサービスはそのドメインオブジェクトを利用するだけ
- 自身のふるまいを変化させる目的で状態を保持しない

### ファクトリ
- オブジェクト指向プログラミングにおけるクラスは道具みたいなもの
- 複雑なオブジェクトの生成処理をオブジェクトとして定義するオブジェクト
- 道具を作る工場になぞらえて「ファクトリ」という
- オブジェクトの生成に関わる知識がまとめられたオブジェクト

### アプリケーションを組み立てるフロー
1. どういった機能が必要か、要求にしたがって考える
2. 必要な機能が定まったら、今度はその機能を成り立たせるために必要となるユースケースを洗い出す
3. ユースケースが定まったら、必要なる概念とそこに存在するルールからアプリケーションが必要とする知識を選び出し、ドメインオブジェクトを準備する
4. ドメインオブジェクトを用いてユースケースを実現するアプリケーションサービスを実装する

- サークル機能を例とする
    1. サークル機能
    2. 「サークルの作成」と「サークルへの参加」これら2つのユースケース
    3. サークルはライフサイクルがあるオブジェクトなのでエンティティであり、ライフサイクルを表現するためには識別子が必要なので、識別子は値オブジェクトとして実装する

            // サークルの識別子となる値オブジェクト
            public class CircleId
            {
                public CircleId(string value)
                {
                    if (value == null) throw new ArgumentNullException(nameof(value));

                    Value = value;
                }

                public string Value { get; }
            }

        またサークルには名前を付けることができるので、サークルの名前を表す値オブジェクトも用意する

            public class CircleName : IEquatable<CircleName>
            {
                public CircleName(string value)
                {
                    if (value == null) throw new ArgumentNullException(nameof(value));
                    if (value.length < 3) throw new ArgumentException("サークル名は3文字以上です", nameof(value));
                    if (value.length > 20) throw new ArgumentException("サークル名は20文字以下です", nameof(value));

                    Value = value;
                }

                public string Value { get; }

                public bool Equals(CircleName other)
                {
                    if (ReferenceEquals(null, other)) return false;
                    if (ReferenceEquals(this, other)) return true;

                    return string.Equals(Value, other.value);
                }

                public override bool Equals(object obj)
                {
                    if (ReferenceEquals(null, other)) return false;
                    if (ReferenceEquals(this, other)) return true;
                    if (obj.GetType() != this.GetType()) return false;

                    return Equals((CircleName) obj);
                }

                public override int GetHashCode()
                {
                    return (Value != null ? Value.GetHashCode() : 0);
                }
            }

        サークルを表すエンティティ

            public class Circle
            {
                public Circle(CircleId id, CircleName name, User owner, List<User> members)
                {
                    if (id == null) throw new ArgumentNullException(nameof(id));
                    if (name == null) throw new ArgumentNullException(nameof(name));
                    if (owner == null) throw new ArgumentNullException(nameof(owner));
                    if (members == null) throw new ArgumentNullException(nameof(members));

                    Id = id;
                    Name = name;
                    Owner = owner;
                    Members = members;
                }

                public CircleId Id { get; }
                public CircleName Name { get; private set; }
                public User Owner { get; private set; }
                public List<User> Members { get; private set; }
            }
        サークルにはサークルのオーナーになるユーザーを表すOwnerと  
        所属しているユーザーの一覧を表すMembersが定義されている

        次にサークルの永続化を行うためにサークルのリポジトリを作成

            public interface ICircleRepository
            {
                void Save(Circle circle);
                Circle Find(CircleId id);
                Circle Find(CircleName name);
            }

        サークルを生成するファクトリも準備する

            public interface ICircleFactory
            {
                Circle Create(CircleName name, User owner);
            }

        またサークルはユーザー名が重複していないか確認する必要がある  
        重複に関するふるまいをCircleクラスで定義すると不自然なのでドメインサービスで定義する

            public class CircleService
            {
                private readonly ICircleRepository circleRepository;

                public CircleService(ICircleRepository circleRepository)
                {
                    this.circleRepository = circleRepository;
                }

                public bool Exists(Circle circle)
                {
                    var duplicated = circleRepository.Find(circle.Name);
                    return duplicated != null;
                }
            }
        ここまでで値オブジェクトからドメインサービスまでひととおりのオブジェクトの用意が終わり、これからユースケースを実現していく

        最初はサークルを作成する処理を実装する

            // サークルの作成処理のコマンドオブジェクト
            public class CircleCreateCommand
            {
                public CircleCreateCommand(string userId, string name)
                {
                    UserId = userId;
                    Name = name;
                }

                public string UserId { get; }
                public string Name { get; }
            }
        クライアントではこのコマンドオブジェクトを使って  
        サークルを作成するユーザー（サークルのオーナー）のIDと  
        作成するサークルの名前を指定する

        実際に処理を行うサークル作成処理はアプリケーションサービスで実装する

            public class CircleApplicationService
            {
                private readonly ICircleFactory circleFactory;
                private readonly ICircleRepository circleRepository;
                private readonly CircleService circleService;
                private readonly IUserRepository userRepository;

                public CircleApplicationService(
                    ICircleFactory circleFactory;
                    ICircleRepository circleRepository;
                    CircleService circleService;
                    IUserRepository userRepository;
                )
                {
                    this.ICircleFactory = circleFactory;
                    this.ICircleRepository = circleRepository;
                    this.CircleService = circleService;
                    this.IUserRepository = userRepository;
                }

                public void Create(CircleCreateCommand command)
                {
                    using (var transaction = new TransactionScope())
                    {
                        var ownerId = new UserId(command.UserId);
                        vat owner = userRepository.Find(ownerId);
                        if (owner == null)
                        {
                            throw new UserNotFoundException(ownerId, "サークルのオーナーとなるユーザーが見つかりませんでした");
                        }

                        var name = new CircleName(command.Name);
                        var circle = circleFactory.Create(name, owner);
                        if (circleService.Exists(circle))
                        {
                            throw new CanNotRegisterCircleException(circle, "サークルはすでに存在しています");
                        }

                        circleRepository.Save(circle);

                        transaction.Complete();
                    }
                }
            }
    - 処理の流れ
        1. 最初にサークルを作成するため、サークルのオーナーとなるユーザーを検索
        2. ユーザーの存在を確認できたらサークルを生成し、重複確認を行う
        3. 重複しないことの確認を取れたらリポジトリに永続化を依頼して処理完了

### 依存について
- テーマページを例にすると
    - MediaThemeClient.phpはMediaTheme.phpを継承している
    - つまり、MediaThemeClient.phpはMediaTheme.phpに依存している
    - 依存は依存する側のモジュールから依存される側のモジュールへと矢印を伸ばして表現する
        - MediaThemeClient.php -> MediaTheme.php

### その他のキーワード
- コンストラクタ
    - オブジェクト指向のプログラミング言語で新たなオブジェクトを生成する際に呼び出されて内容の初期化などを行なう関数あるいはメソッドのこと
    - クラスのインスタンス生成時に実行されるメソッド

            class Sample():

            def __init__(self, item_domain_service):
                '''
                コンストラクタ
                '''

- インスタンス
    - クラスから作られたオブジェクトのことを、オブジェクト指向の用語でインスタンス

- ユビキタス
    - プロジェクトにおける共通言語のことをユビキタス言語
    - 「いつでも、どこでも存在する」という意味
