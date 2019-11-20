---
title: Azure Güvenlik Merkezi'nde kimliği ve erişimi izleme | Microsoft Docs
description: Kullanıcılarınızın erişim etkinliğini ve kimlikle ilgili sorunları izleme amacıyla Azure Güvenlik Merkezi'ndeki tanımlama ve erişim özelliklerini nasıl kullanacağınızı öğrenin.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2018
ms.author: memildin
ms.openlocfilehash: 8b069bedd3c36f27828e54a1110443ae6dfcdf3f
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268853"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Azure Güvenlik Merkezi 'nde kimliği ve erişimi izleme (Önizleme)
Bu makale kullanıcılarınızın kimliğini ve erişim etkinliğini izleme amacıyla Azure Güvenlik Merkezi'ni kullanmanıza yardımcı olur.

> [!NOTE]
> İzleme kimliği ve erişimi önizleme aşamasındadır ve yalnızca güvenlik merkezi 'nin standart katmanında kullanılabilir. Güvenlik Merkezi’nin fiyatlandırma katmanları hakkında daha fazla bilgi almak için bkz. [Fiyatlandırma](security-center-pricing.md).
>

Kimlik, kuruluşunuz için denetim düzlemi olmalıdır, kimliğinizi korumak ise en yüksek önceliğiniz olmalıdır. Güvenlik çevresi, bir ağ çevre öğesinden bir kimlik çevre 'e geliştirilmiştir. Güvenlik, ağınızı erteleme ve verilerinizi erteleme ve uygulamalarınızın ve kullanıcılarınızın güvenliğini yönetme hakkında daha az hale gelir. Günümüzde buluta taşınan veri ve uygulama miktarı arttıkça kimlik yeni savunma hattı haline geliyor.

Kimlik etkinliklerini izleyerek bir olay gerçekleşmeden önce öngörülü eylemlerde veya bir saldırı girişimini durdurmak için reaktif eylemlerde bulunabilirsiniz. Kimlik & erişim panosu, şunları gibi öneriler sağlar:

- Aboneliğinizde ayrıcalıklı hesaplar için MFA'yı etkinleştirin
- Yazma izinleri olan dış hesapları aboneliğinizden kaldırın
- Ayrıcalıklı dış hesapları aboneliğinizden kaldırın

> [!NOTE]
> Aboneliğinizin 600 'den fazla hesabı varsa, Güvenlik Merkezi, aboneliğinize karşı kimlik önerilerini çalıştıramadı. Çalıştırılmayan öneriler aşağıda açıklanan "kullanılamayan değerlendirmeler" altında listelenmiştir.
Güvenlik Merkezi, bulut çözümü sağlayıcısı (CSP) ortağının yönetim aracılarından dolayı kimlik önerilerini çalıştıramıyor.
>

Güvenlik Merkezi tarafından sunulan kimlik ve erişim önerilerinin listesine yönelik [önerilere](security-center-identity-access.md#recommendations) bakın.

## <a name="monitoring-security-health"></a>Güvenlik durumunu izleme
Kaynaklarınızın güvenlik durumunu izleyebilirsiniz **Güvenlik Merkezi-genel bakış** Pano. **Kaynaklar** bölümü, her bir kaynak türü için önem derecelerine gösteren bir sistem durumu göstergesidir.

Seçerek, tüm sorunların bir listesini görüntüleyebilirsiniz **önerileri**. **Kaynaklar**' ın altında, işlem & uygulamalar, veri güvenliği, ağ veya kimlik & erişimine özgü sorunların bir listesini görebilirsiniz. Önerileri uygulama hakkında daha fazla bilgi için bkz. [Azure Güvenlik Merkezi'nde güvenlik önerilerini uygulama](security-center-recommendations.md).

