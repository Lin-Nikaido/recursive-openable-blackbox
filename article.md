
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
そしてエンジニアの本懐とは、 **世界を構造化すること** である。

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

外からは目的が見える。 中には理解可能な部品が入っている。  
そういう構造でなければならない。

そして当然、これを1関数だけで終わらせてはいけない。
プロジェクト全体がそうなっていなければ意味がない。

エントリーポイントを開ける。
ユースケースが見える。
ユースケースを開ける。
ドメイン操作が見える。
ドメイン操作を開ける。
インフラとの接点が見える。
さらに必要ならその先も追える。

どこで止めても理解が成立し、どこまで掘っても破綻しない。
それが理想だ。

この「開けられる箱が、中にも開けられる箱を持っている」という性質は、小さな関数の話にとどまらない。
むしろ、サービスやプロジェクトのような大きな単位になったときにこそ本当の価値を持つ。

だから次に見るべきなのは、Openable Blackbox を**再帰的に**どう作るか、だ。
関数の中だけではなく、アプリケーション全体をどうその構造にするのか。
次章では、そこをもう少し大きなスケールで見ていく。

---

必要ならこのまま続けて、次の **Recursive Openable Blackbox** も、今の口調に合わせて全面的に書き直す。


## Recursive Openable Blackbox
ここではOpenableBlackboxが理解コストを下げることについて理解を得られたところで、
小さなバッチ処理レベルではなくサービスでのユースケースまでスケールする。  
twitterのようなシンプルで大規模なアプリを題材にしてみる。
もちろん実際のコードは知らないが、サーバを起動するエントリーポイントがあり、それはAPIサーバーを立ち上げる。
APIサーバーには関数がいくつかあり、例えば「like」や「tweet」のような関数だろう。それから、「getTimeline」があるかもしれない。  
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
Recursive Openable Blackbox というわけだ。  
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
そしてそのBlackboxはシンプルなだけでなく、自然であるべきだ。
決してトイレで尻を拭くコードと手を洗うコードを別々のBlackboxに分けてはいけない。
それと、ジーンズでぬれた手を拭くコードもだ。

じゃあどうやってシンプルで自然に再帰的で最高にクールなOpenable Blackboxパッキングができるのだろう。  
それこそが始めに言及した「世界の見え方」であり、それを構築するのを考えるのが君の仕事だ。  
しかしそれでは身も蓋もないので、いくつかのポイントを上げたい。

## Almost cheat is not needed
いくつかの"cheat"の例をあげて、分割のポイントを分けるとそんなことをせず「自然」に実装できる例を示す。
例えば、type定義の中で循環参照をしているのを遅延importで回避している例(ファイル分割が不自然)や、
外部ライブラリの非公開メソッドを使って無理やり実現しているケース。(DIの場所が早すぎたり、遅すぎたり)
if文が大量に必要になった結果、複雑かつ一見オシャレにさえ見える三項関演算子やらonelinerやらを多用しているとき。関数内関数内関数。
`hasattr`を使って結果を分類しているケース。文字列の先頭や最後、時にはドラスティックな分割結合の芸術により、値を再構築している例等、枚挙にいとまがない。
後半は、なんならコード実装に詳しい部分をひけらかして、わざとやる人もいて、余計タチが悪い。
合わせて少し抽象化して、記憶に残る学びになるようにする。

## Do not explain. The code does.
これも実際の実例をもとに大きすぎる関数や、複数個所でほとんど同様の実装をしている例等を取り上げたい。
型(オブジェクト)定義が弱く、また本質的に違う要素を同じ変数で受け取っているため、詳細かつ複雑に引数やその関数の動きについてdocstringを書いている例。
変数そのものにコメントを書いているとき。処理をブロックごとにコメントで区切っているとき。というかほとんどすべてのコメントは悪だと思う。
また、長すぎるdocstringや、flatに多すぎる引数とそのそれぞれの説明のセット。Aの時にだけ使えるBとCのOption引数とか。

## Do not write documentation. You already wrote the code, right?
最後に、コード以外に書かれるdocumentationについても触れたい。基本的にすべて悪だ。
コード以外の部分、例えば前提としている開発環境や、秘密鍵、あとはプロジェクトそのものの思想やUXデザインなんかは必要だと思うが。
あとは開発した年と自分の名前でも記念に書いておけばそれで十分だ。それとオシャレなロゴだな。  
何より、何かコードについての説明をdocumentationに書くということは、保守すべきものを増やしていることに他ならない。
そしてdocumentationは大抵version管理の外だ。印刷されファイルに立派にしまわれてしまった日には、コードが変更されたときのその紙に書いてある説明は誰かがボールペンで修正してくれるのかい？  
君が徹夜で書いて印刷したその紙ドキュメントはそのうち嘘だらけになり、誰かを混乱させる。尻でも拭いて便器に流した方がよっぽどましだ。  
当たり前だが、プログラムはコードに書いてある通りに動く。コードは仕様の一次情報だ。それにそれはいつでも変わる。誰かの何かを良くするために。  
わざわざ2次情報としてtypoのリスクを抱えながら、同じことを書いて、コードの変更の度に修正をする羽目になる。少なくとも、documentationを変更する必要があるかどうか確認するハメには必ずなる。  
さらに悪いことに、絶対にそのdocumentationと実際に動いているコードにはそのうち差分が発生する。「嘘のコメントは無いよりひどい」というがdocumentationはその最たる例だ。  
ただしもちろん技術の流出を嫌って顧客や世間に開示する用の開かないBlackboxの説明としてのdocumentationを否定するものではない。  
まあただそれを書くのはエンジニアの仕事ではないからどちらにせよ、我々エンジニアには関係のない話だが。
繰り返すが、既に読めばわかるものにわざわざ説明を書いて、しかもそれが未来のエンジニアに嘘を吹聴するなんて犯罪に手を染めないでほしい。
