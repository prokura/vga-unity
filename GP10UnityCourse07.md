# コリジョン検出 #

コリジョン(collision)＝「衝突」。「コリジョン検出」は、いわゆる「当たり判定」のこと。物理エンジン側で行われているコリジョン判定の結果をゲーム側で受け取ることにより、ゲーム的な処理に用いることができる。

## 準備 ##

  * 床を作る。名前は"Floor"。
  * 球を作る。Rigidbodyコンポーネントを追加する。
  * 適当にバウンドするよう、Physic Materialを両方に与えて、Bouncinessを0.5ぐらいに設定しておく。
  * スクリプトを新規作成する。名前は"CollisionTest"。これをFloorゲームオブジェクトに追加する。

## OnCollisionEnter ##

衝突が発生した瞬間を検出するには、関数OnCollisionEnterを定義する。

スクリプトCollisionTestに次の関数を追加する。

```
function OnCollisionEnter(info : Collision) {
    Debug.Log("COLLISION!!");
}
```

Consoleウィンドウを開いてから、実行する。衝突の度に"COLLISION!!"と出力される。

## OnCollisionExit ##

衝突した物体が離れる瞬間を検出するには、関数OnCollisionExitを定義する。

スクリプトCollisionTestに次の関数を追加する。

```
function OnCollisionExit(info : Collision) {
    Debug.Log("EXIT!!");
}
```

実行する。"COLLISION!!"の次に"EXIT!!"と出力される。最後の衝突は接触しっぱなしなので、"EXIT!!"は表示されない。

## Collisionクラス ##

コリジョン検出時に渡されるCollisionクラスの引数には、衝突に関する情報が格納されている。

### 衝突相手のゲームオブジェクトへのアクセス ###

CollisionクラスのgameObjectプロパティから、衝突相手のゲームオブジェクトにアクセスできる。

```
function OnCollisionEnter(info : Collision) {
    // 衝突相手の名前を表示。
    Debug.Log(info.gameObject.name);
}
```

### 衝突相手のトランスフォームへのアクセス ###

Collisionクラスのtransformプロパティから、衝突相手の位置・回転情報にアクセスできる。

```
function OnCollisionEnter(info : Collision) {
    // 衝突相手の位置を表示。
    Debug.Log(info.transform.position);
}
```

### 衝突相手のRigidbodyへのアクセス ###

Collisionクラスのrigidbodyプロパティから、衝突相手のRigidbodyにアクセスできる。

```
function OnCollisionEnter(info : Collision) {
    // 衝突相手を上に弾く。
    info.rigidbody.AddForce(Vector3.up * 2.0, ForceMode.Impulse);
}
```

### 衝突時の相対速度の取得 ###

CollisionクラスのrelativeVelocityプロパティから、衝突時の相対速度を知ることができる。

```
function OnCollisionEnter(info : Collision) {
    // 衝突時の相対速度を表示。
    Debug.Log(info.relativeVelocity);
}
```

勢いよく当たったときに特定の処理を行いたい、という場合に便利。

### 接触点の取得 ###

Collisionクラスのcontactsプロパティから、衝突相手と接触している点の情報を得ることができる。

```
function OnCollisionEnter(info : Collision) {
    // 接触点の情報を表示。
    for (var contact : ContactPoint in info.contacts) {
        Debug.Log(contact.point);  // 接触点。
        Debug.Log(contact.normal); // 接触点の法線。
    }
}
```

# タグ #

「タグ」(tag)は、ゲームオブジェクトに任意の文字列を与えて管理するための仕組み。

YouTubeとかニコ動とかの「タグ」みたいなもの。ゲームオブジェクトを分類するのに便利。ただし、Unityでは、各ゲームオブジェクトに対してひとつしかタグを与えることができない。

ゲームオブジェクトにタグを与えるには、ゲームオブジェクトを選択したときにInspectorの上部分に表示される"Tag"というラベルの付いたドロップダウンを使う。

Unityにはプリセットのタグがいくつか用意されているが、自分で定義することもできる。ドロップダウンの中にある"Add Tag..."を選択すると、Tag Managerが開く。いちばん上にある"Tags"という項目をクリックして開くと、タグのリストが出現する。そこで自由に追加できる。

設定したタグは、GameObjectクラスのtagプロパティから取得できる。例えば、コリジョン検出の中で次のようにして使える。

```
function OnCollisionEnter(info : Collision) {
    // 衝突相手のタグが"Item"だったらメッセージを表示。
    if (info.gameObject.tag == "Item") {
        Debug.Log("I got an item!!");
    }
}
```

# ゲームオブジェクトの破棄 #

ゲームオブジェクトを破棄する（消す）には、Destroy関数を使う。

例えば、タグ"Item"を持つゲームオブジェクトが衝突した場合にそのゲームオブジェクトを消す、という処理は、次のようにしてできる。

```
function OnCollisionEnter(info : Collision) {
    // 衝突相手のタグが"Item"だったらメッセージを表示。
    if (info.gameObject.tag == "Item") {
        Destroy(info.gameObject);
    }
}
```

### Destroyに関する注意 ###

Destroy関数は、そのゲームオブジェクトを即時に破棄するわけではなく、少し後（今フレームと次フレームの間）に破棄する。そのため、Destroyを読んだ直後から、相手が完全に消えてることを前提とするような処理を書くと、不具合の原因になることがある。

# 乱数 #

Unityで乱数を生成するにはRandomクラスを使う。

## intの乱数生成 ##

```
// 10以上、100未満の乱数を生成。
Random.Range(10, 100);
```

## floatの乱数生成 ##

```
// 10以上、100以下の乱数を生成。
Random.Range(10.0, 100.0);
```

名前が同じで、引数の型によって切り替わる、という点に注意。明示的に".0"を付けるように意識するべし。

## その他 ##

Randomクラスには、その他にも、ランダムな回転を生成するプロパティや、ランダムなベクトルを生成するプロパティなど、ユーティリティ的な要素が備わっている。詳しくは[リファレンス](http://unity3d.com/support/documentation/ScriptReference/Random.html)を参照。

# 課題 #

[手本](http://dl.dropbox.com/u/14572092/VgaUnity/CollisionBasic.html)と同じものを作成する。

来週の授業開始時に回収。

### 提出ディレクトリ ###

```
\\G-Server\Public\GP\Unity\Middleware0629
```

### ヒント ###

床には"Floor"タグを、プレイヤーには"Player"タグを付けてある。箱のスクリプトでコリジョン検出を行い、相手が床であれば自分を消して、プレイヤーであれば相手を消すようにしている。

プレイヤーの左右方向の動きは、transformを直接操作することによって実現している。

```
function Update() {
   transform.position.x += Input.GetAxis("Horizontal") * Time.deltaTime * 10;
}
```

物理挙動を使わないので、Rigidbodyは付けていない。

箱を一定時間毎に生み出す処理は、だいたい次のような感じになる。

```
var cubePrefab : GameObject;
private var timer : float;

function Update () {
    timer += Time.deltaTime;
    if (timer > 0.2) { // 0.2秒毎に以下を実行。
        Instantiate(...);
        timer = 0;
    }
}
```