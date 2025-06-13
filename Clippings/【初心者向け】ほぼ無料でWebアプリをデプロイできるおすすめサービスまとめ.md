---
title: "【初心者向け】ほぼ無料でWebアプリをデプロイできるおすすめサービスまとめ"
source: "https://qiita.com/pam5596/items/feab853f3a62a3d3f0a0"
author:
  - "[[pam5596]]"
published: 2025-06-06
created: 2025-06-14
description: "はじめに 自分が開発したwebアプリを誰かに見てもらいたいときや使って欲しいときがあると思います。せっかく作ったので、どうせならデプロイしちゃいましょう！ 近年いろんなデプロイサービスが提供されており、無料枠の制限もさまざまです。無料枠でデプロイしてしばらく経ったとき、気..."
tags:
  - "clippings"
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=449764)

## はじめに

自分が開発したwebアプリを誰かに見てもらいたいときや使って欲しいときがあると思います。せっかく作ったので、どうせならデプロイしちゃいましょう！

近年いろんなデプロイサービスが提供されており、無料枠の制限もさまざまです。無料枠でデプロイしてしばらく経ったとき、気づいたら課金されてしまっていることも...

今回は私が普段使っているデプロイサービスやクラウドサービスを紹介したいと思います。

若干個人的観点に寄っているところがあるので、ご指摘あればコメントで教えてください！お願いします！

## 1\. GitHub Pages

GitHubが提供する静的Webホスティングサービスです。リポジトリのプロジェクトがそのままデプロイされるので、めっちゃ簡単です。基本無料で利用できます。

| 項目 | 概要 |
| --- | --- |
| 利点 | フロントエンドだけをデプロイするのに向いている |
| 対応技術 | `HTML & CSS & JavaScript` |
| 制限 | 容量1GBまで   バックエンド（API）をデプロイできない |

基本的に `HTML & CSS & JavaScript` を使ったフロントエンドをデプロイする際に優秀です。 `React`, `Vue` でビルドしたものもデプロイ可能です。

## 2\. Render.com

フルスタック対応のクラウドホスティングサービスです。こちらもGitHubリポジトリを紐づけることで、リポジトリのプロジェクトをそのままデプロイできます。こちらは無料＆有料プランがありますが、無料でも非常に自由度が高いです。

| 項目 | 概要 |
| --- | --- |
| 利点 | フロントエンド・バックエンド・データベースなど様々な領域をデプロイできる |
| 対応技術 | `HTML & CSS & JavaScript`, `Node.js`, `Python`, `Ruby`, `Go`, `Rust`, `Elixir`, `Docker`, `PostgreSQL` |
| 無料プラン制限 | 15分間アクセスがないと自動的にスリープ状態になる   月750時間まで稼働可能   PostgreSQLは1GBまで |
| `Socket.io` | ⭕️ |

技術枠がめちゃくちゃ幅広いデプロイサービスです。唯一の難点は15分間ノーアクセスでスリープされる機能ですが、 ~~これは `AWS Lambda` と `AWS EventBridge` を使うことで解決できます。~~ 真似しないように。

また、ホスティングサービスなので、今回紹介している中では唯一 `Socket.io` を組み込めるサービスになります。ぜひ活用してください。

## 3\. Vercel

`Next.js` の開発元が提供しているフロントエンド向けに設計されたクラウドプラットフォームです。こちらもGitHubリポジトリを紐づけることで、リポジトリのプロジェクトをそのままデプロイできます。こちらは基本無料です。

| 項目 | 概要 |
| --- | --- |
| 利点 | 特定のフレームワークを使ったWebアプリのデプロイに向いている |
| 対応技術 | `Angular`, `React`, `Vue`, `Svelte`, `Next.js`, `Nuxt.js`, `Remix`, `SvelteKit`, `Vite` など |
| 無料プラン制限 | `API Router` の最大リクエスト時間が **10秒**   `API Router` へのデータサイズ上限 **4MG** |
| `Socket.io` | フロントでは⭕️、 `APIRouter` では❌ |

こちらは対応技術に書いてあるような、同一の言語（ `JavaScript` や `TypeScript` ）を使ったフレームワークのデプロイに適しています。

`Next.js` や `Nuxt.js` は `React` や `Vue` を使ったフロントエンド、 `API Router` を使ったバックエンドを同じプロジェクト内で実装できるフレームワークです。 `Next.js` は `React` 、 `Nuxt.js` は `Vue` に対応しています。

さらに注意して欲しい点は、 **API Routerのリクエストデータの上限が4MG** という点です。これに引っかかるケースとしては、 **画像や動画などのファイルデータの送信** があります。

