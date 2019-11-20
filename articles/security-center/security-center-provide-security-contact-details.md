---
title: Azure Güvenlik Merkezi 'nde güvenlik iletişim bilgilerini sağlama | Microsoft Docs
description: Bu belgede, Azure Güvenlik Merkezi 'nde güvenlik iletişim ayrıntılarını sağlama gösterilmektedir.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: fabccb7a3f49442a009fd69ee6d3b831760751d2
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201013"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde güvenlik iletişim bilgilerini sağlama
Azure Güvenlik Merkezi, henüz yapmadıysanız Azure aboneliğiniz için güvenlik iletişim bilgilerini sağlamanızı önerir. Bu bilgiler, Microsoft Güvenlik Yanıt Merkezi (MSRC) müşteri verilerinize yasadışı veya yetkisiz bir tarafın eriştiğini belirlerse Microsoft tarafından sizinle iletişim kurmak için kullanılır. MSRC, Azure ağ ve altyapısının güvenlik izlemesini seçme ve üçüncü taraflardan tehdit bilgilerini ve uygunsuz kullanım şikayetini alma işlemini gerçekleştirir.

Bir uyarının gün içinde ilk kez oluşması durumunda ve yalnızca yüksek önem düzeyindeki uyarılar için bir e-posta bildirimi gönderilir. E-posta tercihleri yalnızca abonelik ilkeleri için yapılandırılabilir. Bir aboneliğin altındaki kaynak grupları bu ayarları devralır. 

Uyarı e-posta bildirimleri şu durumlarda gönderilir:
- Yalnızca yüksek önem derecesindeki uyarılar için
- Uyarı türüne göre günde yalnızca tek bir e-posta alıcısına  
- Tek bir günde tek bir alıcıya 3 ' ten fazla e-posta iletisi gönderilmez
- Her e-posta iletisinde tek bir uyarı bulunur, uyarılar toplu gönderilmez
 
Örneğin bir RDP saldırısı konusunda uyarmak için bir e-posta iletisi gönderildiyse uyarı tetiklense dahi aynı gün içinde RDP saldırısıyla ilgili başka bir e-posta iletisi gönderilmez. 

> [!NOTE]
> Bu belge, örnek bir dağıtım kullanarak hizmeti tanıtır.  Bu, adım adım ilerleyen bir kılavuz değildir.

## Uyarılar için e-posta bildirimleri ayarlama<a name="email"></a>

1. Portalda **fiyatlandırma & ayarları**' nı seçin.
1. Aboneliğe tıklayın.
1. **E-posta bildirimleri**öğesine tıklayın.

> [!NOTE]
> Öneri uygulamadıysanız, **öneriler**altında **güvenlik iletişim bilgileri sağla**' yı seçin, Iletişim bilgilerini sağlamak için Azure aboneliğini seçin. Bu, **e-posta bildirimlerini**açar.

   ![Güvenlik ilgili kişisinin bilgilerini sağlayın][2]

   * Güvenlik ilgili kişisinin e-posta adresini veya adreslerini virgülle ayırarak girin. Girebilmeniz gereken e-posta adresi sayısı için bir sınır yoktur.
   * Bir güvenlik kişası uluslararası telefon numarası girin.
   * Yüksek önem derecesine sahip uyarılar hakkında e-posta almak için **bana uyarılar hakkında e-posta gönder**seçeneğini açın.
   * Gelecekte, abonelik sahiplerine e-posta bildirimleri gönderme seçeneğine sahip olursunuz. Bu seçenek şu anda gri renkte.
   * Güvenlik iletişim bilgilerini aboneliğinize uygulamak için **Kaydet** ' i seçin.

## <a name="see-also"></a>Ayrıca bkz.
Güvenlik Merkezi hakkında daha fazla bilgi edinmek için şunlara bakın:

* [Azure Güvenlik Merkezi'nde güvenlik ilkelerini ayarlama](tutorial-security-policy.md) -- Azure abonelikleriniz ve kaynak gruplarınız için güvenlik ilkelerini yapılandırma hakkında bilgi edinin.
* [Azure Güvenlik Merkezi 'nde güvenlik önerilerini yönetme](security-center-recommendations.md) -önerilerin Azure kaynaklarınızı korumanıza nasıl yardımcı olduğunu öğrenin.
* [Azure Güvenlik Merkezi 'Nde güvenlik durumu izleme](security-center-monitoring.md) -Azure kaynaklarınızın sistem durumunu izlemeyi öğrenin.
* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md) -- Güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile iş ortağı çözümlerini izleme](security-center-partner-solutions.md) - İş ortağı çözümlerinizin sistem durumunu nasıl izleyeceğiniz hakkında bilgi edinin.
* [Azure Güvenlik Merkezi ile ilgili SSS](security-center-faq.md) -- Hizmeti kullanımı ile ilgili sık sorulan soruları bulabilirsiniz.
* [Azure Güvenlik blogu](https://blogs.msdn.com/b/azuresecurity/) --en son Azure Güvenlik haberlerini ve bilgilerini alın.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
