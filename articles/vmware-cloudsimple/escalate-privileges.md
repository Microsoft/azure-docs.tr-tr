---
title: CloudSimple özellikli cloudsimple ayrıcalıklarına göre Azure VMware çözümü
description: Özel bulut vCenter 'da yönetim işlevleri gerçekleştirmek için CloudSimple izinlerinin nasıl ilerletiloluşturulacağını açıklar
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d481717a79856583d23f61107678d2ecd1af68d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895736"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Özel bulut vCenter 'da yönetim işlevleri gerçekleştirmek için CloudSimple ayrıcalıklarını ilerletin

CloudSimple ayrıcalık yaklaşımı, vCenter kullanıcılarına normal işlemler gerçekleştirmek için ihtiyaç duydukları ayrıcalıkları vermek üzere tasarlanmıştır. Bazı örneklerde, bir Kullanıcı belirli bir görevi gerçekleştirmek için ek ayrıcalıklar gerektirebilir.  Bir vCenter SSO kullanıcısının ayrıcalıklarını sınırlı bir süre için ilerletebilirsiniz.

Yürüyen ayrıcalıkların nedenleri şunları içerebilir:

* Kimlik kaynakları yapılandırması
* Kullanıcı yönetimi
* Dağıtılmış bağlantı noktası grubu silme
* VCenter çözümlerini yükleme (yedekleme uygulamaları gibi)
* Hizmet hesapları oluşturma

> [!WARNING]
> İlerletilen ayrıcalıklı durumda gerçekleştirilen eylemler sisteminizi olumsuz etkileyebilir ve sisteminizin kullanılamaz hale gelmesine neden olabilir. Yalnızca yükseltme döneminde gerekli eylemleri gerçekleştirin.

CloudSimple portalından, vCenter SSO 'su yerel kullanıcısına ait ayrıcalıkları vCenter SSO 'da [ilerletin](escalate-private-cloud-privileges.md) .  Yalnızca vCenter üzerinde ek kimlik sağlayıcısı yapılandırılmışsa uzak kullanıcının ayrıcalığını ilerletebilirsiniz.  Ayrıcalıkların artırılması, seçilen kullanıcıyı vSphere yerleşik Yöneticiler grubuna eklemeyi içerir.  Yalnızca bir Kullanıcı ilerletilen ayrıcalıklara sahip olabilir.  Başka bir kullanıcının ayrıcalıklarını yükseltmeniz gerekiyorsa, önce geçerli kullanıcıların ayrıcalıklarını de ilerletin.

Ek kimlik kaynaklarındaki kullanıcıların, CloudOwner grubunun üyesi olarak eklenmesi gerekir.

> [!CAUTION]
> Yeni kullanıcılar yalnızca *bulut sahibi grubu*, *bulut-genel-küme-yönetici-grubu*, *bulut-genel-depolama-yönetici-grubu*, bulut-genel- *Ağ-Yönetici-Grup* veya *bulut-genel-VM-yönetici grubu* için eklenmelidir.  *Yöneticiler* grubuna eklenen kullanıcılar otomatik olarak kaldırılacaktır.  Yalnızca hizmet hesaplarının *Yöneticiler* grubuna eklenmesi gerekir ve hizmet hesapları vSphere Web Kullanıcı arabiriminde oturum açmak için kullanılmamalıdır.

Yükseltme döneminde CloudSimple, ortamda yanlışlıkla yapılan değişiklikleri belirlemek için ilişkili uyarı bildirimleriyle otomatik izlemeyi kullanır.
