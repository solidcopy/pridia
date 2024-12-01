################
データベース設計
################

===============
物理データ型
===============

エンティティ設計の論理データ型とPostgreSQLのデータ型の対応を決める。

.. list-table::
   :header-rows: 1
   :widths: 50 50

   * - 論理データ型
     - 物理データ型
   * - ラベル
     - varchar(n)
   * - テキスト
     - text
   * - 日付
     - date
   * - 真偽
     - boolean
   * - 整数
     - integer
   * - 画像
     - bytea
   * - ロジック
     - text

==============
テーブル定義
==============

出来事 (events)
*****************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - activity_id
     - bigint
     - 活動に関する出来事のみ設定あり。
   * - title
     - varchar(1024)
     - 必須。
   * - occured_at
     - date
     - 必須。
   * - content
     - text
     - 活動したことだけ記録する場合があるため任意。

出来事写真 (event_pictures)
******************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - event_id
     - bigint
     - 出来事テーブルの参照。
   * - position
     - integer
     - 必須。同じ出来事で重複せず0からの連番であること。
   * - title
     - varchar(1024)
     -
   * - mime_type
     - mime_types
     - 必須。
   * - picture
     - bytea
     - 必須。

所有物 (items)
*******************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - name
     - varchar(1024)
     - 必須。
   * - own_status
     - own_status
     - 必須。
   * - description
     - text
     -
   * - obtain
     - bigint
     - 入手した出来事の参照。
   * - purchase
     - bigint
     - 購入した取引明細の参照。
   * - abandon
     - bigint
     - 放棄した出来事の参照。
   * - sale
     - bigint
     - 売却した取引明細の参照。

活動 (activities)
********************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - name
     - varchar(1024)
     - 必須。
   * - description
     - text
     -
   * - activity_status
     - activity_status
     - 必須。

活動タグ (activity_tags)
****************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - parent_activity_tag_id
     - bigint
     - 親タグの参照。継承関係は循環してはならない。
   * - name
     - varchar(1024)
     - 必須。

活動タグ - 活動 (activity_tags_activities)
*********************************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - activity_id
     - bigint
     - 必須。活動の参照。
   * - activity_tag_id
     - bigint
     - 必須。タグの参照。

重複したレコードがあってはならない。

取引 (trades)
*****************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - subscription_id
     - bigint
     -
   * - occured_at
     - date
     - 必須。
   * - update_required
     - boolean
     - 必須。

取引明細 (trade_details)
****************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - trade_id
     - bigint
     - 必須。取引の参照。
   * - position
     - integer
     - 必須。同じ取引内で重複せず0からの連番であること。
   * - item
     - varchar(1024)
     - 必須。
   * - quantity
     - integer
     - 必須。電子書籍のような無形物は1個と数える。
   * - subtotal
     - integer
     - 必須。出費を表すため、売却益は負数で表す。
   * - remarks
     - text
     -

取引明細タグ (trade_detail_tags)
*************************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - parent_trade_detail_tag_id
     - bigint
     - 親タグの参照。継承関係は循環してはならない。
   * - name
     - varchar(1024)
     - 必須。

取引明細タグ - 活動 (trade_detail_tags_trade_details)
*******************************************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - trade_detail_id
     - bigint
     - 必須。取引明細の参照。
   * - trade_detail_tag_id
     - bigint
     - 必須。タグの参照。

重複したレコードがあってはならない。

定期購入 (subscriptions)
***************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - name
     - varchar(1024)
     - 必須。
   * - subscription_status
     - subscription_status
     - 必須。
   * - payment_timing_id
     - bigint
     - 必須。支払タイミングの参照。
   * - payment_timing_parameters
     - jsonb
     - 支払タイミングのパラメーター。

定期購入タグ (subscription_tags)
*************************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - parent_subscription_tag_id
     - bigint
     - 親タグの参照。継承関係は循環してはならない。
   * - name
     - varchar(1024)
     - 必須。

定期購入タグ - 定期購入 (subscription_tags_subscriptions)
***********************************************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - subscription_id
     - bigint
     - 必須。定期購入の参照。
   * - subscription_tag_id
     - bigint
     - 必須。タグの参照。

重複したレコードがあってはならない。

支払タイミング (payment_timings)
***********************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - name
     - varchar(1024)
     - 必須。
   * - next_payment_day_logic
     - text
     - 必須。Javascript関数nextPaymentDayを定義する。

単発作品 (standalone_artworks)
**********************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - title
     - varchar(1024)
     - 必須。
   * - expression_form_id
     - bigint
     - 必須。表現形式の参照。

連続作品 (series_artworks)
**********************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - title
     - varchar(1024)
     - 必須。
   * - expression_form_id
     - bigint
     - 必須。表現形式の参照。

表現形式 (expression_forms)
******************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - name
     - varchar(1024)
     - 必須。

単発作品鑑賞 (standalone_appreciations)
******************************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - standalone_artwork_id
     - bigint
     - 必須。単発作品の参照。
   * - media_id
     - bigint
     - 必須。
   * - appreciated_at
     - date
     - 必須。
   * - comment
     - text
     -

連続作品鑑賞 (series_appreciations)
****************************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - appreciation_period_id
     - bigint
     - 必須。鑑賞期間の参照。
   * - media_id
     - bigint
     - 必須。
   * - appreciated_at
     - date
     - 必須。
   * - comment
     - text
     -

媒体 (media)
****************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - name
     - varchar(1024)
     - 必須。

鑑賞期間 (appreciation_periods)
*********************************

.. list-table::
   :header-rows: 1
   :widths: 30 20 50

   * - 列名
     - データ型
     - その他の制約、備考など
   * - series_artwork_id
     - bigint
     - 必須。連続作品の参照。
   * - appreciation_status
     - appreciation_status
     - 必須。
   * - comment
     - text
     -

======
列挙型
======

所有状態 (own_status)
*****************************

.. list-table::
   :header-rows: 1
   :widths: 50 50

   * - 定数
     - 説明
   * - own
     - 所有
   * - not_own
     - 不所有

MIMEタイプ (mime_types)
*****************************

.. list-table::
   :header-rows: 1
   :widths: 50 50

   * - 定数
     - 説明
   * - png
     -
   * - jpeg
     -
   * - webp
     -

活動状態 (activity_status)
*****************************

.. list-table::
   :header-rows: 1
   :widths: 50 50

   * - 定数
     - 説明
   * - new
     - 未着手
   * - in_progress
     - 進行中
   * - suspended
     - 休止中
   * - completed
     - 完了
   * - canceled
     - 中止

定期購入状態 (subscription_status)
**************************************

.. list-table::
   :header-rows: 1
   :widths: 50 50

   * - 定数
     - 説明
   * - in_use
     - 利用中
   * - not_in_use
     - 利用停止中

鑑賞状態 (appreciation_status)
**********************************

.. list-table::
   :header-rows: 1
   :widths: 50 50

   * - 定数
     - 説明
   * - on_going
     - 継続中
   * - completed
     - 完了
   * - canceled
     - 中止
