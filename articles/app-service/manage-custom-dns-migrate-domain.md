---
title: Etkin bir DNS adı geçirme-Azure App Service | Microsoft Docs
description: Canlı bir siteye zaten atanmış olan özel bir DNS etki alanı adının herhangi bir kesinti olmadan Azure App Service nasıl geçirileceğinizi öğrenin.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/21/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 172003b13807720df2431a3610947b36d8303fed
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470356"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Etkin bir DNS adını Azure App Service geçirme

Bu makalede, etkin bir DNS adını herhangi bir kesinti olmadan [Azure App Service](../app-service/overview.md) nasıl geçirebileceğiniz gösterilmektedir.

Canlı bir siteyi ve DNS etki alanı adını App Service 'e geçirdiğinizde, bu DNS adı zaten canlı trafiğe hizmet veriyor. Etkin DNS adını App Service App preemptively 'a bağlayarak geçiş sırasında DNS çözümlemesindeki kapalı kalma süresinden kaçınabilirsiniz.

DNS çözümlemesindeki kapalı kalma süresi konusunda endişelendiğinizde, bkz. [Azure App Service bir özel DNS adını eşleme](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır:

- [App Service UYGULAMANıZıN Ücretsiz katmanında olmadığından emin olun](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Preemptively etki alanı adını bağlama

Özel bir etki alanı preemptively bağladığınızda, DNS kayıtlarınızda herhangi bir değişiklik yapmadan önce aşağıdakilerin her ikisini de gerçekleştirirsiniz:

- Etki alanı sahipliğini doğrulama
- Uygulamanız için etki alanı adını etkinleştirin

Özel DNS adınızı eski siteden App Service uygulamasına geçirdiğinizde, DNS çözümlemesinde kapalı kalma süresi olmayacaktır.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Etki alanı doğrulama kaydı oluştur

Etki alanı sahipliğini doğrulamak için bir TXT kaydı ekleyin. TXT kaydı, _awverify.&lt;alt etki alanından >_ _&lt;AppName >. azurewebsites. net_' e eşlenir. 

İhtiyacınız olan TXT kaydı, geçirmek istediğiniz DNS kaydına bağlıdır. Örnekler için aşağıdaki tabloya bakın (`@` genellikle kök etki alanını temsil eder):

| DNS kaydı örneği | TXT ana bilgisayar | TXT değeri |
| - | - | - |
| \@ (kök) | _awverify_ | _&lt;AppName >. azurewebsites. net_ |
| www (Sub) | _awverify. www_ | _&lt;AppName >. azurewebsites. net_ |
| \* (joker karakter) | _awverify.\*_ | _&lt;AppName >. azurewebsites. net_ |

DNS kayıtları sayfanızda, geçirmek istediğiniz DNS adının kayıt türünü aklınızda bulabilirsiniz. App Service CNAME ve bir kayıtlardan eşleştirmeleri destekler.

> [!NOTE]
> Cloudmercek parlaması gibi bazı sağlayıcılar için `awverify.*` geçerli bir kayıt değildir. Yalnızca `*` kullanın.

> [!NOTE]
> Joker karakter `*` kayıtları, var olan CNAME kaydı ile alt etki alanlarını doğrulamaz. Her alt etki alanı için açıkça bir TXT kaydı oluşturmanız gerekebilir.


### <a name="enable-the-domain-for-your-app"></a>Uygulamanız için etki alanını etkinleştirin

[Azure Portal](https://portal.azure.com), uygulama sayfasının sol gezinti bölmesinde **özel etki alanları**' nı seçin. 

![Özel etki alanı menüsü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**Özel etki alanları** sayfasında, **konak adı Ekle**' nin yanındaki **+** simgesini seçin.

![Konak adı ekleme](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

`www.contoso.com`gibi, TXT kaydını eklediğiniz tam etki alanı adını yazın. Joker (\*. contoso.com gibi) bir etki alanı için joker karakterle eşleşen herhangi bir DNS adını kullanabilirsiniz. 

**Doğrula**'yı seçin.

**Konak adı ekle** düğmesi etkinleştirilir. 

**Ana bilgisayar adı kayıt türünün** GEÇIRMEK istediğiniz DNS kayıt türüne ayarlandığından emin olun.

**Konak adı ekle**'yi seçin.

![Uygulamaya DNS adı ekleme](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Yeni konak adının uygulamanın **Özel etki alanları** sayfasına yansıtılması biraz zaman alabilir. Verileri güncelleştirmek için tarayıcıyı yenilemeyi deneyin.

![CNAME kaydı eklenir](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Özel DNS adınız artık Azure uygulamanızda etkinleştirilmiştir. 

## <a name="remap-the-active-dns-name"></a>Etkin DNS adını yeniden eşleyin

Yapılacak tek şey, etkin DNS kaydınızı App Service göstermek için yeniden eşleştirdir. Şimdi eski sitenize işaret ediyor.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Uygulamanın IP adresini kopyalama (yalnızca bir kayıt)

Bir CNAME kaydını yeniden eşleştirçalışıyorsanız, bu bölümü atlayın. 

Bir kaydı yeniden eşlemek için, **özel etki alanları** sayfasında gösterilen App Service UYGULAMANıN dış IP adresine sahip olmanız gerekir.

Sağ üst köşedeki **X** ' i seçerek **konak adı Ekle** sayfasını kapatın. 

**Özel etki alanları** sayfasında, uygulamanın IP adresini kopyalayın.

![Azure uygulamasına portal gezintisi](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>DNS kaydını Güncelleştir

Etki alanı sağlayıcınızın DNS kayıtları sayfasında yeniden eşlemek için DNS kaydını seçin.

`contoso.com` kök etki alanı örneği için, aşağıdaki tabloda yer alan örnekler gibi A veya CNAME kaydını yeniden eşleyin: 

| FQDN örneği | Kayıt türü | Host | Değer |
| - | - | - | - |
| contoso.com (kök) | A | `@` | [Uygulamanın IP adresini kopyalama](#info) bölümünden IP adresi |
| www\.contoso.com (Sub) | CNAME | `www` | _&lt;AppName >. azurewebsites. net_ |
| \*. contoso.com (joker karakter) | CNAME | _\*_ | _&lt;AppName >. azurewebsites. net_ |

Ayarlarınızı kaydedin.

DNS sorguları, DNS yayma gerçekleştirildikten hemen sonra App Service uygulamanıza çözümlenmelidir.

## <a name="active-domain-in-azure"></a>Azure 'da etkin etki alanı

Azure 'da bir etkin özel etki alanını abonelikler arasında veya aynı abonelik içinde geçirebilirsiniz. Ancak, kapalı kalma süresi olmayan bir geçiş, kaynak uygulama gerektirir ve hedef uygulamaya belirli bir zamanda aynı özel etki alanı atanır. Bu nedenle, iki uygulamanın aynı dağıtım birimine (dahili bir web alanı olarak da bilinir) dağıtılmadığından emin olmanız gerekir. Bir etki alanı adı, her dağıtım biriminde yalnızca bir uygulamaya atanabilir.

Uygulamanızın dağıtım birimini FTP/S URL 'sinin etki alanı adına bakarak bulabilirsiniz `<deployment-unit>.ftp.azurewebsites.windows.net`. Kaynak uygulamayla hedef uygulama arasında dağıtım biriminin farklı olduğundan emin olun ve denetleyin. Bir uygulamanın dağıtım birimi, içindeki [App Service plana](overview-hosting-plans.md) göre belirlenir. Planı oluşturduğunuzda Azure tarafından rastgele seçilir ve değiştirilemez. Azure yalnızca aynı [kaynak grubunda *ve* aynı bölgede oluşturduğunuzda](app-service-plan-manage.md#create-an-app-service-plan)iki plan aynı dağıtım biriminde olduğundan emin olur, ancak planların farklı dağıtım birimlerinde olduğundan emin olmak için herhangi bir mantığı yoktur. Farklı bir dağıtım biriminde bir plan oluşturmanın tek yolu, farklı bir dağıtım birimi alınana kadar yeni bir kaynak grubu veya bölgede bir plan oluşturmaya devam ediyor.

## <a name="next-steps"></a>Sonraki adımlar

App Service için özel bir SSL sertifikasını bağlamayı öğrenin.

> [!div class="nextstepaction"]
> [SSL sertifikasını Azure App Service bağlama](configure-ssl-bindings.md)
