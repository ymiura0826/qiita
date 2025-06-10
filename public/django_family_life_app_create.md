---
title: 家族との生活をアシストするDjango Webアプリ開発 ── データ系技術の検証環境を見据えて
tags:
  - Python
  - Django
  - render
  - 個人開発
  - Supabase
private: false
updated_at: '2025-06-10T22:58:10+09:00'
id: 799758356954cd4df4c5
organization_url_name: null
slide: false
ignorePublish: false
---

# はじめに

日常の課題を解決しつつ、将来的にデータ系技術の検証環境として活用できるWebアプリをDjangoで構築した。  
本稿では、アプリ開発の目的、アーキテクチャ、構成方針を中心に整理する。  
詳細な実装については別記事にて紹介予定。

- GitHubリポジトリ：[family_life_project](https://github.com/ymiura0826/family_life_project)
- Webサイト：[family life](https://family-life.onrender.com/)

# 開発目的

## データ系技術の検証環境の構築

本業でデータエンジニアリング領域の業務に携わり始めたが、気軽に技術検証を行える環境がなかった。  
既存のオープンデータを扱うだけでは面白みに欠けるため、自らデータが生成される仕組みを持ったWebアプリを個人開発し、検証基盤とすることを考えた。

## 日常生活でのストレス軽減

- 乳児のミルク間隔を忘れがち  
- 買い忘れや家族間の伝達ミスが発生しやすい  
- 観光地やレストランの情報をメモし忘れて忘却する

こうした日常の小さな課題を、仕組みで解決することを目的とした。

# アーキテクチャ

本アプリは、無料かつ安定して運用できる構成と、将来的な技術検証に対応可能な拡張性を両立することを設計方針とした。

![family_life_システムアーキテクチャ.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/4097467/386aa92e-0438-40c7-869f-0351149fa934.png "システムアーキテクチャ")

## 使用技術と選定理由

### ■ [Django](https://www.djangoproject.com/)
- **概要**：PythonベースのWebフレームワーク。フォーム処理や認証機能を標準搭載したフルスタック構成。
- **選定理由**：Pythonの利用経験があり構造を理解しやすい。標準機能が充実しており学習コストが低い。

### ■ [Render](https://render.com/)
- **概要**：GitHub連携に対応し、簡単にWebアプリをデプロイできるPaaS。
- **選定理由**：無料枠で運用可能。情報が豊富で初心者にも扱いやすく、Supabaseとの親和性も高い。参考書籍に具体的なデプロイ手順が記載されていた点も決め手。

### ■ [Supabase](https://supabase.com/)
- **概要**：PostgreSQLベースのオープンソースBaaS。認証・リアルタイム通信なども提供。
- **選定理由**：PostgreSQLの検証環境として最適であり、Renderからの接続も容易。無料枠でも実用性は十分。

### ■ [LINE Messaging API](https://developers.line.biz/ja/services/messaging-api/)
- **概要**：LINE公式のメッセージ送信API。グループチャットへのPush通知にも対応。
- **選定理由**：LINEを日常的に使用しており、実用的な通知手段として最適。

なお、選定段階ではFlaskやHeroku、Firebase、LINE Notifyなども候補に挙がったが、継続性・実装容易性の観点から本構成を採用した。

# 構成方針とアプリ分割

アプリケーションは以下の設計方針に基づき構築した。

- 家族単位でのデータ共有を前提としたユーザー設計  
- Djangoアプリを機能単位（account／ikujikanri／shopping）に分割し、責務の明確化を実現  
- モデル、ビュー、通知処理の疎結合化による拡張性の確保  
- モバイルファースト・モバイルフレンドリーなUI設計

ChatGPTを活用し、以下の主要機能を実装した。

- **アカウント管理**：家族ごとの認証情報およびLINEグループIDの管理  
- **育児記録**：ミルク・排泄の登録および通知スケジュールへの自動連携 
- **買い物リスト**：テンプレート機能による項目管理と並び替え、通知との連携  
- **LINE通知**：[UptimeRobot](https://uptimerobot.com/)を利用し、定期実行処理の安定性を確保（Renderのスリープ対策）

ChatGPTとの協働開発で得られた知見や注意点、その他開発を進めるうえで躓いた点などは、別記事で整理予定である。

# データ系技術の検証と今後の展望

開発したアプリを起点としたデータを用いて、今後のは以下の技術検証を予定している。

- **Airbyte**：OSSベースのETL
- **dbt**：SQLベースでデータ変換とバージョン管理  
- **Prefect／Airflow**：ワークフロー管理・監視  
- **DataPlex／OpenMetadata**：メタデータ管理  
- **Kafka／Flink／Iceberg**：OSSベースのストリーミング基盤の構築

# 感想と振り返り

今回が初のフルスタック開発となったが、Djangoを用いて要件定義からデプロイまで一通り実施できた点は大きな収穫である。実装経験の乏しさを補う形で、設計から開発までの全体像を把握する良い機会となった。

一方で、UI設計の優先順位が後回しとなったことや、ChatGPTからの提案を鵜呑みにして外部サービスの仕様を十分に確認しなかった点は反省点であり、今後の改善対象としたい。

# 参考資料
※アフィリエイトリンクを含みます
- [Djangoのツボとコツがゼッタイにわかる本[第3版]](https://amzn.to/3Hua2Yz)  
　Djangoの概要からRenderへのデプロイ手順までを丁寧に解説。非常に実用的で参考になった。

- [いちばんやさしいGit&GitHubの教本 第3版](https://amzn.to/43EmOww)  
　図表が豊富で要点が整理されており、Git/GitHubを初めて扱う人にとってわかりやすい。

- [【GAS】LINEのgroupIDを返してくれるbot](https://qiita.com/enbanbunbun123/items/2504687e4b6c13a289db)  
　LINEグループ通知に必要なgroupID取得の自動化に役立った。

# 今後公開予定の記事

- Django×Render×Supabase構成ガイド  
- ChatGPTとの協働開発における注意点と改善策  
- データ系技術の導入と検証結果のまとめ
