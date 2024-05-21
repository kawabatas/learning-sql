# learning-sql
SQLについての説明資料

[SQL (構造化クエリ言語) とは何ですか?](https://aws.amazon.com/jp/what-is/sql/)
>構造化照会言語 (SQL) は、リレーショナルデータベースに情報を格納および処理するためのプログラミング言語です。
>
>SQL 標準は、構造化照会言語 (SQL) の公式に定義された一連のガイドラインです。

[リレーショナル・データベースの世界](https://mickindex.sakura.ne.jp/database/idx_database.html) -> 紹介のみ

デモ
```sql
docker compose up -d

docker compose exec db bash

mysql -uroot -p
SHOW DATABASES;
USE myapps;
SHOW TABLES;

CREATE TABLE students (
  id int NOT NULL AUTO_INCREMENT,
  student_number int NOT NULL,
  name varchar(50) NOT NULL,
  point int NOT NULL DEFAULT 0,
  PRIMARY KEY (id)
);
SHOW CREATE TABLE students;

INSERT INTO students (student_number, name) VALUES
(101, 'Alice'),
(102, 'Bob'),
(103, 'Charlie'),
(104, 'Daisy'),
(105, 'Ellie');

SELECT * FROM students;

UPDATE students SET point=1 WHERE id=1;

DELETE FROM students WHERE id=1;
```

インデックスとは。本棚の例、Write/Read
```sql
EXPLAIN SELECT * FROM students WHERE id>3;
EXPLAIN SELECT * FROM students WHERE student_number>103;

CREATE INDEX student_number_index on students(student_number);
```
- スロークエリログ
- Spannerのマネージドコンソール(※録画停止)

トランザクション
```sql
UPDATE students SET point=10;

-- Bob -> Charlie へ 5ポイント移動
UPDATE students SET point=point-5 WHERE id=2;
UPDATE students SET point=point+5 WHERE id=3;

BEGIN;
ROLLBACK;
COMMIT;

-- t1
UPDATE students SET point=1 WHERE id=2;
-- t2
UPDATE students SET point=2 WHERE id=2;

-- t1
UPDATE students SET point=1 WHERE id=2;
-- t2
SELECT * FROM students WHERE id=2;

SHOW VARIABLES LIKE '%isolation%';

-- t1
UPDATE students SET point=1 WHERE id=2;
-- t2
SELECT * FROM students WHERE id=2 FOR UPDATE;

-- t1
UPDATE students SET point=point-5 WHERE id=2;
UPDATE students SET point=point+5 WHERE id=3;
-- t2
UPDATE students SET point=point-5 WHERE id=3;
UPDATE students SET point=point+5 WHERE id=2;
```
- [トランザクション分離レベルについてのまとめ](https://qiita.com/song_ss/items/38e514b05e9dabae3bdb)
- [Cloud Spanner を使って様々な Anomaly に立ち向かう](https://medium.com/google-cloud-jp/cloud-spanner-%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6%E6%A7%98%E3%80%85%E3%81%AA-anomaly-%E3%81%AB%E7%AB%8B%E3%81%A1%E5%90%91%E3%81%8B%E3%81%86-5132f691ccf4)
- [Cloud Spanner におけるトランザクションのロックについて](https://cloud.google.com/blog/ja/products/databases/transaction-locking-in-cloud-spanner)
- ネクストキーロック、ギャップロック

SQL特訓！
[データサイエンス100本ノック](https://github.com/The-Japan-DataScientist-Society/100knocks-preprocess) 16m3s
- LIMIT
- ORDER BY
- GROUP BY
- HAVING
- サブクエリ
- WITH
- JOIN

[SQL記述者全員が理解すべきSELECT文の論理的な処理順序のお話](https://qiita.com/k_0120/items/a27ea1fc3b9bddc77fa1)

[B TreeとB+ Treeの違い](https://christina04.hatenablog.com/entry/2017/05/17/190000)

[データ指向アプリケーションデザインを読んでLSM-treeインデックスに基づくKVSを作る](https://dasshshsd.hatenablog.com/entry/2019/12/04/000000)

[第一正規形～第五正規形までを図解](https://bbh.bz/2019/12/04/db-normalization-illustration/)

- [実はインデックスが使用されていないという罠](https://mickindex.sakura.ne.jp/database/db_optimize.html)
- [複合インデックスではwhere句のカラムの順番を合わせないといけないと思っていたが、今はそうではないみたい](https://ser1zw.hatenablog.com/entry/2023/07/12/081043)
- [複数カラムを使ってインデックスを作る場合、その順番は重要](https://docs.google.com/presentation/d/1lxB5pbPCMjOzxZEZSc-Nn06H79pfvgdFADgMb_Z37IA/edit#slide=id.g7a91e3dec5_0_165)

# (Other) References
- [How Compose works](https://docs.docker.com/compose/compose-application-model/)
- [mysql - Official Image](https://hub.docker.com/_/mysql)
