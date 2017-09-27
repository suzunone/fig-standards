オートローダー規格
====================

> **廃止予定** - 2014-10-21現在、PSR-0は非推奨となっています。
代わりに[PSR-4]が推奨されています。

[PSR-4]：http://www.php-fig.org/psr/psr-4/

The following describes the mandatory requirements that must be adhered
to for autoloader interoperability.
以下に、オートローダーの相互利用性を確保するために守らなければならない要件について記述します。

必須要件
---------

* 完全修飾されたネームスペース、及びクラスは、次の構造を持たなければなりません
   * `\<Vendor Name>\(<Namespace>\)*<Class Name>`
* すべてのネームスペースには、トップレベルの名前空間に「ベンダー名」）が必要です。
* すべてのネームスペースには、好きなだけサブネームスペースを持つ事ができます
* 各ネームスペース区切り文字は、ファイルシステムからロードするときに`DIRECTORY_SEPARATOR`に変換されます。
* クラス名の`_`は`、ファイルシステムからロードするときに`DIRECTORY_SEPARATOR`に変換されます。ネームスペースに置いて`_`は特別な意味を持ちません。
* 完全修飾されたネームスペース、及びクラスは、ファイルシステムからロードする際、後ろに'.php'を付けてロードされます
* ベンダー名、ネームスペース、およびクラス名に使用するアルファベットは、小文字と大文字の任意の組み合わせで構いません。

例
--------

* `\Doctrine\Common\IsolatedClassLoader` => `/path/to/project/lib/vendor/Doctrine/Common/IsolatedClassLoader.php`
* `\Symfony\Core\Request` => `/path/to/project/lib/vendor/Symfony/Core/Request.php`
* `\Zend\Acl` => `/path/to/project/lib/vendor/Zend/Acl.php`
* `\Zend\Mail\Message` => `/path/to/project/lib/vendor/Zend/Mail/Message.php`

クラス名と名前空間のアンダースコア(_)
-----------------------------------------

* `\namespace\package\Class_Name` => `/path/to/project/lib/vendor/namespace/package/Class/Name.php`
* `\namespace\package_name\Class_Name` => `/path/to/project/lib/vendor/namespace/package_name/Class/Name.php`


ここで設定する規約は、苦労することなくオートローダの相互運用性を担保するための最小の規約です。

このPHP5.3でクラスを読み込む実装例、SplClassLoaderを利用してこれらの規約に準拠しているかテストをする事ができます。



実装例
----------------------

以下は、上記の規約がどのようにオートロードされているかを簡単に示す関数の例です。

~~~php
<?php

function autoload($className)
{
    $className = ltrim($className, '\\');
    $fileName  = '';
    $namespace = '';
    if ($lastNsPos = strrpos($className, '\\')) {
        $namespace = substr($className, 0, $lastNsPos);
        $className = substr($className, $lastNsPos + 1);
        $fileName  = str_replace('\\', DIRECTORY_SEPARATOR, $namespace) . DIRECTORY_SEPARATOR;
    }
    $fileName .= str_replace('_', DIRECTORY_SEPARATOR, $className) . '.php';

    require $fileName;
}
spl_autoload_register('autoload');
~~~

SplClassLoader 実装例
-----------------------------


以下のgistは、上記の規約に従っている場合に、クラスをロードできるSplClassLoaderの実装例です。

これは、この標準に準拠しているPHP5.3のクラスをロードする方法として現時点で推奨されているものです。

* [http://gist.github.com/221634](http://gist.github.com/221634)

