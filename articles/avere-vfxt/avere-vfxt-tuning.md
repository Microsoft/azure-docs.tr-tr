---
title: Avere vFXT kümesi ayarlama-Azure
description: Azure için avere vFXT içindeki vFXT kümeleri için bir destek temsilcisiyle çalışarak, bazı özel ayarlama hakkında bilgi edinin.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 5d9f81c9438cb992f81bd3e6319532d67db75552
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88272391"
---
# <a name="cluster-tuning"></a>Küme ayarlama

Birçok vFXT kümesi, özelleştirilmiş performans ayarlarından yararlanabilir. Bu ayarlar, kümenin belirli iş akışınız, veri kümeniz ve araçlarınızla en iyi şekilde çalışmasına yardımcı olur.

Bu özelleştirme, avere Denetim Masası 'Nda kullanılamayan özellikleri yapılandırmayı içerebileceği için bir destek temsilcisinden yardım ile yapılmalıdır.

Bu bölümde, yapılabilecek özel ayarlamaların bazıları açıklanmaktadır.

## <a name="general-optimizations"></a>Genel iyileştirmeler

Bu değişiklikler, veri kümesi kalitelerini veya iş akışı stilini temel alan önerilen olabilir.

* İş yükü yazma ağır ise, yazma önbelleğinin boyutunu varsayılan %20 ' den artırın.
* Veri kümesi çok sayıda küçük dosya içeriyorsa, küme önbelleğinin dosya sayısı sınırını artırın.
* İş, iki depo arasında veri kopyalamayı veya taşımayı içeriyorsa, verileri taşımak için kullanılan iş parçacıklarının sayısını ayarlayın:
  * Hızı artırmak için, kullanılan paralel iş parçacığı sayısını artırabilirsiniz.
  * Arka uç depolama birimi aşırı yüklenmişse, kullanılan paralel iş parçacığı sayısını azaltmanıza gerek duyabilirsiniz.
* Küme, NFSv4 ACL 'Leri kullanan bir çekirdek filme için verileri önbelleğe alıyorsa, belirli istemciler için dosya yetkilendirmesini kolaylaştırmak üzere erişim modu önbelleğe almayı etkinleştirin.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Bulut NAS veya bulut ağ geçidi iyileştirmeleri

Bulut NAS veya ağ geçidi senaryosunda, vFXT kümesi bir bulut kapsayıcısına NAS stili erişim sağlar. VFXT kümesi ve bulut depolaması arasındaki daha yüksek veri hızlarından yararlanmak için temsilciniz, önbellekteki depolama hacmine daha fazla veri gönderim için ayarları değiştirmenizi önerebilir. Örnek:

* Küme ve depolama kapsayıcısı arasındaki TCP bağlantısı sayısını artırma

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Bulut, hibrit veya karma WAN iyileştirmeleri

Bulut hibrit senaryosunda veya karma depolama WAN iyileştirmesi senaryosunda, vFXT kümesi, bulut ile şirket içi donanım depolaması arasında tümleştirme sağlar. Bu değişiklikler yararlı olabilir:

* Küme ve çekirdek filme arasında izin verilen TCP bağlantısı sayısını artırma
* Uzak çekirdek filme için WAN optimizasyon ayarını etkinleştirin (Bu ayar, uzak bir şirket içi filme veya farklı bir Azure bölgesindeki bir bulut çekirdeği için kullanılabilir.)
* TCP yuvası arabellek boyutunu artır<sup>*</sup>
* Redundantly önbelleğe alınmış dosyaları azaltmak için "her zaman ilet" ayarını etkinleştirin<sup>*</sup>

<sup>*</sup>Bu ayarlamalar, iş yüküne ve performans gereksinimlerine bağlı olarak tüm sistemlere uygulanmayabilir.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Azure için avere vFXT 'nizi iyileştirmeye yardımcı olma

Bu iyileştirmeler hakkında destek personeline başvurmak için, [sisteminizle ilgili yardım alın](avere-vfxt-open-ticket.md)bölümünde açıklanan yordamı kullanın.
