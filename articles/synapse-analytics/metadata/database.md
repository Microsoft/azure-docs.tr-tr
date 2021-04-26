---
title: Paylaşılan veritabanı
description: Azure SYNAPSE Analytics, sunucusuz Apache Spark havuzunda veritabanı oluşturmak için sunucusuz SQL havuzundan ve SQL havuzu altyapılarından erişilebilir hale getirir.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 93602e522338166abac98c3e4a198e1aff392d21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934977"
---
# <a name="azure-synapse-analytics-shared-database"></a>Azure SYNAPSE Analytics paylaşılan veritabanı

Azure SYNAPSE Analytics, farklı hesaplama çalışma alanı altyapılarının veritabanlarını ve tabloları paylaşmasına izin verir. Şu anda, Apache Spark havuzlarında oluşturulan veritabanları ve Parquet tabloları sunucusuz SQL havuzu altyapısı ile otomatik olarak paylaşılır.

Spark işiyle oluşturulmuş bir veritabanı, sunucusuz SQL havuzu altyapısı dahil, çalışma alanındaki tüm geçerli ve gelecekteki Spark havuzlarıyla aynı ad ile görünür hale gelir. Sunucusuz SQL havuzunu kullanarak doğrudan bu çoğaltılan veritabanına özel nesneler (dış tablolar, görünümler, yordamlar) ekleyemezsiniz.

Adlı Spark varsayılan veritabanı, `default` adlı veritabanı olarak sunucusuz SQL havuzu bağlamında da görünür olacaktır `default` .

Veritabanları sunucusuz SQL havuzu ile zaman uyumsuz olarak eşitlendiğinden, görünene kadar bir gecikme olur.

## <a name="manage-a-spark-created-database"></a>Spark tarafından oluşturulan bir veritabanını yönetme

Spark tarafından oluşturulan veritabanlarını yönetmek için Spark 'ı kullanın. Örneğin, bir Spark havuzu işi aracılığıyla silin ve Spark ' dan tablo oluşturun.

Sunucusuz SQL havuzu kullanarak Spark oluşturulmuş bir veritabanında nesneler oluşturursanız veya veritabanını bırakmaya çalışırsanız, işlem başarılı olur. Ancak özgün Spark veritabanı değiştirilmez.

## <a name="how-name-conflicts-are-handled"></a>Ad çakışmalarının işlenme şekli

Spark veritabanının adı, var olan bir sunucusuz SQL havuzu veritabanının adıyla çakışıyorsa, sunucusuz SQL havuzunda Spark veritabanına bir sonek eklenir. Sunucusuz SQL havuzundaki sonek `_<workspace name>-ondemand-DefaultSparkConnector` .

Örneğin, `mydb` Azure SYNAPSE çalışma alanında adlı bir Spark veritabanı oluşturulduysa `myws` ve bu ada sahip SUNUCUSUZ bir SQL havuzu veritabanı zaten mevcutsa, sunucusuz SQL havuzundaki Spark veritabanının adı kullanılarak başvurulması gerekir `mydb_myws-ondemand-DefaultSparkConnector` .

> [!CAUTION]
> Dikkat: Bu davranışa bir bağımlılık uygulamanız gerekmez.

## <a name="security-model"></a>Güvenlik modeli

Spark veritabanları ve tabloları, SQL altyapısındaki eşitlenmiş temsillerinin yanı sıra, temel alınan depolama düzeyinde güvenli hale getirilir.

Veritabanını oluşturan güvenlik sorumlusu o veritabanının sahibi kabul edilir ve veritabanıyla nesneleri üzerinde tüm haklara sahip olur.

Bir kullanıcı veya güvenlik grubu gibi bir güvenlik sorumlusu sağlamak için, bir veritabanına erişim, dizindeki temel klasörler ve dosyalar için uygun POSIX klasörünü ve dosya izinlerini sağlayın `warehouse` . 

Örneğin, bir güvenlik sorumlusunun bir veritabanındaki bir tabloyu okuyabilmesi için, dizindeki veritabanı klasöründen başlayan tüm klasörlerin, `warehouse` `X` `R` Bu güvenlik sorumlusuna ve izinlerine sahip olması gerekir. Ayrıca, dosyalar (tablonun temel alınan veri dosyaları gibi) için izinler gerekir `R` . 

Bir güvenlik sorumlusu bir veritabanında nesne oluşturma veya nesneleri bırakma özelliğini gerektiriyorsa, `W` klasördeki klasörler ve dosyalar üzerinde ek izinler gereklidir `warehouse` .

## <a name="examples"></a>Örnekler

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>Sunucusuz SQL havuzu ile Spark veritabanı oluşturun ve bu veritabanına bağlanın

İlk `mytestdb` olarak, çalışma alanınızda zaten oluşturmuş olduğunuz Spark kümesi kullanılarak adlı yeni bir Spark veritabanı oluşturun. Bunun için, örneğin, aşağıdaki .NET Spark ifadesiyle bir Spark C# Not defteri kullanarak bunu yapabilirsiniz:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Kısa bir gecikmeden sonra, veritabanını sunucusuz SQL havuzundan görebilirsiniz. Örneğin, sunucusuz SQL havuzundan aşağıdaki ifadeyi çalıştırın.

```sql
SELECT * FROM sys.databases;
```

`mytestdb`Sonuçlara dahil edildiğini doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SYNAPSE Analytics 'te paylaşılan meta veriler hakkında daha fazla bilgi edinin](overview.md)
- [Azure SYNAPSE Analytics ' paylaşılan meta veri tabloları hakkında daha fazla bilgi edinin](table.md)
