# sokol-rust

sokolというCでグラフィックス処理を行なえるライブラリ。
マルチプラットフォームらしい。

## どこで知ったのか

ファンタジーコンピュータの実装を眺めてたらこのライブラリを使った実装を見つけ、そこから知った。
ロゴからしてレトロ向けな雰囲気が漂う。

## バインディング

C/C++向けのライブラリだけど、**自動生成された**公式バインディングが存在する。

以下の4言語に対応している。

- Zig
- Odin
- Nim
- Rust

ここではRustバインディングを使ってみる。

## インストール

crates.ioには登録されていないので、GitHubのアドレスを直接指定する。

`cargo add`が使える場合は以下を実行する。

```sh
cargo add --git https://github.com/floooh/sokol-rust
```

Cargo.tomlに依存が追加される。

## とりあえず単色を表示してみる

[READMEのExamples](https://github.com/floooh/sokol-rust?tab=readme-ov-file#examples)にサンプルが色々載っている。

というよりまともなドキュメントがこれしかないように見える。(これ人によってはかなりキツいのでは...)

ということで一番上にある単色を表示するサンプルを実行してみる。
ただこのサンプルは実装に追従しておらず、`state.pass_action.colors[0].value`ではなく`state.pass_action.colors[0].clear_value`じゃないと値にアクセス出来なかった。
一応手元で動かせたバージョンを載せてみる。

```rust
use sokol::app as sapp;
use sokol::gfx as sg;
use sokol::glue as sglue;

struct State {
    pass_action: sg::PassAction,
}

static mut STATE: State = State {
    pass_action: sg::PassAction::new(),
};

extern "C" fn init() {
    let state = unsafe { &mut STATE };

    sg::setup(&sg::Desc {
        environment: sglue::environment(),
        ..Default::default()
    });

    state.pass_action.colors[0] = sg::ColorAttachmentAction {
        load_action: sg::LoadAction::Clear,
        // 色指定
        clear_value: sg::Color { r: 0.0, g: 0.0, b: 1.0, a: 1.0 },
        ..Default::default()
    };
}

extern "C" fn frame() {
    let state = unsafe { &mut STATE };

    // コメントアウトしてみると...
    // let g = state.pass_action.colors[0].clear_value.g + 0.01;
    // state.pass_action.colors[0].clear_value.g = if g > 1.0 { 0.0 } else { g };

    sg::begin_pass(&sg::Pass { action: state.pass_action, swapchain: sglue::swapchain(), ..Default::default() });
    sg::end_pass();
    sg::commit();
}

extern "C" fn cleanup() {
    sg::shutdown()
}

fn main() {
    let c_str = std::ffi::CString::new("clear").unwrap();
    let window_title: *const i8 = c_str.as_ptr();

    sapp::run(&sapp::Desc {
        init_cb: Some(init),
        cleanup_cb: Some(cleanup),
        frame_cb: Some(frame),
        window_title: window_title,
        width: 800,
        height: 600,
        sample_count: 4,
        icon: sapp::IconDesc {
            sokol_default: true,
            ..Default::default()
        },
        ..Default::default()
    });
}
```


途中にある`let g = ...`から始まる行をコメントアウトすると、こんな感じに色が変化していくようになる。

https://x.com/Comamoca_/status/1780556210393284878

## サンプルの解説

あんまり解説する要素はないけれど、一応やってみる。

### struct State

プログラム全体で持ち回る状態を定義している。
本格的なプログラムならGoのContextみたいなもうちょっと洒落たコードにしたいところ。

```rust
struct State {
    pass_action: sg::PassAction,
}

static mut STATE: State = State {
    pass_action: sg::PassAction::new(),
};
```

### 
