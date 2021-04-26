---
title: Azure Stack Edge & Azure Data Box Gateway 2007 sürüm notları | Microsoft Docs
description: Azure Stack Edge ve 2007 sürümü çalıştıran Data Box Gateway için kritik açık sorunları ve çözümleri açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 5dd835c99f5781b3734983ea64709535a75e1fa8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582782"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Azure Stack Edge ve Azure Data Box Gateway 2007 sürüm notları

Aşağıdaki sürüm notları, Azure Stack Edge ve Data Box Gateway için kritik açık sorunları ve 2007 sürümü için çözülmüş sorunları belirler.

Sürüm notları sürekli olarak güncelleştirilir ve geçici bir çözüm gerektiren kritik sorunlar keşfedildiğinde eklenirler. Azure Stack Edge/Data Box Gateway dağıtmadan önce, sürüm notlarında bulunan bilgileri dikkatle gözden geçirin.

Bu sürüm, yazılım sürümlerine karşılık gelir:

- **Azure Stack Edge 2007 (1.6.1280.1667)** -KB 4566549
- **Data Box Gateway 2007 (1.6.1280.1667)** -KB 4566550

> [!NOTE]
> Güncelleştirme 2007, yalnızca yazılımın genel kullanılabilirlik (GA) sürümlerini çalıştıran tüm cihazlara uygulanabilir.

## <a name="whats-new"></a>Yenilikler

Bu sürüm aşağıdaki hata düzeltmesini içerir:

- **Karşıya yükleme sorunu** -bu sürüm, hata nedeniyle karşıya yükleme yeniden başlatmalarının karşıya yükleme işleminin hızını yavaşlatabileceğinden karşıya yükleme sorununu düzeltir. Bu sorun, birincil olarak bant genişliği azaltma etkin olduğunda, kullanılabilir bant genişliğine göre, özellikle sınırlı olmayan dosyalardan oluşan bir veri kümesi karşıya yüklenirken oluşabilir. Bu değişiklik, belirli bir dosya için karşıya yükleme işleminin yeniden başlatılmasından önce karşıya yükleme işleminin tamamlanması için yeterli fırsat verilmesini sağlar.

Bu sürüm ayrıca aşağıdaki güncelleştirmeleri içerir:

- Windows VHD 'nin temel görüntüsü güncelleştirildi.
- 2020 Mayıs ile yayınlanan tüm toplu Windows güncelleştirmeleri ve .NET Framework güncelleştirmeleri dahil edilmiştir.
- Bu sürüm Azure Stack Edge cihazlarında IoT Edge 1.0.9.3 destekler.

## <a name="known-issues-in-this-release"></a>Bu sürümdeki bilinen sorunlar

Bu yayın için Not belirtilen yeni bir sorun yok. Belirtilen tüm sürüm sorunları önceki sürümlerden devredilir. Bilinen sorunların bir listesini görmek için, [ga sürümündeki bilinen sorunlara](data-box-gateway-release-notes.md#known-issues-in-ga-release)gidin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge dağıtmaya hazırlanma](../databox-online/azure-stack-edge-deploy-prep.md)
- [Azure Data Box Gateway'i dağıtmaya hazırlanma](data-box-gateway-deploy-prep.md)
