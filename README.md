
# Docker ComposeでFuelPHP + Nginx + MySQL + PHP7

docker-composeにて環境構築

## Docker Compse構成

```
.
├── README.md
├── docker-compose.yml 
├── nginx
├── php7
├── redis
└── www
```


## 環境構築


```
docker-compose up --build -d
```

Dockerコンテナのfuel_appに入る

```
docker exec -it fuel_app bash
cd /var/www/
```

* 公式ページにインストール方法が書いてあるのでそのまま実行。  
http://fuelphp.jp/docs/1.7/installation/instructions.html  
実行できない場合は以下のようにhttpsをつけて実行する。


```
curl https://get.fuelphp.com/oil | sh
oil create fuel_server
```


## generateのエラー回避について

* PHP7で環境構築している関係でmigrationコマンドを実行するとエラーになるので、以下を変更
* 以下、1790行目の数値変換が正しく行えていないため、(int)キャストを追加する。


```php:generate.php
                catch (\LogicException $e)
                {
                        throw new Exception("Unable to read existing migrations. Path does not exist, or you may have an 'open_basedir' defined");
                }

                return str_pad($last + 1, 3, '0', STR_PAD_LEFT);
        }

        private static function _update_current_version($version)
        {
```

以下のように修正

```diff:generate.php
- return str_pad($last + 1, 3, '0', STR_PAD_LEFT);
+ return str_pad((int)$last + 1, 3, '0', STR_PAD_LEFT);
```

あとは問題なく利用可能！

##  おまけ

* プロジェクト作成時はFUEL_ENVがdevelopmentになっているので、configもdevelopmentを参照している。
* db.phpのdsnのhostはdocker-compose.ymlに記載したコンテナ名を指せばOK

```php:app/config/development/db.php
return [
    // MySQL ドライバの設定
    'default' => [
        'type'           => 'pdo',
        'connection'     => [
            'dsn'            => 'mysql:host=fuel_mysql;dbname=fuel_db',
            'username'       => 'root',
            'password'       => 'fuel_db_password',
            'persistent'     => false,
            'compress'       => false,
        ],
        'identifier'   => '`',
        'table_prefix'   => '',
        'charset'        => 'utf8',
        'enable_cache'   => true,
        'profiling'      => false,
    ],
];
```
