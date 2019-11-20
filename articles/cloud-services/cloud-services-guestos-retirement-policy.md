---
title: Azure Konuk işletim sistemi için desteklenebilirlik ve emeklilik ilkesi Kılavuzu | Microsoft Docs
description: Microsoft 'un Cloud Services tarafından kullanılan Azure Konuk işletim sistemi ile ilgili olarak desteklediği bilgiler hakkında bilgi sağlar.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: fbe9b3379799fe3cf0a56d921ab257bc87606ca9
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945443"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Azure Konuk işletim sistemi desteklenebilirlik ve emeklilik ilkesi
Bu sayfadaki bilgiler, Cloud Services worker ve Web rolleri (PaaS) için Azure Konuk işletim sistemi ([konuk OS](cloud-services-guestos-update-matrix.md)) ile ilgilidir. Sanal makineler (IaaS) için geçerlidir.

Microsoft 'un [Konuk işletim sistemi için yayımlanmış bir destek ilkesi](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq)vardır. Okuduğunuz sayfa, ilkenin nasıl uygulandığını anlatmaktadır.

İlke

1. Microsoft, **Konuk işletim sisteminin en az iki ailesinden birini**destekleyecektir. Bir aile devre dışı bırakıldığında, müşterilerin resmi kullanımdan kaldırma tarihinden itibaren daha yeni desteklenen bir konuk işletim sistemi ailesine güncelleştirilmesi için 12 ay daha vardır.
2. Microsoft, **Desteklenen Konuk işletim sistemi ailelerinin en az iki sürümünü**destekleyecektir.
3. Microsoft, **en azından Azure SDK 'nın en son iki sürümünü**destekleyecektir. SDK 'nın bir sürümü kullanımdan kaldırıldığında müşterilerin daha yeni bir sürüme güncelleştirilmesi için resmi kullanımdan kaldırma tarihinden itibaren 12 ay olur.

Her zaman iki aileden veya yayınların daha fazlası desteklenebilir. Resmi Konuk işletim sistemi destek bilgileri, [Azure Konuk işletim sistemi sürümleri ve SDK uyumluluk matrisi](cloud-services-guestos-update-matrix.md)'nde görünür.

## <a name="when-a-guest-os-version-is-retired"></a>Konuk işletim sistemi sürümü kullanımdan kaldırıldığında
Yeni Konuk işletim sistemi **sürümleri** , en son MSRC güncelleştirmelerini birleştirmek için her ay hakkında tanıtılmıştır. Normal aylık güncelleştirmeler nedeniyle, Konuk işletim sistemi sürümü normalde, yayınlanmasından sonra 60 gün içinde devre dışı bırakılır. Bu etkinlik, her bir aile için en az iki konuk işletim sistemi sürümünün kullanılabilmesini önler.

### <a name="process-during-a-guest-os-family-retirement"></a>Konuk işletim sistemi ailesi kullanımdan kaldırılması sırasında işlem
Emeklilik duyurulduktan sonra, müşterilerin eski aile hizmetten kaldırılmadan önce 12 aylık "geçiş" süresi vardır. Bu geçiş süresi, Microsoft 'un bir şekilde genişletilebilir. Güncelleştirmeler [Azure Konuk işletim sistemi sürümleri ve SDK uyumluluk matrisi](cloud-services-guestos-update-matrix.md)' ne gönderilir.

Aşamalı bir kullanımdan kaldırma işlemi, geçiş dönemine altı (6) ay sonra başlayacaktır. Şu süre boyunca:

1. Microsoft, emekliliğin müşterilerine bildirim gönderir.
2. Azure SDK 'sının daha yeni sürümü kullanımdan kaldırılan Konuk işletim sistemi ailesini desteklemez.
3. Kullanımdan kaldırılan ailede Cloud Services Yeni dağıtımlar ve yeniden dağıtımlarına izin verilmez

Microsoft, "sona erme tarihi" olarak bilinen geçiş döneminin son gününe kadar en son MSRC güncelleştirmelerini dahil eden yeni Konuk işletim sistemi sürümünü almaya devam edecektir. Sona erme tarihi, Azure SLA altında hala çalışmaya devam Cloud Services desteklenebilir. Microsoft bu tarihten sonra bu hizmetleri yükseltmeyi, silmeyi veya durdurmayı zorlamaya yönelik bir kararına sahiptir.

### <a name="process-during-a-guest-os-version-retirement"></a>Konuk işletim sistemi sürümü kullanımdan kaldırılması sırasında işlem
Müşteriler Konuk işletim sistemini otomatik olarak güncelleştirecek şekilde ayarlarsanız, Konuk işletim sistemi sürümleriyle uğraşmak zorunda kalmaz. Her zaman en son Konuk işletim sistemi sürümünü kullanacaktır.

