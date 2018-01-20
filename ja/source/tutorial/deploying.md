<!--
To deploy an Ember application simply transfer the output from `ember build` to a web server.
This can be done with standard Unix file transfer tools such as `rsync` or `scp`.
There are also services that will let you deploy easily.
-->

Emberアプリケーションをデプロイは、`ember build`の出力したものをWebサーバーに転送するだけです。
これは、`rsync`や`scp`のような標準のUnixファイル転送ツールで行うことができます。
また、簡単にデプロイできるサービスもあります。

<!--
## Building for Deployment
-->

## デプロイ用のビルド

<!--
Before you run `ember build` for this tutorial ensure you have the environment variable `GOOGLE_MAPS_API_KEY` set on your operating system,
so that you can view [the maps we set up previously](../service).
-->

このチュートリアルで`ember build`を実行する前に、オペレーティングシステムで環境変数`GOOGLE_MAPS_API_KEY`を設定しておくことで、[前に設定したマップ](../service)を見ることができます。

<!--
For many Unix shell environments you can simply provide the key in front of the of the build command, such as:
-->

多くのUnixシェル環境では、以下のようにビルドコマンドの前にキーを指定するだけです。

```shell
GOOGLE_MAPS_API_KEY=<your key> ember build --environment=development
```

<!--
For windows environments, type the following:
-->

Windows環境の場合は、次のように実行します。

```shell
set GOOGLE_MAPS_API_KEY=<your key>
ember build --environment=development
```

<!--
We use `--environment=development` here so that Mirage will continue to mock fake data.
However, normally we would use `ember build --environment=production` which optimizes your application for production.
-->

ここではデプロイ用のビルドでもMirageでデータをモックするために`--environment=development`を使用しています。
しかし、通常は、`ember build --environment=production`を使ってアプリケーションをプロダクション向けに最適化します。

<!--
## Deploying with scp
-->

## scpによるデプロイ

<!--
You can deploy your application to any web server by copying the output from `ember build` to any web server:
-->

`ember build`の出力を任意のWebサーバーにコピーすることによって、任意のWebサーバーにアプリケーションをデプロイできます。

```shell
scp -r dist/* myserver.com:/var/www/public/
```

<!--
## Deploying to surge.sh
-->

## surge.shにデプロイする

<!--
[Surge.sh](http://surge.sh/) allows you to publish any folder to the web for free.
To deploy an Ember application you can simply deploy the folder produced by `ember build`. 
-->

[Surge.sh](http://surge.sh/)では、任意のフォルダを無料でWebに公開することができます。
Emberアプリケーションをデプロイするには、単に`ember build`によって生成されたフォルダをデプロイするだけです。

<!--
You will need to have the surge cli tool installed:
-->

Surge CLIツールをインストールする必要があります:

```shell
npm install -g surge
```

<!--
Then you can use the `surge` command to deploy your application.
Note you will also need to rename index.html to 200.html to enable Ember's client-side routing.
-->

次に、`surge`コマンドを使ってアプリケーションをデプロイすることができます。
Emberのクライアント側ルーティングを有効にするには、index.htmlの名前を200.htmlに変更する必要があります。

```shell
mv dist/index.html dist/200.html
surge dist funny-name.surge.sh
```

<!--
We chose funny-name.surge.sh but you may use any unclaimed subdomain you like or
use a custom domain that you own and have pointed the DNS to one of surges servers.
If the second argument is left blank surge will prompt you with a suggested subdomain.
-->

funny-name.surge.shを選択しましたが、まだ取得されていないサブドメインか、DNSをサージサーバに向けてカスタムドメインを使うこともできます。
2番目の引数を空白にすると、サージはサブドメインの提案と入力を促します。

<!--
To deploy to the same URL after making changes, perform the same steps, reusing
the same domain as before.
-->

同じURLに再びデプロイするには、同じドメインを再利用して同じ手順を実行します。

```shell
rm -rf dist
mv dist/index.html dist/200.html
surge dist funny-name.surge.sh
```

<!--
Note we are building with the google maps api key as shown above for UNIX platforms.
For windows you will need to set the variable according the example in the previous section.
-->

上記のUNIXプラットフォームのやり方でGoogleマップAPIキーを設定してビルドしていることに注意してください。
Windowsの場合は前のセクションの例に従って変数を設定する必要があります。

<!--
## Servers
-->

## サーバ

<!--
### Apache
-->

### Apache

<!--
On an Apache server, the rewrite engine (mod-rewrite) must be enabled in order for Ember routing to work properly.
If you upload your dist folder, going to your main URL works,
but when you try to go to a route such as '{main URL}/example' and it returns 404,
your server has not been configured for "friendly" URLs.
-->

Apacheサーバーで、Emberルーティングが正常に動作するためには、mod-rewriteを有効にする必要があります。
distフォルダをアップロードしてメインURLに行き、
'{main URL}/example'などのルートに移動しようとすると404が返ってくる場合、
サーバーの設定が必要です。

<!--
To fix this add a file called '.htaccess' to the root folder of your website.
Add these lines:
-->

'.htaccess'というファイルをWebサイトのルートフォルダに追加します。
次の行を追加します。

```text
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteRule ^index\.html$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule (.*) index.html [L]
</IfModule>
```

<!--
Your server's configuration may be different so you may need different options.
Please see the [Apache URL Rewriting Guide](http://httpd.apache.org/docs/2.0/misc/rewriteguide.html) for more information.
-->

サーバーの設定は他のオプションが必要になる場合があります。
詳細については、[Apache URL Rewriting Guide](http://httpd.apache.org/docs/2.0/misc/rewriteguide.html)を参照してください。
