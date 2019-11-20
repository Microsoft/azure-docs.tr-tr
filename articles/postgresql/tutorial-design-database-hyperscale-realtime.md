---
title: PostgreSQL için Azure veritabanı – Hyperscale (Citus) öğreticisi ile gerçek zamanlı bir pano tasarlama
description: Bu öğreticide, PostgreSQL için Azure veritabanı hiper ölçek (Citus) üzerinde dağıtılmış tabloları oluşturma, doldurma ve sorgulama işlemlerinin nasıl yapılacağı gösterilmektedir.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 32487d65397a96d9e96ae3bf3476eed23ddb8adc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482871"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Öğretici: PostgreSQL için Azure veritabanı – hiper ölçek (Citus) kullanarak gerçek zamanlı analiz panosu tasarlama

Bu öğreticide, şu şekilde nasıl yapılacağını öğrenmek için PostgreSQL için Azure veritabanı-hiper ölçek (Citus) kullanacaksınız:

> [!div class="checklist"]
> * Hiper Ölçek (Citus) (Citus) sunucu grubu oluşturma
> * Şema oluşturmak için psql yardımcı programını kullanma
> * Düğümler arasında parça tabloları
> * Örnek veri oluşturma
> * Toplamaları gerçekleştir
> * Ham ve toplanmış verileri sorgulama
> * Süre sonu verisi

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Şema oluşturmak için psql yardımcı programını kullanma

Psql kullanarak PostgreSQL için Azure veritabanı-hiper ölçek (Citus) bağlantısı kurulduktan sonra bazı temel görevleri tamamlayabilirsiniz. Bu öğretici, Web analizinden trafik verilerini geri alma ve bu verileri temel alan gerçek zamanlı panolar sağlamak üzere verileri toplama konusunda size kılavuzluk eder.

Ham web trafiği verilerimizi kullanacak bir tablo oluşturalım. Psql terminalinde aşağıdaki komutları çalıştırın:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Ayrıca, dakika başına toplaımuzu ve son toplaımızın konumunu tutan bir tabloyu barındıracak bir tablo oluşturacağız. Psql 'de aşağıdaki komutları da çalıştırın:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Yeni oluşturulan tabloları şu psql komutuyla birlikte Tablo listesinde görebilirsiniz:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Düğümler arasında parça tabloları

Bir hiper ölçek dağıtımı, tablo satırlarını Kullanıcı tarafından belirlenen bir sütunun değerine göre farklı düğümlere depolar. Bu "dağıtım sütunu", verilerin düğümler arasında nasıl parçalanmış olduğunu işaretler.

Dağıtım sütununu site\_kimliği, parça anahtarı olacak şekilde ayarlayalim. Psql 'de şu işlevleri çalıştırın:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Örnek veri oluşturma

Artık sunucu grubumuz bazı verileri almaya hazır olmalıdır. Verileri sürekli olarak eklemek için `psql` bağlantımızda şunları yerel olarak çalıştırabiliriz.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

Sorgu her saniye yaklaşık sekiz satır ekler. Satırlar, `site_id`dağıtım sütunuyla yönlendirilen farklı çalışan düğümlerinde depolanır.

   > [!NOTE]
   > Veri oluşturma sorgusunu çalışır durumda bırakın ve bu öğreticideki geri kalan komutlar için ikinci bir psql bağlantısı açın.
   >

## <a name="query"></a>Sorgu

Hiper ölçek barındırma seçeneği, birden çok düğümün hız için paralel olarak sorguları işlemesini sağlar. Örneğin, veritabanı, çalışan düğümlerinde SUM ve say gibi toplamları hesaplar ve sonuçları nihai bir yanıt olarak birleştirir.

İşte birkaç istatistikle birlikte, dakikada Web isteklerini saymak için bir sorgu.
Psql 'de çalıştırmayı deneyin ve sonuçları gözlemleyin.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>Verileri toplama

Önceki sorgu erken aşamalarda ince çalışıyor, ancak verileriniz ölçeklenmesi halinde performansı düşürür. Dağıtılmış işleme dahil olmak üzere, verilerin tekrar tekrar hesaplanmasını yerine önceden hesaplamanız daha hızlıdır.

Ham verileri düzenli olarak toplam bir tabloya aktararak panonuzun hızlı bir şekilde devam etmemesini sağlayabilirsiniz. Toplama süresi ile denemeler yapabilirsiniz. Dakika başına toplama tablosu kullandık, ancak bunun yerine verileri 5, 15 veya 60 dakikaya kesebilirsiniz.

Bu toplaması daha kolay bir şekilde çalıştırmak için bunu bir plpgsql işlevine koyacağız. `rollup_http_request` işlevini oluşturmak için bu komutları psql 'de çalıştırın.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

İşlevimizde, verileri toplamak için yürütün:

```sql
SELECT rollup_http_request();
```

Önceden toplanmış bir formdaki verilerimizde, daha önce de aynı raporu almak için toplama tablosunu sorgulayabiliriz. Aşağıdaki sorguyu çalıştırın:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Süresi dolan eski veriler

Toplamalar sorguları daha hızlı hale getirir, ancak sınırsız depolama maliyetlerinden kaçınmak için eski verilerin süresini de sona ermemiz gerekir. Her ayrıntı düzeyi için verileri ne kadar süreyle saklamak istediğinize karar verin ve zaman aşımına uğramış verileri silmek için standart sorguları kullanın. Aşağıdaki örnekte, ham verileri bir gün ve dakika başına toplamalar için bir ay tutmaya karar verdik:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

Üretimde, bu sorguları bir işleve kaydırabilirsiniz ve bir cron işinde her dakika çağırabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yukarıdaki adımlarda, bir sunucu grubunda Azure kaynakları oluşturdunuz. Gelecekte bu kaynaklara ihtiyaç duymazsanız, sunucu grubunu silin. Sunucu grubunuzun *genel bakış* sayfasında *Sil* düğmesine basın. Bir açılır sayfada istendiğinde, sunucu grubunun adını onaylayın ve son *Sil* düğmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir hiper ölçek (Citus) sunucu grubu sağlamayı öğrendiniz. Bu ağa psql ile bağlanırsınız, bir şema oluşturdunuz ve dağıtılmış veriler. Verilerin her ikisi de ham biçimde sorgulanmasını, verileri düzenli olarak toplamasını, toplanmış tabloları sorgulamayı ve eski verilerin sona ermesini öğrendiniz.

Ardından, hyperscale kavramlarını öğrenin.
> [!div class="nextstepaction"]
> [Hiper ölçek düğüm türleri](https://aka.ms/hyperscale-concepts)