Konuk işletim sistemi sürümleri her ay yayımlanır. Normal sürümlerin oranı nedeniyle, her sürümde sabit bir lifespan bulunur.

Süre 60 gün içinde, bir sürüm "*devre dışı*". "Devre dışı", sürümün portaldan kaldırıldığı anlamına gelir. Sürüm artık CSCFG yapılandırma dosyasından ayarlanamaz. Mevcut dağıtımlar çalışıyor. Ancak, var olan dağıtımlara yönelik yeni dağıtımlar ve kod ve yapılandırma güncelleştirmelerine izin verilmez.

"Devre dışı" olduktan sonra, Konuk işletim sistemi sürümü "süre sonu" ve bu sürümü hala çalıştıran Yüklemeler, güvenlik ve güvenlik açığı sorunlarına açıktır. Genellikle, süre sonu toplu olarak yapılır, bu nedenle, disablement 'ten süre sonuna kadar olan süre farklılık gösterebilir.

Hizmetlerini Konuk işletim sistemini el ile güncelleştirmek üzere yapılandıran müşteriler, hizmetlerinin desteklenen bir konuk işletim sisteminde çalıştığından emin olmalıdır. Bir hizmet Konuk işletim sistemini otomatik olarak güncelleştirecek şekilde yapılandırıldıysa, temel alınan platform uyumluluğa sahip olur ve en son Konuk işletim sistemine yükseltilir.

Bu dönemler, Microsoft 'un müşteri geçişlerini kolaylaştırmak için daha uzun bir süre yapılabilir. [Azure Konuk işletim sistemi sürümleri ve SDK Uyumluluk matrisinde](cloud-services-guestos-update-matrix.md)tüm değişiklikler gönderilir.

### <a name="notifications-during-retirement"></a>Kullanımdan kaldırma sırasındaki bildirimler
* **Aile kullanımdan kaldırma** <br>Microsoft, blog gönderilerini ve Portal bildirimini kullanır. Kullanımdan kaldırılan bir konuk işletim sistemi ailesini kullanmaya devam eden müşteriler, atanan hizmet yöneticilerine doğrudan iletişim (e-posta, Portal iletileri, telefon araması) aracılığıyla bildirilir. Tüm değişiklikler [Azure Konuk işletim sistemi sürümleri ve SDK uyumluluğu matrisine](cloud-services-guestos-update-matrix.md)gönderilir.
* **Sürüm kullanımdan kaldırma** <br>Tüm değişiklikler ve yaptığınız tarihler, yayın, devre dışı ve sona erme dahil olmak üzere [Azure Konuk işletim sistemi sürümleri ve SDK uyumluluğu matrisine](cloud-services-guestos-update-matrix.md)gönderilir. Hizmet yöneticileri, devre dışı bırakılmış bir konuk işletim sistemi sürümünde veya ailesinde çalışan dağıtımlar varsa e-posta alır. Bu e-postaların zamanlaması farklılık gösterebilir. Genellikle bu zamanlama resmi bir SLA değil, disablement 'in en az bir ayda olur.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular
**Geçişin etkilerini nasıl azaltabilirim?**

Cloud Services tasarlamak için en son Konuk işletim sistemi ailesini kullanmanızı öneririz.

1. Geçişinizi daha yeni bir aileden erken planlamaya başlayın.
2. Yeni aile üzerinde çalışan bulut hizmetinizi test etmek için geçici test dağıtımları ayarlayın.
3. Konuk işletim sistemi sürümünüzü **Otomatik** (osVersion = * [. cscfg](cloud-services-model-and-package.md#cscfg) dosyasında) olarak ayarlayın, böylece yeni Konuk işletim sistemi sürümlerine geçiş otomatik olarak gerçekleşir.

**Web uygulamamın işletim sistemi ile daha derin bir tümleştirme gerektirmesi durumunda ne olacak?**

Web uygulaması mimariniz işletim sisteminin temel özelliklerine bağımlıysa, [Başlangıç görevleri](cloud-services-startup-tasks.md) veya diğer genişletilebilirlik mekanizmaları gibi platform tarafından desteklenen özellikleri kullanın. Alternatif olarak, temel işletim sisteminin korunmasından sorumlu olduğunuz [Azure sanal makinelerini](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (hizmet olarak altyapı) de kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
En son [Konuk işletim sistemi sürümlerini](cloud-services-guestos-update-matrix.md)gözden geçirin.
