# SQL

``` sql
/*
SQL TRANING.
*/


/*
----------------------------
WITH句 について
----------------------------
SQLSERVERなどで利用可能な構文。
CTE(Common Table Expression:共通テーブル SQL99規格）と呼ばれ、再帰などに利用する。
ノードを辿るのに用いられる
*/


-- DECLARE @NODES TABLE(Id int, Location VARCHAR(10), Parent VARCHAR(10));

-- prefixを#とすると、一時テーブルとなる。##でグローバル一時テーブルになる
-- テーブルはセッション終了時に削除される。

DROP TABLE IF EXISTS #NODES;
CREATE TABLE #NODES(
     Id int,
	 Location VARCHAR(10),
	 Parent VARCHAR(10)
);

INSERT INTO #NODES
		select 1 as Id, 'Universe' as Location, '' as Parent
union all	select 2 as Id, 'Mars' as Location, 'Universe' as Parent
union all	select 3 as Id, 'Earth' as Location, 'Universe' as Parent
union all	select 4 as Id, 'America' as Location, 'Earth' as Parent
union all	select 5 as Id, 'Japan' as Location, 'Earth' as Parent
union all	select 6 as Id, 'Tokyo' as Location, 'Japan' as Parent
union all	select 7 as Id, 'Osaka' as Location, 'Japan' as Parent
;

SELECT * FROM #NODES;

DECLARE @Tagert Varchar(10);
SET @Tagert = 'Japan';

WITH LOOP_TABLE AS (

    SELECT * FROM #NODES WHERE #NODES.Location = @Tagert
    UNION ALL
    SELECT #NODES.* FROM LOOP_TABLE, #NODES
	WHERE LOOP_TABLE.Parent = #NODES.Location -- 親の定義を階層定義から取得　取得分が次のループとなる

)

SELECT * FROM LOOP_TABLE;



/*
----------------------------
UNION について
----------------------------
union は足し算と考えればよい
*/

-- 重複を許す
select 1,'test1' union all
select 1,'test1' union all
select 3,'test3'

-- 重複を許さない　重複チェックがある分、速度が遅い
select 1,'test1' union
select 1,'test1' union
select 3,'test3'


/*
----------------------------
OVER句 について
----------------------------
集約関数をOVER句とともに利用することで、
移動平均、累積集計、集計途中経過などを算出することができる。
*/
DECLARE @T1 TABLE(Center VARCHAR(10), Period VARCHAR(2),Val int);

INSERT INTO @T1
			select 'C01' as Center, '01' as Period, 1 as Val
union all	select 'C01' as Center, '02' as Period, 1 as Val
union all	select 'C01' as Center, '03' as Period, 1 as Val
union all	select 'C01' as Center, '04' as Period, 1 as Val
union all	select 'C01' as Center, '05' as Period, 1 as Val
union all	select 'C01' as Center, '06' as Period, 1 as Val
union all	select 'C01' as Center, '07' as Period, 1 as Val
union all	select 'C01' as Center, '08' as Period, 1 as Val
union all	select 'C01' as Center, '09' as Period, 1 as Val
union all	select 'C01' as Center, '10' as Period, 1 as Val
union all	select 'C01' as Center, '11' as Period, 1 as Val
union all	select 'C01' as Center, '12' as Period, 1 as Val
union all	select 'C02' as Center, '01' as Period, 1 as Val
union all	select 'C02' as Center, '02' as Period, 1 as Val
union all	select 'C02' as Center, '03' as Period, 1 as Val
union all	select 'C02' as Center, '04' as Period, 1 as Val
union all	select 'C02' as Center, '05' as Period, 1 as Val
union all	select 'C02' as Center, '06' as Period, 1 as Val
union all	select 'C02' as Center, '07' as Period, 1 as Val
union all	select 'C02' as Center, '08' as Period, 1 as Val
union all	select 'C02' as Center, '09' as Period, 1 as Val
union all	select 'C02' as Center, '10' as Period, 1 as Val
union all	select 'C02' as Center, '11' as Period, 1 as Val
union all	select 'C02' as Center, '12' as Period, 1 as Val;

-- Center毎の毎月の計上値を累計表示にする
INSERT INTO @T2
select Center, SUM(Val) OVER(Partition by Center order by Period) FROM @T1;

select * FROM @T2;

/*
----------------------------
累積値を単月値に戻す
----------------------------
LAG（対象列、何行前を取得するか、Nullの場合の値）を利用して、累積値を単月値に戻す。
*/
Select Center, Period, Val - LAG(Val,1,0) OVER(Partition by Center order by Period ASC) FROM @T2;


/*
----------------------------
Pivotについて
----------------------------
行を列に変換する。
*/

DECLARE @T4 TABLE(ID int, Country VARCHAR(10), Product VARCHAR(10),AMOUNT int);

INSERT INTO @T4
          select 1 as ID, 'Japan'   as Country, 'TV'    as Product, 100 as AMOUNT
union all select 2 as ID, 'Japan'   as Country, 'Phone' as Product, 200 as AMOUNT
union all select 3 as ID, 'America' as Country, 'TV'    as Product, 300 as AMOUNT
union all select 4 as ID, 'America' as Country, 'Phone' as Product, 400 as AMOUNT
union all select 5 as ID, 'Jamaica' as Country, 'TV'    as Product, 500 as AMOUNT
;

SELECT * FROM @T4;

select * FROM @T4
PIVOT(
SUM(AMOUNT) FOR Product IN(TV,Phone)
) AS PV;


/*
----------------------------
UnPivotについて
----------------------------
列を行に変換する。
*/
DECLARE @T5 TABLE(TV VARCHAR(10), Phone VARCHAR(10));


INSERT INTO @T5
          select 100, 200 as Amount
union all select 200, 300 as Amount
union all select 400, 500 as Amount
union all select null, 300 as Amount
union all select 200, null as Amount
union all select null, null as Amount
;

SELECT * FROM @T5;

SELECT Product,Amount FROM @T5
UNPIVOT(
Amount FOR Product IN (TV,Phone)
) AS UP;


-- カタログビューとは？
-- Pivot,Unpivotは値が変わると動的に取得することができない。
-- そのため、工夫が必要。https://www.casleyconsulting.co.jp/blog/engineer/162/



/*
----------------------------
動的SQL
----------------------------
*/
exec sp_executesql "select 1";
-- exec sp_executesql 'select 1'; -- 実行不可



/*
----------------------------
カーソル操作
----------------------------
*/


-- カーソル定義
DECLARE CUR_TABLES CURSOR FOR
  SELECT sys.tables.object_id,sys.tables.name
  FROM sys.tables
  INNER JOIN sys.objects
  ON sys.tables.object_id = sys.objects.object_id
;


DECLARE @CUR_T TABLE(object_id VARCHAR(100),table_name VARCHAR(100),ODD VARCHAR(100));

DECLARE @object_id VARCHAR(100);
DECLARE @table_name VARCHAR(100);


-- カーソルオープン
OPEN  CUR_TABLES;


-- データを格納
FETCH NEXT FROM CUR_TABLES
INTO @object_id,@table_name
;

 -- カーソルのデータが終わるまで繰り返す（この実装はややださい。WHILE 1 = 1 で無限ループさせ、FETCH_STATUSを中で確認し、FETCH文を一か所にしか登場させない実装もある。）
WHILE @@FETCH_STATUS = 0
BEGIN

	-- IFの構文はBEGIN - ENDを定義するが、省略可能
	IF (@object_id % 15) = 0
		-- BEGIN
			INSERT INTO @CUR_T VALUES(@object_id,@table_name,'fizzbuzz')
		-- END
	ELSE IF (@object_id % 3) = 0
		-- BEGIN
			INSERT INTO @CUR_T VALUES(@object_id,@table_name,'fizz')
		-- END
	ELSE IF (@object_id % 5) = 0
		-- BEGIN
			INSERT INTO @CUR_T VALUES(@object_id,@table_name,'buzz')
		-- END
	ELSE
		-- BEGIN
			INSERT INTO @CUR_T VALUES(@object_id,@table_name,'')
		-- END

	FETCH NEXT FROM CUR_TABLES
	INTO @object_id,@table_name

END

-- カーソルクローズ
CLOSE CUR_TABLES;

-- メモリを解放する
DEALLOCATE CUR_TABLES;

-- 結果の確認
SELECT * FROM @CUR_T;
```
