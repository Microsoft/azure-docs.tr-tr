---
title: Azure Güvenlik Merkezi 'nde çapraz Kiracı Yönetimi | Microsoft Docs
description: " Azure Güvenlik Merkezi 'nde veri toplamayı etkinleştirmeyi öğrenin. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: f7aa75601639bc0c5652010fb44b01dec0d2622b
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996950"
---
# <a name="cross-tenant-management-in-security-center"></a>Güvenlik Merkezi 'nde çapraz Kiracı Yönetimi

Çapraz Kiracı Yönetimi, [Azure tarafından atanan kaynak yönetiminden](../lighthouse/concepts/azure-delegated-resource-management.md) yararlanarak Güvenlik Merkezi 'nde birden çok kiracının güvenlik duruşunu görüntülemenize ve yönetmenize olanak sağlar. Her kiracının dizininde oturum açmak zorunda kalmadan, birden çok kiracıyı tek bir görünümden verimli bir şekilde yönetin.

- Hizmet sağlayıcıları, birden çok müşteri için kaynakların güvenlik duruşunu kendi kiracılarının içinden yönetebilir.

- Birden çok kiracıya sahip kuruluşların güvenlik ekipleri, güvenlik duruşunu tek bir konumdan görüntüleyebilir ve yönetebilir.

## <a name="set-up-cross-tenant-management"></a>Çapraz kiracı yönetimini ayarlama

[Azure Temsilcili kaynak yönetimi](../lighthouse/concepts/azure-delegated-resource-management.md)'ni kullanarak kendi kiracınıza yönetilen kiracılar kaynaklarına erişim yetkisi vererek çapraz Kiracı Yönetimi ayarlayın.

> [!NOTE]
> Azure Temsilcili kaynak yönetimi, Azure Use 'ın temel bileşenlerinden biridir.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Güvenlik Merkezi 'nde çapraz Kiracı Yönetimi nasıl çalışır?

Birden çok Kiracıdaki abonelikleri, tek bir kiracıda birden çok aboneliği yönettiğiniz şekilde gözden geçirebilir ve yönetebilirsiniz.

Üst menü çubuğundan filtre simgesine tıklayın ve her bir kiracının dizininden görüntülemek istediğiniz abonelikleri seçin.

  ![Kiracıları filtrele](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Görünümler ve eylemler temelde aynıdır. İşte bazı örnekler:

- **Güvenlik Ilkelerini yönetme**: tek bir görünümden, [ilkelerle](tutorial-security-policy.md)birçok kaynağın güvenlik duruşunu yönetin, güvenlik önerileri ile eylemler gerçekleştirin ve güvenlikle ilgili verileri toplayın ve yönetin.
- **Güvenli puanı ve uyumluluğu iyileştirme**: çapraz kiracı görünürlüğü, tüm Kiracılarınızın genel güvenlik duruşunu ve bunların her biri için [güvenli puan](security-center-secure-score.md) ve [Uyumluluk](security-center-compliance-dashboard.md) duruşunu en iyi şekilde nasıl iyileştirebileceğinizi görüntülemenize olanak sağlar.
- **Önerileri düzeltin**: aynı anda çeşitli kiracılardan çok sayıda kaynak için bir [öneriyi](security-center-recommendations.md) izleyin ve düzeltin. Daha sonra tüm kiracılar genelinde en yüksek riski sunan güvenlik açıklarını hemen ekleyebilirsiniz.
- **Uyarıları yönetme**: farklı kiracılar genelinde [uyarıları](security-center-alerts-overview.md) algıla. Eyleme dönüştürülebilir [Düzeltme adımlarıyla](security-center-managing-and-responding-alerts.md)uyumlu olmayan kaynaklar üzerinde işlem yapın.

- **Gelişmiş bulut savunma özelliklerini ve daha fazlasını yönetin**: [tam zamanında (JIT) VM erişimi](security-center-just-in-time.md), [Uyarlamalı ağ sağlamlaştırma](security-center-adaptive-network-hardening.md), [Uyarlamalı uygulama denetimleri](security-center-adaptive-application.md)ve daha fazlası gibi çeşitli tehdit algılama ve koruma hizmetlerini yönetin.
 
## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, çapraz kiracı yönetiminin güvenlik merkezi 'nde nasıl çalıştığı açıklanmaktadır. Güvenlik Merkezi hakkında daha fazla bilgi edinmek için şunlara bakın:

* [Azure Güvenlik Merkezi ile güvenlik duruşunuzu güçlendirin](security-center-monitoring.md) -Azure kaynaklarınızın sistem durumunu nasıl izleyeceğinizi öğrenin.
* [Azure Güvenlik Merkezi hakkında SSS](security-center-faq.md) -hizmeti kullanma hakkında sık sorulan soruları bulun.
* [Kurumsal senaryolarda Azure ışıklı kullanımı hakkında bilgi edinin](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) -Azure açık thouse 'ın birden çok Azure AD kiracısının kullanıldığı bir kuruluşta çapraz kiracı yönetimini nasıl basitleştireceğinizi öğrenin.