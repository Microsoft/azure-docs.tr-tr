---
title: Uygulamanın yayımcı etki alanını yapılandırma | Mavisi
titleSuffix: Microsoft identity platform
description: Kullanıcıların bilgilerinin gönderildiği yeri bilmesini sağlamak için bir uygulamanın yayımcı etki alanını nasıl yapılandıracağınızı öğrenin.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 9b3840ffb39a204cfa24dcb0430f20ac16587582
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104187"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Nasıl yapılır: bir uygulamanın yayımcı etki alanını yapılandırma

Uygulamanın yayımcı etki alanı, kullanıcıların bilgilerinin gönderildiği yeri bilmesini sağlamak için [uygulamanın onay isteminde](application-consent-experience.md) kullanıcılara görüntülenir. 21 Mayıs 2019 ' den sonra kaydedilen çok kiracılı uygulamalar, yayımcı etki alanı **doğrulanmamış** olarak görünür. Çok kiracılı uygulamalar, tek bir kuruluş dizini dışında hesapları destekleyen uygulamalardır; Örneğin, tüm Azure AD hesaplarını destekler veya tüm Azure AD hesaplarını ve kişisel Microsoft hesaplarını destekler.

## <a name="new-applications"></a>Yeni uygulamalar

Yeni bir uygulamayı kaydettiğinizde, uygulamanızın yayımcı etki alanı bir varsayılan değere ayarlanabilir. Değer uygulamanın kaydedildiği yere, özellikle uygulamanın bir kiracıya kaydedilip kaydedilmediğine ve kiracıda kiracının doğrulanmış etki alanlarına bağlıdır.

Kiracı tarafından doğrulanan etki alanları varsa, uygulamanın yayımcı etki alanı varsayılan olarak, kiracının birincil doğrulanmış etki alanı olur. Kiracı tarafından doğrulanan etki alanı yoksa (uygulama bir kiracıda kayıtlı değilse), uygulamanın yayımcı etki alanı null olarak ayarlanır.

Aşağıdaki tabloda, yayımcı etki alanı değerinin varsayılan davranışı özetlenmektedir.  

| Kiracı tarafından doğrulanan etki alanları | Yayımcı etki alanının varsayılan değeri |
|-------------------------|----------------------------|
| null | null |
| *. onmicrosoft.com | *. onmicrosoft.com |
| -*. onmicrosoft.com<br/>-domain1.com<br/>-domain2.com (birincil) | domain2.com |

Çok kiracılı bir uygulamanın yayımcı etki alanı ayarlanmamışsa veya. onmicrosoft.com ile biten bir etki alanına ayarlandıysa, uygulamanın onay istemi yayımcı etki alanının yerine **doğrulanmamış** olarak gösterilir.

## <a name="grandfathered-applications"></a>Grandfathered uygulamaları

Uygulamanız 21 Mayıs 2019 tarihinden önce kaydedilmişse, bir yayımcı etki alanı ayarlanmamışsa uygulamanızın onay istemi **doğrulanmamış** olarak gösterilmez. Kullanıcıların bu bilgileri uygulamanızın izin isteminde görebilmesi için yayımcı etki alanı değerini ayarlamanızı öneririz.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Azure portal kullanarak yayımcı etki alanını yapılandırma

Uygulamanızın yayımcı etki alanını ayarlamak için aşağıdaki adımları izleyin.

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden fazla kiracıya erişiminiz varsa,  :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: uygulamanın kaydedildiği kiracıyı seçmek Için üst menüdeki Dizin + abonelik filtresini kullanın.
1. Yapılandırmak istediğiniz uygulamayı bulmak ve seçmek için [Azure Active Directory > uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) gidin.

   Uygulamayı seçtikten sonra uygulamanın **genel bakış** sayfasını görürsünüz.
1. **Yönet** altında **marka**' i seçin.
1. **Yayımcı etki alanı** alanını bulun ve aşağıdaki seçeneklerden birini belirleyin:

   - Zaten bir etki alanı yapılandırmadıysanız, **etki alanı Yapılandır** ' ı seçin.
   - Bir etki alanı zaten yapılandırılmışsa **etki alanını güncelleştir** ' i seçin.

Uygulamanız bir kiracıda kayıtlıysa, seçebileceğiniz iki sekme görürsünüz: **doğrulanmış bir etki alanı seçin** ve **Yeni bir etki alanı doğrulayın**.

