# モデルデータの使用 #

Unityは色々な3Dツールのデータ形式に対応しているが、迷ったときはとりあえずfbx形式でやりとりしておくのが最も無難と言われる。

  * [サンプルデータ(VantanKun.zip)](http://vga-unity.googlecode.com/files/VantanKun.zip)をダウンロードして解凍しておく。

サンプルデータの中にはモデルデータ(VantanKun.fbx)とテクスチャデータ(VantanKun.png)が入っている。

## モデルデータの取り込み ##

データを取り込むには、Projectビューにドラッグ＆ドロップするだけ。

  * VantanKun.fbxとVantanKun.pngをProjectビューにドラッグ＆ドロップする。

モデルデータを取り込むと、モデルデータが追加されるのと同時に、モデルデータ内に含まれるマテリアルが自動的に作成される。このデータの場合であればVantanKunという名前のマテリアル。

マテリアルと同名のテクスチャが存在する場合は、自動的にそのテクスチャがマテリアルに貼られる。

  * マテリアルVantanKunの設定を確認する。もしテクスチャが貼られていなかったら、VantanKun.pngをテクスチャに設定する。

Projectビューの中で、モデルデータVantanKunは階層を持っているように見える。

  * モデルデータVantanKunの階層を開いて中身を見てみる。

モデルデータの中には、階層構造と、メッシュデータと、アニメーションデータが含まれる。VantanKunの場合は、階層構造のトップである"Pelvis"と、メッシュデータである"Cahracter"と、複数のアニメーションデータが見える。

## シーンへの追加 ##

モデルをシーンに追加するには、モデルデータをSceneビューにドラッグ＆ドロップするだけ。

  * VantanKunをSceneビューにドラッグ＆ドロップする。

シーンに追加されたモデルは、とりあえずデフォルトのアニメーションが自動再生されるようになっている。

  * 実行して、アニメーションが再生されるのを確認する。

デフォルトアニメーションを変えるには、AnimationコンポーネントにあるAnimationを変更する。

  * シーンに配置したVantanKunを選択する。
  * Animationコンポーネント内のAnimation項目の横にある丸をクリックする。
  * アニメーション選択ウィンドウが表示されるので、その中から"Walk"を選択する。
  * 実行して、歩くアニメーションが1回だけ再生されるのを確認する。

## FBXインポート設定の調整 ##

アニメーションデータのデフォルトのインポート設定では、アニメーションは1回しか再生されないようになっている。大抵の場合、これはループされるようにしておいた方が使いやすい。

  * ProjectビューのモデルデータVantanKunを選択する。
  * FBXImporterの中にある項目"Animation Wrap Mode"を"Loop"に変更する。
  * FBXImporterのいちばん下にある"Apply"ボタンを押して、変更を適用する。
  * 実行して、ループ再生されるようになったことを確認する。

  * VantanKunに含まれているアニメーションを一通り確認する。

## アニメーションの制御 ##

### 単純に再生してみる ###

アニメーションの再生をスクリプトから制御するにはanimationプロパティを使う。

  * スクリプトAnimtionTestを新規作成する。
  * スクリプトをシーンに配置したVantanKunにドラッグ＆ドロップする。
  * 警告ダイアログ("Losing Prefab")が表示されるが、"Continue"で続ける。
  * スクリプトの中身を次のようにする。

```
function Start() {
    animation.Play("Walk");
    yield WaitForSeconds(3.0);
    animation.Play("Greet");
}
```

  * 動作確認する。

歩きアニメーションが3秒間再生されて、そのあと挨拶アニメーションに変化する。

### インタラクティブに制御してみる ###

とりあえず、キー入力されている間、歩きアニメーションが再生されるようにしてみる。

  * スクリプトの中身を次のようにする。

```
function Update() {
    if (Mathf.Abs(Input.GetAxis("Horizontal")) > 0.5) {
        animation.Play("Walk");
    } else {
        animation.Play("Idle");
    }
}
```

Play関数は、アニメーションが既に再生中の場合は何もしないので、こんな感じで毎フレーム呼んでも問題ない。

### アニメーションの切り替えを滑らかにする ###

Playの代わりにCrossFadeを使うと、アニメーションの切り替えが滑らかになる。

  * スクリプトの中身を次のようにする。

```
function Update() {
    if (Mathf.Abs(Input.GetAxis("Horizontal")) > 0.5) {
        animation.CrossFade("Walk");
    } else {
        animation.CrossFade("Idle");
    }
}
```

CrossFadeを使うと、古いアニメーションと新しいアニメーションの間でクロスフェーディングが行われる。クロスフェーディングの長さは第２引数で調整可能。デフォルトでは0.3秒に設定されている。

  * クロスフェーディングの長さを0.1秒に調整して、レスポンスが変わることを確認する。

## アニメーションの設定調整 ##

animationプロパティは、各アニメーションを格納した仮想配列のようにも使える。例えば、アニメーション"Walk"の設定を調整するには、`animation["Walk"]`のようにする。

  * スクリプトに次の内容を追加する。

```
function Start() {
    animation["Walk"].speed = 2.0;
}
```

  * 動作確認する。

歩きアニメーションの再生スピードが倍になれば成功。

再生スピード以外では、ループのモードを変更するためのプロパティWrapModeが、よく使われる。

  * Start関数の内容を次のように変更する。

```
function Start() {
    animation["Idle"].wrapMode = WrapMode.PingPong;
}
```

  * 動作確認する。

待機アニメーションがピンポン再生されるようになれば成功。


## アニメーション・レイヤー ##

Unityのアニメーションシステムにはレイヤーの概念が用意されている。レイヤーを使うと、複数のアニメーションを合成したり、再生中のアニメーションを別のアニメーションで上書きしたりできる。

### レイヤー番号 ###

レイヤーは番号によって管理される。デフォルトでは0番レイヤーが使用される。レイヤー番号は大きい方が優先される。

### アニメーションの合成 ###

  * Start関数の内容を次のようにする。

```
function Start() {
    animation["Greet"].layer = 1;
    animation.Play("Greet");
    animation["Greet"].weight = 0.5;
}
```

  * 動作確認する。

上のスクリプトでは、まず挨拶アニメーションのレイヤーを1番に設定している。その次に挨拶アニメーションの再生を開始し、最後にアニメーションの合成率を50%に変更している。

weightの変更はPlayの後に行うのがコツ（Playは常に100%で再生を開始する）。

### アニメーションの上書き ###

  * Update関数に次のような節を加える。

```
    if (Input.GetButtonDown("Fire1")) {
    	animation["Punch"].wrapMode = WrapMode.Once;
    	animation["Punch"].layer = 2;
    	animation.CrossFade("Punch", 0.1);
    }
```

  * 実行してFire1ボタン（左CTRLキー）を押してみる。

上のスクリプトでは、まずパンチアニメーションのループを無し（単発再生）に設定してから、レイヤーを2番に設定している。最後にパンチアニメーションを早めのクロスフェードで再生開始する。

このように挿し込みで再生するアニメーションはレイヤーで上書き再生すると簡単。レイヤーを使わないと、アニメーションを再生し終えてから元のアニメーションを復帰させる処理などを行わなくてはならなく、かなり面倒。

## キュー再生 ##

複数のアニメーションをキューで繋げて再生予約することができる。

  * Update関数に加えたFire1ボタンの処理を、次のように変更する。

```
    if (Input.GetButtonDown("Fire1")) {
    	animation["Punch"].wrapMode = WrapMode.Once;
    	animation["Crouch"].wrapMode = WrapMode.Once;
    	animation["Punch"].layer = 2;
    	animation["Crouch"].layer = 2;
    	animation.CrossFadeQueued("Punch", 0.1);
    	animation.CrossFadeQueued("Crouch", 0.1);
    	animation.CrossFadeQueued("Punch", 0.1);
    }
```

  * 実行してFire1ボタン（左CTRLキー）を押してみる。

パンチ・しゃがみ・パンチの順で再生されれば成功。

CrossFadeQueuedは、現在再生中のアニメーションが終了してから、指定されたアニメーションをクロスフェード再生する。なお、PlayQueuedというのも存在する。

## 階層構造の参照 ##

シーンに配置したVantanKunの下には階層構造（ボーン構造）が存在する。

  * 階層構造を開いて内容を確認する。

> ちなみに、モデルを作るときに間違えてLeftとRightが逆になってしまっている……

ボーン構造の一番上(Pelvis)にはSkinned Mesh Rendererコンポーネントが存在する。実際にモデルの描画を行っているのは、このコンポーネント。

  * Skinned Mesh Rendererの横にあるチェックボックスをクリックして、モデルの表示がOn/Offされることを確認する。

その更に下には、キャラクターのボーン構造が存在する。ひとつのボーンは、だいたいひとつの関節に対応している。

  * 実行しながら各ボーンがどのように動くか確認する。

この階層構造を使えば、任意のボーンの下にパーツやエフェクトを追加することができる。

  * Cubeを新規作成する。
  * 作成したCubeをRight Handの子の構造として追加する。
  * Inspector上でPositionを(0, 0, 0)に調整する。
  * 動作確認する。

箱が手に付いて動くようになれば成功。

例えば、頭のボーンにTriggerを付ければ、正確に頭部の当たり判定を行うことができる（ヘッドショット判定など……）。

# 課題 #

[手本](http://dl.dropbox.com/u/14572092/VgaUnity/Animation1.html)と同じものを作成する。

ポイントは以下の通り。

  * カーソルキーで左右に動く。
  * 動いている間はWalkアニメーションが再生される。
  * 止まっているときはIdleアニメーションが再生される。
  * 動いている方に向く（できれば滑らかに）。
  * Fire1ボタンでパンチが出る。

余裕があれば、[Jumpボタンでジャンプできるようにしてみる](http://dl.dropbox.com/u/14572092/VgaUnity/Animation2.html)。

来週の授業開始時に回収。

## 提出ディレクトリ ##

```
\\G-Server\Public\GP\Unity\Middleware0713
```

## ヒント ##

移動にはTransform.positionを直接いじればいい。こんな感じ。

```
Transform.position.x += Input.GetAxis("Horizontal") * speed * Time.deltaTime;
```

ジャンプには放物線の数式を使う。

ジャンプから着地した瞬間、着地のインパクトを表現するために、一瞬だけCrouchアニメーションをクロスフェード無しで再生している。