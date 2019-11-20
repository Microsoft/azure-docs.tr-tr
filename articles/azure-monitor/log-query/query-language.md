---
title: Azure Izleyici günlük sorguları | Microsoft Docs
description: Azure Izleyici 'de günlük sorgularının nasıl yazılacağını öğrenmek için kaynaklara başvurular.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: 7c6530416f0eb6b822b6f5f3eb53f59aeae05d53
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894344"
---
# <a name="azure-monitor-log-queries"></a>Azure Izleyici günlük sorguları
Azure Izleyici günlükleri Azure Veri Gezgini kurulmuştur ve Azure Izleyici günlük sorguları aynı kusto sorgu dilinin bir sürümünü kullanır. [Kusto sorgu dili belgeleri](/azure/kusto/query) , dilin tüm ayrıntılarına sahiptir ve Azure izleyici günlük sorgularını yazmak için birincil kaynağınız olmalıdır. Bu sayfa, dilin Azure Izleyici uygulamasıyla nasıl sorgu yazılacağını ve farklılıkları hakkında bilgi almak için diğer kaynaklara bağlantılar sağlar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Başlangıç

- [Azure izleyici 'yi kullanmaya başlama Log Analytics](get-started-portal.md) , sorgu yazma ve sonuçlarla çalışma ile ilgili bir ders olan Azure Portal.
- [Azure izleyici günlük sorgularını kullanmaya başlama](get-started-queries.md) , Azure izleyici günlük verilerini kullanarak sorgu yazma dersi.

## <a name="concepts"></a>Kavramlar
- [Azure izleyici 'de günlük verilerini analiz etmek](../../azure-monitor/log-query/log-query-overview.md) , günlük sorgularına kısa bir genel bakış sağlar ve Azure izleyici günlük verilerinin nasıl yapılandırıldığını açıklar.
- [Azure izleyici 'de günlük verilerini görüntüleme ve çözümleme](../../azure-monitor/log-query/portals.md) , günlük sorguları oluşturup çalıştırdığınız portalları açıklar.

## <a name="reference"></a>Başvuru

- [Sorgu dili başvurusu](/azure/kusto/query) , kusto sorgu dili için tüm dil başvurusudur.
- [Azure izleyici günlük sorgusu dil farkları](data-explorer-difference.md) , kusto sorgu dilinin sürümleri arasındaki farkları açıklar.
- [Azure İzleyici günlüğü kayıtlarında standart özellikler](../../azure-monitor/platform/log-standard-properties.md) tüm Azure izleyici günlük verileri için standart olan özellikleri açıklar.
- [Azure izleyici 'de çapraz kaynak günlük sorguları gerçekleştirme](../../azure-monitor/log-query/cross-workspace-query.md) birden çok Log Analytics çalışma alanından ve Application Insights uygulamadan veri kullanan günlük sorgularının nasıl yazılacağını açıklar.


## <a name="examples"></a>Örnekler

- [Azure izleyici günlük sorgusu örnekleri](examples.md) , Azure izleyici günlük verilerini kullanarak örnek sorgular sağlar.



## <a name="lessons"></a>Dersler

- [Azure izleyici günlük sorgularında dizelerle çalışma](string-operations.md) , dize verileriyle nasıl çalışabileceğinizi açıklar.
- [Azure izleyici günlük sorgularında tarih saat değerleriyle çalışma](datetime-operations.md) , tarih ve saat verileriyle nasıl çalışabileceğinizi açıklar. 
- Azure izleyici günlük sorguları ve [Gelişmiş toplamalar](advanced-aggregations.md) içindeki [toplamalar](aggregations.md) , verileri nasıl toplayacağını ve özetleyeceğinizi açıklamaktadır.
- [Azure izleyici günlük sorgularında birleşimler,](joins.md) birden çok tablodan verilerin nasıl birleştirileceğini açıklar.
- [Azure izleyici günlük SORGULARıNDA JSON ve veri yapıları Ile çalışma](json-data-structures.md) , JSON verilerinin nasıl ayrıştırılacağını açıklar.
- [Azure izleyici 'de Gelişmiş günlük sorguları yazma](advanced-query-writing.md) , karmaşık sorgular oluşturma ve kodu yeniden kullanma stratejilerini açıklar.
- [Azure izleyici günlük sorgularından grafikler ve diyagramlar oluşturma,](charts.md) günlük sorgusundan verilerin nasıl görselleştirileneceğini açıklar.

## <a name="cheatsheets"></a>Başvuru sayfaları

-  [SQL 'Den Azure 'A izleyici günlük sorgusu SQL 'e](sql-cheatsheet.md) zaten tanıdık olan kullanıcılara yardımcı olur.
-  [Azure izleyici günlük sorgusuna yönelik splunk](splunk-cheatsheet.md) , splunk ile zaten tanıdık olan kullanıcılara yardımcı olur.
 
## <a name="next-steps"></a>Sonraki adımlar

- [Kusto sorgu dili için tüm başvuru belgelerine](/azure/kusto/query/)erişin.
