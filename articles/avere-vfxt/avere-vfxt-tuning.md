---
title: Avere vFXT kümesi ayarlama-Azure
description: Azure için avere vFXT içindeki performansı iyileştirmek için özel ayarlara genel bakış
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 274a20f84b978b38a284d1feb315d6621b7d3ccd
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256157"
---
# <a name="cluster-tuning"></a>Küme ayarlama


Birçok vFXT kümesi, özelleştirilmiş performans ayarlarından yararlanabilir. Bu ayarlar, kümenin belirli iş akışınız, veri kümeniz ve araçlarınızla en iyi şekilde çalışmasına yardımcı olur. 

Bu özelleştirme, genellikle avere Denetim Masası 'Nda kullanılamayan özellikleri yapılandırmayı içerdiğinden, bir destek temsilcisiyle birlikte yapılmalıdır.

Bu bölümde, yapılabilecek özel ayarlamadan bazıları açıklanmaktadır.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Genel iyileştirmeler

Bu değişiklikler, veri kümesi kalitelerini veya iş akışı stilini temel alan önerilen olabilir.

* İş yükü yazma ağır ise, yazma önbelleğinin boyutunu varsayılan% 20 ' den artırın. 
* Veri kümesi çok sayıda küçük dosya içeriyorsa, küme önbelleğinin dosya sayısı sınırını artırın. 
* İş, iki depo arasında veri kopyalamayı veya taşımayı içeriyorsa, verileri taşımak için kullanılan iş parçacıklarının sayısını ayarlayın: 
  * Hızı artırmak için, kullanılan paralel iş parçacığı sayısını artırabilirsiniz.
  * Arka uç depolama birimi aşırı yüklenmişse, kullanılan paralel iş parçacığı sayısını azaltmanıza gerek duyabilirsiniz.
* Küme, NFSv4 ACL 'Leri kullanan bir çekirdek filme için verileri önbelleğe alıyorsa, belirli istemciler için dosya yetkilendirmesini kolaylaştırmak üzere erişim modu önbelleğe almayı etkinleştirin.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Bulut NAS veya bulut ağ geçidi iyileştirmeleri

Bir bulut NAS veya ağ geçidi senaryosunda vFXT kümesi ile bulut depolaması arasındaki daha yüksek veri hızlarından yararlanmak için (vFXT kümesinin bir bulut kapsayıcısına NAS stili erişimi sağladığı), temsilciniz bunlar gibi ayarların daha fazla değiştirilmesini önerebilir önbellekten depolama birimine verileri bir daha Agresif iletme:

* Küme ve depolama kapsayıcısı arasındaki TCP bağlantısı sayısını artırma

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Bulut, hibrit veya karma WAN iyileştirmeleri

Bulut patlaması senaryosunda veya karma depolama WAN iyileştirmesi senaryosunda (vFXT kümesinin bulut ve şirket içi donanım depolaması arasında tümleştirme sağladığı), bu değişiklikler yararlı olabilir:

* Küme ve çekirdek filme arasında izin verilen TCP bağlantısı sayısını artırma
* Uzak çekirdek filme için WAN optimizasyon ayarını etkinleştirin (Bu ayar, uzak bir şirket içi filme veya farklı bir Azure bölgesindeki bir bulut çekirdeği için kullanılabilir.)
* TCP yuvası arabellek boyutunu (iş yüküne ve performans gereksinimlerine bağlı olarak) artırın
* Redundantly önbelleğe alınmış dosyaları azaltmak için "her zaman ilet" ayarını etkinleştirin (iş yüküne ve performans gereksinimlerine bağlı olarak)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Azure için avere vFXT 'nizi iyileştirmeye yardımcı olma

Bu iyileştirmeler hakkında destek personeline başvurmak için [sisteminizle ilgili yardım alın](avere-vfxt-open-ticket.md) bölümünde açıklanan yordamı kullanın. 