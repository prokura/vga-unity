# トリガー #

Unityの「トリガー」(Trigger)は、自動ドアのセンサーのようなものを実現するための仕組み。当たり判定は行いたいが、物体としての存在は無い（物理挙動に干渉しない）、という場合に使う。

Unityでは「トリガー」と呼ばれるが、他のゲームエンジン（物理エンジン）では、「センサー」(Sensor)とか「ファントム」(Pahtom)とか呼ばれることもある。現場によって名前がバラバラ。

## 準備 ##

  * Cubeで床を作る。
  * 適当に光源を置く。
  * Sphereでボールを作る。Rigidbodyを与えて、床の上に落とすようにする。
  * 床を少し傾けて、床の上をボールが転がるようにする。
  * ボールが転がる先にCubeで障害物を作る。

## トリガーの作り方 ##

Colliderコンポーネント（Cubeであれば"Box Collider"、Sphereであれば"Sphere Collider"）にある"Is Trigger"のスイッチをONにする。

  * 障害物のColliderコンポーネントのIs Triggerをオンにしてみる。

ボールが障害物を通過するようになれば成功。

## トリガーの接触検出 ##

### 接触始め - OnTriggerEnter ###

トリガーに物体が接触したことを検出するにはOnTriggerEnter関数を実装する。この辺りはコリジョン検出に似ている。

  * スクリプトTriggerTestを新規作成する。
  * 障害物のトリガーにドラッグ＆ドロップする。
  * スクリプトの中身を次のようにする。

```
function OnTriggerEnter(other : Collider) {
    Debug.Log("Enter!");
}
```

  * コンソールウィンドウを前に出してから、動作確認する。

接触した瞬間に"Enter!"と出力されれば成功。

### 接触終わり - OnTriggerExit ###

トリガーから物体が抜け出ていった（接触が終わった）ことを検出するにはOnTriggerExit関数を実装する。

  * スクリプトTriggerTestに次の関数を追加する。

```
function OnTriggerExit(other : Collider) {
    Debug.Log("Exit!");
}
```

  * 動作確認する。

ボールが障害物をすり抜けた後に"Exit!"と出力されれば成功。

### 接触中 - OnTriggerStay ###

物体がトリガーに触れている間、1フレーム毎にOnTriggerStay関数が呼ばれる。

  * スクリプトTriggerTestに次の関数を追加する。

```
function OnTriggerStay(other : Collider) {
    Debug.Log("Stay!");
}
```

  * 動作確認する。

### 補足 ###

これらのOnTrigger系関数は、トリガー自身だけでなく、トリガーに接触した物体側でも呼ばれるようになっている。

  * ボール側にスクリプトを追加して確認してみる。

## 接触相手の取得 ##

OnTrigger系関数の引数otherには、接触した相手のColliderが渡される。これを使えば、接触した相手の情報を取得することができる。

ただし、コリジョン判定とは違って、相手のColliderが特定できるだけ。接触点や相対速度は分からない。

### 例：相手の位置を特定する ###

```
function OnTriggerEnter(other : Collider) {
    Debug.Log(other.transform.position);
}
```

### 例：トリガーに触れたボールを破棄する ###

```
function OnTriggerEnter(other : Collider) {
    Destroy(other.gameObject);
}
```

### 例：トリガーに触れたボールを加速する ###

```
function OnTriggerEnter(other : Collider) {
    other.rigidbody.AddForce(other.rigidbody.velocity.normalized * 20.0, ForceMode.Impulse);
}
```

Rigidbodyの速度ベクトルを正規化することにより、単位方向ベクトルを得る。その方向へと一定量加速する。

# コリジョン（当たり判定）の発生ルール #

OnCollision系関数やOnTrigger系関数は、コンポーネントの組み合わせによっては呼ばれないことがある。言葉で表すと、次のようになる。

> 「OnCollision系関数は、少なくともどちらか一方が非KinematicなRigidbodyを持つ場合に呼ばれる。トリガーに対しては一切呼ばれない。」

> 「OnTrigger系関数は、少なくともどちらか一方がRigidbodyを持ち、少なくともどちらか一方のColliderがトリガーになっている場合に呼ばれる」

表に直すと、次のようになる。

### コリジョンの発生する組み合わせ（OnCollision系関数） ###

