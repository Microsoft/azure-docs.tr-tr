---
title: Azure 'daki SAP HANA için işletim sistemi yükseltmesi (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA için Işletim sistemi yükseltmesi gerçekleştirme (büyük örnekler)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6341f58791c2fad71a65650e32cff02fb52d78c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098674"
---
# <a name="operating-system-upgrade"></a>İşletim sistemi yükseltme
Bu belgede, HANA büyük örneklerinde işletim sistemi yükseltmeleriyle ilgili ayrıntılar açıklanmaktadır.

>[!NOTE]
>İşletim sistemi yükseltmesi müşterilerin sorumluluğudur, Microsoft operasyon desteği yükseltme sırasında izlemek için önemli alanlara kılavuzluk edebilir. Bir yükseltmeyi planlayabilmeniz için, işletim sistemi satıcınıza da başvurmanız gerekir.

HLI birim sağlama sırasında, Microsoft operasyon ekibi işletim sistemini yükler. Zaman içinde, işletim sistemini korumanız gerekir (örnek: Düzeltme eki uygulama, ayarlama, yükseltme vb.) , HLI biriminde.

İşletim sisteminde büyük değişiklikler yapmadan önce (örneğin, SP1 'i SP2'YE yükseltmek için), bir destek bileti açarak Microsoft Operasyon ekibine başvurmanız gerekir.

Biletini ekleyin:

* HLI abonelik KIMLIĞINIZ.
* Sunucu adınız.
* Uygulanmasını planladığınız düzeltme eki düzeyi.
* Bu değişikliği planlamanızın tarihi. 

Bu bileti, Işlem ekibinin, sunucu dikey penceresinde bellenim yükseltmesinin gerekli olup olmadığını denetlemesi nedeniyle, istenen yükseltme tarihinden önce en az bir hafta önce açmanız önerilir.


Farklı Linux sürümlerindeki farklı SAP HANA sürümlerinin destek matrisi için bkz. [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Bilinen sorunlar

Yükseltme sırasında yaygın olarak karşılaşılan birkaç sorun aşağıda verilmiştir:
- SKU türü II sınıf SKU 'sunda, yazılım altyapısı yazılımı (SFS) işletim sistemi yükseltmesinden sonra kaldırılır. İşletim sistemi yükseltmesinden sonra uyumlu SFS 'yi yeniden yüklemeniz gerekir.
- Ethernet kartı sürücüleri (ENIC ve FNıC) eski sürüme geri döndürüldü. Yükseltmeden sonra sürücülerin uyumlu sürümünü yeniden yüklemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar
- İşletim sistemi yedekleme türü ı SKU sınıfı için [yedekleme ve geri yükleme](hana-overview-high-availability-disaster-recovery.md) bölümüne bakın.
- Tür II SKU sınıfı için [Düzeltme 3 damgalarının tür II SKU 'ları Için Işletim sistemi yedeklemesi](os-backup-type-ii-skus.md) ' ne bakın.
