
# AI時代にエンジニアがまだ必要な理由は、コードではなく構造にある: Beauty lives in the Code.

# Abstract
AI がコードを書ける時代になっても、持続可能なソフトウェアを開発するエンジニアの仕事は消えない。  
その鍵になるのは、実装の巧さではなく、世界をどう切り分け、どう構造化するかという設計の視点だ。  
本稿では、その詳細と、ヒントとなる **OpenableBlackbox** というコンセプトを軸に整理していく。  
これは、近年しばしば語られる **理解負債** を、実装技術ではなく **世界の切り分け方** の問題として捉え直し、その対応のための考え方を提案する試みでもある。

# Contents

1. [An engineer's task is to structure the world, not to type code.](#an-engineers-task-is-to-structure-the-world-not-to-type-code)
2. [OpenableBlackbox](#openableblackbox)
3. [RecursiveOpenableBlackbox](#recursiveopenableblackbox)
4. [The beauty must be simple and natural.](#the-beauty-must-be-simple-and-natural)
5. [Almost no cheats are needed.](#almost-no-cheats-are-needed)
6. [Do not explain. The code does.](#do-not-explain-the-code-does)
7. [Do not write documentation. You already wrote the code, right?](#do-not-write-documentation-you-already-wrote-the-code-right)
8. [To Structure the World Beautifully.](#to-structure-the-world-beautifully)


# An engineer's task is to structure the world, not to type code.

エンジニアにとって最も重要なスキルは何だろうか。

言語仕様の深い理解。フレームワークの最適な活用。アルゴリズムの知識。実装速度。  
どれも重要ではあるが、本質ではない。

エンジニアの仕事はコードを書くことではない。 **世界を構造化すること** だ。  
コードとは、その構造を表現するための媒体にすぎない。

いくつか例を見てみよう。
```python
def read_message(user_mail: str):
    user = verify_user(user_mail)
    messages = load_messages(user)
    return [message.content for message in messages]
```


```python
def read_message(user_mail: str):
    # verify user
    user_record = users_db.get(query=f"user_mail eq {user_mail}")
    user = convert_user_object_from_record(user_record)
    
    # load messages form db
    messages_records = message_db.get(query=f"user_id eq {user.id}")
    messages = [
        Message.model_validate(messages_record)
        for messages_record in messages_records
    ]
    
    # return message content
    return [message.content for message in messages]
```

どちらも動く。しかし、後者は **美しく** ない。

多くのコードは「動く」という条件だけで評価されてしまう。確かに、動くコードは価値を生む。しかし、それは最低条件に過ぎない。
長く使われ価値を提供し続けるコードや、大規模なOSSのように数千人が関わるコードは、単に動くだけでなく、持続可能である。

持続可能コードには共通点がある。

それは **読むことができる** ということだ。しかも、苦労せずに。  
コードを読むとき、人は無意識にコストを計算している。 理解するのに時間がかかりそうなら、読むのをやめる。
これは怠慢ではない。合理的な判断だ。

美しいコードは、人に **読む気** を要求しない。むしろ、人が理解する努力を最小化するように設計されている。
それは単に書き方の問題ではない。もっと手前の段階、つまり **世界の分割の仕方** によって決まる。

コードとは、現実世界の問題を何らかの構造に分解し、それを再構成したものだ。

- ユーザー
- メッセージ
- 認証
- 保存
- 取得

こうした概念をどのように切り分けるか。どの単位をひとつの責務とするか。どこまでをひとつの操作とみなすか。  
この設計が、そのままコードの読みやすさになる。  
つまり、 **読みやすさを意識して実装する** わけではない。美しく設計されたコードは **結果として読みやすい** 。

ここでの「美しいコード」とは、なにも芸術的なコードを意味しているわけではない。   
美しいコードとは、 **世界の構造がそのまま表現されているコード** だ。  
そしてエンジニアの本懐とは、 **世界を美しく構造化すること** である。

つまり  
- 責務の境界をどこに置くのか。  
- どの粒度で抽象化するのか。  
- どの概念を独立させるのか。  

AIがコードを書くようになったとしても、世界の構造を設計する仕事は依然として残る。  
むしろ、より **重要** になる。これは、少しAIにコードを書かせてみた人なら納得できるだろう。

では、良い「世界の切り分け方」とは何だろうか。 どうすれば、理解しやすく、変更しやすく、持続可能な構造を構築できるのだろうか。  
そのヒントになる考え方が **OpenableBlackbox** だ。

次章では、この概念をもとに、理解コストを最小化する構造の作り方について考えていく。


# OpenableBlackbox

ここで言いたいのは、「美しいコード」とは単に見た目が整っているコードのことではない、ということだ。  
その本質のひとつは、**理解しやすいこと** にある。  
ただし、ここで言う理解しやすさとは、「すべてを説明してくれること」ではない。むしろ逆だ。  

**理解を途中でやめられること**だ。

それこそが、本当に理解しやすいコードの条件だ。
 
どんなプログラムも、突き詰めれば機械語に翻訳され、CPUでデコードされ、半導体の中のどれかの回路で計算されることになる。  
しかしそれを全て説明できる人は少ないだろうし、もちろんそんなことは必要ない。

コンパイラはコードをCPU命令に翻訳する、  
CPUは計算をする機能があって、  
コンピュータは期待通りの動きをする。

詳細を知らなくても、理解し、やりたいことを実現できるのは、 Blackbox による秘匿化のおかげだ。  
Blackbox のお陰で我々は **理解をやめる** ことができ、それは **大きく理解コストを下げる** ことにつながる。

しかし一方で、問題が起こった時や、改善の為に修正が必要になった途端に、その Blackbox を開ける必要に迫られる。
実際にCPUが壊れたからと言って分解して直す人はいないと思うが、これがもし同じプロジェクトの他人が実装したコードや機能だったらどうだろうか。

大抵そういう時、作った人は居なくなっており、Blackbox を開ければ美味しそうなスパゲティが入っている。  
そして、藁にも縋る思いで開発ドキュメントを探す。そして、無い、もしくは古い。

つまり、Blackbox化するだけでは、それは技術負債として埋められ、いつか誰かが踏んで左足を失うことになる。
だからこそ **開けられる** Blackbox (OpenableBlackbox) が必要だ。

例えば、次のような関数があったとしよう。
```python
class CreateOrderRequest(BaseModel):
    user_id: str
    item_id: str

@app.post("/createOrder")
def create_order_endpoint(item: CreateOrderRequest):
    return create_order(
        user_id=item.user_id,
        item_id=item.item_id,
    )
```
なるほど、このAPIは、userとitemに基づいて、オーダーを作るんだと。  
これだけわかるなら、君はここで **理解を止めて** 。このAPIを使う実装作業に移ればいい。

もしも、`create_order`関数が実際に何をしているのかを知りたければ。  
その OpenableBlackbox を開ければよい。 (多くのIDEでは Ctrl+click が役に立つ)
```python
def create_order(user_id: str, item_id: str) -> Order:
    user = get_active_user(user_id)
    item = get_purchasable_item(item_id)
    order = build_order(user=user, item=item)
    save_order(order)
    return order
```

この時点で、注文作成の流れは十分に把握でき、満足できる。  
君のタスクの為にはこれで十分なら、理解を止めていい。  
もし「`get_purchasable_item` は何をしているのか」が気になれば、その箱だけ開ければいい。  
もし「`build_order` のロジック」が気になれば、そこだけ見ればいい。  
すべてを一気に理解する必要はなく、理解コストは最小限だ。

一方で、これが次のようだったらどうだろうか。

```python
def create_order(user_id: str, item_id: str):
    user_record = user_table.find(user_id)
    if user_record is None:
        raise Exception("user not found")

    if user_record["status"] != "active":
        raise Exception("invalid user")

    item_record = item_table.find(item_id)
    if item_record is None:
        raise Exception("item not found")

    if item_record["deleted"] is True:
        raise Exception("invalid item")

    if item_record["stock"] <= 0:
        raise Exception("out of stock")

    now = datetime.utcnow()
    order_id = str(uuid.uuid4())

    order = {
        "id": order_id,
        "user_id": user_record["id"],
        "item_id": item_record["id"],
        "price": item_record["price"],
        "created_at": now.isoformat(),
    }

    order_table.insert(order)

    return Order.model_validate(order)
```

君はきっとため息をついて、一旦コーヒーを入れに行くだろう。  
まず量が多い。しかも、どこまでが「注文作成」という責務で、どこからがユーザー検証で、どこからが商品判定なのかも曖昧だ。
霧の中で何かをつかもうと模索する苦しみと疲労が想像できる。

これでは **Openable** になっていない。
OpenableBlackbox では、秘匿化するだけではない。  
理解をやめる Breakpoint として振る舞い、時には、さらに詳細を提供できるようにするべきだ。

**OpenableBlackbox** 重要なのは、箱の外から見たときに「何をしているか」がわかること。そうで無ければ、 **理解を止めること** はできない。
そして、一方で、中を開けたときに「どうやっているか」も無理なく追えることだ。 そうで無ければ、コードはすぐに腐ってしまう。

外からは目的が見え、中には理解可能な部品が入っている。だからエンジニアは安心して **理解を止められる** 。  
**OpenableBlackbox** というコンセプトの中心はそこだ。

# RecursiveOpenableBlackbox

さて、 OpenableBlackbox が理解コストを下げることについて、一定の理解を得られたところで、
もう少し踏み込んでみよう。   
OpenableBlackbox はサービスやプロジェクトのような大きな単位になったときにこそ本当の価値を持つ。

Twitterのようなシンプルで大規模なアプリを題材にしてみよう。もちろん実際のコードは知らないが、
サーバを起動するエントリーポイントがあり、それはAPIサーバーを立ち上げる。  
APIサーバーにendpointと対応する関数がいくつかある。  
 例えば`like`や`tweet`のような関数だろう。それから、`getTimeline`があるかもしれない。  

```python

app = FastAPI()


@dataclass
class Status(str, Enum):
    OK = "ok"
    ERROR = "error"

    
@dataclass
class LikeResponse:
    status: Status
    message: str | None
    

@app.post("/like")
def like(user_id: str, target_post_id: str) -> LikeResponse:
    response = like_usecase(user_id, target_post_id)
    return LikeResponse(response)


@dataclass
class TweetResponse:
    status: Status
    message: str | None


@app.post("/tweet")
def tweet(user_id: str, tweet_content: str) -> TweetResponse:
    response = tweet_usecase(user_id, tweet_content)
    return TweetResponse(response)


@dataclass
class TimelineResponse:
    status: Status
    message: str | None
    timeline: Timeline


@app.post("/getTimeline")
def get_timeline(user_id: str) -> TimelineResponse:
    response = get_timeline_usecase(user_id)
    return TimelineResponse(response)

```
これをみれば、ひとまずどんな入り口があるのかがわかる。
そしてもし、timelineの取得の仕組みが知りたければ、`get_timeline_usecase`関数を見に行くべきだ。
すると例えば、こうなっている。
```python
def get_timeline_usecase(user_id) -> Timeline:
    if not auth_user(user_id):
        raise PermissionError()

    timeline = get_timeline(user_id)
    return timeline
```
なるほど、認証してからタイムラインを取得して返しているんだなあと。  
ここで理解を止めてもいいし、もう一つBlackboxを開けてもいい。  
認証はどうなっているのか？具体的にtimelineを取得するのはどうしているのだろうか？もしくは返り値のTimeline型はどんな情報が詰まっているのだろう？と。  
せっかくなので`get_timeline`というBlackboxを開けてみよう。

```python
def get_timeline(user_id) -> Timeline
    tweets_from_follower = get_follower_tweets(user_id)
    retweets_by_follower = get_follower_retweets(user_id)
    tweets_from_ads = get_ads_tweets(user_id)
    tweets_from_recommend = get_recommend_tweets(user_id)

    return build_timeline(
        tweets_from_follower=tweets_from_follower,
        retweets_by_follower=retweets_by_follower,
        tweets_from_ads=tweets_from_ads,
        tweets_from_recommend=tweets_from_recommend,
    )
```
ほう、そうか。timelineはフォロワーのツイートと、リツイート、広告とレコメンドを使って作られているんだなと。  
Twitterの仕組みもそこそこに気になるところだが、それは後にして、少し振り返ろう。

OpenableBlackboxを開けるたび、いくつかのOpenableBlackboxを見つけ、それらの組み合わせでそのOpenableBlackboxは何をしているのかを理解できた。  
さらに付け加えるとそれぞれのOpenableBlackboxは充分シンプルで簡単に理解できた。
自分の知りたい部分が知れたなら、いつでも理解を止められる。  

しかし、例えば、最初にTwitterのBlackboxを開けて、こんなコードがあったとしたら。

```python

app = FastAPI()
...
...
...


@app.post("/tweet")
def tweet(user_id: str, tweet_content: str) -> TweetResponse:
    # at first, auth user.
    auth_db = pymysql.connect(f"mysql:auth.twitter.com:3306@{hoge}@{fuga}")
    records = auth_db.query_exec("........")
    if len(records) != 1:
        raise RuntimeError("invalid record")
    record = records[0]
    account_info = record.get("accountInfo")
    ...
    ...
    ...
    # build query
    query = f"INSERT .......{....}....{....}.."
    try:
        result = tweet_db.query_exec(query)
    except Exception as e:
        if isinstance(e, MySQLError):
            if not .............
        if .....:
    ...
    ...
    # set status for result
    status = "ok"
    if ...
        ...
    ...
    ...
    obj = {
        "status": status,
        "message": hoge + hoge + "".join(messages)
    }
    return obj
```

OK. 私たちにできることは右上の赤いバツボタンを推す以外になにもなさそうだ。  
だが、もしも家で息子がおなかを空かせているなら、君はこれをやり遂げなければならない。エナジードリンクを1ケース買いに行くことになるだろう。

つまり、 OpenableBlackbox の中には理解が簡単にできるほどにシンプルな構造で少ない数の OpenableBlackbox が **再帰的** に入っていなければならない。  
**再帰的** な構造のおかげで、小さなバッチ処理から大規模なサービスまでスケールできる。

**RecursiveOpenableBlackbox** というわけだ。  

エントリーポイントを開ける -> ユースケースが見える  
ユースケースを開ける -> ドメイン操作が見える  
ドメイン操作を開ける -> インフラとの接点が見える  
さらに必要ならその先も追える。

どこで止めても理解が成立し、どこまで掘っても破綻しない。  

この「開けられる箱が、中にも開けられる箱を持っている」という性質は、小さな関数の話にとどまらない。むしろ中規模以上のプロジェクトで力を発揮する。  
いわゆる "CLEAN", "Hexagonal" といったアーキテクチャはこの OpenableBlackbox を実現するための、区切り方の指標とみることもできる。


# The beauty must be simple and natural.

ここからは、いかにして自分が実装した Blackbox が Openable な状態にできるか。開けた人をびっくりさせないようにするかについてだ。  
うすうす気づいているとは思うが、シンプルであるべきだ。

何かを実装していて、コードが複雑になってきて忘れないようにコメントを書きたくなったとき。　　
別の人がこのコードを読んだ時のヒントになるようにドキュメントを書こうとしたとき。　　
複数の機能がある関数の命名をどっちにするか迷ったとき。  
それはBlackboxにパッキングするいい機会だ。 「世界の切り取り方」をもう一度考えてみよう。

そしてそのパッキングはシンプルなだけでなく、**自然** であるべきだ。
決してトイレで尻を拭くコードと手を洗うコードを別々のBlackboxに分けてはいけない。 それと、ジーンズでぬれた手を拭くコードも一緒にパッキングすべきだ。

ではどうやってシンプルで、自然に、再帰的で、最高にクールな OpenableBlackbox パッキングができるのだろう。

それこそが始めに言及した「**世界の見え方**」であり、それを美しく構築することがエンジニアは **本懐** であり、君の仕事だ。
RecursiveOpenableBlackbox というコンセプトは、君が美しく世界を切り取り構造化した物を表現する良い手段になるだろう。

しかしそれでは身も蓋もないので、いくつかのポイントを上げたい。


# Almost no cheats are needed.

**自然である** ということは、特別なことをしないということだ。つまり、ほとんどのチートや工夫は必要ない。  
例えば、次のようなコードを見たことがあるかもしれない。

```python
# user.py
from __future__ import annotations

class User:
    def __init__(self, orders: list["Order"]):
        self.orders = orders

    def total_price(self) -> float:
        from order import Order  # avoid circular import
        return sum(order.price for order in self.orders)
```

```python
# order.py
from __future__ import annotations

class Order:
    def __init__(self, user: "User", price: float):
        self.user = user
        self.price = price
```
一見、問題は解決しているように見える。 Python では forward reference もできるし、遅延 import もできる。  
しかしここで起きていることは、「型の関係が壊れている」のを実行順序で隠しているだけだ。  

このコードでは、世界の見え方として
- `user` は `orders: list[Order]` を持つ。
- `order` はオーダーしたユーザを参照する `user` を持つ。

これは正しそうではある。  
しかし、`user.orders[0].user`と参照でき、それは、`user`と一緒なのか、違うのか?  
自然な世界の見え方としては、`user`と`user.orders[0].user`は同一なもののはずで、別なオブジェクトとして参照できるのは世界を美しく構成できていない。  
つまり、問題の本質は、userとorderの階層関係が文字通り循環していて、所有関係がうやむやだということだ。

この問題に対して、forward reference, delayed importを用いて回避している。
つまりエンジニアの怠慢の為に便利に定義された **チート** が存在するわけだ。  

もっと美しく世界を切り取ると
- `orders: list[order]`がグローバルに唯一存在する。
- `user` が存在している。
- `user` はそのidを属性として持つ
- `order` はオーダーしたユーザを参照する `user.id` を持つ。

```python
# orders.py
from __future__ import annotations
from order import Order

orders: list[Order] = []  # 通常これはDBであるだろう。
```

```python
# order.py
from __future__ import annotations

class Order:
    def __init__(self, user_id: str, price: float):
        self.user_id = user_id
        self.price = price
```

```python
# user.py
from __future__ import annotations
from orders import orders

class User:
    def __init__(self, user_id: str):
        self.id = user_id

    def total_price(self) -> float:
        return sum(order.price for order in orders if order.user_id == self.id)
```

もしくは、世界の切り取り方をこうしてもいい
- `order` が存在する。
- `user` が存在する
- `user` は自身の`order`を保持している。

```python
# order.py
class Order:
    def __init__(self, price: float):
        self.price = price
```

```python
# user.py
from __future__ import annotations
from order import Order


class User:
    def __init__(self, orders: list[Order]):
        self.orders = orders
    
    def add_order(self, order: Order):
        self.orders.append(order)

    def total_price(self) -> float:
        return sum(order.price for order in self.orders)
```

どちらの見え方を採用するかはビジネス要件による。  
例えば、`order`そのものから、それをオーダーしたユーザを参照したいのなら、後者の切り取り方は採用できないだろう。

別の例を見てみよう。
```python
# types.py
from datetime import datetime
from pydantic import BaseModel, Field

class User(BaseModel):
    name: str


class AdminUser(User):
    admin: bool = True
    last_login_datetime: datetime = Field(default_factory=datetime.now)
    
    def to_dict(self) -> dict:
        data = self.model_dump()
        data["last_login_datetime"] = self.last_login_datetime.isoformat()
        return data

    
def get_user(user_name) -> dict:
    # get_user_from_db returns User object.
    user = get_user_from_db(user_name=user_name)
    
    if hasattr(user, "to_dict"):
        return user.to_dict()
    else:
        return user.model_dump()
```

せめて、 **せめて** だ。  
`isinstance(user, AdminUser)`で判定すべきだろう。userがAdminかどうか判定するのに、attributeをアテにするなんて狂っている。  
そしてこういう実装をしているときの脳内はこうだろう。
1. 要件より、AdminUserには、`last_login_datetime`が必要だ。
2. APIとして返すときに、`datetime` objectは JSON serializableでないので、別で作らなくては
3. `AdminUser.to_dict`を作ろう
4. (大抵はここで、エラーに当たって)
5. `user`として取得したuserの中に、`AdminUser`が含まれていると、`TypeError: Object of type datetime is not JSON serializable` になる。
6. 回避するために、`to_dict`があれば、そっちを優先して、無ければ、普通に `user.model_dump`を呼ぼう。

なんで、君のメガネを通じて世界を見ると、`AdminUser`だけが、`to_dict`というメソッドを持っているんだい？  
`pydantic`には、`serializer`があるから、そっちを使った方が良いんじゃないかという話をしているのではない。
もしも、君が`pydantic`に詳しくなくて、`serializer`の仕組みを知らなかったとしよう。

だとして、こうだ。
```python
# types.py
from datetime import datetime
from pydantic import BaseModel, Field

class User(BaseModel):
    name: str

    def to_dict(self) -> dict:
        return self.model_dump()

    
class AdminUser(User):
    admin: bool = True
    last_login_datetime: datetime = Field(default_factory=datetime.now)
    
    def to_dict(self) -> dict:
        data = self.model_dump()
        data["last_login_datetime"] = self.last_login_datetime.isoformat()
        return data
```

```python
def get_user(user_name) -> dict:
    # get_user_from_db returns User object.
    user = get_user_from_db(user_name=user_name)
    return user.to_dict()
```

`pydantic`の`serializer`を知らなかったとしての、不完全なコードでも、前者と後者では、全く違う。  
後者は、「`user`には統一された辞書型を取得する手段がある」という点で、美しく世界を切り取れている。この人には、単に`pydantic`の`serializer`の仕組みのリンクを送れば済む。  
一方で、前者の実装をした人には、「なぜ君にはそんな風に世界が見えているんだい？」と小一時間問い詰める必要があるだろう。

この章で伝えたいのは、個々のテクニックの是非ではない。 遅延 import が悪いわけでも、三項演算子が悪いわけでも、hasattr が常に悪というわけでもない。  
問題は、それらが必要になっている 理由 だ。ほとんどの場合、cheat は設計の歪みを知らせるシグナルとして現れる。

こうした状況では、コードは徐々に判定や特別な場合のハンドリングが必要になってくる。
コメントが増え、docstring が長くなり、注意書きが増え、例外処理が増え、条件分岐が増える。

逆に、構造が自然であれば、コードは静かになる。

特別なテクニックは要らない。  
属性の有無を探らなくてよい。  
型を見れば意味がわかる。  
関数名を見れば責務がわかる。  
呼び出し順を追えば処理の流れがわかる。  

つまり、 構造が正しければ、コードは **普通** になる。  
そして「普通であること」は、ソフトウェアにおいて非常に価値が高い。

だから、何か特別なテクニックを思いついたときほど、一度立ち止まるべきだ。
「もっと賢く書く方法はないか」ではなく、  
「そもそも、この分割は **自然** で **美しいか**？」  
と問い直す。

多くの場合、本当に必要なのはテクニックではない。世界を美しく切り取り、再構成する事だ。  
「テクニックを使うな」という意味ではない。自然に分割された構造では、ほとんどのテクニックは **必要なくなる** 。という意味だ。


# Do not explain. The code does.

cheat のほかに コメントも設計の歪みを知らせるシグナルとして現れる。
PEP 8 には、以下のような記載がある。多くのコーディングスタイルにも似たような記載はあるだろう。
> Inline comments are unnecessary and in fact distracting if they state the obvious.`  
> Don’t do this:
> ```python
> x = x + 1  # Increment x
> ```
これは確かに、最低だ。このコメントを保存するために消費された情報量一つ一つに謝るべきだ。

一方で、こんなコードはどうだろうか。
```python
def process_user_data(
    data: dict,
    include_history: bool = False,
    history_limit: int | None = None,
    include_orders: bool = False,
    include_payment_info: bool = False,
    include_subscription: bool = False,
    include_deleted: bool = False,
) -> dict:
    """
    Process user data and optionally include related resources.

    Parameters
    ----------
    data : dict
        user data dict returned from API.
        expected keys:
            id : str
            name : str
            created_at : isoformat str

    include_history : bool
        include login history list

    history_limit : int | None
        max history records to include
        only used when include_history=True

    include_orders : bool
        include order list

    include_payment_info : bool
        include payment information

    include_subscription : bool
        include subscription info

    include_deleted : bool
        include logically deleted resources
    
    Returns
    -------
    result : dict
        id: user id
        name: user name
        history: user login history
        orders: user orders
        payment: payment info
        subscription: subscription info
    """

    result = {}

    # basic fields
    result["id"] = data["id"]
    result["name"] = data["name"]

    # include history if requested
    if include_history:
        history = load_login_history(data["id"])

        if history_limit:
            history = history[:history_limit]

        result["history"] = history

    # include orders if requested
    if include_orders:
        orders = load_orders(data["id"])

        if not include_deleted:
            orders = [o for o in orders if not o["deleted"]]

        result["orders"] = orders

    # include payment info
    if include_payment_info:
        result["payment"] = load_payment_info(data["id"])

    # include subscription info
    if include_subscription:
        result["subscription"] = load_subscription(data["id"])

    return result
```

コメントは別に読めばわかる実装を説明しているわけではなく、むしろ、コメントのおかげで、コード実装からは分からない、それぞれのフェーズで何をするのかを明確にする助けになっている。  
それに冒頭のdocstringに、包括的な情報があり、それぞれの引数がどんな役割をするのかがわかる。

問題は、 読めば分かるという部分だ。 **読めば** だ。誰がこんなコードを読むだろうか、悪いが私はごめんだ。  
docstringを読んで、コードを読んで、コードの中のコメントも読んで、とたくさん読むだろうか。我々エンジニアは暇じゃない。

引数の`data: dict # user data dict returned from API.`というコメントは、引数の名前を `user_data`とすれば分かるし。  
ましてそれが、 `from API` かどうかは、関数の責務ではなく呼び出し側の責務だ。型で縛るべきだろう。  
もっと言えば、APIから返ってくるuserの情報を正しく格納する`User`オブジェクトを定義して、`user: User`とでもしておけばもっと明確だ。

このような、コードで分かりづらい物をコメントで補足するような方法は、 冒頭で言及されていた、
```python
x = x + 1  # Increment x
```
これと対比すれば、
```python
import ctypes

# Increment x
mask = 1
while mask != 0:
    carry = x & mask
    x = x ^ mask
    mask = carry << 1
```
のようなものだ、わざわざわかりづらい実装にして、コメントでわざわざ補足している。  
確かにこの実装は、コメントが必要だ。  
ただ、コメントの前に、そんなに複雑に実装する必要がない。

変数そのものにコメントを書いているとき。処理をブロックごとにコメントで区切っているとき。　というかほとんどすべてのコメントは悪だ。  
説明を必要とするコードとは裏を返せば、説明がなければ理解できないコードである。  
確かに、 **なぜ** についてコメントが必要な場面はある。ただし、充分に自然な実装であれば、ほとんどの場合で理由は自明になる。  
本当に、 **本当に** そのコメントが不可欠で構造では説明できないかどうか熟慮する必要があるだろう。そしてそれを考えることがエンジニアの本懐だ。

```python
def build_user_view(user_id: str) -> UserView:
    profile = get_user_profile(user_id)
    orders = get_user_orders(user_id)

    return UserView(
        profile=profile,
        orders=orders,
    )

def get_user_profile(user_id: str) -> UserProfile:
    return user_repository.get(user_id)


def get_user_orders(user_id: str) -> list[Order]:
    return order_repository.list_by_user(user_id)


def get_login_history(user_id: str, limit: int | None = None) -> list[LoginHistory]:
    history = history_repository.list_by_user(user_id)

    if limit:
        history = history[:limit]

    return history
```

このようにそれぞれの **自然な** 切れ目で機能を分割し、Blackbox　にしまって名前を付けるだけで、読みやすくなる。  
決して小粋なことはしていない。コードそのものが実装の説明になる。  

それぞれの入出力に対してスキーマを定義して、意味づけすると、`data`って何？というような疑問はそもそもわかない。　　
必要な処理を自然に区切り、関数名をつけて OpenableBlackbox にすれば、コードをコメントでブロックにわけ整理する動機はなくなる。

もしも、自身のコードに何か説明をしたくなった時、それは検討の足りないアーキテクチャの解読を後の誰かに擦り付ける為の君の怠惰でないと120%確信が持てるかどうか
改めて自身に問いかけるチャンスだ。  

当然だが、コードは書く人よりも読む人の方が圧倒的に多い。  
つまり君一人の怠惰で美しくないコードの説明コメントを書いたとすると。
その怠惰のせいで、少なく見積もって未来の君を含む数十人が、その美しくないコードをコメントを手掛かりに嗚咽しながら理解コストを書けることになる。  
重大な技術負債と **理解負債** になりえる。

忘れてくれるな、エンジニアの本懐は **世界を美しく構造化すること** だ。


# Do not write documentation. You already wrote the code, right?

最後に、コード以外に書かれる`documentation`についても触れたい。  
基本的にすべて悪だ。

コード以外の部分、例えば前提としている開発環境や、秘密鍵の取り扱い、あとはプロジェクトそのものの思想やUXデザインなんかは必要だと思うが。
あとは開発した年と自分の名前でも記念に書いておけばそれで十分だ。それとオシャレなロゴだな。

ただしもちろん技術の流出を嫌って顧客や世間に開示する、開かないBlackboxの説明としてのドキュメントを否定するものではない。  
また、OpenableBlackboxの最外殻となる `entry point` に対する説明は有用だとも思う。例えば、API referenceが代表的であろう。  

今回言及したいのは、**ソフトウェアの実装を理解するために書かれるドキュメント群** についてだ。
何より、何かコードについての説明をドキュメントに書くということは、保守すべきものを増やしていることに他ならない。
印刷されファイルされてしまった日には、コードが変更されたときのその紙に書いてある説明は誰かがボールペンで修正してくれるのかい？  
君が徹夜で書いて印刷したその紙ドキュメントはそのうち嘘だらけになり、誰かを混乱させる。尻でも拭いて便器に流した方がよっぽどましだ。  

当たり前だが、プログラムはコードに書いてある通りに動く。コードは仕様の一次情報だ。  
それにそれはいつでも変わる。誰かの何かを良くするために。    
わざわざ2次情報である、`documentation`を書いたとして、コードと同じことを書いて、コードの変更の度に修正をするハメになる。  
さらに悪いことに、絶対にその`documentation`と実際に動いているコードにはそのうち差分が発生する。「嘘のコメントは無いよりひどい」というがdocumentationはその最たる例だ。

これでもまだ、不必要ドキュメント執筆罪に手を染めるエンジニアの一定数には、  
「私のコードを読んでもらっても理解してもらえないかもしれない。」という不安があるのかもしれない。  

君は仲間をみくびりすぎだ。 君のチームのエンジニアをもっと信頼して、そして期待していい。  
君が一生懸命検討した美しいコードであれば、君の友人はきっと読んで理解できるし、理解できるスキルレベルを求めるべきだ。  

さらにもし、君の信頼する友人が君のコードでわからないところがあったとすれば、それはチャンスだ。  
きっと、うまくない設計や構造が隠れていて、友人が **改善のチャンス** を教えてくれているということに他ならない。  
これはコードが持続的に成長するにあたり、非常によくある、そして放置すると大きな癌に成長する小さなほころびだ。大きくなる前に手を打てる。  

繰り返すが、既に読めばわかるものにわざわざ説明を書いて、しかもそれが未来のエンジニアに嘘を吹聴するなんて犯罪に手を染めないでほしい。


# To Structure the World Beautifully.

ここまで、美しいコードとは何かについて、いくつかの角度から見てきた。  
美しいコードとは、 **世界の構造が自然に表現されているコード** だった。

責務の境界が自然で、名前が責務を語り、必要なときだけ OpenableBlackbox を開ければよく、開けた先にも再帰的に理解可能な構造が続いている。
必要な深度まで理解し、 **理解を止められる**。

そのために必要なのは **世界をどう切り分けるか** という視点だ。

その判断が、そのままコードになる。  
コードの美しさとは、実装技術の上手さというより、**構造化の美しさ** の表れだ。  

構造が自然であれば、コードは読みやすくなる。変更しやすくなる。壊れにくくなる。持続可能になる。
つまり、プロジェクトが長く生きられるようになる。

逆に、世界の切り分け方が曖昧なまま書かれたコードは、少しずつ説明を必要とし始める。
コメントが増える。docstring が伸びる。注意書きが増える。特殊な前提が増える。**cheat** が増える。
そして最終的には、書いた本人しか触れないコードになる。負債の塊になるだろう。

だから、美しいコードを書くという言い方は、少し正確ではないのかもしれない。  
本当にやるべきことは、**世界を美しく構造化すること** だ。  
その結果、 **コードは美しくなる。**

そして、この考え方は、今後ますます重要になるだろう。  
AI はすでに、かなりの量の「動くコード」を書ける。これから先、その傾向はさらに強くなるはずだ。  
そして、彼らは大量の動くだけのコードを結構な量で出してくる。  

責務の境界が曖昧なまま、歪んだ構造のまま、動く実装だけを増やしていけば、AI が書こうが人が書こうが、いずれ破綻する。  
AIにコードを書かせたことがあれば、イメージがつくだろう。AIが出力した「動く」が、構造は見えない実装コード。何か小さい変更をしたいのだが、分からず、結局AIに書かせる。  
理解負債がどんどんたまっていき、開発エンジニアなのに、サービスを手中に収められてない違和感が残り。少なくともスケールはできない。  
AI時代では、このサイクルも加速する。技術負債も理解負債もすぐに返済不能になる。

一方で、世界が美しく切り分けられ、構造が明確で、実装の意図が自然に定まっていれば、AI は非常に強力な実装者になる。

- どの箱を作るべきか。
- その箱の責務は何か。
- 入出力はどうあるべきか。
- どこまでを独立させるべきか。

そうした世界の見え方をエンジニアが与えてはじめて、AI は「ただ動く」以上のコードを書けるようになる。  
AI時代では、その力が、以前よりもはっきり価値を持つようになる。

AIはこのエンジニアの仕事に付随する、サブタスクの「コードをタイプする」という部分を代わりにやってくれるらしい。  
我々はより、仕事に集中できるわけだ。 **世界はどうあるべきか** それを実装の設計に落とし込む。 

**忘れてはならない。**

エンジニアの仕事は、コードをタイプすることではない。  
エンジニアの仕事は、**世界を美しく構造化すること** だ。

そして、その構造が自然で、単純で、  
Blackbox化されていて外から機能が理解でき  
Openableでより詳細に踏み込むこともでき  
再帰的に続いていて、いつでも理解を止められるならば。

そのコードは、美しい。