多少リスクはありますが、フロントエンド側で送信先の外部サーバのクライアントを作成し、フロントエンド側でファイルデータを送信すれば解決できます。

app.tsx（AWS S3へ保存したい場合）

```tsx
import { S3Client, PutObjectCommand } from '@aws-sdk/client-s3';
import { getSignedUrl } from '@aws-sdk/s3-request-presigner';

/// Reactの場合、コンポーネント内でクライアントを作ってあげる ///
const s3 = new S3Client({
  region: 'ap-northeast-1', // AWS東京リージョン
  credentials: {
    accessKeyId: process.env.AWS_ACCESS_KEY_ID, // AWSのアクセスキー
    secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY, // AWSのシークレットキー
  },
});

/// 以下コンポーネント ///
export default function ComponentName() {
    // ...
    const putFileToS3 = () => {
        const command = new PutObjectCommand({
            Bucket: bucketName, // バケット名
            Key: filename // ファイルの保存先プレフィックス
          });
        
        const url = await getSignedUrl(s3, command);
    }
    ///...
    
    return (
        // ...
    )
}
```

## 4\. AWS S3

AWS（Amazon Web Service）が提供するクラウドストレージサービスです。イメージとしてはGoogleDrive、OneDriveに近いです。ファイルデータ（オブジェクト）を保存することができるのは同様ですが、バケットごとにURLを割り当てることができるため、 `HTML` にオブジェクトURLを割り当てることでURLを介して `HTML` を見ることができます。

| 項目 | 概要 |
| --- | --- |
| 利点 | フロントエンドのデプロイに向いている |
| 対応技術 | `HTML & CSS & JavaScript` |
| 制限 | 特になし |
| 難点 | GitHubとの連携をしたい場合、GitHubActionを使う必要がある |

こちらも料金が発生しますが、 **保存容量が5GBを少し超えるぐらいでやっと1円/月** なのでとても安価です。また、 **AWS CloudFront** と併用することで、独自ドメイン + HTTPS対応が可能となり、さらに高速かつ安全なWebサイトを構築できます。

## 5\. AWS Lambda - Serverless（中級者向け）

## AWS Lambdaとは

AWS（Amazon Web Service）が提供するサーバー不要でソースコード関数を実行できるサービスです。要は特定のソースコードをここに登録しておけば、外部からLambdaのAPIを叩いたり、他のAWSサービスと連携したりして同じソースコードを好きな用途で使うことができます。

こちらには毎月無料枠と **使用量に応じた有料枠が課せられます** が、月100万回の実行+さらに32,500回でようやく1円を超過するレベルなので本当にスズメの涙です。

## Serverless Frameworkとは

アプリケーションをLambdaの関数として扱い、サーバーレスアプリケーションを簡単に構築・デプロイ・管理できるフレームワークです。使用するには専用のCLIをインストールする必要があります。こちらは基本無料です。

| 項目 | 概要 |
| --- | --- |
| 利点 | バックエンドのデプロイに向いている |
| 対応技術 | `Node.js`, `Python`, `Ruby`, `Java`, `Go`, `C#`, `PowerShell` |
| 制限 | 特になし |
| 難点 | アプリの規模が大きくなると複雑化しやすい   AWSのサービス制限に依存する   IAMなどCLIのポリシー設定が必要になる |
| `Socket.io` | ❌ |

こちらはバックエンドのデプロイに適しているサービスですが、CLIの扱い方など背景理解が必要な点がいくつかあり、初心者には少し難しい印象です。

しかし前に紹介したようなデプロイサービスより大きな制限もないので中規模のアプリケーションにおすすめです。

また、AWS APIGateWayでのパスルーティングやAWS DynamoDBとのデータベース連携にも対応しているのでインフラをAWSで完結できる点は大きいです。

## 6\. AWS ElasticBeanstalk（おまけ）

AWS（Amazon Web Service）が提供するアプリケーション自動デプロイ＆運用管理サービスです。AWS EC2という仮想サーバーサービスを使ってプロジェクトのファイルを`.zip` に圧縮してアップロードするだけで自動的にアプリケーションをデプロイしてくれるサービスです。使用するには専用のCLIが必要になります。

| 項目 | 概要 |
| --- | --- |
| 利点 | フロントエンド・バックエンド・データベースなど様々な領域をデプロイできる |
| 対応技術 | `Node.js`, `Python`, `Ruby`, `Java`, `Go`, `PHP`, `Docker` など |
| 制限 | 特になし |
| 難点 | 細かいインフラ設定に対しては柔軟性が劣る   sklearnなどの巨大ライブラリを使うアプリではサイズ不足   IAMなどCLIのポリシー設定が必要になる |
| `Socket.io` | ⭕️ |

