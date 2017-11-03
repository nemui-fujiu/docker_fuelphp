


docker-composeにて環境構築

Docker Compse構成
```
.
├── README.md
├── docker-compose.yml 
├── nginx
├── php7
├── redis
└── www
```


環境構築


```
docker-compose up --build -d
```

fuel_appに入ってプロジェクト作成

```
docker exec -it fuel_app bash
cd /var/www/
php oil create fuel_project
```

PHP7で環境構築している関係でmigrationコマンドを実行するとエラーになるので、以下を変更
以下、1790行目の数値変換が正しく行えていないため、(int)キャストを追加する。


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