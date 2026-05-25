---
title: "【Flutter】3.44.0リリースの変更点まとめ"
emoji: "💻"
type: "tech"
topics: [Flutter, Dart, Google, AI]
published: true
---
![Flutter 3.44](/images/flutter_3_44_hero.png =600x)

# 記事の概要
Google I/O 2026のタイミングで、Flutter 3.44がリリースされた。今回は「新機能の派手さ」よりも、**普段の開発体験を変える**アップデートが多めの印象。

# 特に注目したいポイント
まず「ここだけは押さえておきたい」というポイントをピックアップ。

- 🤖 **Agentic Hot Reload**: AIコーディングエージェントが、起動中のFlutterアプリに自動接続してホットリロードまで面倒を見てくれる
- 🎨 **Material / Cupertino が SDK 本体から分離へ**: `material_ui` / `cupertino_ui` という独立パッケージへ移行する流れがスタート
- 📦 **Swift Package Manager がiOS/macOSのデフォルトに**: 長らくお世話になったCocoaPodsとの別れが現実的に
- ⚡ **Hybrid Composition++ (HCPP)**: Android のプラットフォームビュー(WebView/Maps等)がVulkan + SurfaceControlで大きく改善
- 🪟 **デスクトップでマルチウィンドウAPIが実験的に登場**: メンテナンスのリードがCanonicalに

# AI / Agent開発支援
今回の目玉カテゴリ。AIエージェントと一緒にFlutterアプリを作る/動かす世界に振り切ったアップデートが揃った。

- **Agentic Hot Reload**
  - 🛠 **何ができる**: AIに「このボタンの色変えて」と頼むだけで、コード修正 → 保存 → ホットリロードまでエージェントが完結。開発者は画面を見て結果を確認するだけになる。
  - 💡 **メリット**: 「AIに書かせる → 自分でリロード → 確認」というモグラ叩きが消える。フィードバックの往復が体感「分」から「秒」へ縮む。
