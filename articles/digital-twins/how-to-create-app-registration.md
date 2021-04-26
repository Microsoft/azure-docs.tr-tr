---
title: Uygulama kaydı oluşturma
titleSuffix: Azure Digital Twins
description: İstemci uygulamaları için kimlik doğrulama seçeneği olarak Azure AD uygulama kaydı oluşturma konusuna bakın.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bd45bb264f8e29a2aad870a7daff45fdd44e0d3c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478820"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Azure dijital TWINS ile kullanmak üzere bir uygulama kaydı oluşturma

Bir Azure dijital TWINS örneğiyle çalışırken, özel bir istemci uygulaması veya [Azure Digital TWINS Explorer](quickstart-azure-digital-twins-explorer.md)gibi bir örnek gibi istemci uygulamaları aracılığıyla o örnekle etkileşimde bulunmak yaygındır. Bu uygulamaların etkileşimde bulunmak için Azure dijital TWINS ile kimlik doğrulaması yapması gerekir ve uygulamaların kullanabileceği bazı [kimlik doğrulama mekanizmaları](how-to-authenticate-client.md) [Azure ACTIVE DIRECTORY (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) **uygulama kaydı** içerir.

Bu, tüm kimlik doğrulama senaryolarında gerekli değildir. Ancak, bir kimlik doğrulama stratejisi veya bir **ISTEMCI kimliği** ve **Kiracı kimliği** de dahil olmak üzere bir uygulama kaydı gerektiren kod örneği kullanıyorsanız, bu makalede nasıl bir ayarlama yapılacağı gösterilir.

## <a name="using-azure-ad-app-registrations"></a>Azure AD uygulama kayıtlarını kullanma

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) , Microsoft 'un bulut tabanlı kimlik ve erişim yönetimi hizmetidir. Azure AD 'de bir **uygulama kaydı** kurmak, Istemci uygulamasına Azure dijital TWINS erişimi sağlamanın bir yoludur.

