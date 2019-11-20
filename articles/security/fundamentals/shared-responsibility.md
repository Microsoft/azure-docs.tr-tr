---
title: Bulutta paylaşılan sorumluluk-Microsoft Azure
description: Paylaşılan sorumluluk modelini ve hangi güvenlik görevlerinin bulut sağlayıcısı tarafından işlendiğini ve sizin tarafınızdan hangi görevlerin işlendiğini anlayın.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2019
ms.author: terrylan
ms.openlocfilehash: 8f16105d6bda1798828bc423ec8a158d49e0cf2b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518375"
---
# <a name="shared-responsibility-in-the-cloud"></a>Bulutta paylaşılan sorumluluk

Genel bulut hizmetlerini göz önünde bulundurun ve değerlendirdiğiniz gibi, paylaşılan sorumluluk modelini ve hangi güvenlik görevlerinin bulut sağlayıcısı tarafından işlendiğini ve sizin tarafınızdan hangi görevlerin işlendiğini anlamak önemlidir. İş yükü sorumlulukları, iş yükünün hizmet olarak yazılım (SaaS), hizmet olarak platform (PaaS), hizmet olarak altyapı (IaaS) veya şirket içi veri merkezinde barındırılmasına bağlı olarak farklılık gösterir

## <a name="division-of-responsibility"></a>Sorumluluk bölümü
Şirket içi bir veri merkezinde tüm yığının sahibi olursunuz. Buluta geçtiğinizde, bazı sorumluluklar Microsoft 'a aktarır. Aşağıdaki diyagramda, yığınınızın dağıtım türüne göre siz ve Microsoft arasındaki sorumluluğun bulunduğu konular gösterilmektedir.

![Sorumluluk alanları](./media/shared-responsibility/shared-responsibility.png)

Tüm bulut dağıtım türleri için verileriniz ve kimlikleriniz size aittir. Verilerinizin ve kimliklerinizin güvenliğini, şirket içi kaynaklarınızı ve denetlediğiniz bulut bileşenlerini (hizmet türüne göre farklılık gösterir) korumaktan siz sorumlusunuz.

Dağıtım türünden bağımsız olarak, aşağıdaki sorumluluklar her zaman sizin tarafınızdan tutulur:

- Veriler
- Uç Noktalar
- Hesap
- Erişim yönetimi

## <a name="cloud-security-advantages"></a>Bulut güvenliği avantajları
Bulut, uzun süreli bilgi güvenliği güçlüklerinin çözümünde önemli avantajlar sağlar. Şirket içi bir ortamda kuruluşlar, saldırganların tüm katmanlarda güvenlik açıklarından yararlanabileceği bir ortam oluşturan sorumlulukların ve sınırlı kaynakların güvenlik altına yatırmasını sağlar.

Aşağıdaki diyagramda, sınırlı kaynaklar nedeniyle birçok güvenlik sorumlulukların karşılandığı geleneksel bir yaklaşım gösterilmektedir. Bulut özellikli yaklaşımda, günlük güvenlik sorumluluklarını bulut sağlayıcınızda kaydırabiliyor ve kaynaklarınızı yeniden tahsis edebilirsiniz.

![Bulut çağının güvenlik avantajları](./media/shared-responsibility/cloud-enabled-security.png)

Bulut özellikli yaklaşımda, daha fazla verimlilik için bulut tabanlı güvenlik özelliklerinden yararlanabilir ve tehdit algılama ve yanıt süresini geliştirmek için bulut zekasını kullanabilirsiniz. Kuruluşlar bulut sağlayıcısına kaydırarak, güvenlik kaynaklarını ve bütçesini diğer iş öncelikleriyle yeniden ayırmaya olanak sağlayan daha fazla güvenlik kapsamı alabilir.

## <a name="next-steps"></a>Sonraki adımlar
SaaS, PaaS ve IaaS dağıtımında sizinle Microsoft arasındaki sorumluluk bölümü hakkında daha fazla bilgi için bkz. [bulut bilgi işlem Için paylaşılan sorumluluklar](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).