Kimlik ve erişim önerilerinin tüm listesi için bkz. [öneriler](security-center-identity-access.md#recommendations).

Devam etmek için, **kaynaklar** veya güvenlik merkezi ana menüsü altında **kimlik & erişim** ' i seçin.

![Güvenlik Merkezi panosu][1]

## <a name="monitor-identity-and-access"></a>Kimlik ve erişimi izleme
**Kimlik & erişimi**altında iki sekme vardır:

- **Genel bakış**: Güvenlik Merkezi tarafından tanımlanan öneriler.
- **Abonelikler**: aboneliklerinizin ve geçerli güvenlik durumunun listesi.

![Kimlik ve Erişim][2]

### <a name="overview-section"></a>Genel bakış bölümü
**Genel bakış**' ın altında, önerilerin bir listesi bulunur. İlk sütunda öneriler listelenmiştir. İkinci sütunda, bu öneriden etkilenen toplam abonelik sayısı gösterilmektedir. Üçüncü sütun, sorunun önem derecesini gösterir.

1. Bir öneri seçin. Öneriler penceresi açılır ve şunları görüntüler:

   - Önerinin açıklaması
   - Sağlıksız ve sağlıklı aboneliklerin listesi
   - Başarısız bir değerlendirme nedeniyle taranmamış kaynakların listesi veya kaynak ücretsiz katmanda çalışan bir aboneliğin altında ve değerlendirilmedi

   ![Öneri penceresi][3]

1. Ek ayrıntı için listeden bir abonelik seçin.

### <a name="subscriptions-section"></a>Abonelikler bölümü
**Abonelikler**' in altında, aboneliklerin listesi bulunur. İlk sütunda abonelikler listelenir. İkinci sütun, her abonelik için toplam öneri sayısını gösterir. Üçüncü sütun sorunların önem derecelerine gösterir.

![Abonelikler sekmesi][4]

1. Bir abonelik seçin. Özet görünümü ile üç sekme açar:

   - **Öneriler**: Değerlendirme başarısız oldu Güvenlik Merkezi tarafından gerçekleştirilen göre.
   - **Değerlendirmeler geçirilen**: Güvenlik Merkezi tarafından geçirilen gerçekleştirdiği değerlendirmeler listesi.
   - **Kullanılamayan değerlendirmeler**: bir hata nedeniyle veya abonelikte 600 'den fazla hesap bulunduğundan başarısız olan değerlendirmelerin listesi.

   **Öneriler** altında, seçilen abonelik ve her bir önerinin önem derecesine ilişkin önerilerin bir listesidir.

   ![Abonelik seçme önerileri][5]

1. Önerinin açıklaması, sağlıksız ve sağlıklı aboneliklerin listesi ve taranmamış kaynakların bir listesi için bir öneri seçin.

   ![Önerinin açıklaması][6]

   Altında **değerlendirmeleri geçirilen** geçilen iç değerlendirmeler listesidir.  Bu değerlendirmeler önemini her zaman büyük/küçük harf yeşildir.

   ![Geçilen iç değerlendirmeler][7]

1. Değerlendirmenin açıklaması ve sağlıklı aboneliklerin listesi için listeden bir geçmiş değerlendirmesi seçin. Sağlıksız abonelikler için başarısız olan tüm abonelikleri listeleyen bir sekme bulunur.

   ![Geçilen iç değerlendirmeler][8]

## <a name="recommendations"></a>Öneriler
Kullanılabilir kimlik & erişim önerilerini ve uygulamayı uyguladığınızda her birinin ne yaptığını anlamanıza yardımcı olması için aşağıdaki tabloyu bir başvuru olarak kullanın.

|Kaynak türü|Güvenlik puanı|Öneri|Açıklama|
|----|----|----|----|
|Subscription|50|MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir|Hesapların veya kaynakların ihlal oluşmasını engellemek için yönetici ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication 'ı (MFA) etkinleştirin.|
|Subscription|40|MFA, abonelik hesaplarınız üzerinde yazma izinlerine sahip olmalıdır|Hesapların veya kaynakların ihlal oluşmasını engellemek için yazma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication 'ı (MFA) etkinleştirin.|
|Subscription|30|Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır|İzlenmeyen erişimi engellemek için aboneliğinizde sahip olan dış hesapları aboneliğinizden kaldırın.|
|Subscription|30|MFA, okuma izinlerine sahip abonelik hesaplarınız üzerinde etkinleştirilmelidir|Hesapların veya kaynakların ihlal oluşmasını engellemek için okuma ayrıcalıklarına sahip tüm abonelik hesapları için Multi-Factor Authentication 'ı (MFA) etkinleştirin.|
|Subscription|25|Yazma izinleri olan dış hesapların aboneliğinizden kaldırılması gerekir|İzlenmeyen erişimi engellemek için aboneliğinizden yazma izinleri olan dış hesapları kaldırın. |
|Subscription|20|Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır|Sahip olduğu kullanımdan kaldırılan hesapları aboneliklerinizden kaldırın.|
|Subscription|5|Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır|Yalnızca geçerli kullanıcılara erişim sağlamak için aboneliklerinizden kullanım dışı hesapları kaldırın. |
|Subscription|5|Aboneliğinize birden fazla sahip atanmalıdır|Yönetici erişiminin artıklığını sağlamak için birden fazla abonelik sahibi belirleyin.|
|Subscription|5|Aboneliğiniz için en fazla 3 sahip belirtilmelidir|Güvenliği aşılmış bir sahibe göre ihlal olasılığını azaltmak için 3 ' ten az abonelik sahibi belirleyin.|
|Anahtar kasası|5|Key Vault tanılama günlükleri etkinleştirilmelidir|Günlükleri etkinleştirmek ve bunları bir yıla kadar korur. Bu, etkinlik kayıtlarını araştırma amacıyla bir güvenlik olayı ortaya veya ağınızın tehlikeye yeniden oluşturmanıza olanak sağlar. |
|Subscription|15|Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir|İzlenmeyen erişimi engellemek için aboneliğinizdeki okuma ayrıcalıklarına sahip dış hesapları kaldırın.| 

> [!NOTE]
> MFA 'yı gerektiren ancak dışlamaları olan bir koşullu erişim ilkesi oluşturduysanız, bazı kullanıcıların MFA olmadan Azure 'da oturum açmasını sağladığından Güvenlik Merkezi MFA önerisi değerlendirmesi ilkeyi uyumlu değil olarak değerlendirir.

## <a name="next-steps"></a>Sonraki adımlar
Diğer Azure kaynak türlerine uygulanan öneriler hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure Güvenlik Merkezi'nde makinelerinizi ve uygulamalarınızı koruma](security-center-virtual-machine-protection.md)
- [Azure Güvenlik Merkezi'nde ağınızı koruma](security-center-network-recommendations.md)
- [Azure Güvenlik Merkezi 'nde Azure SQL hizmetinizi ve verilerinizi koruma](security-center-sql-service-recommendations.md)

Güvenlik Merkezi hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Güvenlik Merkezi'nde uyarıları yönetme ve güvenlik olaylarına yanıt vermeyi öğrenin.
* [Azure Güvenlik Merkezi'ndeki güvenlik uyarılarını anlama](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Farklı güvenlik uyarısı türleri hakkında bilgi edinin.
* [Azure Güvenlik Merkezi SSS](security-center-faq.md). Güvenlik Merkezi kullanımı ile ilgili sık sorulan soruların yanıtlarını bulun.


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png
