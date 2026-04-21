
# Beauty lives in the Code.

## Indies
1. [An engineer's task is to structure the world, not to type code.](#an-engineers-task-is-to-structure-the-world-not-to-type-code)
2. [Openable Blackbox](#openable-blackbox)
3. [Recursive Openable Blackbox](#recursive-openable-blackbox)
4. [The beauty must be simple and natural](#the-beauty-must-be-simple-and-natural)
5. [Almost cheat is not needed](#almost-cheat-is-not-needed)
6. [Do not explain. The code does.](#do-not-explain-the-code-does)
7. [Do not write documentation. You already wrote the code, right?](#do-not-write-documentation-you-already-wrote-the-code-right)

## An engineer's task is to structure the world, not to type code.

エンジニアにとって最も重要なスキルは何だろうか。

言語仕様を暗記することだろうか。フレームワークを使いこなすことだろうか。アルゴリズムの知識量だろうか。  
どれも重要ではあるが、本質ではない。

エンジニアの仕事はコードを書くことではない。 エンジニアの仕事は **世界を構造化すること** だ。  
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

どちらも動く。しかし、後者は **美しく** ない

多くのコードは「動く」という条件だけで評価されてしまう。確かに、動くコードは価値を生む。しかし、それは最低条件に過ぎない。
一方で、長く使われ続けるコード、他人に読み継がれるコード、大規模なOSSのように数千人が関わるコードは、単に動くだけでは成立しない。

それらのコードには共通点がある。

**読むことができる。** しかも、苦労せずに。  
コードを読むとき、人は無意識にコストを計算している。 理解するのに時間がかかりそうなら、読むのをやめる。
これは怠慢ではない。合理的な判断だ。

優れたコードは、人に **「読む気」** を要求しない。むしろ、人が理解する努力を最小化するように設計されている。
この差はどこから生まれるのか。それは単に書き方の問題ではない。  
もっと手前の段階、つまり **世界の分割の仕方** によって決まる。

コードとは、現実世界の問題を何らかの構造に分解し、それを再構成したものだ。

- ユーザー
- メッセージ
- 認証
- 保存
- 取得

こうした概念をどのように切り分けるか。どの単位をひとつの責務とするか。どこまでをひとつの操作とみなすか。  
この設計が、そのままコードの読みやすさになる。  
つまり、 **読みやすさを意識して実装する** わけではない。美しく設計されたコードは **結果として読みやすい** 。

大規模なOSSプロジェクトでは、何千人もの開発者が同じコードベースに関わる。それでも成立するのは、コードが共有可能な形で世界を表現しているからだ。  
人は入れ替わる。プロジェクトは長く続く。仕様は変わり続ける。構造が適切であれば、コードは進化し続けることができる。一方で、構造が崩れていれば、変更のたびに破綻する。  
誰が読んでも理解できること以上に、 **簡単に** 理解できる事が重要だ。


「美しいコード」とは、別に芸術的なコードではない。  
トリッキーなワンライナーでもない。巧妙なメタプログラミングでもない。抽象化のための抽象化でもない。  

美しいコードとは、 **世界の構造がそのまま表現されているコード** だ。  
そしてエンジニアの本懐とは、 **世界を美しく構造化すること** である。

つまり  
- 責務の境界をどこに置くのか。  
- どの粒度で抽象化するのか。  
- どの概念を独立させるのか。  

これは単なるスタイルの問題ではない。設計そのものの問題だ。そして、この設計能力こそがエンジニアが本質的にやるべきことだ。  
AIがコードを書くようになったとしても、世界の構造を設計する仕事は依然として残る。むしろ、より重要になる。これは、少しAIにコードを書かせてみた人なら納得できるだろう。

---

では、良い「世界の切り分け方」とは何だろうか。 どうすれば、理解しやすく、変更しやすく、長く生きる構造を作ることができるのだろうか。  
そのヒントになる考え方が **Openable Blackbox** だ。

次章では、この概念をもとに、理解コストを最小化する構造の作り方について考えていく。

## Openable Blackbox
いい。1章目はかなり方向性が出ている。
その流れを受けて、語り口を合わせつつ、次の「Recursive Openable Blackbox」に自然につながるようにすると、`Openable Blackbox` はこう書ける。

---

## Openable Blackbox

ここで言いたいのは、「美しいコード」とは単に見た目が整っているコードのことではない、ということだ。
その本質のひとつは、**理解しやすいこと** にある。

ただし、ここで言う理解しやすさとは、「すべてを説明してくれること」ではない。  
むしろ逆だ。  
**理解を途中でやめられること**。  
それこそが、本当に理解しやすいコードの条件だ。

 
どんなプログラムも、突き詰めれば機械語に翻訳され、cpuでデコードされ、半導体の中のどれかの回路で計算されることになる。  
しかしそれを全て説明出来る人は少ないだろうし、もちろんそんなことは必要ない。

コードはコンパイラにより機械語に翻訳され、  
CPUは計算をする機能があって、  
コンピュータは期待通りの動きをする。

詳細を知らなくても、理解し、やりたいことを実現できるのは、 Blackbox のおかげだ。  
Blackbox のお陰で我々は **理解をやめる** ことができ、それは **大きく理解コストを下げる** 事に繋がる。

しかし一方で、問題が起こった時や、改善の為に修正が必要になった途端に、その Blackbox を開ける必要に迫られる。
実際にCPUが壊れたからと言って開けて直す人はいないと思うが、これがもし同じプロジェクトの他人が実装したコードや機能だったらどうだろうか。

大抵そういう時、作った人は居なくなっており、また開ければ美味しそうなスパゲティが入っている。  
そして、藁にも縋る思いで開発ドキュメントを探す。そして、無い、もしくは古い。  
まあ、いずれにせよ、役には立たない。そもそもドキュメントを探して読むなんて苦行を強要されるんて、悪夢だ。

つまり、Blackbox化するだけでは、それは技術負債として埋められ、いつか誰かが踏んで左足を失うことになる。
だからこそ、それは **開けられる** Blackbox (Openable Blackbox) 

つまり、ふだんは Blackbox として扱える。必要なときだけ開けられる。  
そして開けたとき、中に入っているものもちゃんと読める。  
これが Openable Blackbox の感覚だ。

例えば、次のような関数があったとする。
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
これだけわかるなら、君はここで理解をやめて。このAPIを使う実装に作業に移ればいい。

もしも、`create_order`関数が実際に何をしているのかを知りたければ。  
その Openable Blackbox を開ければよい。 (多くのIDEでは Ctrl+click が役に立つ)
```python
def create_order(user_id: str, item_id: str) -> Order:
    user = get_active_user(user_id)
    item = get_purchasable_item(item_id)
    order = build_order(user=user, item=item)
    save_order(order)
    return order
```

この関数を見たとき、君はひとまず満足できる。  
ああ、ユーザーと商品を確認して、注文を組み立てて、保存して、`order object` を返しているのだな、と。  
ここで理解を止めてもよい。

もし「`get_purchasable_item` は何をしているのか」が気になれば、その箱だけ開ければいい。  
もし「`build_order` のロジック」が気になれば、そこだけ見ればいい。  
すべてを一気に理解する必要はない。必要な箱だけ開けて、満足すれば閉じればいい。

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

これも動く。
しかし、君はきっとため息をついて、一旦コーヒーを入れに行くだろう。  
まず量が多い。しかも、どこまでが「注文作成」という責務で、どこからがユーザー検証で、どこからが商品判定なのかも曖昧だ。
霧の中で何かをつかもうと模索する苦しみと疲労が待っている。

これではOpenableになっていない。

Openable Blackbox とは、隠すことと逃げることを混同しない設計のことだ。  
責務を押し込めるのではない。責務を切り分けるのだ。  
見えなくするのではない。必要なときに自然に見に行けるようにしておくのだ。

重要なのは、箱の外から見たときに「何をしているか」がわかること。
そして、中を開けたときに「どうやっているか」も無理なく追えることだ。

外からは目的が見え、中には理解可能な部品が入っている。そういう構造でなければならない。
これが、 **Openable Blackbox** という設計だ。

だから次に見るべきなのは、Openable Blackbox を**再帰的に**どう作るか、だ。  
関数の中だけではなく、アプリケーション全体をどうその構造にするのか。  
次章では、そこをもう少し大きなスケールで見ていく。  


## Recursive Openable Blackbox
ここではOpenableBlackboxが理解コストを下げることについて理解を得られたところで、
小さなバッチ処理レベルではなくサービスでのユースケースまでスケールする。 
Openable Blackbox はサービスやプロジェクトのような大きな単位になったときにこそ本当の価値を持つ。

twitterのようなシンプルで大規模なアプリを題材にしてみる。
もちろん実際のコードは知らないが、サーバを起動するエントリーポイントがあり、それはAPIサーバーを立ち上げる。
APIサーバーには関数がいくつかあり、例えば`like`や`tweet`のような関数だろう。それから、`getTimeline`があるかもしれない。  

```python

app = FastAPI()


@dataclass
class LikeResponse:
    status: Enum("ok", "error")
    message: str | None
    

@app.post("/like")
def like(user_id: str, target_post_id: str) -> LikeResponse:
    response = like_usecase(user_id, target_post_id)
    return LikeResponse(response)


@dataclass
class TweetResponse:
    status: Enum("ok", "error")
    message: str | None


@app.post("/tweet")
def tweet(user_id: str, tweet_content: str) -> TweetResponse:
    response = tweet_usecase(user_id, tweet_content)
    return TweetResponse(response)


@dataclass
class getTimelineResponse:
    status: Enum("ok", "error")
    message: str | None
    timeline: Timeline


@app.post("/getTimeline")
def get_timeline(user_id: str) -> getTimelineResponse:
    response = get_timeline_usecase(user_id)
    return getTimelineResponse(response)

```
これをみれば、ひとまずどんな入り口があるのかがわかる。
そしてもし、timelineの取得の仕組みが知りたければ、`get_timeline_usecase`関数を見に行くべきだ。
すると例えば、こうなっている。
```python
def get_timeline_usecase(user_id) -> Timeline:
    if auth_user(user_id):
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
twitterの仕組みもそこそこに気なるところだが、それは後にして、少し振り返ろう。
BlackBoxを開けるたびいくつかのBlackBoxをみつけ、それらの組み合わせでそのBlackBoxは何をしているのかを理解できた。自分の知りたい部分が知れて満足すれば、仕事に戻ればいい。  
知る必要がある箱があれば空け、満足するまで探索できる。さらに付け加えるとそれぞれのBlackBoxは充分シンプルで簡単に理解できた。
最初にtwitterのBlackBoxを開けて、

```python

app = FastAPI()
...
...
...


@app.post("/tweet")
def tweet(user_id: str, tweet_content: str) -> TweetResponse:
    # at first, auth user.
    auth_db = pymysql.conect(f"mysql:auth.twitter.com:3306@{hoge}@{fuga}")
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

つまり、Openable Blackboxの中には理解が簡単にできるほどにシンプルな構造で少ない数のOpenable Blackboxが再帰的に入っていなければならない。  
そして最終的に充分小さく、深いところにあるOpenable Blackboxを開けると、書いているネイティブ言語で書かれている充分簡単に理解できるシンプルな実装が出てくる。  

**Recursive Openable Blackbox** というわけだ。  

エントリーポイントを開ける -> ユースケースが見える  
ユースケースを開ける -> ドメイン操作が見える  
ドメイン操作を開ける -> インフラとの接点が見える  
さらに必要ならその先も追える。

どこで止めても理解が成立し、どこまで掘っても破綻しない。
この「開けられる箱が、中にも開けられる箱を持っている」という性質は、小さな関数の話にとどまらない。  
いわゆる "CLEAN", "Hexagonal" といったアーキテクチャはこの Openable Blackbox を実現するための、区切り方の指標とみることもできる。

ちなみに、みんなが愛用するCpythonであれば、最終的にはコンパイルされたC言語という開かないBlackBoxが出てくる。  
とはいえ、ソースコードを見ればそこにはまたOpenable Blackboxがあるだろう。最もそんな深くまで探索しないと仕事が進められないということは本当にごく稀だろう。  

Openable BlackboxはそれがOpenableであることも重要だが、取り組んでいるものが、batchファイルやスクリプトのレベルではなく、「プロジェクト」と呼べるようなものであれば、
充分深く、私以外誰一人こんな深いところまで来ないだろうと120%の確信が持てるところまで、全て再帰的にOpenable Blackboxにして、誰かがその箱を開けてもすぐに閉じないようにするべきだ。

## The beauty must be simple and natural
ここからは、いかにして自分が実装したBlackboxがOpenableな状態にできるか。開けた人をびっくりさせないようにするかについて。  
うすうす気が付いているとは思うが、シンプルであるべきだ。

何かコードを書いていて複雑になってきて忘れないようにコメントを書きたくなった時、  
別の人がこのコードを読んだ時のヒントになるようにドキュメントを書こうとしたとき。  
それはBlackboxにパッキングするいい機会だ。

そしてそのBlackboxはシンプルなだけでなく、**自然** であるべきだ。
決してトイレで尻を拭くコードと手を洗うコードを別々のBlackboxに分けてはいけない。
それと、ジーンズでぬれた手を拭くコードもだ。

ではどうやってシンプルで自然に再帰的で最高にクールなOpenable Blackboxパッキングができるのだろう。  
それこそが始めに言及した「**世界の見え方**」であり、それを美しく構築することが君の仕事だ。
Recursive Openable Blackbox は、君が美しく世界を切り取り構造化した物を表現する良い手段になるだろう。

しかしそれでは身も蓋もないので、いくつかのポイントを上げたい。

## Almost cheat is not needed
**自然** であるということは、特別なことをしないということだ。つまり、ほとんどのチートや工夫は必要ない。  
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
一見、問題は解決しているように見える。  Python では forward reference もできるし、遅延 import もできる。  
しかしここで起きていることは、「型の関係が壊れている」のを実行順序で隠しているだけだ。  

このコードでは、世界の見え方として
- `user` は `orders: list[Order]` を持つ。
- `order` はオーダーしたユーザを参照する `user` を持つ。

これは正しそうではある。  
しかし、`user.orders[0].user`と参照でき、それは、`user`と一緒なのか、違うのか、違うときはエラーにするべきなのか？ 考えるべきことがどんどん増えてくる。  
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

orders: list[Order] = []
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
- `user` が存在する
- `user` は自身のオーダーを保持している。
- `order` が存在する。

```python
# order.py
class Order:
    def __init__(self, user_id: str, price: float):
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
例えば、`order`そのものから、それをオーダーしたユーザを参照したいのなら、後者の切り取り方にはならないだろう。

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
```

```python
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
        data = self.model_dump()
        return data

    
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
前者の実装をした人には、「何故君にはそんな風に世界が見えているんだい？」と小一時間問い詰めてやらなきゃならないだろう。

この章で伝えたいのは、個々のテクニックの是非ではない。 遅延 import が悪いわけでも、三項演算子が悪いわけでも、hasattr が常に悪というわけでもない。  
問題は、それらが必要になっている 理由 だ。  
ほとんどの場合、cheat は設計の歪みを知らせるシグナルとして現れる。

- import 順序を調整しないと型が定義できない
- attribute の有無を実行時に調べないと型が扱えない
- 一つの関数で複数の責務を扱わざるを得ない
- 型ではなく文字列や dict の形で意味を再構築している

こうした状況では、コードは徐々に注釈や付随する説明が必要になってくる。
コメントが増え、docstring が長くなり、注意書きが増え、例外処理が増え、条件分岐が増える。

逆に、構造が自然であれば、コードは静かになる。

特別なテクニックは要らない。  
実行順序に依存しない。  
属性の有無を探らなくてよい。  
型を見れば意味がわかる。  
関数名を見れば責務がわかる。  
呼び出し順を追えば処理の流れがわかる。  

つまり、 構造が正しければ、コードは **普通** になる。  
そして「普通であること」は、ソフトウェアにおいて非常に価値が高い。

普通のコードは、読み手を選ばない。 将来の自分を裏切らない。

一方で、cheat に依存したコードは、書いた本人しか扱えなくなる。 書いた本人がいなくなると、誰も触れなくなる。  
そして触れないコードは、変更されないコードになる。変更されないコードは、いずれ現実と乖離する。

だから、何か特別なテクニックを思いついたときほど、一度立ち止まるべきだ。
「もっと賢く書く方法はないか」ではなく、  
「そもそも、この分割は自然か？」  
と問い直す。

多くの場合、本当に必要なのはテクニックではない。世界を美しく切り取り、再構成する事だ。  
「テクニックを使うな」という意味ではない。自然に分割された構造では、ほとんどのテクニックは **必要なくなる** 。という意味だ。

## Do not explain. The code does.
PEP8には,以下のような記載がある。多くのコーディングスタイルにも近い記載はあるだろう。
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

コメントは別に読めばわかる実装を説明しているわけではなく。  
むしろ、コメントのおかげで、コード実装からは分からない、それぞれのフェーズで何をするのかを明確にする助けになっている。  
それに暴騰のdocstringに、包括的な情報があり、それぞれの引数がどんな役割をするのかがわかる。

問題は、 読めば分かるという部分だ。 **読めば** だ。誰がこんなコードを読むだろうか、悪いが私はごめんだ。

引数の`data: dict # user data dict returned from API.`というコメントは、引数の名前を `user_data`とすれば分かるし。 もっと言えば、APIから返ってくるuserの情報を正しく格納する`User`オブジェクトを定義して、`user: User`とでもしておけばもっと明確だ。

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
確かにこの実装は、コメントが必要だ。ただ、コメントの前に、そんなに複雑に実装する必要がない。

変数そのものにコメントを書いているとき。処理をブロックごとにコメントで区切っているとき。というかほとんどすべてのコメントは悪だ。  
説明を必要とするコードとは裏を返せば、説明がなければ理解できないコードである。

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

このようにそれぞれの **自然な** 切れ目で機能を分割し、BlackBox　にしまって名前を付けるだけで、読みやすくなる。  
それぞれの入出力に対してスキーマを定義して、意味づけすると、`data`って何？というような疑問はそもそもわかない。　　
必要な処理を自然に区切り、関数名をつけてOpenableBlackBoxにすれば、コードをコメントでブロックにわけ整理する動機はなくなる。

決して小粋なことはしていない。自然に世界を切り取ると説明は基本的に不要になる。正確には、コードそのものが実装の説明になる。  
もしも、自身のコードに何か説明をしたくなった時、それは検討の足りないアーキテクチャの解読を後の誰かに擦り付ける為の君の怠惰出ないと120%確信が持てるかどうか
改めて自身に問いかけるチャンスだ。  

当然だkが、コードは書く人よりも読む人の方が圧倒的に多い。  
つまり君一人の怠惰で美しくないコードの説明を書いたとすると。
その怠惰のせいで、少なく見積もって未来の君を含む数十人が、その美しくないコードをコメントを手掛かりに嗚咽しながら理解コストを書けることになる。  
これは、無差別テロといって差支えない。

忘れてくれるな、エンジニアの本懐は **世界を美しく構造化すること** だ。


## Do not write documentation. You already wrote the code, right?
最後に、コード以外に書かれるdocumentationについても触れたい。  
基本的にすべて悪だ。

コード以外の部分、例えば前提としている開発環境や、秘密鍵の取り扱い、あとはプロジェクトそのものの思想やUXデザインなんかは必要だと思うが。
あとは開発した年と自分の名前でも記念に書いておけばそれで十分だ。それとオシャレなロゴだな。

ただしもちろん技術の流出を嫌って顧客や世間に開示する用の開かないBlackboxの説明としてのdocumentationを否定するものではない。  
また、OpenableBlackBoxの再外核となる entry point に対する説明は有用だとも思う。例えば、API referenceが代表的であろう。

今回言及したいのは、**ソフトウェアの実装を理解するために書かれるドキュメント群** についてだ。
何より、何かコードについての説明をdocumentationに書くということは、保守すべきものを増やしていることに他ならない。
そしてdocumentationは大抵version管理の外だ。  
印刷されファイルに立派にしまわれてしまった日には、コードが変更されたときのその紙に書いてある説明は誰かがボールペンで修正してくれるのかい？  
君が徹夜で書いて印刷したその紙ドキュメントはそのうち嘘だらけになり、誰かを混乱させる。尻でも拭いて便器に流した方がよっぽどましだ。  

当たり前だが、プログラムはコードに書いてある通りに動く。  
コードは仕様の一次情報だ。それにそれはいつでも変わる。誰かの何かを良くするために。    
わざわざ2次情報documentationを書いたとして、typoのリスクを抱えながら、同じことを書いて、コードの変更の度に修正をするハメになる。  
少なくとも、documentationを変更する必要があるかどうか確認するハメには必ずなる。  
さらに悪いことに、絶対にそのdocumentationと実際に動いているコードにはそのうち差分が発生する。「嘘のコメントは無いよりひどい」というがdocumentationはその最たる例だ。

これでもまだ、不必要ドキュメント執筆罪に手を染めるエンジニアの一定数には、  
「私のコードを読んでもらっても理解してもらえないかもしれない。」という不安があるのかもしれない。  

君は仲間をみくびりすぎだ。 君のチームのエンジニアをもっと信頼して、そして期待していい。  
君が一生懸命検討した美しいコードであれば、君の友人はきっと読んで理解できるし、理解できるスキルレベルを求めるべきだ。  

さらにもし、君の信頼する友人が君のコードでわからないところがあったとすれば、それはチャンスだ。  
きっと、うまくない設計や構造が隠れていて、友人が教えてくれているということに他ならない。  
これはコードが持続的に成長するにあたり、非常によくある、そして放置すると大きな癌に成長する小さなほころびだ。大きくなる前に手を打てる。  
またそういった議論をもとに、責務分離が不明確な部分が見つかったり、過剰に責務を持っている関数が見つかったりもする。

繰り返すが、既に読めばわかるものにわざわざ説明を書いて、しかもそれが未来のエンジニアに嘘を吹聴するなんて犯罪に手を染めないでほしい。

