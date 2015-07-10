##############################
MQTT as a Service sango ヘルプ
##############################

お問い合わせは GitHub の issues を使って管理しております。

何かお問い合わせがある場合は GitHub の issues に書き込みをお願いします。

基本的に 10 営業日以内には回答させて頂きます。

スタンダードプランの方で、早急に回答が必要な場合はダッシュボードに載せておりますスタンダードプラン専用のサポートメールアドレスにお問い合わせください。



REST API ドキュメント
==============================

**REST APIはスタンダードプランの方のみ使用できます**


共通
-------

ダッシュボードから事前に取得したREST API Access TokenをHTTP Headerに入れます。

::

   Authorization: Bearer<space><token>

例::

   Authorization: Bearer eyJ0eXAiOiJKBBciCOJSUAAI1


Publish
-------

::

   POST https://sango.shiguredo.jp/v1/api/publish:publish

   {
        "topic": "shirou@github/kasumi",      # 省略不可
        "payload": {                          # 省略不可。hashではなくstringでもよい
            "some": "thing"
        },
        "clientId": "client-0",              # 省略可
        "qos": 0,                            # 省略可
        "retain": False,                     # 省略可
        "will": {                            # 省略可
            "topic": "shirou@github/will",   # willがある場合省略不可
            "payload": "kasumiwill",         # willがある場合省略不可
            "qos": 0,                        # 省略可
            "retain": False                  # 省略可
        },
        "clean": True,                       # 省略可
    }

Response::

  {
    "result": "success",
    "message": "success"
  }

失敗時::

  {
    "Status": "false"
  }

Subscribe
-----------

long-polling を用いて、メッセージを受け取れます。複数のtopicをそれぞれ異なるQoSでsubscribeできます。

::

   POST https://sango.shiguredo.jp/v1/api/subscribe:pull

   {
     "clientId": "clientid-0",             # 省略不可
     "maxMessages": 2,                     # 省略不可
     "maxTimeout": 10                      # 省略可。最大10秒、デフォルト3秒
     "subscribes": [                       # 省略不可
        {
          "topic": "shirou@github/kasumi",
          "qos": 0
        },
        {
          "topic": "shirou@github/foo",
          "qos": 0
        }
     ]
   }



POSTを送信すると、応答を待ち受ける。maxMessages分だけメッセージが来たらその時点でサーバーから返答が返ってきます。複数のTopicをSubscribeしていた場合、すべてのTopicからのメッセージの合計数がこの値になったら返ってきます。

maxTimeoutで指定された秒数を超えたが、maxMessagesの数だけメッセージがなかった場合には接続が切れて、受け取れた分だけのメッセージを返ってきます。

maxTimeoutの値は以下の通りです。

- デフォルト値: 3 (秒)
- 最大値: 10 (秒)


Message
``````````

payloadはPubishで送ったデータそのままが含まれて返ってきます。

::

   {
     "receivedMessages": [
       {
         "payload": "payload....",
         "qos": 0,
         "topic": "shirou@github/kasumi"
       },
       {
         "payload": "payload222",
         "qos": 0,
         "topic": "shirou@github/kasumi"
       }
     ]
   }

