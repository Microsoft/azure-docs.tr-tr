---
title: Azure AD Connect Health'i - tanılama yinelenen öznitelik Eşitleme hataları | Microsoft Docs
description: Bu belgede, yinelenen öznitelik eşitleme hatalarını tanılama işlemi ve olası düzeltme doğrudan Azure portalından yalnız bırakılmış nesneye senaryolar açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1fd5f9746299d72ed58a3209013822505b19b56
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702545"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Tanılama ve yinelenen öznitelik eşitleme hatalarını Düzelt

## <a name="overview"></a>Genel Bakış
Eşitleme hataları vurgulamak için bir adım daha da esnetmenize alma, Azure Active Directory (Azure AD) Connect Health kendi kendine düzeltme tanıtır. Yinelenen öznitelik Eşitleme hataları giderir ve yalnız bırakılmış nesneler, Azure AD'den giderir.
Tanılama özelliği bu avantajlara sahiptir:
- Bu, yinelenen öznitelik Eşitleme hataları daraltır tanılama bir yordam sağlar. Ve düzeltmeler sağlar.
- Tek bir adımda hatayı gidermek için Azure AD'den bir düzeltme adanmış senaryoları için geçerlidir.
- Yükseltme ya da yapılandırma, bu özelliği etkinleştirmek için gereklidir.
Azure AD hakkında daha fazla bilgi için bkz. [kimlik eşitleme ve yinelenen öznitelik dayanıklılığı](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Sorunları
### <a name="a-common-scenario"></a>Yaygın bir senaryo
Zaman **QuarantinedAttributeValueMustBeUnique** ve **AttributeValueMustBeUnique** Eşitleme hataları meydana, bu yaygın bir **UserPrincipalName** veya **Ara sunucu adresleri** Azure AD'de çakışması. Eşitleme hataları, şirket içi tarafı çakışan kaynak nesneden güncelleştirerek çözebilir. Eşitleme hatası sonra bir sonraki eşitlemede çözülmüş olacaktır. Örneğin, bu görüntüyü iki kullanıcı çakışma olduğunu gösterir. bunların **UserPrincipalName**. Her ikisi de **Joe.J\@contoso.com**. Çakışan nesneler, Azure AD'de karantinaya alınır.

![Eşitleme hatası yaygın bir senaryo tanılayın](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Yalnız bırakılmış nesneye senaryosu
Bazen, mevcut bir kullanıcının kaybettiğini bulabileceğiniz **kaynak bağlantısı**. Kaynak nesne silme işlemi, şirket içi Active Directory'de oldu. Ancak silme sinyal değişikliği hiçbir zaman Azure AD'ye eşitlenen. Eşitleme altyapısı sorunları veya etki alanına geçiş gibi nedenlerle bu kaybı olur. Mevcut bir kullanıcının aynı nesneyi veya geri yeniden, mantıksal olarak, kullanıcı gelen eşitleme olmalıdır **kaynak bağlantısı**. 

Mevcut bir kullanıcının yalnızca bulutta yer alan bir nesne, Azure AD'ye eşitlenen çakışan kullanıcı da görebilirsiniz. Kullanıcı için varolan nesne eşitleme eşleştirilemedi. Yeniden eşlemek için doğrudan bir yolu yoktur **kaynak bağlantısı**. Hakkında daha fazla bilgi [varolan Bilgi Bankası](https://support.microsoft.com/help/2647098). 

Örneğin, ALi lisansını Azure AD'de mevcut nesnesi korur. Farklı bir ile eşitlenen bir nesne **kaynak bağlantısı** Azure AD'de yinelenen öznitelik durumunda oluşur. Şirket içi Active Directory'de ALi değişiklikleri Can'ın özgün kullanıcı (var olan nesne), Azure AD'de uygulanmaz.  

![Eşitleme hatası yalnız bırakılmış nesneye senaryosu tanılayın](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Tanılama ve sorun giderme adımları Connect Health 
Tanılama özelliği, şu yinelenen öznitelikleri olan kullanıcı nesneleri destekler:

| Öznitelik adı | Eşitleme hata türleri|
| ------------------ | -----------------|
| userPrincipalName | QuarantinedAttributeValueMustBeUnique veya AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique veya AttributeValueMustBeUnique | 
| sipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Bu özelliğe erişmek için **genel yönetici** iznine veya **katkıda bulunan** RBAC ayarlarını izni gereklidir.
>

Eşitleme hata ayrıntılarını daraltmak ve daha özel çözümler için Azure portalından adımları izleyin:

![Eşitleme hata tanılama adımları](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

Azure portalından düzeltilebilir olduğu belirli senaryolar tanımlamak için bazı adımları uygulayın:  
1.  Denetleme **durumu Tanıla** sütun. Durumu, Azure Active Directory'den doğrudan bir eşitleme hatayı düzeltmek için olası bir biçimde olup olmadığını gösterir. Diğer bir deyişle, bir sorun giderme akış mevcut olan bir hata durumu daraltmak ve potansiyel olarak düzeltin.

| Durum | Bu ne demektir? |
| ------------------ | -----------------|
| Başlatılmadı | Bu tanılama işlemi ziyaret etmediniz. Tanılama sonucu bağlı olarak doğrudan portaldan eşitleme hatayı düzeltmek için olası bir yolu yoktur. |
| El ile düzeltme gerekli | Hata ölçütleri portalından kullanılabilir düzeltmelerin sığmıyor. Kullanıcılar, çakışan iki nesne türlerini değil veya zaten tanılama adımlara geçmeden ve herhangi bir düzeltme çözüm portalından kullanılabilir. İkinci durumda, şirket içi tarafındaki bir düzeltme hala çözümleri biridir. [Hakkında daha fazla bilgi şirket içi düzeltmeleri](https://support.microsoft.com/help/2647098). | 
| Eşitleme bekleniyor | Bir düzeltme uygulandı. Portal, hata temizlemek için bir sonraki eşitleme döngüsü bekliyor. |

  >[!IMPORTANT]
  > Tanı durumu sütun sonra her bir eşitleme döngüsü sıfırlar. 
  >

1. Seçin **Tanıla** düğmesi hata ayrıntılarını altında. Birkaç soruyu yanıtlayın ve eşitleme hata ayrıntılarını tanımlayın. Soruların yanıtlarını yalnız bırakılmış nesneye çalışması belirlemenize yardımcı olur.

1. Varsa bir **Kapat** tanılama sonunda düğmesinin yoktur hızlı düzeltme yanıtlarınıza göre portalında kullanılabilir. Son adımda gösterilen çözümde bakın. Şirket içi düzeltmeleri hala çözümdür. Seçin **Kapat** düğmesi. Geçerli eşitleme hatası durumunu geçer **el ile düzeltme gerekli**. Durum geçerli bir eşitleme döngüsü sırasında kalır.

1. Yalnız bırakılmış nesneye çalışması tanımlandıktan sonra yinelenen öznitelikler Portalı'ndan doğrudan eşitleme hataları giderebilirsiniz. İşlemi tetiklemek için seçin **düzeltme** düğmesi. Geçerli eşitleme hatası güncelleştirmelerin durumunu **eşitleme bekleyen**.

1. Sonraki eşitleme döngüsü sonra hata listeden kaldırılması gerekir.

## <a name="how-to-answer-the-diagnosis-questions"></a>Tanılama soruları nasıl cevaplıyor 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Kullanıcının şirket içi Active Directory'nizde var mı?

Bu soru, mevcut bir kullanıcının şirket içi Active Directory'den kaynak nesnesi tanımlamayı dener.  
1. Azure Active Directory ile sağlanan bir nesne olup olmadığını kontrol **UserPrincipalName**. Aksi takdirde, yanıt **Hayır**.
2. Varsa, nesnenin hala eşitleme için kapsam içinde olup olmadığını denetleyin.  
   - Azure AD bağlayıcı alanında DN kullanarak arama yapın.
   - Nesne bulunursa **bekleyen ekleme** durum, yanıt **Hayır**. Azure AD Connect için doğru Azure AD nesnesi nesne bağlanamıyor.
   - Nesne bulunamazsa, yanıt **Evet**.

Bu örneklerde, soru tanımlamayı dener olmadığını **ALi Jackson** yine de şirket içi Active Directory'de mevcut.
İçin **sık karşılaşılan bir senaryodur**, her iki kullanıcı **ALi Johnson** ve **ALi Jackson** şirket içi Active Directory'de mevcut. İki farklı kullanıcılar karantinaya alınmış nesneleridir.

![Eşitleme hatası yaygın bir senaryo tanılayın](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

İçin **yalnız bırakılmış nesneye senaryo**, yalnızca tek kullanıcı **ALi Johnson** şirket içi Active Directory'de mevcuttur:

![Eşitleme hatası yalnız bırakılmış nesneye tanılama * kullanıcısı mevcut * senaryosu](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Bu hesapların her ikisi de aynı kullanıcıya mı ait?
Bu soru, aynı kullanıcıya ait olup olmadığını görmek için Azure AD'de gelen çakışan bir kullanıcıyı ve mevcut kullanıcı nesnesi denetler.  
1. Çakışan nesne, Azure Active Directory'ye yeni eşitlendi. Nesnelerin öznitelik karşılaştırın:  
   - Görünen Ad
   - Kullanıcı asıl adı
   - Nesne Kimliği
2. Azure AD karşılaştırma başarısız olursa, Active Directory ile sağlanan nesneler olup olmadığını denetlemek **belirtilen userprincipalnames adlı**. Yanıt **Hayır** her ikisini de bulursanız.

Aşağıdaki örnekte, iki nesnenin aynı kullanıcıya ait **ALi Johnson**.

![Eşitleme hatası yalnız bırakılmış nesneye tanılama * aynı kullanıcı * senaryosu](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Yalnız bırakılmış nesneye senaryoda düzeltme uygulandıktan sonra ne olur
Göreceğiniz önceki soruların yanıtlarını bağlı olarak, **düzeltme** Azure AD'den kullanılabilir bir düzeltme olduğunda düğme. Bu durumda, şirket içi nesne beklenmeyen bir Azure ile eşitleniyor AD nesnesi. Kullanarak iki nesnenin eşlenen **kaynak bağlantısı**. **Düzeltme** değişiklik alan bunlar veya benzer adımları:
1. Güncelleştirmeleri **kaynak bağlantısı** Azure AD'de doğru nesnenin.
2. Varsa, Azure AD'de çakışan nesneyi siler.

![Eşitleme hatası düzeltme tanılayın](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> **Düzeltme** değişiklik yalnızca yalnız bırakılmış nesneye durumlar için geçerlidir.
>

Önceki adımlardan sonra kullanıcı var olan bir nesneye yönelik bağlantıyı özgün kaynak erişebilir. **Durumu Tanıla** değeri liste görünümünde güncelleştirmeleri **eşitleme bekleniyor**. Eşitleme hatası sonra bir sonraki eşitlemede çözülmüş olacaktır. Sistem durumu olacak uzun gösterilmiyor liste görünümünde çözümlenen eşitleme hatası bağlanın.

## <a name="failures-and-error-messages"></a>Hataları ve hata iletileri
**Çakışan bir öznitelik ile Azure Active Directory'de silinmiş yazılım kullanıcıdır. Yeniden deneme önce silinmiş kullanıcı sabit olduğundan emin olun.**  
Düzeltme uygulayabilmeniz için önce kullanıcının Azure AD'de çakışan bir öznitelik ile temizlenmesi. Kullanıma [kullanıcının Azure AD'de kalıcı olarak silme](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore) düzeltme yeniden denemeden önce. Kullanıcı aynı zamanda otomatik olarak 30 gün sonra kalıcı olarak yazılım silinmiş durumda silinir. 

**Bulut tabanlı kullanıcıya kiracınızdaki güncelleştirme kaynak bağlantısı desteklenmiyor.**  
Kullanıcının Azure AD'deki bulut tabanlı kaynak bağlantısı yok. Bu durumda, güncelleştirme kaynak bağlantısı desteklenmiyor. El ile düzeltme gelen şirket içi gereklidir. 

## <a name="faq"></a>SSS
**S.** Ne olur yürütülmesini **düzeltme** başarısız oluyor?  
**C.** Yürütme başarısız olursa bir dışarı aktarma hata Azure AD Connect'in çalıştığını mümkündür. Portal sayfayı yenileyin ve sonra bir sonraki eşitlemede yeniden deneyin. Varsayılan eşitleme döngüsü 30 dakikadır. 


**S.** Peki **varolan nesne** silinecek nesne olmalıdır?  
**C.** Varsa **varolan nesne** olmalıdır silindi, işlem, bir değişiklik içermeyen **kaynak bağlantısı**. Genellikle, şirket içi Active Directory'den olarak düzeltebilirsiniz. 


**S.** Hangi izin düzeltmeyi uygulamak bir kullanıcının gerekir?  
**C.** **Genel yönetici**, veya **katkıda bulunan** RBAC ayarlarından tanılama ve sorun giderme işlemi erişim iznine sahiptir.


**S.** Azure AD Connect yapılandırma veya bu özelliği için Azure AD Connect Health Aracısı güncelleştirmesi gerekiyor mu?  
**C.** Hayır, tanılama eksiksiz bir bulut tabanlı özellik işlemidir.


**S.** Tanılama işlemi var olan nesne silinmiş de yazılım ise, nesne yeniden etkin sunacaksınız?  
**C.** Hayır, düzeltme nesne öznitelikleri dışında güncelleştirme olmaz **kaynak bağlantısı**.
