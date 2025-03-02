# t-rex-runner

JavaScriptのゲームプログラム

[wayou](https://github.com/wayou/t-rex-runner)さんのソースで練習させてもらいました。  
GameEngineを全く使わず、純粋HTML/CSS/JSでの実装なのでかなり勉強になりました。  
実はまだ、理解できてないので、シーケンス図を作成して理解を深める予定。

wayouさん、ありがとうございます。

## シーケンス

```mermaid
sequenceDiagram
participant cook as コック
participant kitchenware1 as フライパン
participant kitchenware2 as 鍋

    cook ->>+ kitchenware1: ハンバーグを焼く
    Note over kitchenware1: 8分ほど待つ
    Note over cook, kitchenware1: 8分ほど待つ
    cook ->>+ kitchenware1: 蓋をする
    kitchenware1 -->>- cook: ふっくら
    kitchenware1 -->>- cook : 焼き上がり
    Note right of kitchenware1: 竹串を刺して透明な汁が出たら完成

    %% ### 条件分岐　###
    alt ビーフカレー
      cook ->> kitchenware2:牛肉を入れる
    else チキンカレー
      cook ->> kitchenware2:鶏肉を入れる
    end

    %% ### 条件　###
    opt 辛いもの好き
      cook ->> kitchenware2: チリペッパーを入れる
    end

    %% ### loop　& 背景色 ###
    rect rgba(255, 0, 255, 0.2)
      cook ->> kitchenware2: カレーを煮込む
      loop ときどき
        cook ->> kitchenware2: 混ぜる
      end
    end
```

```mermaid
sequenceDiagram
actor user as ユーザ
participant js as JavaScript
participant runner as Runner
participant distanceMeter as DistanceMeter
participant horizon as Horizon
participant tRex as TRex
participant gameOverPanel as GameOverPanel

user ->> js: HTTPアクセス<br/>"index.js"読込み
js ->> js: (function () {})()
Note right of js: 関数定義、定数定義<br/>CollisionBox生成x(11+7)とか
rect rgba(255, 0, 255, 0.03)
  js ->> js: onDocumentLoad()
  js ->> runner: new Runner('.interstitial-wrapper')
  Note right of runner: Singletonで生成
  runner ->> js: document.querySelector('.interstitial-wrapper')
  runner ->> runner: loadImages()
  Note right of runner: イメージ読込み。<br/>全キャラクタのイメージが一枚絵<br/>になってる。切り出して使用する
  %% --------------- Runner::init() {
  activate runner
    runner ->> runner: init()
    %% --------------- Runner::adjustDimensions() {
    activate runner
      rect rgba(0, 255, 0, 0.05)
        Note right of js: 画面サイズ調整
        runner ->> runner: adjustDimensions()
        runner ->> js: clearInterval()
        runner ->> js: getComputedStyle('.interstitial-wrapper')
        Note right of runner: paddingとか保持
        Note over runner, tRex: ↓↓↓初回起動時は通らない。
        rect rgba(0, 0, 0, 0.3)
        opt activated==true ... ゲーム中なら
          runner ->> runner: setArcadeModeContainerScale()
          Note right of runner: 画面横一杯に<br/>広げる
          runner ->> runner: updateCanvasScaling()
          Note right of runner: canvasサイズをadjust.
          runner ->> distanceMeter: calcXPos()
          runner ->> runner: clearCanvas()
          runner ->> horizon: update()
          runner ->> tRex: update()
        end
        alt (this.playing || this.crashed || this.paused)
          Note right of runner: '.interstitial-wrapper'の縦横サイズ設定
          runner ->> distanceMeter: update()
          runner ->> runner: stop()
        else else
          runner ->> tRex: draw()
        end
        opt this.crashed && this.gameOverPanel ... クラッシュしたら
          runner ->> gameOverPanel: updateDimensionss()
          runner ->> gameOverPanel: draw()
        end
      end
    deactivate runner
    %% } // --------------- Runner::adjustDimensions()
    end

    %% --------------- Runner::setSpeed() {
    activate runner
      runner ->> runner: setSpeed()
    deactivate runner
    %% } // --------------- Runner::setSpeed()

  deactivate runner
  %% } // --------------- Runner::init()
end
```
