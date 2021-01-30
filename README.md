# ポートフォリオ紹介

[https://gadget-community.com](https://gadget-community.com)

『ガジェコミ！』はガジェット好きが集まれるSNS型のWebアプリケーションです。

簡単に言ってしまえば『Twitterクローン』に近いものなので、SNSに必要な機能を重点的に実装済み。

スマホ利用を想定しているのでモバイルからも気軽にお試し下さい！（ゲストログイン有ります）

#### トップページ
![toppage.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/d49a4b2d-00dc-3492-0b07-f61d540f7586.png)

#### レスポンシブ対応

スマホ利用も想定したUI設計。

![responsive.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/ad9c516a-6424-d08c-37c5-e565d33861b3.gif)

#### Ajax処理

ネストした要素でもAjaxに動作します。

例） 『コメントアイコンをタップ → コメント欄を表示 → 既にあるコメントに返信』

![post_comment_reply.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/bf395ffa-3d25-6d3e-a842-9a3414a1a138.gif)

例） 『コメントを削除』

![post_comment_delete.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/541375a5-43db-2963-673f-c2e448cf90cf.gif)

# ポートフォリオに使用した技術

#### 各バージョン

- Ruby 2.7.1
- Rails 6.0.3.4
- nuxt 2.14.6
- @nuxtjs/vuetify 1.11.2
- Docker 19.03.13
- docker-compose 1.27.4
- Terraform 0.14.3

#### フロントエンド
| 名称 | 説明 |
| ---- | ---- |
| Nuxt.js (SSR mode) | フロントエンドフレームワーク |
| Vuetify | UIコンポーネント |
| Firebase Authentication | JWTを用いたログイン・ログアウト |

- Vuetifyコンポーネントを導入することで、スマホ利用を想定したレスポンシブデザインを実現。

- Vuexストアでステート管理。

- 個人情報（メールアドレス・パスワード）は、外部API（Firebase Authentication）に保存する仕組みで、外部APIで発行されるJWTをSession Storageに保存してログイン・ログアウト機能を実装しました。

- 未ログイン状態でアクセスして欲しくないページ（ /users/editなど ）へのアクセス対策には、Nuxt.jsのmiddlewareを活用することで自動的にリダイレクトするようにしました。

- ログイン状態でアクセスして欲しくないページ （ログインページ, 新規作成ページ） へのアクセスも同じくリダイレクトします。

#### バックエンド
| 名称 | 説明 |
| ---- | ---- |
| Rails (API mode) | APIサーバーとして利用 |
| PostgreSQL | データベース |

- RailsはAPIサーバーとして利用しており、フロントエンドコンテナからのリクエストに対してJSONデータを返しています。

- 画像データはActiveStorage経由でS3バケットに保存。

- その他のデータはRDSに保存。

- 個人情報（メールアドレス・パスワード）は外部API（Firebase Authentication）に保存しているので、バックエンド側にセンシティブな情報を保存しない仕組みです。

#### テストコード
| 名称 | 説明 |
| ---- | ---- |
| Jest | フロントエンドテスト, Vuexストアの動作を少しだけ |
| RSpec | バックエンドテスト, バリデーションとアソシエーションのテストのみ |

- CodePipelineのTestステージで実行するテストコードです。

#### インフラ
| 名称 | 説明 |
| ---- | ---- |
| ECS Fargate | サーバーレスな本番環境, オートスケール |
| CodePipeline | CI/CD環境 |
| RDS | 本番用DB（PostgreSQL） |
| Docker, Docker-compose | コンテナ環境 |
| Github | バージョン管理 |
| Terraform | 本番用インフラをコード管理 |

- ローカル開発環境からデプロイまで一貫してDockerを使用。

- ALBを通すことで常時SSL通信化。

- CodePipelineは、 『Sourceステージ => Testステージ => Buildステージ => Deployステージ』の順で実行され、Testステージで問題が発生した場合は当該ソースでのDeployは実行されません。

- 本番環境の環境変数については SSM で管理。『システム環境変数 => Terraform => SSM => 各AWSサービス』というフローで環境変数を受け渡しています。 

#### インフラ構成図

インフラの全体像をまとめたものがこちら。
RDSにはPostgreSQLを採用し、画像を除いたデータを保管。
画像データはS3に保管しています。

![スクリーンショット 2021-01-26 19.28.33.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/4b873df7-3103-702f-2db7-273f6bd43149.png)

#### ER図

一貫性のあるテーブル名称を意識しました。

![ER_latest.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/5e990b5c-9302-9d78-26f0-c3de6a09ae25.png)

# アプリの機能紹介

### 0. 機能一覧
| 機能名 | 説明 |
| ---- | ---- |
| ユーザー機能 | 新規登録、登録内容変更、アバター登録、ログイン、ログアウト、フォロー |
| つぶやき機能 | 投稿、編集、削除、画像複数枚登録 |
| つぶやきコメント機能 | つぶやきに対してコメント投稿、コメントへのリプライ投稿、削除、画像複数枚登録 |
| つぶやきいいね機能 | つぶやきをいいねできる、マイページのいいねタブにいいねしたつぶやきを一覧表示 |
| 掲示板機能 | 質問掲示板、雑談掲示板の作成、編集、削除、画像複数枚登録 |
| 掲示板コメント機能 | 掲示板に対してコメント投稿、コメントへのリプライ投稿、削除、画像複数枚登録 |
| 私物ガジェット機能 | 登録、編集、削除、画像複数枚登録 |
| フィード機能 | つぶやき新着表示、タイムライン表示、タグフィード表示 |
| タグ管理機能 | つぶやき・掲示板・私物ガジェットにはタグを登録可能、タグをタップしてタグ詳細ページを表示、タグ詳細ページではタグを含むコンテンツを表示 |
| 検索機能 | 各コンテンツを検索可能 |
| 通知機能 | つぶやきにいいね・コメント、掲示板にコメント、他ユーザーからフォローされると通知を表示 |
| 管理者モード | Godmode のトグルスイッチをONにすると、一時的に管理者権限が有効化、各コンテンツの削除メニューが表示される |

### 1. ユーザー機能

#### 登録・編集・削除

メールアドレスとパスワードを入力するだけでアカウント作成できます。
メールアドレス確認機能は未実装。

VeeValidateを使用してフォームにバリデーションを設定しています。

![user_create.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/9365c676-c632-f40c-c4dd-ddbdaa308781.gif)


詳細設定ページで登録情報を編集できます。

![user_edit.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/ed2d448e-9eb8-0d2e-e359-80d501380078.gif)

ユーザーにはアバターを設定できます。

![user_avatar.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/7859aef6-382d-f5f1-dfaa-5d2e8775a39a.gif)

アバター画像は各コンテンツで表示されます。

![user_avatar_view.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/f5187e9a-fecf-33ba-9a65-4c5a19fa46df.png)

### 2. つぶやき機能

#### 投稿・編集・削除

つぶやき作成は即時反映されます。
![post_create.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/ed23f37a-b7e4-31ce-5142-391c8fb2e4f2.gif)

つぶやき編集は即時反映されます。

![post_edit.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/9011bbdf-29d2-8e8c-76ad-8b400fa4303f.gif)

つぶやき削除は即時反映されます。

![post_delete.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/524b41ae-9d54-4385-a9dc-8516ecf1cc60.gif)

#### タグ付け

つぶやきにはタグを複数登録できます。

![post_tag.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/c9744119-500e-926a-eefb-3ac5bde096a5.gif)

#### コメント

つぶやきにはコメントすることができます。

![post_comment_create.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/9af196ee-4fc8-5c5e-ba57-6d77bc58b370.gif)

コメントに返信できます。

![post_comment_reply.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/02edb77b-eccc-2c06-6c92-6ffad73842da.gif)

コメントは削除のみ可能です。編集機能はありません。
親コメントを削除すると子コメントも削除されます。

![post_comment_delete.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/7f2ca317-b031-8ec2-7526-7929527b1d2a.gif)

#### いいね

つぶやきにいいねできます。

![post_like.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/93615f3a-f987-6a89-3b8f-5d9531dc48ff.gif)

### 3. 掲示板機能

#### 作成・編集・削除

掲示板作成は即時反映されます。

![board_create.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/4decc163-1f94-1c88-2245-6a77c68fea3e.gif)

掲示板編集は即時反映されます。

![board_edit.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/3d0fc81a-c490-ef01-c3a1-94a561d67dcb.gif)

掲示板削除は即時反映されます。

![board_delete.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/9ab687d6-18f1-bd52-98ce-4e55740d1063.gif)

#### タグ付け

掲示板にはタグを複数登録できます。

![board_tag.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/17db3839-b6c3-3d05-6285-107e470b75cf.gif)

#### 掲示板タイプ

『雑談』『質問』を選択できます。

![board_type.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/a6220c6a-4c42-70ea-41a4-cc5eecb2f418.gif)

#### コメント

掲示板にはコメントすることができます。

![board_comment.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/ad3be049-a611-9809-80bc-edb5bbf42326.gif)

コメントに返信できます。

![board_comment_reply.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/1456c34b-c0bf-72e4-6e4f-360b6769e1a1.gif)

コメントは削除のみ可能です。編集機能はありません。
親コメントを削除すると子コメントも削除されます。

![board_comment_delete.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/b8a87e3f-f82b-0818-814a-795a8fd12777.gif)

### 4. 私物ガジェット機能

#### 登録・編集・削除

自分の私物ガジェットやお気に入りアイテムを登録できます。
登録したガジェットは一覧ページでは『みんなのガジェット』として新着表示されます。

![gadget_create.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/a61f5080-4a0b-94b6-3070-3d3ce2bcc441.gif)

ガジェット編集は即時反映されます。

![gadget_edit.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/0d4f2f5b-eb13-68d8-bd01-f85dbf3b1f87.gif)

ガジェット削除は即時反映されます。

![gadget_delete.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/3371f318-6be2-0be4-226a-9d933e072a05.gif)

#### タグ付け

私物ガジェットにはタグを複数登録できます。

![gadget_tag.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/eb1235f3-a0c9-5df7-17a5-fe5368de4000.gif)

### 5. フィード機能

つぶやきを一覧表示します。
無限スクロールで順次読み込みます。

＜タブによって表示する内容が異なります＞

- 新着順 => 全てのつぶやきを新着順で表示
- タイムライン => フォロー済みユーザーのつぶやきのみ新着順で表示
- タグフィード => フォロー済みタグを含むつぶやきのみ新着順で表示

![feed_new.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/17205179-4ec2-0213-e85d-b5f353ecb2d6.gif)

### 6. タグ管理機能

タグをフォローすることができます。
タグフィードに表示したいタグをフォローします。

![tag_follow.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/c52e25f3-04e8-cf4a-2fdb-5f426eb8fe80.gif)

つぶやき・掲示板・私物ガジェットはタグ付けすることができます。
タグをタップするとタグ詳細ページに遷移し、タグを含むつぶやき・掲示板・私物ガジェット・フォローしているユーザーの一覧を表示します。

![tag_show.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/de80ed94-7a9a-3539-5bd0-289f736e1bec.gif)

### 7. 検索機能

検索ページでは、検索フォームに入力した内容に合わせて結果がリアルタイムに表示される機能を実装しております。

![search.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/a1a27342-7bee-e1e0-9fef-70d8e7c677ab.gif)

### 8. 通知機能

つぶやき・掲示板・ユーザーフォローにおいて、イベントが発生すると通知が表示されます。

#### 新着通知

新しい通知が発生すると、ヘッダー内のベルアイコンにバッジが表示されます。
タップすると新しい通知がリスト表示され、通知をタップするとイベント発生元へ画面遷移します。

![notices.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/8112aa0c-0e04-1bb8-458d-73fdf5f3c300.gif)

##### 通知一覧

通知一覧をタップすると一覧ページへ画面遷移します。
通知一覧ページでは、今まで受け取った全ての通知を表示します。

![notices_index.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/76777320-8764-9c52-a9ff-838322ac8478.gif)

### 9. ゲストユーザー機能

ユーザー専用機能を簡単に利用できるように、ゲストユーザーログイン機能を実装しています。

『ゲストユーザーとしてログイン』をタップするだけです。

![guest_loggin.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/a8a6ef72-8463-bc11-c726-3f51cf1adfef.gif)

未ログイン状態でユーザー専用機能にアクセスすると、ゲストユーザーログインを促すダイアログを表示するようにしております。

![guest_loggin_support.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/51983e7b-5c7b-fa73-e7ee-4f913ddfc927.gif)

### 10. God mode

一時的に管理者権限を有効にするモードです。（管理者という概念が存在することを確認して頂くためのモードです。）

DB内 の Userモデルの adminカラム の true/false で 各ユーザーの管理者権限を管理しておりますが、God modeではVuexストアの値を一時的に変更して管理者権限を有効化しています。（DB上には保存されないのでブラウザ再読み込みでfalseに戻ります。）

つぶやき・掲示板・私物ガジェットにはそれぞれ『管理メニュー』が設定されており、作成者本人にのみ表示される仕組みです。

![スクリーンショット 2021-01-29 21.04.32.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/62c2cfb0-72f9-7397-4d54-a369c5cf54a1.png)

God mode を有効化にすると他のユーザーが作成したコンテンツでも『管理メニュー』を表示するようになり、『削除のみ』実行可能となります。

![admin_mode.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/290888/c2654eac-2261-cbe4-072c-fbda8c81234c.gif)