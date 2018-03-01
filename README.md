# アプリのセキュリティについて

スマートフォンアプリのセキュリティは、大きく2つの場面に分けられます。

一つはスマートフォン本体側、もう一つはアプリ側のセキュリティです。

次の図は、ログインが必要なアプリを使用する手順を例示したものです。

```puml
@startuml

title ユーザー新規登録手順

actor ユーザー as user
participant App as app
database サーバー as server

user -> app : 新規登録ボタン
user <-- app : 新規登録画面
user -> app : ユーザー情報入力
app -> server : 登録処理
user <<-- server : 仮登録情報メール送信
user -> app : 仮登録情報入力
app -> server : 仮登録情報
server -> app : 認証確認
app -> user : ログイン状態で操作受付

@enduml
```

## 新規ユーザー登録

ユーザーの登録をアプリから行う場合は、一連の登録手続きを行う必要があります。

```puml
@startuml

title アプリの起動とログイン

actor ユーザー as user

participant Phone as phone

participant アプリ as app

database サーバー as server

== スマートフォン ==

user -> phone : ロック解除指示
user <<-- phone : 操作可能な画面
user -> phone : アプリ起動指示

== アプリ ==

create app
phone -> app : アプリ起動
app -> user : ログイン画面
loop 認証成功まで
    user -> app : ログイン操作
    app -> server : 認証要求
    alt 認証失敗
        app <-- server : 認証失敗情報
        app -> user : ログイン失敗画面
    else
        app <-- server : 認証成功情報
    end
end
app -> user : ログイン済画面

@enduml
```

この手続きでは、メールを別途送付しています。これには、２つ理由があります。

1. 登録メールアドレスは、アプリ利用者が使用しており、到達することを確認する
1. メールによるパスワードリセット可能にする

アプリ利用者に届くメールが登録されていないと、パスワードを忘れた場合の復帰が不可能になってしまいます。

※一般にパスワードそのものは、どこにも保存されず、復元できません。

### サーバーとローカル

ローカル（手元側）にだけパスワードなど認証情報を保存している場合は、外部との接触の機会が減ります。

そのため、情報の外部流出機会も減ります。

ただし、パスワード忘れた場合に、パスワードリセットを行って復帰することは困難です。

また、バックアップをサーバーに作成することは、端末の故障などに対する有効な手段です。