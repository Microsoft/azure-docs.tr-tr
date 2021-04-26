---
title: Apache Hive-Azure HDInsight ile Twitter verilerini çözümleme
description: Ham TWitter verilerini aranabilir bir Hive tablosuna dönüştürmek için HDInsight üzerinde Apache Hive ve Apache Hadoop kullanmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: 74a18be37608b2ac0c8a64655fa833753111706a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942777"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>HDInsight üzerinde Apache Hive ve Apache Hadoop kullanarak Twitter verilerini çözümleme

Twitter verilerini işlemek için [Apache Hive](https://hive.apache.org/) kullanmayı öğrenin. Sonuç, belirli bir kelime içeren en fazla sayıda fazla alanı gönderen Twitter kullanıcılarının bir listesidir.

> [!IMPORTANT]  
> Bu belgedeki adımlar HDInsight 3,6 ' de test edilmiştir.

## <a name="get-the-data"></a>Verileri alma

Twitter, her Tweet için verileri bir JavaScript Nesne Gösterimi (JSON) belgesi olarak REST API aracılığıyla almanıza olanak sağlar. API için kimlik doğrulaması için [OAuth](https://oauth.net) gereklidir.

### <a name="create-a-twitter-application"></a>Twitter uygulaması oluşturma

1. Bir Web tarayıcısından ' de oturum açın [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/) . Twitter hesabınız yoksa **Şimdi kaydolun** bağlantısını seçin.

2. **Yeni uygulama oluştur**' u seçin.

3. **Ad**, **Açıklama**, **Web sitesi** girin. **Web sitesi** alanı IÇIN bir URL oluşturabilirsiniz. Aşağıdaki tabloda, kullanılacak bazı örnek değerler gösterilmektedir:

   | Alan | Değer |
   |--- |--- |
   | Ad |MyHDInsightApp |
   | Description |MyHDInsightApp |
   | Web Sitesi |`https://www.myhdinsightapp.com` |

4. **Evet, kabul** ediyorum ' u seçin ve ardından **Twitter uygulamanızı oluştur**' u seçin.

5. **İzinler** sekmesini seçin. Varsayılan izin **salt okunurdur**.

6. **Anahtarlar ve erişim belirteçleri** sekmesini seçin.

7. **Erişim belirtecimi oluştur**' u seçin.

8. Sayfanın sağ üst köşesindeki **Test OAuth** ' ı seçin.

9. **Tüketici anahtarı**, **Tüketici parolası**, **erişim belirteci** ve **erişim belirteci gizli** anahtarını yazın.

### <a name="download-tweets"></a>Fazla doldurulabilir yükleme

Aşağıdaki Python kodu Twitter 'dan 10.000 tasarruf sağlar ve **tweets.txt** adlı bir dosyaya kaydeder.

> [!NOTE]  
> Python zaten yüklü olduğundan, HDInsight kümesinde aşağıdaki adımlar gerçekleştirilir.

1. Kümenize bağlanmak için [SSH komutunu](./hdinsight-hadoop-linux-use-ssh-unix.md) kullanın. CLUSTERNAME öğesini kümenizin adıyla değiştirerek aşağıdaki komutu düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. [Pamallik](https://www.tweepy.org/), [ilerleme çubuğu](https://pypi.python.org/pypi/progressbar/2.2)ve diğer gerekli paketleri yüklemek için aşağıdaki komutları kullanın:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. **Gettweets.py** adlı bir dosya oluşturmak için aşağıdaki komutu kullanın:

   ```bash
   nano gettweets.py
   ```

1. ,, `Your consumer secret` `Your consumer key` Ve, `Your access token` `Your access token secret` Twitter uygulamanızdan ilgili bilgileri değiştirerek aşağıdaki kodu düzenleyin. Ardından düzenlenmiş kodu **gettweets.py** dosyasının içeriği olarak yapıştırın.

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=100

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!TIP]  
    > Popüler anahtar sözcükleri izlemek için son satırdaki konular filtresini ayarlayın. Betiği çalıştırdığınız sırada popüler anahtar sözcüklerin kullanılması, verilerin daha hızlı yakalanmasını sağlar.

1. Dosyayı kaydetmek için **CTRL + X**, sonra **Y** kullanın.

1. Dosyayı çalıştırmak ve arası dosyaları indirmek için aşağıdaki komutu kullanın:

    ```bash
    python gettweets.py
    ```

    İlerleme göstergesi görünür. Bu, çok sayıda %100 ' e kadar sayılır ve bu da doldurulabilir.

   > [!NOTE]  
   > İlerleme çubuğunun ileri bir süre sürüyorsa, eğilimi izlemek için filtreyi değiştirmelisiniz. Filtrenizin konusu hakkında çok fazla sayıda yer varsa, gereken 100 hayvan 'yı hızlıca alabilirsiniz.

### <a name="upload-the-data"></a>Verileri karşıya yükleme

Verileri HDInsight depolama alanına yüklemek için aşağıdaki komutları kullanın:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Bu komutlar, verileri kümedeki tüm düğümlerin erişebileceği bir konumda depolar.

## <a name="run-the-hiveql-job"></a>HiveQL işini çalıştırma

1. [Hiveql](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) deyimlerini içeren bir dosya oluşturmak için aşağıdaki komutu kullanın:

   ```bash
   nano twitter.hql
   ```

    Dosyanın içeriği olarak aşağıdaki metni kullanın:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

1. **CTRL + X** tuşlarına basın ve ardından dosyayı kaydetmek için **Y** tuşuna basın.

1. Dosyasında bulunan HiveQL 'i çalıştırmak için aşağıdaki komutu kullanın:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Bu komut **Twitter. HQL** dosyasını çalıştırır. Sorgu tamamlandıktan sonra bir `jdbc:hive2//localhost:10001/>` istem görürsünüz.

1. Beeline istem içinden, verilerin içeri aktarıldığını doğrulamak için aşağıdaki sorguyu kullanın:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Bu sorgu, ileti metninde **Azure** sözcüğünü içeren maksimum 10 adet Evcil hayvan döndürür.

    > [!NOTE]  
    > Komut dosyasındaki filtreyi değiştirdiyseniz `gettweets.py` , **Azure** 'u kullandığınız filtrelerden biriyle değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

Yapılandırılmamış bir JSON veri kümesini yapılandırılmış bir [Apache Hive](https://hive.apache.org/) tablosuna nasıl dönüştürebileceğinizi öğrendiniz. HDInsight 'ta Hive hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

* [HDInsight'ı kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight kullanarak Uçuş gecikmesi verilerini çözümleme](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)