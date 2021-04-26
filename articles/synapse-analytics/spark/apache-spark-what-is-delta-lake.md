---
title: Delta Lake nedir?
description: Delta Gölü ve Azure SYNAPSE Analytics 'in bir parçası olarak nasıl çalıştığı hakkında genel bakış
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 23a269fe9498295a3ff325a261c6539f5e1e31aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676223"
---
# <a name="what-is-delta-lake"></a>Delta Lake nedir?

Azure SYNAPSE Analytics, Linux Foundation Delta Gölü ile uyumludur. Delta Gölü, Apache Spark ve büyük veri iş yüklerine ACID (Atomicity, tutarlılık, yalıtım ve dayanıklılık) işlemlerini getiren açık kaynaklı bir depolama katmanıdır.

Azure SYNAPSE ile birlikte sunulan Delta Gölü sürümünün geçerli sürümü, Scala, PySpark ve .NET için dil desteğine sahiptir. Sayfanın en altında daha ayrıntılı örnekler ve belgeler için bağlantılar vardır.

## <a name="key-features"></a>Önemli özellikler

| Özellik | Açıklama |
| --- | --- |
| **ACID Işlemleri** | Veri Lakes genellikle birden çok işlem ve işlem hattı ile doldurulur ve bazıları okumalarla eşzamanlı olarak veri yazıyor. Delta Gölü ve işlemlerin eklenmesinden önce veri mühendislerinin, veri bütünlüğünü sağlamak için el ile hata eden bir işlemden geçmesi gerekiyordu. Delta Gölü, tanıdık ACID işlemlerini veri Lakes 'e getirir. En güçlü yalıtım düzeyi düzeyi olan serializyeteneği sağlar. [Delta Gölü 'da daha fazla bilgi edinin: Işlem günlüğü açılıyor](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html).|
| **Ölçeklenebilir meta veri Işleme** | Büyük verilerde bile meta verilerin kendisi "büyük veri" olabilir. Delta Gölü meta verileri, tüm meta verilerini işlemek için Spark 'ın dağıtılmış işleme gücünden yararlanarak verileri tıpkı verilerle değerlendirir. Sonuç olarak, Delta Gölü, milyarlarca bölüm ve dosya içeren petablarca ölçekli tabloları kolayca işleyebilir. |
| **Seyahat süresi (veri sürümü oluşturma)** | Bir değişikliği "geri alma" veya önceki sürüme geri dönme özelliği, işlemlerin temel özelliklerinden biridir. Delta Gölü, denetimler, geri alma veya yeniden oluşturma için verilerin önceki sürümlerine dönmenize olanak sağlayan verilerin anlık görüntülerini sağlar. [Büyük ölçekli veri ölçeği Için Delta Gölü zaman gezine giriş konusunda](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html)daha fazla bilgi edinin. |
| **Biçim aç** | Apache Parquet, Delta Gölü için temel biçimdir ve biçimde yerel olan etkili sıkıştırma ve kodlama düzenlerinden yararlanmanızı sağlar. |
| **Birleşik toplu Işlem ve akış kaynağı ve havuzu** | Delta Gölü bir tablo, hem bir toplu iş tablosu hem de bir akış kaynağı ve havuz. Akış verisi alma, toplu geçmiş geri doldurma ve etkileşimli sorgular yalnızca kutudan çıkar. |
| **Şema zorlaması** | Şema zorlaması, veri türlerinin doğru ve gerekli sütunların mevcut olduğundan ve verilerin tutarsızlığına neden olmasını önlemeye yardımcı olur. Daha fazla bilgi için bkz [. Delta Gölü içine atlama: şema zorlama & evrimi](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Şema evrimi** | Delta Gölü, geçiş DDL yazmak zorunda kalmadan otomatik olarak uygulanabilen bir tablo şemasında değişiklik yapmanızı sağlar. Daha fazla bilgi için bkz [. Delta Gölü içine atlama: şema zorlama & evrimi](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Denetim geçmişi** | Delta Gölü işlem günlüğü, değişikliklerin tam denetim izini sağlayan verilerde yapılan her değişiklikle ilgili ayrıntıları kaydeder. |
| **Güncelleştirmeler ve silmeler** | Delta Gölü, çeşitli işlevler için Scala/Java/Python ve SQL API 'Lerini destekler. Birleştirme, güncelleştirme ve silme işlemleri için destek, uyumluluk gereksinimlerini karşılamanıza yardımcı olur. Daha fazla bilgi için, bkz. Delta Gölü [0.6.1 sürümü](https://delta.io/news/delta-lake-0-6-1-released/)duyurusu, delta  [Lake 0,7 sürümü](https://delta.io/news/delta-lake-0-7-0-released/) ve basit, güvenilir ve silme DML, Update ve DELETE DML komutlarının kod parçacıkları dahil olmak üzere [Python API 'Leri kullanılarak Delta Lake tablolarında silme](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html). |
| **Apache Spark API ile uyumlu %100** | Geliştiriciler, var olan Spark uygulamalarıyla tam olarak uyumlu olduğundan, Delta Gölü, en az değişikliğe sahip mevcut veri komut zincirleriyle birlikte kullanılabilir. |

Tam belgeler için bkz. Delta Gölü [belge sayfası](https://docs.delta.io/latest/delta-intro.html)

Daha fazla bilgi için bkz. [Delta Lake projesi](https://github.com/delta-io/delta).

## <a name="next-steps"></a>Sonraki adımlar

- [Apache Spark belgeleri için .NET](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)