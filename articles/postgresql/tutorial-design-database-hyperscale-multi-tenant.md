---
title: PostgreSQL için Azure veritabanı – Hyperscale (Citus) öğreticisi ile çok kiracılı bir veritabanı tasarlama
description: Bu öğreticide, PostgreSQL için Azure veritabanı hiper ölçek (Citus) üzerinde dağıtılmış tabloları oluşturma, doldurma ve sorgulama işlemlerinin nasıl yapılacağı gösterilmektedir.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 130c3e9f5abb24ffcc4e0c4ad6b96af5fca62090
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496545"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Öğretici: PostgreSQL için Azure veritabanı – hiper ölçek (Citus) kullanarak çok kiracılı bir veritabanı tasarlama

Bu öğreticide, şu şekilde nasıl yapılacağını öğrenmek için PostgreSQL için Azure veritabanı-hiper ölçek (Citus) kullanacaksınız:

> [!div class="checklist"]
> * Hiper Ölçek (Citus) (Citus) sunucu grubu oluşturma
> * Şema oluşturmak için psql yardımcı programını kullanma
> * Düğümler arasında parça tabloları
> * Örnek verileri ekleme
> * Kiracı verilerini sorgulama
> * Kiracılar arasında veri paylaşma
> * Kiracı başına şemayı özelleştirme

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Şema oluşturmak için psql yardımcı programını kullanma

Psql kullanarak PostgreSQL için Azure veritabanı-hiper ölçek (Citus) bağlantısı kurulduktan sonra bazı temel görevleri tamamlayabilirsiniz. Bu öğreticide, reklamcılarının kampanyalarını izlemelerine izin veren bir Web uygulaması oluşturma işlemi adım adım açıklanmaktadır.

Birden çok şirket uygulamayı kullanabilir, böylece şirketler için bir tablo oluşturalım ve kampanyalar için başka bir tablo oluşturalım. Psql konsolunda şu komutları çalıştırın:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Her kampanya reklamları çalıştırmak için ödeme yapar. Yukarıdaki koddan sonra psql 'de aşağıdaki kodu çalıştırarak reklamları için de tablo ekleyin:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Son olarak, her ad için tıklama ve aksaklılar hakkında istatistikleri izliyoruz:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

Yeni oluşturulan tabloları şu şekilde psql 'de bulunan tablolar listesinde görebilirsiniz:

```postgres
\dt
```

Çok kiracılı uygulamalar, tüm birincil ve yabancı anahtarların şirket KIMLIĞINI içermesi neden olan her kiracı için benzersizlik uygulayabilir.

## <a name="shard-tables-across-nodes"></a>Düğümler arasında parça tabloları

Bir hiper ölçek dağıtımı, tablo satırlarını Kullanıcı tarafından belirlenen bir sütunun değerine göre farklı düğümlere depolar. Bu "dağıtım sütunu", hangi kiracının hangi satırlara sahip olduğunu işaretler.

Dağıtım sütununu şirket\_kimliği, kiracı tanımlayıcısı olacak şekilde ayarlayalim. Psql 'de şu işlevleri çalıştırın:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Örnek verileri ekleme

Şu anda psql dışında, normal komut satırında örnek veri kümelerini indirebilirsiniz:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Psql içinde geri dönerek verileri toplu yükleyin. Psql ' i veri dosyalarını indirdiğiniz aynı dizinde çalıştırmayı unutmayın.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Bu veriler artık çalışan düğümlerine yayılacaktır.

## <a name="query-tenant-data"></a>Kiracı verilerini sorgulama

Uygulama tek bir kiracı için veri istediğinde, veritabanı sorguyu tek bir çalışan düğümünde yürütebilir. Tek kiracılı sorgular tek bir kiracı KIMLIĞINE göre filtreleyerek. Örneğin, aşağıdaki sorgu, reklamlar ve aksaklamalar için `company_id = 5` filtreler. Sonuçları görmek için psql 'de çalıştırmayı deneyin.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Kiracılar arasında veri paylaşma

Artık tüm tablolar `company_id`tarafından dağıtılana kadar, ancak bazı veriler doğal olarak hiçbir kiracıya özel olarak "ait değildir" ve paylaşılabilir. Örneğin, örnek ad platformundaki tüm şirketler, IP adreslerine bağlı olarak kendi hedef kitlesi için coğrafi bilgiler almak isteyebilir.

Paylaşılan coğrafi bilgileri tutacak bir tablo oluşturun. Psql 'de aşağıdaki komutları çalıştırın:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Sonra, her çalışan düğümünde tablonun bir kopyasını depolamak için bir "başvuru tablosu" `geo_ips`.

```sql
SELECT create_reference_table('geo_ips');
```

Örnek verilerle yükleyin. Bu komutu, veri kümesini indirdiğiniz Dizin içinden psql 'de çalıştırmayı unutmayın.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Tıklama tablosunun coğrafi\_IP 'leri ile katılması tüm düğümlerde etkilidir.
Ad 'ye tıklanan herkesin konumlarını bulmak için bir JOIN aşağıda verilmiştir
290. Sorguyu psql 'de çalıştırmayı deneyin.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Kiracı başına şemayı özelleştirme

Her kiracının, diğerlerinin gerek duymayan özel bilgileri depolaması gerekebilir. Ancak, tüm kiracılar aynı veritabanı şemasıyla ortak bir altyapı paylaşır. Fazla veri nereden gidebileceği?

Tek bir adım PostgreSQL 'in JSONB gibi bir açık uçlu sütun türü kullanmaktır.  Şemamız `user_data`adlı `clicks` bir JSONB alanına sahiptir.
Şirket (Şirket beşini), kullanıcının bir mobil cihazda olup olmadığını izlemek için sütununu kullanabilir.

İşte kimin daha fazla tıklatığını bulmak için bir sorgu: mobil veya geleneksel ziyaretçiler.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

[Kısmi bir dizin](https://www.postgresql.org/docs/current/static/indexes-partial.html)oluşturarak bu sorguyu tek bir şirket için iyileştirebiliriz.

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Daha genel olarak, sütun içindeki her anahtar ve değer üzerinde bir [gın dizini](https://www.postgresql.org/docs/current/static/gin-intro.html) oluştururuz.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yukarıdaki adımlarda, bir sunucu grubunda Azure kaynakları oluşturdunuz. Gelecekte bu kaynaklara ihtiyaç duymazsanız, sunucu grubunu silin. Sunucu grubunuzun *genel bakış* sayfasında *Sil* düğmesine basın. Bir açılır sayfada istendiğinde, sunucu grubunun adını onaylayın ve son *Sil* düğmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir hiper ölçek (Citus) sunucu grubu sağlamayı öğrendiniz. Bu ağa psql ile bağlanırsınız, bir şema oluşturdunuz ve dağıtılmış veriler. Kiracılar içindeki ve içindeki verileri sorgulamayı ve kiracı başına şemayı özelleştirmeyi öğrendiniz.

Ardından, hyperscale kavramlarını öğrenin.
> [!div class="nextstepaction"]
> [Hiper ölçek düğüm türleri](https://aka.ms/hyperscale-concepts)