Bu uygulama kaydı, [Azure dijital TWINS API 'leri](how-to-use-apis-sdks.md)için erişim izinlerini yapılandırdığınız yerdir. Daha sonra, istemci uygulamaları, kayıt **istemci ve KIRACı kimliği değerlerini** kullanarak uygulama kaydında kimlik doğrulaması yapabilir ve bu nedenle API 'lere yapılandırılmış erişim izinleri verilmek olur.

>[!TIP]
> Tek yapmanız gereken her seferinde yeni bir uygulama kaydı ayarlamayı tercih edebilir *veya* bunu yalnızca bir kez yapmanız, bunu gerektiren tüm senaryolar arasında paylaşılacak tek bir uygulama kaydı oluşturmak isteyebilirsiniz.

## <a name="create-the-registration"></a>Kayıt oluşturma

Azure portal [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) giderek başlayın (Bu bağlantıyı kullanabilir veya Portal arama çubuğundan bulabilirsiniz). Hizmet menüsünden *uygulama kayıtları* ' i ve sonra *+ Yeni kayıt*' ı seçin.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="' Uygulama kayıtları ' menü seçeneğini ve ' + yeni kayıt ' düğmesini vurgulayarak Azure portal Azure AD hizmeti sayfasının görünümü":::

Aşağıdaki *uygulama kaydet* sayfasında, istenen değerleri girin:
* **Ad**: kayıt ile Ilişkilendirilecek BIR Azure AD uygulama görünen adı
* **Desteklenen hesap türleri**: *yalnızca bu kuruluş dizinindeki hesapları seçin (yalnızca varsayılan dizin-tek kiracı)*
* **Yeniden yönlendirme URI 'si**: Azure AD uygulaması Için BIR *Azure AD uygulama yanıt URL 'si* . İçin *genel bir istemci/yerel (mobil & Masaüstü)* URI 'si ekleyin `http://localhost` .

İşiniz bittiğinde *Kaydet* düğmesine basın.

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Belirtilen değerlerin doldurulduğu ' bir uygulama kaydetme ' sayfasının görünümü":::

Kayıt kurulumu tamamlandığında, Portal sizi ayrıntılar sayfasına yönlendirecektir.

## <a name="collect-client-id-and-tenant-id"></a>İstemci KIMLIĞINI ve kiracı KIMLIĞINI topla

Ardından, Ayrıntılar sayfasından uygulama kaydı hakkında bazı önemli değerler toplayın:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Uygulama kaydı için önemli değerlerin Portal görünümü":::

**Sayfanızda gösterilen** _**uygulama (istemci) kimliğini**_ ve _**Dizin (kiracı) kimliğini**_ bir yere göz atın. Bunlar, bir istemci uygulamanın Azure dijital TWINS ile kimlik doğrulamak için bu kaydı kullanması gereken değerlerdir.

## <a name="provide-azure-digital-twins-api-permission"></a>Azure dijital TWINS API 'SI izni sağlama

Daha sonra, temel izinlerle oluşturduğunuz uygulama kaydını Azure dijital TWINS API 'Leri için yapılandırın.

Uygulama kaydınız için portal sayfasında, menüden *API izinleri* ' ni seçin. Aşağıdaki izinler sayfasında *+ Izin Ekle* düğmesine basın.

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Azure portal ' API izinleri ' menü seçeneğini ve ' + izin Ekle ' düğmesini vurgulayarak uygulama kaydının görünümü":::

Aşağıdaki *API Izinleri iste* sayfasında *Kuruluşumun kullandığı API* 'lere geçin ve *Azure dijital TWINS* için arama yapın. Azure dijital TWINS API 'Leri için izin atamaya devam etmek için arama sonuçlarından _**Azure dijital TWINS**_ ' i seçin.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Bir uygulama (istemci) KIMLIĞI olan 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 ile Azure dijital TWINS 'i gösteren ' API Izinleri ıste ' sayfa arama sonucu görünümü.":::

>[!NOTE]
> Aboneliğiniz, hizmetin önceki genel önizlemesinden (2020 Temmuz 'dan önce) mevcut bir Azure dijital TWINS örneğine sahip olmaya devam ediyorsa, bunun yerine _**Azure akıllı boşluklar hizmetini**_ arayıp seçmeniz gerekir. Bu, aynı API kümesi için daha eski bir addır ( *uygulamanın (istemci) kimliğinin* yukarıdaki ekran görüntüsünde aynı olduğuna dikkat edin) ve deneyiminiz bu adımın ötesinde değiştirilmez.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Azure akıllı boşluklar hizmetini gösteren ' API Izinleri ıste ' sayfası arama sonucu görünümü":::

Ardından, bu API 'Lere verilecek izinleri seçersiniz. **Oku (1)** iznini genişletin ve bu uygulama kayıt okuyucuyu ve yazıcı izinlerini vermek için *Read. Write* yazan kutusunu işaretleyin.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Azure Digital TWINS API 'Leri için ' Read. Write ' izinleri seçilerek ' API Izinleri ıste ' sayfasının görünümü":::

Bittiğinde *Izinleri Ekle* düğmesine basın.

### <a name="verify-success"></a>Başarıyı doğrula

*API izinleri* sayfasında, Azure Digital TWINS için okuma/yazma izinlerini yansıtan bir girdi olduğunu doğrulayın:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Azure dijital TWINS için ' okuma/yazma erişimi ' gösteren Azure AD uygulama kaydı için API izinlerinin Portal görünümü":::

Ayrıca, API izinleri eklenirken Azure dijital TWINS bilgileriyle otomatik olarak güncellenen Azure Digital TWINS ile *ilgilimanifest.jsüzerinde* uygulama kaydı içinde olan bağlantıyı doğrulayabilirsiniz.

Bunu yapmak için menüden *bildirim* ' ı seçerek uygulama kaydının bildirim kodunu görüntüleyin. Kod penceresinin en altına kaydırın ve altındaki bu alanları bulun `requiredResourceAccess` . Değerler aşağıdaki ekran görüntüsünde olanlarla eşleşmelidir:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Azure AD uygulama kaydı bildiriminin Portal görünümü. ' RequiredResourceAccess ' altında iç içe geçmiş bir ' Resourceappıd ' değeri, 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 ve bir ' resourceAccess > ID ' değeri 4589bd03-58cb-4e6c-b17f-b580e39652f8 ' dir":::

Bu değerler eksikse, [API iznini eklemek için bölümündeki](#provide-azure-digital-twins-api-permission)adımları yeniden deneyin.

## <a name="other-possible-steps-for-your-organization"></a>Kuruluşunuz için olası diğer adımlar

Kuruluşunuzun, bir uygulama kaydını başarıyla ayarlaması için abonelik sahipleri/yöneticileri 'nden ek eylemler gerektirmesi olasıdır. Gerekli adımlar, kuruluşunuzun ayarlarına göre değişiklik gösterebilir.

Aşağıda abonelik sahibinin/yöneticisinin gerçekleştirmesi gerekebilecek bazı yaygın etkinlikler verilmiştir. Bu ve diğer işlemler Azure portal [*Azure AD uygulaması kayıtları*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) sayfasından gerçekleştirilebilir.
* Uygulama için yönetici onayı verin. Kuruluşunuzun Azure AD 'de aboneliğinizdeki tüm uygulama kayıtları için *yönetici onayı gerekir* . Bu durumda, uygulama kaydının geçerli olması için sahip/yöneticinin, uygulama kaydının *API izinleri* sayfasında şirketiniz için bu düğmeyi seçmeniz gerekir:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="API izinleri altında ' yönetici onayı verme ' düğmesinin Portal görünümü":::
  - Onay başarıyla verildiyse, Azure Digital TWINS girişi için giriş için verilen bir *durum* değeri gösterilir _**(şirketiniz)**_
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Şirket için API izinleri kapsamında verilen yönetici izninin Portal görünümü":::
* Ortak istemci erişimini etkinleştir
* Web ve masaüstü erişimi için belirli yanıt URL 'Lerini ayarlayın
* Örtük OAuth2 kimlik doğrulama akışları için izin ver

Uygulama kaydı ve farklı kurulum seçenekleri hakkında daha fazla bilgi için bkz. [*Microsoft Identity platformu ile uygulama kaydetme*](/graph/auth-register-app-v2).

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure dijital TWINS API 'Leri ile istemci uygulamalarının kimliğini doğrulamak için kullanılabilecek bir Azure AD uygulama kaydı ayarlarsınız.

Daha sonra, uygulama kayıtlarını kullanan bir tane dahil olmak üzere kimlik doğrulama mekanizmaları hakkında bilgi edinin ve şunları yapın:
* [*Nasıl yapılır: uygulama kimlik doğrulama kodunu yazma*](how-to-authenticate-client.md)