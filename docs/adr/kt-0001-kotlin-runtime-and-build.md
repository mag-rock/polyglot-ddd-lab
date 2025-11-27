# ADR kt-0001: Kotlin ランタイムとビルド管理の選定

- 日付: 2025-11-26
- ステータス: 承認
- 適用範囲: Kotlin 実装
- ナンバリング方針: 共通は `pj-000x`、言語固有は `<lang>-000x`（本ADRは Kotlin 向けで `kt-`）
- 決定者: プロジェクトチーム（学習者承認前提）

## 背景
- Kotlin で最初の実装を行い、他言語へ展開する計画。
- ツールチェーンは長期サポート(LTS)版を用い、学習・運用コストを抑えたい。
- Gradle を共通ビルド基盤とし、依存バージョンを体系的に管理したい。

## 決定
- 言語/ランタイム: Kotlin (JVM) + 最新の利用可能な LTS JDK（現時点では JDK 21 を採用）。
- ビルド/管理: Gradle (Kotlin DSL) を採用し、Version Catalogs (`libs.versions.toml`) で依存とプラグインを管理する。

## 選定理由
- LTS JDK を使うことでライフサイクルとセキュリティアップデートが明確。JDK 21 は最新の LTS であり、Kotlin/JVM で安定。
- Gradle Kotlin DSL は型安全な記述が可能で、マルチモジュール構成と相性が良い。
- Version Catalogs により依存/プラグインバージョンを一元管理し、各言語実装でも構成を揃えやすい。

## トレードオフ・懸念
- Kotlin DSL は Groovy DSL よりビルドキャッシュが効きにくいケースがあるが、型安全性と補完性を優先。
- LTS 固定により最新JDK機能の即時活用は遅れるが、安定性を優先。

## 影響範囲
- Kotlin 実装のビルドスクリプト、CI/CD 設定、依存バージョン管理方針。
- 将来の他言語実装でも、Version Catalogs のような中央集約管理を模倣する方針の基盤となる。

## 採用後の TODO
- `settings.gradle.kts` / `build.gradle.kts` に Version Catalogs を設定し、主要依存を `gradle/libs.versions.toml` に集約。
- CI で JDK 21 を用意するワークフローを追加。
- マルチモジュール構成（core, booking, pricing, billing, account, integration, api, shared 等）に合わせた共通プラグイン設定を定義。