![http://lh6.googleusercontent.com/-Z1egV0nwf-k/ThGNnKG4sdI/AAAAAAAAHGI/KypymgkzUZo/s800/Collision%252520Action%252520Matrix%2525201.png](http://lh6.googleusercontent.com/-Z1egV0nwf-k/ThGNnKG4sdI/AAAAAAAAHGI/KypymgkzUZo/s800/Collision%252520Action%252520Matrix%2525201.png)

### トリガーの発動する組み合わせ（OnTrigger系関数） ###

![http://lh6.googleusercontent.com/-Gg40EUJmBxA/ThGNnD4kgoI/AAAAAAAAHGM/CdREOFgZDFs/s800/Collision%252520Action%252520Matrix%2525202.png](http://lh6.googleusercontent.com/-Gg40EUJmBxA/ThGNnD4kgoI/AAAAAAAAHGM/CdREOFgZDFs/s800/Collision%252520Action%252520Matrix%2525202.png)

トリガー同士でトリガーを発動したい場合には、トリガーにKinematicなRigidbodyを付けるという、ちょっと特殊な設定を行う必要がある。

# 物理エンジン補足 #

## 離散(discrete)コリジョン判定と、連続(continuous)コリジョン判定 ##

コリジョン判定には2種類の方式がある。離散的なアプローチの「離散コリジョン判定」(discrete collision detection)と、連続的なアプローチの「連続コリジョン判定」(continuous collision detection)。

「離散」の方は、一定時間毎の点で判定を行う。「連続」の方は、一定時間の動きを繋いで判定を行う。下の図が分かりやすい。

![http://http.developer.nvidia.com/GPUGems3/elementLinks/33fig02.jpg](http://http.developer.nvidia.com/GPUGems3/elementLinks/33fig02.jpg)

http://http.developer.nvidia.com/GPUGems3/gpugems3_ch33.html

「離散」の方が処理が軽いが、高速で動く物体が他の物体をすり抜けてしまう可能性がある。Unityではデフォルトで「離散」が使用される設定になっている。

  * [例1 - 高速で動く物体が壁をすり抜ける。](http://dl.dropbox.com/u/14572092/VgaUnity/PhysicsCaveat1.html)
  * [例2 - スクリプトによって動かしているKinematic Rigidbodyが物体をすり抜ける。](http://dl.dropbox.com/u/14572092/VgaUnity/PhysicsCaveat2.html)

この「すり抜け」を対処するには、物体の速度を落とすとか、物体の厚みを増やすとか、何らかの手段によって物体を当たりやすくする必要がある。

特定の条件下においては、Unityでも連続コリジョン判定を使うことができる。Rigidbodyコンポーネントの設定の中にある"Collision Detection"を"Continuous"か"Continuous Dynamic"に変更する。

**Collision DetectionをContinuousにすると** - 静止しているMesh Colliderに対してのみ連続コリジョン判定が使われるようになる。3Dモデルとして作られた地形モデルに対するコリジョン判定だけを厳密化しよう、というもの。

**Collision DetectionをContinuous Dynamicにすると** - 静止しているMesh Colliderに加えて、ContinuousあるいはContinuous Dynamicが設定されている動的なRigidbodyに対しても、連続コリジョン判定が使われるようになる。

いずれの場合もスクリプトで動かしているKinematic Rigidbodyに対しては離散のままなので、注意すること。

## スリープ ##

物理エンジンにはほぼ間違いなく搭載されている機能。ほぼ停止したRigidbodyの物理挙動を切ることによって、処理負荷の軽減を図るというもの。

「ほぼ停止した」と判定される基準は、Physics Managerの中の設定にある（メインメニューEdit→Project Settings→Physics）。速度がSleep Velocity以下になり、なおかつ角速度がSleep Angular Velocityとなったとき、「ほぼ停止した」と判断される。

スリープ状態に入ると、物理挙動が働かなくなるほか、一部の機能が使えなくなる（例えばOnCollisionStayが呼ばれなくなる、など）。動いている物体が触れたり、AddForceされたりすると、自動的にスリープが解除されるため、普段はあまり問題無い。

スリープの解除が自動的に行われない例外のひとつとして、重力の変化がある。例えば、重力を変更することで物体を動かそうとしても、スリープ状態にある物体は動いてくれない。

  * [スリープと重力変化を確認するサンプル。](http://dl.dropbox.com/u/14572092/VgaUnity/PhysicsCaveat3.html)

キー操作で重力を変更する。スリープに入ったボールは赤色に変化する。いったんスリープしてしまったボールは、キー操作の影響を受けなくなってしまうが、カプセルに触れると復帰する。カプセルは毎フレームWakeUp関数を呼ぶことによってスリープを防いでいる。

# 課題 #

[手本](http://dl.dropbox.com/u/14572092/VgaUnity/TriggerBasic.html)と同じものを作成する。

余裕があれば、[もうちょっと凝ったもの](http://dl.dropbox.com/u/14572092/VgaUnity/TriggerAdvanced.html)を作ってみる。

来週の授業開始時に回収。

### 提出ディレクトリ ###

```
\\G-Server\Public\GP\Unity\Middleware0706
```

### ヒント ###

マウスカーソルの位置にゲームオブジェクトを動かすには、次のようなスクリプトを使う。

```
function Update () {
    var screenPoint = Input.mousePosition;
    screenPoint.z = -camera.main.transform.position.z;
    var worldPoint = camera.main.ScreenToWorldPoint(screenPoint);
    transform.position = worldPoint;
}
```