- **[Dart / Flutter MCP Server](https://github.com/dart-lang/ai)**
  - 🛠 **何ができる**: Claude CodeやCursorなどのAIエージェントが、ローカルのDart Analyzer経由でプロジェクトの型情報・ウィジェットツリー・依存関係を理解した状態でコード操作・テスト実行できるようになる。
  - 💡 **メリット**: 「テキスト一致でコードを書き換える」レベルから「壊さないリファクタ・型安全チェック」レベルへ。ハルシネーションが減り、提案の信頼度が上がる。
- **[GenUI SDK](https://docs.flutter.dev/ai/genui/get-started)** ([`flutter/genui`](https://github.com/flutter/genui))
  - 🛠 **何ができる**: AIの応答を「テキストの壁」ではなく**本物のFlutter UI**として返せる。例: 「今週末のイベント教えて」→ チャットの中にイベントカードのリストが組み上がる、という体験を作れる。
  - 💡 **メリット**: チャット型UIが「読むだけ」から「会話の中で操作できるアプリ」へ進化する。アプリ側に仕込んだコンポーネントカタログから組み立てる方式なので、AIが書いたコードを直接実行するわけではなく**安全性も担保**される。
- **[A2UI Protocol](https://a2ui.org/)**
  - 🛠 **何ができる**: GenUIの裏で動く、AI↔アプリ間でやり取りする**UIの共通フォーマット**(v0.9)。同じプロトコルを話せば別のモデル・別の実装でも同じUIが組める。
  - 💡 **メリット**: 動的UIを作るたびに各社オレオレ仕様にならず、ベンダーロックインを避けやすい。
- **解釈型バイトコード(エフェメラルUI配信)** ※Dart 3.12時点では研究フェーズ
  - 🛠 **何ができる(将来)**: AppStore審査を挟まずに、**その場限りのUI**をアプリへ配信できるようになる構想。
  - 💡 **メリット**: A/Bテスト用UIや短期キャンペーン画面を、リリースサイクルから切り離して投入できる。
- **[Agent Skills](https://docs.flutter.dev/ai/agent-skills)**
  - 🛠 **何ができる**: 「統合テストを足して」「ローカライズを整えて」などのFlutter特有タスクを、**そのプロジェクトのお作法に沿って**エージェントに実行させられる。
  - 💡 **メリット**: 「一般的なFlutter知識でなんとなく書く」のではなく、「このリポジトリならこう書く」を踏まえた変更になる。

# Framework (Material / Cupertino)
コアの凍結が大きなトピック。個別ウィジェットは「メニュー」「カスタマイズ性」「堅牢性」の3軸で進化した。

- **Material / Cupertino のコア凍結**: 今後は `material_ui` / `cupertino_ui` という独立パッケージで配信予定(SDKの3ヶ月サイクルから解放)
- **Cupertino刷新**: `CupertinoMenuAnchor` 追加、`CupertinoSheetRoute` のスクロール・ドラッグ対応、`CupertinoFocusHalo` のスーパーエリプス対応など、iOSネイティブの感触に寄せた更新
- **新ウィジェット / コンストラクタ**: `RoundedSuperellipseInputBorder`、`SizedBox.square()` を追加
- **カスタマイズ性の拡張**: `Carousel`(無限スクロール)、`TabBar`(任意の `ScrollController`)、`NavigationRail` / `DropdownMenu` / `Stepper` / `Hero` / `MenuAnchor` / `AnimatedCrossFade` / `ModalBottomSheet` などに細かなパラメータ追加
- **堅牢性・修正**: 0x0サイズ環境でのクラッシュ防止(`EditableText` 他)、`RangeSlider` のフォーカス挙動、`DateRangePicker` のテーマ適用、`SegmentedButton` の幅配分などの不具合修正

# レンダリング (Impeller)
派手な新機能はないが、いくつかの改善が並ぶ。

- **Vulkanメモリ管理**: キャッシュ管理とGPU/CPU同期効率を改善(フレームドロップ時の挙動が安定)
- **Skiaバックエンド廃止(Android 10+)**: 起動時のシェーダーコンパイルによるジャンクを排除
- **円描画の刷新**: signed-distance function ベースに変更し、エイリアシングを解消
- **テキストレンダリング**: 非均一スケール時に双線形フィルタを使用、`TextFrame` の共有データを削減

# iOS / macOS
依存管理とXcode周りに変化が集中。

- **Swift Package Manager がデフォルト**: CLIがXcodeプロジェクトを自動移行、CocoaPodsプラグインはフォールバック扱いで警告
- **[Add-to-app(iOS)もSPM経由が推奨に](https://docs.flutter.dev/add-to-app/ios/project-setup)**: 既存iOSアプリへの組み込みも `flutter build swift-package --platform ios` で生成したSwiftパッケージを取り込む方式に。既存のCocoaPods / embedded frameworks 統合は一旦削除してから移行する必要あり(Xcode 15.0+ 必須)
- **Xcode 26 対応**: arm64 除外設定に対する警告を追加
- **SwiftPM 最小プラットフォーム不一致の診断改善**: 依存解決失敗時のメッセージを分かりやすく
- **CocoaPods × SwiftPM 競合時のガイド付きエラー**: 移行中のプロジェクトで詰まらないように
- **UIScene ライフサイクル警告**: Apple の今後の要件に備えた事前通知
- **インラインテキスト予測入力**: iOS のインライン補完をサポート
- **iOSシミュレータの偽Impellerイメージ回避策を撤去**

# Android
プラットフォームビュー周りが改善。

- **Hybrid Composition++ (HCPP)**: Vulkan + SurfaceControl で合成をOSに委譲、`--enable-hcpp` で有効化
- **Predictive Back 拡張**: `FlutterFragment` / `FlutterFragmentActivity` でも予測バックをサポート
- **Android API 36 対応**: 新しい `CheckState` API への対応
- **AGP 9 対応**: Kotlin のビルトイン化に伴うプラグイン適用の見直しが必要
- **Huawei ImageReader の不具合修正**
- **コンテンツサイジングの改善**

# Web
細かい修正が中心。

- **iframe内のスクロールイベント伝播を修正**
- **テキストスタイル同期によるIME・選択挙動の修正**
- **iOSのiframe内テキスト入力をビュー表示**
- **Safari iOS 26 のオートフィル不具合を修正**

# Desktop / Embedded
体制の変化と新APIの兆し。

- **マルチウィンドウAPI(実験的)**: ツールチップ・ポップアップ・別ダイアログウィンドウなどに対応予定
- **デスクトップのリードメンテナがCanonicalに**: Linux/デスクトップ周りの動きが活発化
- **Toyota RAV4 (2026)**: 車載インフォテイメントがFlutterで稼働
- **LG webOS SDK**: スマートTV向けSDKが近日提供予定

# ツール / DevTools
日々の開発体験に直結する改善。

- **DevTools が WASM コンパイルへ移行**: 起動・操作レスポンスが軽快に
- **Widget Previews のメモリ使用量を最大50%削減**: Dart Analysis Server 活用で効率化
- **ハードウェアキーボードの規則性警告をデフォルト無効化**
- **`TestWidgetsApp` ユーティリティ追加**: テストの定型処理を簡略化
- **スタイルガイド更新**: dot shorthand、拡張メソッドの推奨方針を整理

# Dart 3.12
Flutter 3.44に同梱されるDartのアップデート。

- **プライベート名前付きパラメータ**: `_field` を直接コンストラクタ引数として受けられる
- **プライマリコンストラクタ(実験)**: クラス宣言と同時にコンストラクタを書ける構文
- **Genkit Dart**: モデル非依存のフルスタックAIフレームワークがDart対応

# 破壊的変更
アップグレード前にチェックしておきたい変更。詳細は[公式の破壊的変更ガイド](https://docs.flutter.dev/release/breaking-changes)を参照。

- **`ExtendSelectionByPageIntent` の削除**: `ExtendSelectionVerticallyToAdjacentPageIntent` へ置き換え。
- **`TextDecoration` が `final` に**: 継承・実装不可。`TextDecoration.combine` で組み合わせるか描画レイヤで対応。`maskValue` の扱いもプラットフォーム間で統一。
- **ページ遷移ビルダーのインポートパス変更**: `CupertinoPageTransitionsBuilder` / `PageTransitionsBuilder` / `pageTransitionsTheme` の参照元が整理。`rg "CupertinoPageTransitionsBuilder|PageTransitionsBuilder|pageTransitionsTheme"` で洗い出し推奨。
- **Android: 組み込みKotlin + AGP 9 対応**: 手動で `org.jetbrains.kotlin.android` を適用しているとビルドが壊れる可能性。[Kotlin移行ガイド](https://docs.flutter.dev/release/breaking-changes/migrate-to-built-in-kotlin/for-app-developers)に沿って整理が必要。
- **iOS: [UIScene ライフサイクル](https://docs.flutter.dev/release/breaking-changes/uiscenedelegate)移行の予告**: Apple の将来要件に向けて `UISceneDelegate` への移行が推奨される。
- **iOS/macOS: SwiftPM デフォルト化に伴う依存解決の変化**: CocoaPods依存プラグインはフォールバック警告。SwiftPMと併用時は競合検出される場合あり。**CocoaPodsレジストリは2026年12月2日に読み取り専用化**されるため、Add-to-appを含めて移行は早めに。
- **`DropdownMenu` の非null性に関する変更(一部リバート)**: 3.43向けに書き換えていた場合は再調整が必要になることがある。

# まとめ
Agentic Hot ReloadとSwiftPMデフォルト化は、これからの開発スタイルに影響するので、新規プロジェクトから導入する価値がある。
Material/Cupertinoのパッケージ分離は今後も注視が必要。

# 参考
## リリース全体
- [Flutter 3.44.0 リリースノート(公式)](https://docs.flutter.dev/release/release-notes/release-notes-3.44.0)
- [What's new in Flutter 3.44(公式ブログ)](https://blog.flutter.dev/whats-new-in-flutter-3-44-b0cc1ad3c527)
- [Announcing Dart 3.12(Dart公式ブログ)](https://dart.dev/blog/announcing-dart-3-12)

## AI / Agent開発支援
- [Flutter & AI(概要ハブ)](https://docs.flutter.dev/ai)
- [Agent Skills](https://docs.flutter.dev/ai/agent-skills) / [`flutter/skills`](https://github.com/flutter/skills) / [`dart-lang/skills`](https://github.com/dart-lang/skills)
- [AI rules for Flutter and Dart](https://docs.flutter.dev/ai/ai-rules)
- [GenUI 概要](https://docs.flutter.dev/ai/genui) / [Get Started](https://docs.flutter.dev/ai/genui/get-started) / [`flutter/genui`](https://github.com/flutter/genui)
- [`genui` パッケージ(pub.dev)](https://pub.dev/packages/genui) / [`genui_a2ui` パッケージ(pub.dev)](https://pub.dev/packages/genui_a2ui)
- [A2UI Protocol(公式)](https://a2ui.org/)
- [Dart / Flutter MCP Server(`dart-lang/ai`)](https://github.com/dart-lang/ai)
- [Hot Reload(Agentic Hot Reload の前提)](https://docs.flutter.dev/tools/hot-reload)

## iOS / SwiftPM 関連
- [Add-to-app: iOS project setup](https://docs.flutter.dev/add-to-app/ios/project-setup)
- [Swift Package Manager 概要](https://docs.flutter.dev/packages-and-plugins/swift-package-manager)
- [Swift Package Manager for app developers](https://docs.flutter.dev/packages-and-plugins/swift-package-manager/for-app-developers)

## 破壊的変更 / 移行ガイド
- [Flutter Breaking Changes 一覧](https://docs.flutter.dev/release/breaking-changes)
- [Migrate to built-in Kotlin (for app developers)](https://docs.flutter.dev/release/breaking-changes/migrate-to-built-in-kotlin/for-app-developers)
- [UISceneDelegate adoption](https://docs.flutter.dev/release/breaking-changes/uiscenedelegate)
