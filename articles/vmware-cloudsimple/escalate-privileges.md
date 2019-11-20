---
title: CloudSimple özellikli cloudsimple ayrıcalıklarına göre Azure VMware çözümü
description: Özel bulut vCenter 'da yönetim işlevleri gerçekleştirmek için CloudSimple izinlerinin nasıl ilerletiloluşturulacağını açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 524772578ad724e969bbeab0be0a3edcf32a845f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619618"
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

CloudSimple portalından, vCenter SSO 'su yerel kullanıcısına ait [ayrıcalıkları](escalate-private-cloud-privileges.md) vCenter SSO 'da ilerletin.  Yalnızca vCenter üzerinde ek kimlik sağlayıcısı yapılandırılmışsa uzak kullanıcının ayrıcalığını ilerletebilirsiniz.  Ayrıcalıkların artırılması, seçilen kullanıcıyı vSphere yerleşik Yöneticiler grubuna eklemeyi içerir.  Yalnızca bir Kullanıcı ilerletilen ayrıcalıklara sahip olabilir.  Başka bir kullanıcının ayrıcalıklarını yükseltmeniz gerekiyorsa, önce geçerli kullanıcıların ayrıcalıklarını de ilerletin.

Ek kimlik kaynaklarındaki kullanıcıların, CloudOwner grubunun üyesi olarak eklenmesi gerekir.

Yükseltme döneminde CloudSimple, ortamda yanlışlıkla yapılan değişiklikleri belirlemek için ilişkili uyarı bildirimleriyle otomatik izlemeyi kullanır.
