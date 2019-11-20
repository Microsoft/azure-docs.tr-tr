---
title: Azure Data Box Gateway & Azure Data Box Edge 1906 sürüm notları | Microsoft Docs
description: Azure Data Box Gateway için kritik açık sorunlar ve çözümleri ve 1906 sürümü çalıştıran Azure Data Box Edge açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71099492"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure Data Box Edge ve Azure Data Box Gateway 1906 sürüm notları

Aşağıdaki sürüm notları, Azure Data Box Edge ve Azure Data Box Gateway için 1906 sürümüne yönelik kritik açık sorunları ve çözülmüş sorunları belirler.

Sürüm notları sürekli olarak güncelleştirilir ve geçici bir çözüm gerektiren kritik sorunlar keşfedildiğinde eklenirler. Data Box Edge/Data Box Gateway dağıtmadan önce, sürüm notlarında bulunan bilgileri dikkatle gözden geçirin.

Bu sürüm, yazılım sürümlerine karşılık gelir:

- **Data Box Gateway 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> Güncelleştirme 1906 yalnızca, yazılımın genel kullanılabilirlik (GA) veya 1905 sürümünü çalıştıran Data Box Edge cihazlara uygulanabilir.

## <a name="whats-new"></a>Yenilikler

- **Kurtarma anahtarı yönetimi iş akışında hata onarımı** -önceki sürümde, kurtarma anahtarının uygulanmadığı bir hata oluştu. Bu hata bu sürümde düzeltilmiştir. Kurtarma anahtarı, cihazın önyüklenememe durumunda Cihazdaki verileri kurtarmanızı sağlayan bu güncelleştirmeyi uygulamanızı kesinlikle öneririz. Daha fazla bilgi için bkz. [Data Box Edge veya Data Box Gateway dağıtımı sırasında kurtarma anahtarını kaydetme](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device).
- **Alan programlanabilir kapılı dizi (FPGA) günlüğü geliştirmeleri** -1905 sürümünden başlayarak, FPGA ile ilgili günlüğe kaydetme ve uyarı geliştirmeleri yapılmıştır. Bu, Edge işlem özelliğini FPGA ile kullanıyorsanız Data Box Edge için gerekli bir güncelleştirme olmaya devam eder. Daha fazla bilgi için bkz. [Data Box Edge Edge COMPUTE ile verileri dönüştürme](data-box-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>GA sürümündeki bilinen sorunlar

Bu yayın için Not belirtilen yeni bir sorun yok. Belirtilen tüm sürüm sorunları önceki sürümlerden devredilir. Bilinen sorunların bir listesini görmek için, [ga sürümündeki bilinen sorunlara](data-box-gateway-release-notes.md#known-issues-in-ga-release)gidin.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Box Gateway'i dağıtmaya hazırlanma](data-box-gateway-deploy-prep.md)
- [Azure Data Box Edge’i dağıtmaya hazırlanma](data-box-edge-deploy-prep.md)
