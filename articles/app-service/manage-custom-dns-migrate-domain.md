---
title: Etkin bir DNS adını geçirme
description: Canlı bir siteye zaten atanmış olan özel bir DNS etki alanı adının herhangi bir kesinti olmadan Azure App Service nasıl geçirileceğinizi öğrenin.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: e1b50675bef0f883ff617b3098a742d3491b3c13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89484316"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Etkin bir DNS adını Azure App Service geçirme

Bu makalede, etkin bir DNS adını herhangi bir kesinti olmadan [Azure App Service](../app-service/overview.md) nasıl geçirebileceğiniz gösterilmektedir.

Canlı bir siteyi ve DNS etki alanı adını App Service 'e geçirdiğinizde, bu DNS adı zaten canlı trafiğe hizmet veriyor. Etkin DNS adını App Service App preemptively 'a bağlayarak geçiş sırasında DNS çözümlemesindeki kapalı kalma süresinden kaçınabilirsiniz.

DNS çözümlemesindeki kapalı kalma süresi konusunda endişelendiğinizde, bkz. [Azure App Service bir özel DNS adını eşleme](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır:

- [App Service UYGULAMANıZıN Ücretsiz katmanında olmadığından emin olun](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Preemptively etki alanı adını bağlama

Özel bir etki alanı preemptively bağladığınızda, var olan DNS kayıtlarınızda herhangi bir değişiklik yapmadan önce aşağıdakilerin her ikisini de gerçekleştirirsiniz:

- Etki alanı sahipliğini doğrulama
- Uygulamanız için etki alanı adını etkinleştirin

Özel DNS adınızı eski siteden App Service uygulamasına geçirdiğinizde, DNS çözümlemesinde kapalı kalma süresi olmayacaktır.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="get-domain-verification-id"></a>Etki alanı doğrulama KIMLIĞINI al

[Etki alanı doğrulama kimliğini al](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)' da bulunan adımları izleyerek uygulamanızın etki alanı doğrulama kimliğini alın.

### <a name="create-domain-verification-record"></a>Etki alanı doğrulama kaydı oluştur

Etki alanı sahipliğini doğrulamak için, etki alanı doğrulaması için bir TXT kaydı ekleyin. TXT kaydı için ana bilgisayar adı, eşlemek istediğiniz DNS kayıt türünün türüne bağlıdır. Aşağıdaki tabloya bakın ( `@` genellikle kök etki alanını temsil eder):

| DNS kaydı örneği | TXT ana bilgisayar | TXT değeri |
| - | - | - |
| \@ Asıl | _asuıd_ | [Uygulamanız için etki alanı doğrulama KIMLIĞI](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| www (Sub) | _asuıd. www_ | [Uygulamanız için etki alanı doğrulama KIMLIĞI](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| \* liyorsa | _asuıd_ | [Uygulamanız için etki alanı doğrulama KIMLIĞI](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |

DNS kayıtları sayfanızda, geçirmek istediğiniz DNS adının kayıt türünü aklınızda bulabilirsiniz. App Service CNAME ve bir kayıtlardan eşleştirmeleri destekler.

> [!NOTE]
> Joker karakter `*` kayıtları, var olan CNAME kaydı ile alt etki alanlarını doğrulamaz. Her alt etki alanı için açıkça bir TXT kaydı oluşturmanız gerekebilir.

### <a name="enable-the-domain-for-your-app"></a>Uygulamanız için etki alanını etkinleştirin

1. [Azure Portal](https://portal.azure.com), uygulama sayfasının sol gezinti bölmesinde **özel etki alanları**' nı seçin. 

    ![Özel etki alanı menüsü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **Özel etki alanları** sayfasında **özel etki alanı Ekle**' yi seçin.

    ![Konak adı ekleme](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Geçirmek istediğiniz tam etki alanı adını yazın; örneğin,, veya gibi, oluşturduğunuz TXT kaydına karşılık gelir `contoso.com` `www.contoso.com` `*.contoso.com` . **Doğrula**'yı seçin.

    **Özel etki alanı Ekle** düğmesi etkinleştirilir. 

1. **Ana bilgisayar adı kayıt türünün** GEÇIRMEK istediğiniz DNS kayıt türüne ayarlandığından emin olun. **Konak adı ekle**'yi seçin.

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

**Özel etki alanları** sayfasında, uygulamanın IP adresini kopyalayın.

![Azure uygulamasına portal gezintisi](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>DNS kaydını Güncelleştir

Etki alanı sağlayıcınızın DNS kayıtları sayfasında yeniden eşlemek için DNS kaydını seçin.

`contoso.com`Kök etki alanı örneği için, bir veya CNAME kaydını aşağıdaki tabloda yer alan örneklerle yeniden eşleyin: 

| FQDN örneği | Kayıt türü | Ana bilgisayar | Değer |
| - | - | - | - |
| contoso.com (kök) | A | `@` | [Uygulamanın IP adresini kopyalama](#info) bölümünden IP adresi |
| www \. contoso.com (Sub) | CNAME | `www` | _&lt;AppName>. azurewebsites.net_ |
| \*. contoso.com (joker karakter) | CNAME | _\*_ | _&lt;AppName>. azurewebsites.net_ |

Ayarlarınızı kaydedin.

DNS sorguları, DNS yayma gerçekleştirildikten hemen sonra App Service uygulamanıza çözümlenmelidir.

## <a name="migrate-domain-from-another-app"></a>Başka bir uygulamadan etki alanı geçirme

Azure 'da bir etkin özel etki alanını abonelikler arasında veya aynı abonelik içinde geçirebilirsiniz. Ancak, kapalı kalma süresi olmayan bir geçiş, kaynak uygulama gerektirir ve hedef uygulamaya belirli bir zamanda aynı özel etki alanı atanır. Bu nedenle, iki uygulamanın aynı dağıtım birimine (dahili bir web alanı olarak da bilinir) dağıtılmadığından emin olmanız gerekir. Bir etki alanı adı, her dağıtım biriminde yalnızca bir uygulamaya atanabilir.

Uygulamanızın dağıtım birimini FTP/S URL 'sinin etki alanı adına bakarak bulabilirsiniz `<deployment-unit>.ftp.azurewebsites.windows.net` . Kaynak uygulamayla hedef uygulama arasında dağıtım biriminin farklı olduğundan emin olun ve denetleyin. Bir uygulamanın dağıtım birimi, içindeki [App Service plana](overview-hosting-plans.md) göre belirlenir. Planı oluşturduğunuzda Azure tarafından rastgele seçilir ve değiştirilemez. Azure yalnızca aynı [kaynak grubunda *ve* aynı bölgede oluşturduğunuzda](app-service-plan-manage.md#create-an-app-service-plan)iki plan aynı dağıtım biriminde olduğundan emin olur, ancak planların farklı dağıtım birimlerinde olduğundan emin olmak için herhangi bir mantığı yoktur. Farklı bir dağıtım biriminde bir plan oluşturmanın tek yolu, farklı bir dağıtım birimi alınana kadar yeni bir kaynak grubu veya bölgede bir plan oluşturmaya devam ediyor.

## <a name="next-steps"></a>Sonraki adımlar

App Service için özel bir TLS/SSL sertifikası bağlamayı öğrenin.

> [!div class="nextstepaction"]
> [Azure App Service bir TLS bağlaması ile özel bir DNS adının güvenliğini sağlama](configure-ssl-bindings.md)
