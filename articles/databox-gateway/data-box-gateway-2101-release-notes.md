---
title: Azure Data Box Gateway 2101 sürüm notları | Microsoft Docs
description: 2101 sürümü çalıştıran Azure Data Box Gateway için kritik açık sorunları ve çözümleri açıklar.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99072728"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Azure Data Box Gateway 2101 sürüm notları

Aşağıdaki sürüm notları, Azure Data Box Gateway 2101 sürümü için kritik açık sorunları ve çözülmüş sorunları belirler.

Sürüm notları sürekli olarak güncelleştirilir. Geçici bir çözüm gerektiren kritik sorunlar söz konusu olduğunda, bunlar eklenir. Azure Data Box Gateway dağıtmadan önce, sürüm notlarındaki bilgileri dikkatle gözden geçirin.  

Bu sürüm, yazılım sürümlerine karşılık gelir:

- **Data Box Gateway 2101 (1.6.1475.2528)** -KB 4603462

> [!NOTE]
> Güncelleştirme 2101, yalnızca yazılımın genel kullanılabilirlik (GA) sürümlerini çalıştıran tüm cihazlara uygulanabilir.

## <a name="whats-new"></a>Yenilikler

Bu sürüm aşağıdaki hata düzeltmesini içerir:

- **Karşıya yükleme sorunu** -bu sürüm, hata nedeniyle karşıya yükleme yeniden başlatmalarının bir karşıya yüklenmesi sorununu düzeltir. Bu sorun, birincil olarak bant genişliği azaltma etkin olduğunda, kullanılabilir bant genişliğine göre, özellikle sınırlı olmayan dosyalardan oluşan bir veri kümesi karşıya yüklenirken oluşabilir. Bu değişiklik, belirli bir dosya için karşıya yüklemeyi yeniden başlatmadan önce karşıya yükleme işleminin tamamlanmasına yetecek kadar fırsat sağlar.

Bu sürüm ayrıca aşağıdaki güncelleştirmeleri içerir:

- Tüm toplu Windows güncelleştirmeleri ve .NET Framework güncelleştirmeleri 2020 Ekim 'e göre yayınlandı.
- Azure Data Box Gateway statik IP adresi yazılım güncelleştirmeleri arasında tutulur.

## <a name="known-issues-in-this-release"></a>Bu sürümdeki bilinen sorunlar

Bu yayın için Not belirtilen yeni bir sorun yok. Belirtilen tüm sürüm sorunları önceki sürümlerden devredilir. Bilinen sorunların bir listesini görmek için, [ga sürümündeki bilinen sorunlara](data-box-gateway-release-notes.md#known-issues-in-ga-release)gidin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Box Gateway'i dağıtmaya hazırlanma](data-box-gateway-deploy-prep.md)