こちらはrender.comのように対応する技術幅の広いサービスですが、CLIの扱い方など背景理解が必要な点がいくつかあり、初心者には難しいです。

**AWS EC2への直接的なデプロイも可能** ですが、初心者にはとても難しいため、AWS EC2へチャレンジしようという方は初めに触っていると良いのではないでしょうか。

また、AWS EC2はAWS Lambdaのような実行回数ではなく、 **使用時間に応じて料金が課せられるため、高額になりやすい** ケースがあります。新規AWSアカウント作成から12ヶ月以内 & 最も小さいサーバモデル（t2.micro）なら月750時間以内の稼働なら無料ですが、それ以降は **月1000円弱** かかります。

しかしその分、他AWSサービスとの連携や、カスタムドメインの割り当て、SSH認証にも対応しておりかなり実践的にインフラを構築できます。難易度の高い領域へのチャレンジに投資しても良いかもしれません。

## まとめ：フロント、バックおすすめの組み合わせ

今回は私がいろいろ比較検証した結果、ローコストでデプロイが実現できるサービスを紹介しました。ご参考までに。

以上紹介したものを組み合わせることで、いろんな選択肢が見えてくると思います。最後にざっくりまとめておきます。

## フロントエンド

| 言語 | フレームワーク等 | +αでおすすめ | おすすめサービス |
| --- | --- | --- | --- |
| `HTML & CSS & JavaScript` | \- | `AWS Cloudfront` | [AWS S3](https://qiita.com/pam5596/items/#4-aws-s3)   [GitHub Pages](https://qiita.com/pam5596/items/#1-github-pages) |
| `JavaScript`, `TypeScript` | `React`,`Vue` | `Vite` | [Vercel](https://qiita.com/pam5596/items/#3-vercel) |
| `JavaScript`, `TypeScript` | `Next.js`, `Nuxt.js` | \- | [Vercel](https://qiita.com/pam5596/items/#3-vercel) |

- スピード重視なら `GitHub Pages` 、凝りたいなら `AWS S3 + AWS CloudFront` で最適化してみよう
- `Next.js` 、 `Nuxt.js` は軽量なバックエンドも構築できる点が有能
- `React` の場合、 `create-react-app` は非推奨のため `Vite` がおすすめ

## バックエンド

| 言語 | フレームワーク等 | +αでおすすめ | おすすめサービス |
| --- | --- | --- | --- |
| `Python` | `FastAPI`, `Flask` | `pydantic`, `Mangum` | [Render.com](https://qiita.com/pam5596/items/#2-render-com)   [AWS Lambda - serverless](https://qiita.com/pam5596/items/#5-aws-lambda---serverless%E4%B8%AD%E7%B4%9A%E8%80%85%E5%90%91%E3%81%91) |
| `JavaScript`, `TypeScript` | `express`, `hono` | `zod` | [AWS Lambda - serverless](https://qiita.com/pam5596/items/#5-aws-lambda---serverless%E4%B8%AD%E7%B4%9A%E8%80%85%E5%90%91%E3%81%91) |
| `Ruby` | `Rails` | \- | [Render.com](https://qiita.com/pam5596/items/#2-render-com) |
| `PHP` | `Laravel` | `Bref` | [AWS Lambda - serverless](https://qiita.com/pam5596/items/#5-aws-lambda---serverless%E4%B8%AD%E7%B4%9A%E8%80%85%E5%90%91%E3%81%91) |
| `Go` | `gin` | \- | [AWS Lambda - serverless](https://qiita.com/pam5596/items/#5-aws-lambda---serverless%E4%B8%AD%E7%B4%9A%E8%80%85%E5%90%91%E3%81%91) |

- `Python` では、機械学習アルゴリズムやライブラリを導入するなら `Render.com` 、軽量APIなら `AWS Lambda - serverless` がおすすめ
- `Render.com` は `sklearn` などの重量系ライブラリに対応できる
- `Python` で型を補完するライブラリには `pydantic` がおすすめ
- `express` や `hono` でのバリデーションライブラリは `zod` などがおすすめ

## まとめ

今回は私がいろいろ比較検証した結果、ローコストでデプロイが実現できるサービスを紹介しました。ご参考までに。

[2](https://qiita.com/pam5596/items/#comments)

[209](https://qiita.com/pam5596/items/feab853f3a62a3d3f0a0/likers)

227