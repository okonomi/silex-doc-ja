テンプレートでのアセット管理
=================================

Silexアプリケーションはウェブルートディレクトリに配置されているとは限りません。
別のページへのリンクを作成する際に、何度もベースパスを記述することを避けるために、 :doc:`URL generator service provider
</providers/url_generator>` を使用することが強く推奨されます。

しかし、画像やスタイルシート、JavaScriptファイルはどのようにすればいいのでしょうか。
これらのURLはSilexのルーターによっては管理されません。しかし、これらのリソースにもベースパスによるプレフィックスが必要です。幸運にもリクエストオブジェクトは、我々がプレフィックスとして欲しいアプリケーションベースパスを保有しています。 ::

    // /css/styles.cssにあるスタイルシートへのリンク
    $request->getBasePath().'/css/styles.css';

Twigテンプレートでは同じことを、より簡単な方法で実行できます。

.. code-block:: jinja

    {{ app.request.basepath }}/css/styles.css

あなたのアセットが異なるホスト下にある場合は、Silexのパラメータを設定することで、パスを抽象化してください。 ::

    $app['asset_path'] = 'http://assets.examples.com';

これはテンプレートでは以下のようにして使うことが出来ます。

.. code-block:: jinja

    {{ app.asset_path }}/css/styles.css

アセットとは独立なロジックが必要なら、文字列の代わりにサービスを定義してください。 ::

    $app['asset_path'] = $app->function () {

        // アセットへのパスを決定するのに必要なロジックをここに実装する。
    
        return 'http://assets.examples.com';
    };

これも、先ほどと同様に使用することができます。

.. code-block:: jinja

    {{ app.asset_path }}/css/styles.css

もし、アセットの場所がアセットのタイプやパスに依存する場合、さらなる抽象化が必要です。以下にTwig関数を使って、それを行った例を示します。 ::

    $app->extend('twig', function($twig, $app) {
        $twig->addFunction(new \Twig_SimpleFunction('asset', function ($asset) {
        // アセットへのパスを決定するのに必要なロジックをここに実装する。

            return sprintf('http://assets.examples.com/%s', ltrim($asset, '/'));
        }));

        return $twig;
    });

``asset`` 関数は以下のようにして使用可能です。

.. code-block:: jinja

    {{ asset('/css/styles.css') }}


commit: fc8bbb623f33ce448c8bf1d4a95aa26360032de1
original: https://github.com/silexphp/Silex/blob/master/doc/cookbook/assets.rst
