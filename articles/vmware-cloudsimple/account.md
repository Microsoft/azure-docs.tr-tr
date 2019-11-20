---
title: Hesap yönetimi-CloudSimple portalı tarafından Azure VMware çözümü
description: Azure VMware çözümünde, CloudSimple Portal tarafından hesapların nasıl yönetileceğini açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69564192"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>CloudSimple Portal tarafından Azure VMware çözümünde hesapları yönetme

CloudSimple hizmetinizi oluşturduğunuzda, CloudSimple üzerinde bir hesap oluşturur. Hesap, hizmetin bulunduğu Azure aboneliğiniz ile ilişkilendirilir. Abonelikte sahip ve katkıda bulunan rollerine sahip tüm kullanıcıların CloudSimple portalına erişimi vardır. CloudSimple hizmetiyle ilişkili Azure abonelik KIMLIĞI ve kiracı KIMLIĞI hesaplar sayfasında bulunur.

CloudSimple portalındaki hesapları yönetmek için [portala erişin](access-cloudsimple-portal.md) ve yan menüdeki **Hesap** ' ı seçin.

Şirketinizin CloudSimple yapılandırması hakkındaki bilgileri görüntülemek için **Özet** ' i seçin. Bulut yapılandırmanızın geçerli kapasitesi, özel bulutlar sayısı, toplam depolama, vSphere küme yapılandırması, düğüm sayısı ve işlem çekirdekleri dahil gösteriliyor. Geçerli yapılandırma tüm ihtiyaçlarınızı karşılamıyorsa ek düğümler satın almak için bir bağlantı eklenmiştir.

## <a name="email-alerts"></a>E-posta uyarıları

Özel bulut yapılandırmasında değişiklikler hakkında bilgilendirmek istediğiniz kişilerin e-posta adreslerini ekleyebilirsiniz.

1. **Ek e-posta uyarıları** alanında **Yeni Ekle**' ye tıklayın.
2. E-posta adresini girin.
3. Return tuşuna basın.  

Bir girişi kaldırmak için **X**' e tıklayın.

## <a name="cloudsimple-operator-access"></a>CloudSimple işleç erişimi

Operatör erişim ayarı, cloudsimple portalında oturum açmaya yönelik bir destek mühendisine izin vererek CloudSimple 'ın sorun gidermeye yardımcı olmasına olanak tanır.  Ayar varsayılan olarak etkindir. Müşteri hesabınızda oturum açarken destek mühendisi tarafından gerçekleştirilen tüm eylemler, **etkinlik** > **Denetim** sayfasında gözden geçirmeniz için kaydedilir ve kullanılabilir.

Erişimi açmak veya kapatmak için **Cloudsimple işleç erişimi etkin** ' e tıklayın.