Uygulamanız bir kiracıda kayıtlı değilse, yalnızca uygulamanız için yeni bir etki alanı doğrulama seçeneğini görürsünüz.

### <a name="to-verify-a-new-domain-for-your-app"></a>Uygulamanıza yönelik yeni bir etki alanını doğrulamak için

1. Adlı bir dosya oluşturun `microsoft-identity-association.json` ve AŞAĞıDAKI JSON kod parçacığını yapıştırın.

   ```json
   {
      "associatedApplications": [
         {
            "applicationId": "{YOUR-APP-ID-HERE}"
         },
         {
            "applicationId": "{YOUR-OTHER-APP-ID-HERE}"
         }
      ]
    }
   ```

1. *{Size-App-ID-Here}* yer tutucusunu uygulamanıza karşılık gelen uygulama (ISTEMCI) kimliğiyle değiştirin.

1. Dosyayı şurada barındırın: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json` . *{-DOMAIN-Here}* yer tutucusunu doğrulanmış etki alanıyla eşleşecek şekilde değiştirin.

1. **Etki alanını doğrula ve Kaydet** düğmesine tıklayın.

### <a name="to-select-a-verified-domain"></a>Doğrulanmış bir etki alanı seçmek için

- Kiracınızda etki alanları doğrulandıysa, **doğrulanmış etki alanı seç** açılır listesinden etki alanlarından birini seçin.

>[!Note]
> Döndürülmesi gereken ' Content-Type ' üst bilgisi bekleniyordu `application/json` . Beğendiği gibi başka bir şey kullanıyorsanız, aşağıda belirtildiği gibi bir hata alabilirsiniz `application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Uygulama onay isteminde oluşan etkiler

Yayımcı etki alanını yapılandırmak, kullanıcıların uygulama onay isteminde neleri görbileceklerini etkiler. Onay isteminin bileşenlerini tam olarak anlamak için bkz. [uygulama onay deneyimlerini anlama](application-consent-experience.md).

Aşağıdaki tabloda, 21 Mayıs 2019 ' den önce oluşturulan uygulamalar için davranış açıklanmaktadır.

![21 Mayıs 2019 tarihinden önce oluşturulan uygulamalar için onay istemi](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

21 Mayıs 2019 ' den sonra oluşturulan yeni uygulamalar için davranış, yayımcı etki alanına ve uygulamanın türüne bağlı olarak değişir. Aşağıdaki tabloda farklı yapılandırma birleşimleri ile görmeyi beklemeniz gereken değişiklikler açıklanmaktadır.

![21 Mayıs 2019 ' den sonra oluşturulan uygulamalar için onay istemi](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Yeniden yönlendirme URI 'Lerinin etkileri

Herhangi bir iş veya okul hesabı veya kişisel Microsoft hesabı ([çok kiracılı](single-and-multi-tenant-apps.md)) ile kullanıcılar oturum açmak, yeniden yönlendirme URI 'leri belirtirken birkaç kısıtlamayla tabidir.

### <a name="single-root-domain-restriction"></a>Tek köklü etki alanı kısıtlaması

Çok kiracılı uygulamalar için yayımcı etki alanı değeri null olarak ayarlandığında, uygulamaların yeniden yönlendirme URI 'Leri için tek bir kök etki alanı paylaşması kısıtlıdır. Örneğin, contoso.com, kök etki alanı fabrikam.com ile eşleşmediğinden aşağıdaki değer birleşimine izin verilmez.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Alt etki alanı kısıtlamaları

Alt etki alanlarına izin verilir, ancak kök etki alanını açıkça kaydetmeniz gerekir. Örneğin, aşağıdaki URI 'Ler tek bir kök etki alanını paylaştığında, birleşimine izin verilmez.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Ancak, geliştirici kök etki alanını açıkça eklerse, birleşimine izin verilir.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Özel durumlar

Aşağıdaki durumlar tek köklü etki alanı kısıtlamasına tabi değildir:

- Tek bir dizindeki hesapları hedefleyen tek kiracılı uygulamalar veya uygulamalar
- Yeniden yönlendirme URI 'Leri olarak localhost kullanımı
- URI 'Leri özel düzenlerle yeniden yönlendir (HTTP olmayan veya HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Yayımcı etki alanını program aracılığıyla yapılandırma

Şu anda, yayımcı etki alanını program aracılığıyla yapılandırmak için REST API veya PowerShell desteği yoktur.
