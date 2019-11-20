---
title: Kalem testi | Microsoft Docs
description: Bu makalede, sızma testi (pentest) işlemine genel bakış ve Azure altyapısında çalışan uygulamalarınıza karşı pentest gerçekleştirme işlemleri sunulmaktadır.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: barclayn
ms.openlocfilehash: 3a2addce83862ef109089f1474330f3821daaed7
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726710"
---
# <a name="penetration-testing"></a>Sızma Testi
Uygulama testi ve dağıtımı için Azure kullanmanın avantajlarından biri, oluşturulan ortamları hızlıca almanızı sağlar. Kendi şirket içi donanımlarınızı talep eden, alma ve "izleme ve yığınlama" konusunda endişelenmeniz gerekmez.

Bu harika değildir, ancak yine de normal güvenlik süresi ile bu işlemi gerçekleştirdiğinizden emin olmanız gerekir. Büyük olasılıkla yapmak istediğiniz şeylerden biri, Azure 'da dağıttığınız uygulamaları test ediyor.

Microsoft 'un [Azure ortamımızın sızma testi](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)gerçekleştirdiğini zaten biliyorbilirsiniz. Bu, Azure 'un geliştirilmesine yardımcı olur.

Uygulamanızı sizin için test etmeyin, ancak kendi uygulamalarınızda test yapmanız gerektiğini ve istediğinizi anladık. Bu iyi bir şeydir çünkü uygulamalarınızın güvenliğini geliştirmenizde tüm Azure ekosisteminin daha güvenli hale getirilmesine yardımcı olursunuz.

15 Haziran 2017 itibariyle, Microsoft artık Azure kaynaklarına karşı bir deneme testi yapmak için önceden onay gerektirmez. Microsoft Azure karşı yaklaşan test görevlendirmesini isteyen müşteriler, [Azure hizmeti sızma testi bildirim formunu](https://portal.msrc.microsoft.com/en-us/engage/pentest)doldurmaları önerilir. Bu işlem yalnızca Microsoft Azure ile ilgilidir ve diğer Microsoft Bulut Hizmetleri için geçerli değildir.

>[!IMPORTANT]
>Pen test etkinliklerinin Microsoft 'a bildirimde bulunmak için artık gerekli müşterilerin, [katılımın Microsoft bulut birleştirilmiş test kuralları](https://technet.microsoft.com/mt784683)ile uyumlu olması gerekir.

Gerçekleştirebileceğiniz standart testler şunlardır:

* [Açık Web uygulaması güvenlik projesini (OWASP) ilk 10 güvenlik açığını açmak](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) için uç noktalarınızda test edin
* Uç noktalarınızın [belirsizlik testi](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* Uç noktalarınız için [bağlantı noktası taraması](https://en.wikipedia.org/wiki/Port_scanner)

Gerçekleştirmeniz gereken bir test türü, herhangi bir [hizmet reddi (DOS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) saldırısı türüdür. Bu, bir DoS saldırının kendisini başlatmayı veya herhangi bir DoS saldırısı türünü tespit edebilir, gösterebilir veya benzetim yaparak ilgili testlerin gerçekleştirilmesini içerir.

## <a name="next-steps"></a>Sonraki adımlar

- Microsoft Azure ' de barındırılan uygulamalarınıza yönelik yaklaşan bir testi bir şekilde belgelemek isterseniz, [katılım testi kuralları ' nın](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) üzerine gidin ve test bildirim formunu doldurun.
