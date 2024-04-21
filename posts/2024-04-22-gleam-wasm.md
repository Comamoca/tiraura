# gleam-wasm

Gleamコンパイラをwasmで動かしてみたいのでその試行錯誤を書いていく。バージョンはv1.1.0。

## RustのWasmコンパイル

言わずもがなRustはWasmにもコンパイルできる。その手順をざっくりまとめると、

- wasm-pack buildする
- 生成されたwasmファイルとそのラッパーであるJSファイルが生成される
- それらを呼び出す

こんな感じ。簡単そうに書いたけど、これが何気に難しい。

## GleamとWasm

GleamはWasmでの実行にも対応している。
主なユースケースとしては、Gleam公式のLanguage tourとかGleam Playgroundとか。
ただ、Gleam Playgroundは更新が2年前から途絶えていて最新の構文が使えない。(それをなんとかするために調べている面がある)

ソースは[公式リポジトリ](https://github.com/gleam-lang/gleam/blob/main/compiler-wasm)の`compiler-wasm`ディレクトリにある。

## ビルド

とりあえず`wasm-pack build --target web`をしてみた。
すんなりビルドできたのでひとまず安心。

## 呼び出し

呼び出すには`gleam_wasm.js`を読み込む。

バージョンアップで関数などが色々変更されたらしく、バージョンを上げただけではGleam Playgroundのコードは動かなかった...残念
でも呼び出す大枠は変わっていないっぽい。大体こんな感じで呼び出す。


- `gleam_wasm.js`を`import`で読み込む
- `init`関数を呼び出し初期化する
  この関数は関数が含まれたオブジェクトを返すので、必要に応じて変数に格納する

眠いのでとりあえずここまで。明日の授業中にでも更新します。
