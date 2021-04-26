---
title: Azure AD Connect Health-yinelenen öznitelik eşitleme hatalarını tanılama | Microsoft Docs
description: Bu belgede, yinelenen öznitelik eşitleme hatalarının tanılama işlemi ve doğrudan Azure portal yalnız bırakılmış nesne senaryolarında olası bir çözüm açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: maheshu
editor: billmath
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fe6af43c9ca44095c328356e8171da10717875e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728246"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Yinelenen öznitelik eşitleme hatalarını tanılama ve düzeltme

## <a name="overview"></a>Genel Bakış
Azure Active Directory (Azure AD) Connect Health, eşitleme hatalarını vurgulamada bir adım daha, self servis düzeltmesini tanıtır. Yinelenen öznitelik eşitleme hatalarını giderir ve Azure AD 'den yalnız bırakılmış nesneleri düzeltir.
Tanılama özelliğinin şu avantajları vardır:
- Yinelenen öznitelik eşitleme hatalarını daraltmakta olan bir tanılama yordamı sağlar. Ayrıca belirli düzeltmeleri sağlar.
- Tek bir adımda hatayı çözümlemek için Azure AD 'den adanmış senaryolar için bir çözüm uygular.
- Bu özelliği etkinleştirmek için yükseltme veya yapılandırma gerekli değildir.
Azure AD hakkında daha fazla bilgi için bkz. [kimlik eşitlemesi ve yinelenen öznitelik dayanıklılığı](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Sorunlar
### <a name="a-common-scenario"></a>Yaygın bir senaryo
**QuarantinedAttributeValueMustBeUnique** ve **AttributeValueMustBeUnique** Eşitleme hataları olduğunda, Azure AD 'de bir **userPrincipalName** veya **proxy adresi** çakışması olması yaygındır. Çakışan kaynak nesneyi şirket içi taraftan güncelleştirerek eşitleme hatalarını çözebilirsiniz. Eşitleme hatası, bir sonraki eşitlemeden sonra çözümlenir. Örneğin, bu görüntüde iki kullanıcının **userPrincipalName**'in bir çakışması olduğunu gösterir. Her ikisi de **ali. J \@ contoso.com**. Çakışan nesneler Azure AD 'de karantinaya alınır.

![Eşitleme hatasını tanılama genel senaryosu](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Yalnız bırakılmış nesne senaryosu
Bazen, mevcut bir kullanıcının **kaynak bağlantısını** kaybettiğini fark edebilirsiniz. Kaynak nesnenin silinmesi şirket içi Active Directory oldu. Ancak, silme sinyali değişikliği hiçbir zaman Azure AD ile eşitlenmez. Bu kayıp, eşitleme altyapısı sorunları veya etki alanı geçişi gibi nedenlerle meydana gelir. Aynı nesne geri yüklendiğinde veya yeniden oluşturulduğunda, mantıksal olarak, mevcut bir kullanıcının **kaynak çıpası** ile eşitlenmesi için Kullanıcı olması gerekir. 

Mevcut bir Kullanıcı yalnızca bulut nesnesürümlerse, çakışan kullanıcıyı Azure AD 'ye eşitlenmiş olarak da görebilirsiniz. Kullanıcı var olan nesneyle eşitlenmiş olarak eşleştirilemiyor. **Kaynak bağlayıcısını** yeniden eşlemek için doğrudan bir yol yoktur. [Mevcut bilgi tabanı](https://support.microsoft.com/help/2647098)hakkında daha fazla bilgi için bkz.. 

Örnek olarak, Azure AD 'deki mevcut nesne Joe lisansını korur. Azure AD 'de yinelenen bir öznitelik durumunda farklı **kaynak bağlantısı** olan yeni eşitlenmiş bir nesne oluşur. Şirket içi Active Directory için olan değişiklikler, Azure AD 'de ali 'nin özgün kullanıcısına (mevcut nesne) uygulanmaz.  

![Eşitleme hatası yalnız bırakılmış nesne senaryosunu Tanıla](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Connect Health içindeki tanılama ve sorun giderme adımları 
Tanıla özelliği, aşağıdaki yinelenen özniteliklere sahip kullanıcı nesnelerini destekler:

| Öznitelik adı | Eşitleme hata türleri|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique veya AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique veya AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Bu özelliğe erişmek için, **genel yönetici** Izni veya Azure RBAC Ile **katkıda bulunan** izin gerekir.
>

Eşitleme hata ayrıntılarını daraltmak ve daha belirli çözümler sağlamak için Azure portal adımları izleyin:

![Eşitleme hatası tanılama adımları](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

Azure portal, özel düzeltilebilir senaryoları belirlemek için birkaç adım gerçekleştirin:  
1.  **Tanılama durumu** sütununu kontrol edin. Durum, doğrudan Azure Active Directory eşitleme hatasını gidermenin olası bir yolu olup olmadığını gösterir. Diğer bir deyişle, hata durumunu daraltabilecek olabilecek bir sorun giderme akışı vardır ve bu sorunu çözebilir.

| Durum | Ne anlama geliyor? |
| ------------------ | -----------------|
| Başlatılmadı | Bu tanılama işlemini ziyaret etmediniz. Tanılama sonucuna bağlı olarak, doğrudan portaldan eşitleme hatasını gidermenin olası bir yolu vardır. |
| El ile düzeltilmesi gerekiyor | Hata, portaldan sunulan düzeltmelerin ölçütlerine uymuyor. Çakışan nesne türleri Kullanıcı değildir veya tanılama adımlarında zaten ilerlemenizin yanı sıra portalda bir onarım çözümü yoktu. İkinci durumda, şirket içi taraftan bir çözüm hala çözümlerden biridir. [Şirket içi düzeltmeler hakkında daha fazla bilgi edinin](https://support.microsoft.com/help/2647098). | 
| Eşitleme bekleniyor | Bir düzelme uygulandı. Portal, hatayı temizlemek için bir sonraki eşitleme döngüsünü bekliyor. |

  >[!IMPORTANT]
  > Tanılama durumu sütunu her eşitleme döngüsünden sonra sıfırlanır. 
  >

1. Hata ayrıntıları altındaki **Tanıla** düğmesini seçin. Birkaç soruyu yanıtlayıp eşitleme hata ayrıntılarını tanımlayacaksınız. Soruların yanıtları yalnız bırakılmış nesne durumunun tanımlanmasına yardımcı olur.

1. Tanılamanın sonunda bir **Kapat** düğmesi görünürse, yanıtlarınıza göre portaldan bir hızlı düzelme yoktur. Son adımda gösterilen çözüme bakın. Şirket içinden gelen düzeltmeler hala çözümlerdir. **Kapat** düğmesini seçin. Geçerli eşitleme hatası, **el ile düzeltilmesi için gereken** durum. Durum, geçerli eşitleme döngüsünün içinde kalır.

1. Yalnız bırakılmış bir nesne durumu tanımlandıktan sonra, yinelenen öznitelik eşitleme hatalarını doğrudan portaldan çözebilirsiniz. İşlemi tetiklemek için, **onarım Uygula** düğmesini seçin. Geçerli eşitleme hatasının durumu **bekleyen eşitlemeye** göre güncelleştirilir.

1. Sonraki eşitleme döngüsünden sonra, hatanın listeden kaldırılması gerekir.

## <a name="how-to-answer-the-diagnosis-questions"></a>Tanılama sorularını yanıtlama 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Kullanıcı şirket içi Active Directory mi var?

Bu soru, mevcut kullanıcının kaynak nesnesini şirket içi Active Directory doğrulamaya çalışır.  
1. Azure Active Directory, belirtilen **userPrincipalName** öğesine sahip bir nesneye sahip olup olmadığını denetleyin. Aksi takdirde, yanıt **No**.
2. Bu durumda, nesnenin eşitlemeye yönelik kapsamda hala olup olmadığını kontrol edin.  
   - DN 'yi kullanarak Azure AD bağlayıcı alanında arama yapın.
   - Nesne **bekleyen ekleme** durumunda bulunursa yanıt **No**. Azure AD Connect nesneyi doğru Azure AD nesnesine bağlayamıyorum.
   - Nesne bulunamazsa **Evet** yanıtını verin.

Bu örneklerde, bu soru, **Joe Jackson** 'ın şirket içi Active Directory hala mevcut olup olmadığını belirlemeyi dener.
**Ortak senaryo** Için hem **ali Johnson** hem de **ali jackson** kullanıcıları şirket içi Active Directory vardır. Karantinaya alınan nesneler iki farklı kullanıcıdır.

![Eşitleme hatasını tanılama genel senaryosu](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

**Yalnız bırakılmış nesne senaryosunda**, şirket içi Active Directory yalnızca tek kullanıcılı **Joe Johnson** vardır:

![Eşitleme hatasını Tanıla yalnız bırakılmış nesne * Kullanıcı var * senaryosu](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Bu hesapların her ikisi de aynı kullanıcıya ait mi?
Bu soru, aynı kullanıcıya ait olup olmadığını görmek için Azure AD 'deki bir çakışan kullanıcıyı ve mevcut Kullanıcı nesnesini denetler.  
1. Çakışan nesne Azure Active Directory ile yeni eşitlendi. Nesnelerin özniteliklerini karşılaştırın:  
   - Görünen Ad
   - Kullanıcı Asıl Adı
   - Nesne kimliği
2. Azure AD bunları karşılaştıramazsa, Active Directory, belirtilen **Userprincipalnames** nesnelerine sahip olup olmadığını denetleyin. Her ikisini de bulursanız yanıt **No** .

Aşağıdaki örnekte, iki nesne aynı kullanıcı **ali Johnson**' a aittir.

![Eşitleme hatasını Tanıla yalnız bırakılmış nesne * aynı kullanıcı * senaryosu](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Bu, yalnız bırakılmış nesne senaryosunda düzeltmeler uygulandıktan sonra ne olur?
Yukarıdaki sorulara verilen yanıtlara bağlı olarak, Azure AD 'de kullanılabilir bir sorun olduğunda **onarım Uygula** düğmesini görürsünüz. Bu durumda, şirket içi nesne beklenmeyen bir Azure AD nesnesiyle eşitleniyor. İki nesne, **kaynak bağlantısı** kullanılarak eşleştirilir. **Çözüm Uygula** değişikliği, bu veya benzer adımları gerçekleştirir:
1. **Kaynak bağlayıcıyı** Azure AD 'de doğru nesneye güncelleştirir.
2. Varsa, Azure AD 'de çakışan nesneyi siler.

![Onarma sonrasında eşitleme hatasını Tanıla](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> **Çözüm Uygula** değişikliği yalnızca yalnız bırakılmış nesne durumları için geçerlidir.
>

Yukarıdaki adımlardan sonra, Kullanıcı, var olan bir nesneye bağlantı olan özgün kaynağa erişebilir. Liste görünümündeki **Tanılama durumu** değeri **bekleyen eşitleme** için güncellenir. Eşitleme hatası, bir sonraki eşitlemeden sonra çözümlenir. Connect Health artık liste görünümünde çözümlenen eşitleme hatasını göstermeyecektir.

## <a name="failures-and-error-messages"></a>Hatalar ve hata iletileri
**Çakışan özniteliğe sahip Kullanıcı Azure Active Directory geçici olarak silinir. Yeniden denemeden önce kullanıcının kalıcı olarak silindiğinden emin olun.**  
Azure AD 'de çakışan özniteliği olan Kullanıcı, bir çözüm uygulayabilmeniz için önce temizlenmelidir. Çözümü yeniden denemeden önce [Azure AD 'de kullanıcıyı kalıcı olarak silme hakkında](../fundamentals/active-directory-users-restore.md) bilgi edinin. Kullanıcı ayrıca geçici olarak silinen durumda 30 gün sonra otomatik olarak silinir. 

**Kiracınızdaki bulut tabanlı kullanıcı için kaynak bağlayıcısını güncelleştirme desteklenmiyor.**  
Azure AD 'deki bulut tabanlı Kullanıcı kaynak tutturucusu içermemelidir. Bu durumda kaynak bağlayıcının güncelleştirilmesi desteklenmiyor. Şirket içinde el ile düzeltilmesi gerekiyor. 

**Onarım işlemi değerleri güncelleştiremedi.**
[Azure AD Connect Içindeki Usergeri yazma](./how-to-connect-preview.md#user-writeback) gibi belirli ayarlar desteklenmez. Lütfen ayarlarda devre dışı bırakın. 

## <a name="faq"></a>SSS
**S.** **Uygulama düzeltmesinin** yürütülmesi başarısız olursa ne olur?  
**A.** Yürütme başarısız olursa, Azure AD Connect bir dışarı aktarma hatası çalıştırıyor olabilir. Portal sayfasını yenileyip bir sonraki eşitlemeden sonra yeniden deneyin. Varsayılan eşitleme çevrimi 30 dakikadır. 


**Ç.** **Mevcut nesnenin** silinecek nesne olması gerekiyorsa ne olur?  
**A.** **Mevcut nesnenin** silinmesi gerekiyorsa, Işlem **kaynak bağlayıcının** bir değişikliğini içermez. Genellikle, bunu şirket içi Active Directory çözebilirsiniz. 


**Ç.** Kullanıcının bu çözümü uygulamak için hangi izni vardır?  
**A.** **Genel yönetici** veya Azure RBAC 'den **katılımcı** , tanılama ve sorun giderme sürecine erişme iznine sahiptir.


**Ç.** Bu özellik için Azure AD Connect yapılandırıp Azure AD Connect Health aracıyı güncelleştirmem gerekir mi?  
**A.** Hayır, tanılama işlemi bulut tabanlı tam bir özelliktir.


**Ç.** Varolan nesne geçici olarak silinirse, tanılama işlemi nesneyi yeniden etkin hale getirir mi?  
**A.** Hayır, bu çözüm **kaynak bağlantısı** dışındaki nesne özniteliklerini güncelleştirmez.