---
title: Paylaşılan meta veri modeli
description: Azure SYNAPSE Analytics, farklı çalışma alanı hesaplama altyapılarının, sunucusuz Apache Spark havuzları, sunucusuz SQL havuzu ve adanmış SQL havuzları arasında veritabanlarını ve tabloları paylaşmasına izin verir.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: b10b6f011fa7daee4094f0cc7b819d36127fedcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460354"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Azure SYNAPSE Analytics paylaşılan meta verileri

Azure SYNAPSE Analytics, farklı çalışma alanı hesaplama altyapılarının, sunucusuz Apache Spark havuzları ve sunucusuz SQL havuzu arasında veritabanlarını ve tabloları paylaşmasına izin verir.

Paylaşım, bu şekilde adlandırılan modern veri ambarı modelini destekler ve çalışma alanı SQL altyapılarını Spark ile oluşturulan veritabanlarına ve tablolara erişim sağlar. Ayrıca, SQL altyapılarının diğer altyapılarla paylaşılmayan kendi nesnelerini oluşturmalarına de olanak tanır.

## <a name="support-the-modern-data-warehouse"></a>Modern veri ambarını destekleme

Paylaşılan meta veri modeli, modern veri ambarı modelini aşağıdaki şekilde destekler:

1. Veri Gölü veriler, çok sayıda veritabanında bulunan (büyük olasılıkla bölümlenmiş) Parquet tarafından desteklenen tablolar depolanarak Spark ile birlikte hazırlanır ve verimli bir şekilde yapılandırılmıştır.

2. Spark tarafından oluşturulan veritabanları ve tüm tabloları Azure SYNAPSE çalışma alanı Spark havuz örneklerinde görünür hale gelir ve Spark işlerinin herhangi birinden kullanılabilir. Çalışma alanındaki tüm Spark havuzlarında aynı temel katalog meta deposunu paylaştığından, bu özellik [izinlere](#security-model-at-a-glance) tabidir.

3. Spark tarafından oluşturulan veritabanları ve kendi Parquet tarafından desteklenen tabloları, çalışma alanı sunucusuz SQL havuzunda görünür hale gelir. [Veritabanları](database.md) sunucusuz SQL havuzu meta verilerinde otomatik olarak oluşturulur ve Spark işi tarafından oluşturulan [dış ve yönetilen tabloların](table.md) her ikisi de `dbo` karşılık gelen veritabanının şemasında sunucusuz SQL havuzu meta verilerinde dış tablo olarak erişilebilir hale getirilir. 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Nesne eşitleme zaman uyumsuz olarak gerçekleştirilir. Nesneler, SQL bağlamında görünene kadar birkaç saniyelik bir gecikme olur. Bu olduklarında, bunlara erişimi olan SQL altyapılarını sorgulanabilir, ancak güncelleştirilemez veya değiştirilmez.

## <a name="shared-metadata-objects"></a>Paylaşılan meta veri nesneleri

Spark veritabanları, dış tablolar, yönetilen tablolar ve görünümler oluşturmanıza olanak sağlar. Spark görünümleri, tanımlayan Spark SQL ifadesini işlemek için bir Spark altyapısı gerektirdiğinden ve bir SQL altyapısı tarafından işlenemediği için, yalnızca veritabanları ve Parquet depolama biçimini kullanan dış ve yönetilen tabloları, çalışma alanı SQL altyapısıyla paylaşılır. Spark görünümleri yalnızca Spark havuz örnekleri arasında paylaşılır.

## <a name="security-model-at-a-glance"></a>Bir bakışta güvenlik modeli

Spark veritabanları ve tabloları, SQL altyapısındaki eşitlenmiş temsillerinin yanı sıra, temel alınan depolama düzeyinde güvenli hale getirilir. Tablo, sorgu gönderenin kullanım hakkına sahip olduğu altyapılardan herhangi biri tarafından sorgulandığında, sorgu gönderenin güvenlik sorumlusu, temel alınan dosyalara geçirilir. İzinler dosya sistemi düzeyinde denetlenir.

Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics paylaşılan veritabanı](database.md).

## <a name="change-maintenance"></a>Değişiklik Bakımı

Meta veri nesnesi silinir veya Spark ile değiştirilirse, değişiklikler alınır ve sunucusuz SQL havuzuna yayılır. Eşitleme zaman uyumsuzdur ve değişiklikler, kısa bir gecikmeden sonra SQL altyapısına yansıtılır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SYNAPSE Analytics ' paylaşılan meta veri veritabanları hakkında daha fazla bilgi edinin](database.md)
- [Azure SYNAPSE Analytics ' paylaşılan meta veri tabloları hakkında daha fazla bilgi edinin](table.md)

