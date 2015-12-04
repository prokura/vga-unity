## イントロ ##
  * 自己紹介
  * Unityとは

## 箱を置く ##
  * 箱を作る。
  * スケーリング。
  * 移動。
  * ライト追加。
  * テクスチャ追加。

## 物理挙動 ##
  * Rigidbodyを追加。
  * 動かしてみる。
  * Physic Materialを追加。
  * 箱を積み上げてみる。

## 弾を撃つ ##
  * 弾を作る。
  * 物理挙動を与える。
  * プレハブ化する。
  * Gunスクリプトをカメラに追加する。
```
var bulletPrefab : GameObject;
  function Update () {
     if (Input.GetButtonDown("Fire1")) {
         var bullet : GameObject =
           Instantiate(bulletPrefab, transform.position, transform.rotation);
     } 
}
```
  * 速度を与える。
```
var bulletPrefab : GameObject; 
 function Update () {
     if (Input.GetButtonDown("Fire1")) {
         var bullet : GameObject =
           Instantiate(bulletPrefab, transform.position, transform.rotation);
         bullet.rigidbody.velocity = Vector3(0, 0, 30);
     } 
}
```
  * マウスカーソルの位置に向けて撃つようにする。
```
var bulletPrefab : GameObject;  
function Update () {
     if (Input.GetButtonDown("Fire1")) {
         var bullet : GameObject =
           Instantiate(bulletPrefab, transform.position, transform.rotation);
          var screenPoint = Input.mousePosition;
         screenPoint.z = camera.nearClipPlane;
         var worldPoint = Camera.main.ScreenToWorldPoint(screenPoint);
          var direction = (worldPoint - transform.position).normalized;
         bullet.rigidbody.velocity = direction * 30.0;
     } 
}
```

## 弾で破壊する ##
  * Bulletスクリプトを追加する。
```
function OnCollisionEnter(collision : Collision) {
     Destroy(collision.gameObject);
     Destroy(gameObject); 
}
```
  * Destructibleタグを追加する。
  * 箱にDestructibleタグを付ける。
  * タグの判定を追加する。
```
function OnCollisionEnter(collision : Collision) {
     if (collision.gameObject.tag == "Destructible") {
         Destroy(collision.gameObject);
     }
     Destroy(gameObject); 
}
```

## 追加要素 ##
  * 爆発エフェクトを作ってみよう。
  * 箱を壊すのではなく、飛ばしてみよう。