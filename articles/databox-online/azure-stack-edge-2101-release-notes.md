---
title: Azure Stack Edge Pro, FPGA 2101 sürüm notları | Microsoft Docs
description: Azure Stack Edge 2101 sürümü için kritik açık sorunları ve çözümleri açıklar.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/01/2021
ms.author: alkohli
ms.openlocfilehash: 5c95da8d6ee88786ff983b4963c1cb96394886cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727558"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Azure Stack Edge Pro, FPGA 2101 sürüm notları

Aşağıdaki sürüm notları, yerleşik bir alan programlanabilir bir geçit dizisi (FPGA) ile Azure Stack Edge Pro 'nun 2101 sürümü için kritik açık sorunları ve çözülmüş sorunları belirler.

Sürüm notları sürekli olarak güncelleştirilir. Geçici bir çözüm gerektiren kritik sorunlar söz konusu olduğunda, bunlar eklenir. Azure Stack Edge cihazınızı dağıtmadan önce, sürüm notlarındaki bilgileri dikkatle gözden geçirin.  

Bu sürüm, yazılım sürümüne karşılık gelir:

- **Azure Stack Edge 2101 (1.6.1475.2528)** -KB 4599267

> [!NOTE]
> Güncelleştirme 2101, yalnızca yazılımın genel kullanılabilirlik (GA) veya sonraki sürümlerini çalıştıran cihazlara uygulanabilir.

## <a name="whats-new"></a>Yenilikler

Bu sürüm aşağıdaki hata düzeltmesini içerir:

- **Karşıya yükleme sorunu** -bu sürüm, yükleme yeniden başlatmalarının bir hatadan kaynaklanan karşıya yükleme işleminin hızını yavaşlatabilecek bir karşıya yükleme sorununu düzeltir. Bu sorun, birincil olarak bant genişliği azaltma etkin olduğunda, kullanılabilir bant genişliğine göre büyük olan ancak bunlarla sınırlı olmayan dosyalardan oluşan bir veri kümesi karşıya yüklenirken oluşabilir. Bu değişiklik, belirli bir dosya için karşıya yüklemeyi yeniden başlatmadan önce karşıya yükleme işleminin tamamlanmasına yetecek kadar fırsat sağlar.

Bu sürüm ayrıca aşağıdaki güncelleştirmeleri içerir:

- Tüm toplu Windows güncelleştirmeleri ve .NET Framework güncelleştirmeleri 2020 Ekim 'e göre yayınlandı.
- Temel kart yönetim denetleyicisi (BMC) bellenim sürümü, fabrika yüklemesi sırasında, daha yeni Dell güç kaynağı birimleri ile uyumsuzluk sağlamak için 3.32.32.32 'ten 3.36.36.36 'e yükseltilir.
- Bu sürüm Azure Stack Edge cihazlarında IoT Edge 1.0.9.3 destekler.

## <a name="known-issues-in-this-release"></a>Bu sürümdeki bilinen sorunlar

Bu yayın için Not belirtilen yeni bir sorun yok. Belirtilen tüm sürüm sorunları önceki sürümlerden devredilir. Bilinen sorunların bir listesini görmek için, [ga sürümündeki bilinen sorunlara](../databox-gateway/data-box-gateway-release-notes.md#known-issues-in-ga-release)gidin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge dağıtmaya hazırlanma](../databox-online/azure-stack-edge-deploy-prep.md)