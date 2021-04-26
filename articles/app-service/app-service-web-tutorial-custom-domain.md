---
title: 'Öğretici: var olan özel DNS adını eşle'
description: Azure App Service bir Web uygulamasına, mobil uygulama arka ucuna veya API uygulamasına mevcut bir özel DNS etki alanı adı (Vanity etki alanı) ekleme hakkında bilgi edinin.
keywords: uygulama hizmeti, azure app service, etki alanı eşlemesi, etki alanı adı, mevcut etki alanı, konak adı
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18, devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: 8e310cb0507146eb53c7b55c2aaed492baa79521
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833263"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Öğretici: mevcut bir özel DNS adını Azure App Service eşleme

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar. Bu öğreticide, mevcut bir özel etki alanı adı sistemi (DNS) adını App Service eşleme gösterilmektedir.

![Azure uygulamasına Azure portal gezintisini gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * CNAME kaydı kullanarak bir alt etki alanını (örneğin, `www.contoso.com` ) eşleyin.
> * Bir kök etki alanını (örneğin, `contoso.com` ) bir kayıt kullanarak eşleyin.
> * CNAME kaydı kullanarak joker karakter etki alanını (örneğin, `*.contoso.com` ) eşleyin.
> * Varsayılan URL 'YI özel bir dizine yönlendirin.
> * Betiklerle etki alanı eşlemeyi otomatikleştirin.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

* [Bir App Service uygulaması oluşturun](./index.yml) veya başka bir öğretici için oluşturduğunuz bir uygulamayı kullanın.
* Bir etki alanı adı satın alın ve etki alanı sağlayıcınız (GoDaddy gibi) için DNS kayıt defterine erişiminizin olduğundan emin olun.

  Örneğin, `contoso.com` ve `www.contoso.com` için DNS girdileri eklemek üzere `contoso.com` kök etki alanının DNS ayarlarını yapılandırabilmeniz gerekir.

  > [!NOTE]
  > Mevcut bir etki alanı adınız yoksa [Azure Portal kullanarak bir etki alanı satın almayı](manage-custom-dns-buy-domain.md)düşünün.

## <a name="prepare-the-app"></a>Uygulamayı hazırlama

Özel bir DNS adını bir Web uygulamasına eşlemek için, Web uygulamasının [App Service planı](https://azure.microsoft.com/pricing/details/app-service/) ücretli bir katman (paylaşılan, temel, standart, Premium veya Azure Işlevleri için tüketim) olmalıdır. Bu adımda, App Service uygulamasının desteklenen bir fiyatlandırma katmanında olduğundan emin olursunuz.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

  > [!NOTE]
  > Web uygulamanıza özel bir etki alanı kaldırmak veya eklemek sitenizin yeniden başlatılmasına neden olur.
### <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure Portal](https://portal.azure.com)açın ve Azure hesabınızla oturum açın.

### <a name="select-the-app-in-the-azure-portal"></a>Azure portal uygulamayı seçin

1. **Uygulama hizmetleri**' ni arayıp seçin.

   ![Uygulama Hizmetleri seçimini gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. **Uygulama hizmetleri** sayfasında, Azure uygulamanızın adını seçin.

   ![Azure uygulamasına Portal gezintisi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/select-app.png)

App Service uygulamasının yönetim sayfasını görüyorsunuz.

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Fiyatlandırma katmanını denetleme

1. Uygulama sayfasının sol bölmesinde **Ayarlar** bölümüne gidin ve **ölçeği büyütme (App Service planı)** seçeneğini belirleyin.

   ![Ölçeği yukarı (App Service planı) menüsünü gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Uygulamanın geçerli katmanı mavi kenarlıkla vurgulanmıştır. Uygulamanın **F1** katmanında olmadığından emin olun. Özel DNS **F1** katmanında desteklenmez.

   ![Önerilen Fiyatlandırma katmanlarını gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. App Service planı **F1** katmanında yoksa, **Ölçek artırma** sayfasını kapatın ve [CNAME kaydını eşlemek](#map-a-cname-record)için atlayın.

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>App Service planının ölçeğini artırma

1. Ücretsiz olmayan katmanlardan birini seçin (**D1**, **B1**, **B2**, **B3** veya **Üretim** kategorisindeki herhangi bir katmanı). Ek seçenekler için **ek seçenekleri göster**' i seçin.

1. **Uygula**’yı seçin.

   ![Fiyatlandırma katmanını denetlemeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Aşağıdaki bildirimi gördüğünüzde, ölçeklendirme işlemi tamamlanmıştır.

   ![Ölçek işlemi onayını gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-a-domain-verification-id"></a>Etki alanı doğrulama KIMLIĞI al

Uygulamanıza özel bir etki alanı eklemek için etki alanı sağlayıcınızla bir TXT kaydı olarak bir doğrulama KIMLIĞI ekleyerek etki alanının sahipliğinin doğrulanması gerekir. Uygulama sayfanızın sol bölmesinde **özel etki alanları**' nı seçin. Bir sonraki adım için **özel etki alanları** sayfasındaki **özel etkı alanı doğrulama kimliği** kutusunda kimliği kopyalayın.

![Özel etki alanı doğrulama KIMLIĞI kutusunda KIMLIĞI gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> Özel etki alanına etki alanı doğrulama kimlikleri eklemek, DNS girdilerinin tehlikellerini önleyebilir ve alt etki alanı devrallarından kaçınmanıza yardımcı olur. Daha önce bu doğrulama KIMLIĞI olmadan yapılandırdığınız özel etki alanları için, doğrulama KIMLIĞINI DNS kaydınızdan ekleyerek aynı riskten korumanız gerekir. Bu genel yüksek öneme sahip tehdit hakkında daha fazla bilgi için bkz. alt [etki alanı](../security/fundamentals/subdomain-takeover.md).

## <a name="map-your-domain"></a>Etki alanınızı eşleme

Özel DNS adını App Service'e eşlemek için CNAME kaydı veya A kaydı kullanabilirsiniz. İlgili adımları izleyin:

- [CNAME kaydını eşleme](#map-a-cname-record)
- [A kaydını eşleme](#map-an-a-record)
- [(CNAME kaydı ile) Joker karakter etki alanını eşleme](#map-a-wildcard-domain)

> [!NOTE]
> Kök etki alanları dışındaki tüm özel DNS adları için CNAME kayıtları kullanmanız gerekir (örneğin, `contoso.com`). Kök etki alanları için A kayıtlarını kullanın.

### <a name="map-a-cname-record"></a>CNAME kaydını eşleme

Öğretici örneğinde, `www` alt etki alanı için CNAME kaydı eklersiniz (örneğin, `www.contoso.com`).

Dışında başka bir alt etki alanınız varsa `www` , alt etki `www` alanınız ile değiştirin (örneğin, `sub` özel etki alanınız varsa `sub.constoso.com` ).

#### <a name="access-dns-records-with-a-domain-provider"></a>DNS kayıtlarına bir etki alanı sağlayıcısıyla erişme

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>CNAME kaydı oluşturma

Bir alt etki alanını uygulamanın varsayılan etki alanı adına eşleyin ( `<app-name>.azurewebsites.net` , burada `<app-name>` uygulamanızın adıdır). Alt etki alanı için bir CNAME eşlemesi oluşturmak için `www` iki kayıt oluşturun:

| Kayıt türü | Ana bilgisayar | Değer | Yorumlar |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | Etki alanı eşlemesi. |
| TXT | `asuid.www` | [Daha önce aldığınız doğrulama KIMLIĞI](#get-a-domain-verification-id) | App Service, `asuid.<subdomain>` özel etki alanının sahipliğini doğrulamak için txt kaydına erişir. |

CNAME ve TXT kayıtlarını ekledikten sonra, DNS kayıtları sayfası aşağıdaki örneğe benzer şekilde görünür:

![Bir Azure uygulamasına Portal gezintisi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Azure'da CNAME kaydı eşlemesini etkinleştirme

1. Azure portal uygulama sayfasının sol bölmesinde **özel etki alanları**' nı seçin.

    ![Özel etki alanları menüsünü gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Uygulamanın **özel etki alanları** sayfasında, tam özel DNS adını ( `www.contoso.com` ) listeye ekleyin.

1. **Özel etki alanı ekle**'yi seçin.

    ![Konak adı Ekle öğesini gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. CNAME kaydı eklediğiniz tam etki alanı adını (örneğin, `www.contoso.com`) yazın.

1. **Doğrula**'yı seçin. **Özel etki alanı Ekle** sayfası görüntülenir.

1. **Ana bilgisayar adı kayıt türünün** **CNAME (www \. example.com veya herhangi bir alt etki alanı)** olarak ayarlandığından emin olun. **Özel etki alanı ekle**'yi seçin.

    ![Özel etki alanı Ekle düğmesini gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Yeni özel etki alanının uygulamanın **özel etki alanları** sayfasında yansıtılması biraz zaman alabilir. Verileri güncelleştirmek için tarayıcıyı yenileyin.

    ![CNAME kaydını eklemeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Özel etki alanınız için bir uyarı etiketi henüz bir TLS/SSL sertifikasına bağlanmamış demektir. Bir tarayıcıdan özel etki alanınızı HTTPS istekleri, tarayıcıya bağlı olarak bir hata veya uyarı alır. TLS bağlama eklemek için, bkz. [Azure App Service BIR TLS/SSL bağlaması ile özel BIR DNS adını güvenli hale getirme](configure-ssl-bindings.md).

    Bir adım kaçırdıysanız veya daha önce bir yerde yazım hatası yaptıysanız, sayfanın alt kısmında bir doğrulama hatası görüntülenir.

    ![Doğrulama hatası gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>A kaydını eşleme

Öğretici örneğinde, kök etki alanı (örneğin, `contoso.com`) için A kaydı eklersiniz.

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Uygulamanın IP adresini kopyalama

A kaydını eşlemek için, uygulamanın dış IP adresine ihtiyacınız vardır. Bu IP adresini, Azure portal uygulamanın **özel etki alanları** sayfasında bulabilirsiniz.

1. Azure portal uygulama sayfasının sol bölmesinde **özel etki alanları**' nı seçin.

   ![Özel etki alanları menüsünü gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **Özel etki alanları** sayfasında, uygulamanın IP adresini kopyalayın.

   ![Bir Azure uygulamasına Portal gezintisi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-a-domain-provider"></a>DNS kayıtlarına bir etki alanı sağlayıcısıyla erişme

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>A kaydı oluşturma

Bir kaydı bir uygulamayla eşlemek için genellikle kök etki alanına iki kayıt oluşturun:

| Kayıt türü | Ana bilgisayar | Değer | Yorumlar |
| - | - | - |
| A | `@` | [Uygulamanın IP adresini kopyalama](#info) bölümünden IP adresi | Etki alanı eşlemesi ( `@` genellikle kök etki alanını temsil eder). |
| TXT | `asuid` | [Daha önce aldığınız doğrulama KIMLIĞI](#get-a-domain-verification-id) | App Service, `asuid.<subdomain>` özel etki alanının sahipliğini doğrulamak için txt kaydına erişir. Kök etki alanı için kullanın `asuid` . |

> [!NOTE]
> `www.contoso.com`Önerilen [CNAME kaydı](#map-a-cname-record)yerine bir kayıt kullanarak bir alt etki alanı (gibi) eklemek için BIR kayıt ve txt kaydımızda aşağıdaki tablo gibi görünmesi gerekir:
>
> | Kayıt türü | Ana bilgisayar | Değer |
> | - | - | - |
> | A | `www` | [Uygulamanın IP adresini kopyalama](#info) bölümünden IP adresi |
> | TXT | `asuid.www` | [Daha önce aldığınız doğrulama KIMLIĞI](#get-a-domain-verification-id) |
>

Kayıtlar eklendikten sonra, DNS kayıtları sayfası aşağıdaki örneğe benzer şekilde görünür:

![DNS kayıtları sayfasını gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Uygulamada A kaydı eşlemesini etkinleştirme

Azure portal uygulamanın **özel etki alanları** sayfasına geri döndüğünüzde, tam özel DNS adını (örneğin, `contoso.com` ) listeye ekleyin.

1. **Özel etki alanı ekle**'yi seçin.

    ![Ana bilgisayar adı eklemeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. A kaydını yapılandırdığınız tam etki alanı adını (örneğin, `contoso.com`) yazın. 

1. **Doğrula**'yı seçin. **Özel etki alanı Ekle** sayfası gösterilir.

1. **Konak adı kayıt türü**'nün **A kaydı (example.com)** olarak ayarlandığından emin olun. **Özel etki alanı ekle**'yi seçin.

    ![Uygulamaya bir DNS adı eklemeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Yeni özel etki alanının uygulamanın **özel etki alanları** sayfasında yansıtılması biraz zaman alabilir. Verileri güncelleştirmek için tarayıcıyı yenileyin.

    ![Bir kayıt eklemeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > Özel etki alanınız için bir uyarı etiketi henüz bir TLS/SSL sertifikasına bağlanmamış demektir. Bir tarayıcıdan özel etki alanınızı HTTPS istekleri, tarayıcıya bağlı olarak bir hata veya uyarı alır. TLS bağlama eklemek için, bkz. [Azure App Service BIR TLS/SSL bağlaması ile özel BIR DNS adını güvenli hale getirme](configure-ssl-bindings.md).
    
    Bir adım kaçırdıysanız veya daha önce bir yerde yazım hatası yaptıysanız, sayfanın alt kısmında bir doğrulama hatası görüntülenir.
    
    ![Doğrulama hatasını gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Joker karakter etki alanını eşleme

Öğretici örneğinde, CNAME kaydı ekleyerek [joker karakter DNS adını](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (örneğin, `*.contoso.com`) App Service uygulamasına eşlersiniz.

#### <a name="access-dns-records-with-a-domain-provider"></a>DNS kayıtlarına bir etki alanı sağlayıcısıyla erişme

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>CNAME kaydı oluşturma

`*`Uygulamanın varsayılan etki alanı adına ( `<app-name>.azurewebsites.net` , uygulamanızın adıdır) bir joker karakter adı eşleyin `<app-name>` . Joker karakter adını eşlemek için iki kayıt oluşturun:

| Kayıt türü | Ana bilgisayar | Değer | Yorumlar |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Etki alanı eşlemesi. |
| TXT | `asuid` | [Daha önce aldığınız doğrulama KIMLIĞI](#get-a-domain-verification-id) | App Service, `asuid` özel etki alanının sahipliğini doğrulamak için txt kaydına erişir. |

`*.contoso.com` etki alanı örneğinde, CNAME kaydı `*` adını `<app-name>.azurewebsites.net` ile eşler.

CNAME eklendiğinde, DNS kayıtları sayfası aşağıdaki örnekte gösterildiği gibi görünür:

![Bir Azure uygulamasına gezinmeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Uygulamada CNAME kaydı eşlemesini etkinleştirme

Artık joker karakter adıyla eşleşen herhangi bir alt etki alanını (örneğin,, `sub1.contoso.com` `sub2.contoso.com` ve `*.contoso.com` her ikisi de `*.contoso.com` ) ekleyebilirsiniz.

1. Azure portal uygulama sayfasının sol bölmesinde **özel etki alanları**' nı seçin.

    ![Özel etki alanları menüsünü gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **Özel etki alanı ekle**'yi seçin.

    ![Ana bilgisayar adı eklemeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Joker karakter etki alanıyla (örneğin, `sub1.contoso.com`) eşleşen bir tam etki alanı adı yazın ve **Doğrula**'yı seçin.

    **Özel etki alanı Ekle** düğmesi etkinleştirilir.

1. **Ana bilgisayar adı kayıt türünün** **CNAME kaydı (www \. example.com veya herhangi bir alt etki alanı)** olarak ayarlandığından emin olun. **Özel etki alanı ekle**'yi seçin.

    ![Uygulamaya bir DNS adı eklenmesini gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Yeni özel etki alanının uygulamanın **özel etki alanları** sayfasında yansıtılması biraz zaman alabilir. Verileri güncelleştirmek için tarayıcıyı yenileyin.

1. **+** Joker karakter etki alanıyla eşleşen başka bir özel etki alanı eklemek için simgeyi tekrar seçin. Örneğin, `sub2.contoso.com` ekleyin.

    ![CNAME kaydı eklemeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > Özel etki alanınız için bir uyarı etiketi henüz bir TLS/SSL sertifikasına bağlanmamış demektir. Bir tarayıcıdan özel etki alanınızı HTTPS istekleri, tarayıcıya bağlı olarak bir hata veya uyarı alır. TLS bağlama eklemek için, bkz. [Azure App Service BIR TLS/SSL bağlaması ile özel BIR DNS adını güvenli hale getirme](configure-ssl-bindings.md).
    
## <a name="test-in-a-browser"></a>Bir tarayıcıda test etme

Daha önce yapılandırdığınız DNS adlarına gidin (örneğin,,, `contoso.com` `www.contoso.com` `sub1.contoso.com` ve `sub2.contoso.com` ).

![Bir Azure uygulamasına gezinmeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>Çözümle 404 "bulunamadı"

Özel etki alanınızı URL 'sine gözattığınızda bir HTTP 404 (bulunamadı) hatası alırsanız, <a href="https://www.nslookup.io/" target="_blank">Nslookup.io</a>kullanarak etki ALANıNıZı uygulamanızın IP adresine çözümlediğinizi doğrulayın. Aksi takdirde, A ve CNAME kayıtlarının aynı site kullanılarak doğru şekilde yapılandırıldığını doğrulayın. IP 'yi doğru şekilde çözümlerse ancak yine de 404 alıyorsanız, tarayıcınız etki alanının eski IP adresini önbelleğe alabilir. Önbelleği temizleyin ve DNS çözümlemesini yeniden test edin. Windows makinesinde önbelleği `ipconfig /flushdns` ile temizlersiniz.

## <a name="migrate-an-active-domain"></a>Etkin bir etki alanını geçirme

Canlı siteyi ve onun DNS etki alanı adını hiçbir kesinti olmadan App Service'e geçirmek için, bkz. [Etkin DNS adını Azure App Service'e geçirme](manage-custom-dns-migrate-domain.md).

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Özel bir dizine yeniden yönlendirme

Varsayılan olarak, App Service web isteklerini uygulama kodunuzun kök dizinine yönlendirir. Ancak bazı Web çerçeveleri kök dizinde başlatılmaz. Örneğin, [Laravel](https://laravel.com/)`public` alt dizininden başlar. DNS örneğine devam etmek için `contoso.com` , bu tür bir uygulamaya adresinden erişilebilir `http://contoso.com/public` , ancak `http://contoso.com` bunun yerine dizine doğrudan yönlendirmek istersiniz `public` . Bu adım DNS çözümlemesi içermez ancak sanal dizinin özelleştirilmesi ile ilgilidir.

Windows uygulamaları için bir sanal dizin özelleştirmek için, Web uygulaması sayfanızın sol bölmesindeki **uygulama ayarları** ' nı seçin. 

> [!NOTE]
> Linux uygulamalarında Bu sayfa yoktur. Linux uygulamalarının site kökünü değiştirmek için dile özgü yapılandırma kılavuzlarından (örneğin,[php](configure-language-php.md?pivots=platform-linux#change-site-root)) bakın.

Sayfanın en altında, kök sanal dizin `/` varsayılan olarak `site\wwwroot` dizinine (uygulama kodunuzun kök dizini) işaret eder. Bunu, örneğin `site\wwwroot\public` dizinine işaret edecek şekilde değiştirin ve değişikliklerinizi kaydedin.

![Sanal dizini özelleştirmeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

İşlem bittikten sonra uygulamanız kök yolda doğru sayfayı döndürmelidir (örneğin, `http://contoso.com` ).

## <a name="automate-with-scripts"></a>Betiklerle otomatikleştirme

[Azure CLI](/cli/azure/install-azure-cli) veya [Azure PowerShell](/powershell/azure/)kullanarak betiklerle özel etki alanlarının yönetimini otomatik hale getirebilirsiniz.

### <a name="azure-cli"></a>Azure CLI

Aşağıdaki komut, App Service uygulamasına özel bir DNS adı yapılandırır.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Daha fazla bilgi için bkz. [Özel bir etki alanını web uygulamasıyla eşleme](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki komut, App Service uygulamasına özel bir DNS adı yapılandırır.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Daha fazla bilgi için bkz. [Özel bir etki alanını web uygulamasına atama](scripts/powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * CNAME kaydı kullanarak bir alt etki alanı eşleyin.
> * Bir kayıt kullanarak kök etki alanını eşleyin.
> * CNAME kaydı kullanarak joker bir etki alanı eşleyin.
> * Varsayılan URL 'YI özel bir dizine yönlendirin.
> * Betiklerle etki alanı eşlemeyi otomatikleştirin.

Özel bir TLS/SSL sertifikasını bir Web uygulamasına bağlamayı öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure App Service 'de TLS/SSL bağlaması ile özel bir DNS adının güvenliğini sağlama](configure-ssl-bindings.md)
