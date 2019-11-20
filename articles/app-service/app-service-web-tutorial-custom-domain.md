---
title: Varolan özel DNS adını eşle-Azure App Service | Microsoft Docs
description: Mevcut özel DNS etki alanı adını (gösterim etki alanı) web uygulamasına, mobil uygulama arka ucuna veya Azure App Service'te API uygulamasına eklemeyi öğrenin.
keywords: uygulama hizmeti, azure app service, etki alanı eşlemesi, etki alanı adı, mevcut etki alanı, konak adı
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fa8acbab8179eea752607c4410851d74ae4e9444
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835866"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Öğretici: mevcut bir özel DNS adını Azure App Service eşleme

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar. Bu öğreticide, mevcut bir özel DNS adını Azure App Service ile nasıl eşleyebileceğiniz gösterilmektedir.

![Azure uygulamasına portal gezintisi](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * CNAME kaydı kullanarak alt etki alanını (örneğin, `www.contoso.com`) eşleme
> * A kaydı kullanarak kök etki alanını (örneğin, `contoso.com`) eşleme
> * CNAME kaydı kullanarak joker karakter etki alanını (örneğin, `*.contoso.com`) eşleme
> * Varsayılan URL'yi özel bir dizine yeniden yönlendirme
> * Etki alanı eşlemesini betiklerle otomatikleştirme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

* [Bir App Service uygulaması oluşturun](/azure/app-service/) veya başka bir öğretici için oluşturduğunuz bir uygulamayı kullanın.
* Etki alanı adı satın alın ve etki alanı sağlayıcınızın (GoDaddy gibi) DNS kayıt defterine erişebildiğinizden emin olun.

  Örneğin, `contoso.com` ve `www.contoso.com` için DNS girdileri eklemek üzere `contoso.com` kök etki alanının DNS ayarlarını yapılandırabilmeniz gerekir.

  > [!NOTE]
  > Mevcut etki alanı adınız yoksa, [Azure Portal'ı kullanarak etki alanı satın almayı](manage-custom-dns-buy-domain.md) göz önünde bulundurabilirsiniz.

## <a name="prepare-the-app"></a>Uygulamayı hazırlama

Özel bir DNS adını bir Web uygulamasına eşlemek için, Web uygulamasının [App Service planı](https://azure.microsoft.com/pricing/details/app-service/) ücretli bir katman (**paylaşılan**, **temel**, **Standart**, **Premium** veya Azure işlevleri için **Tüketim** ) olmalıdır. Bu adımda, App Service uygulamasının desteklenen bir fiyatlandırma katmanında olduğundan emin olursunuz.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure Portal](https://portal.azure.com)'ı açın ve Azure hesabınızla oturum açın.

### <a name="select-the-app-in-the-azure-portal"></a>Azure portal uygulamayı seçin

**Uygulama hizmetleri**' ni arayıp seçin.

![Uygulama hizmetlerini seçin](./media/app-service-web-tutorial-custom-domain/app-services.png)

**Uygulama hizmetleri** sayfasında, Azure uygulamanızın adını seçin.

![Azure uygulamasına portal gezintisi](./media/app-service-web-tutorial-custom-domain/select-app.png)

App Service uygulamasının yönetim sayfasını görüyorsunuz.  

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Fiyatlandırma katmanını denetleme

Uygulama sayfasının sol gezintisini **Ayarlar** bölümüne kaydırın ve **Ölçeği artır (App Service planı)** öğesini seçin.

![Ölçeği artır menüsü](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Uygulamanın geçerli katmanı mavi kenarlıkla vurgulanmıştır. Uygulamanın **F1** katmanında olmadığından emin olun. **F1** katmanında özel DNS desteklenmez. 

![Fiyatlandırma katmanını denetleyin](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

App Service planı **F1** katmanında değilse, **Ölçeği artır** sayfasını kapatın ve [CNAME kaydını eşle](#cname)'ye atlayın.

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>App Service planının ölçeğini artırma

Ücretsiz olmayan katmanlardan birini seçin (**D1**, **B1**, **B2**, **B3** veya **Üretim** kategorisindeki herhangi bir katmanı). Ek seçenekler için **Ek seçeneklere bakın**’a tıklayın.

**Apply (Uygula)** düğmesine tıklayın.

![Fiyatlandırma katmanını denetleyin](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Aşağıdaki bildirimi gördüğünüzde, ölçeklendirme işlemi tamamlanmıştır.

![Ölçeklendirme işlemi onayı](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="map-your-domain"></a>Etki alanınızı eşleme

Özel DNS adını App Service'e eşlemek için **CNAME kaydı** veya **A kaydı** kullanabilirsiniz. İlgili adımları izleyin:

- [CNAME kaydını eşleme](#map-a-cname-record)
- [A kaydını eşleme](#map-an-a-record)
- [(CNAME kaydı ile) Joker karakter etki alanını eşleme](#map-a-wildcard-domain)

> [!NOTE]
> Kök etki alanları dışındaki tüm özel DNS adları için CNAME kayıtları kullanmanız gerekir (örneğin, `contoso.com`). Kök etki alanları için A kayıtlarını kullanın.

### <a name="map-a-cname-record"></a>CNAME kaydını eşleme

Öğretici örneğinde, `www` alt etki alanı için CNAME kaydı eklersiniz (örneğin, `www.contoso.com`).

#### <a name="access-dns-records-with-domain-provider"></a>Etki alanı sağlayıcısı ile DNS kayıtlarına erişme

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>CNAME kaydı oluşturma

Bir alt etki alanını uygulamanın varsayılan etki alanı adına (`<app_name>.azurewebsites.net`, `<app_name>` uygulamanızın adı olduğu) eşlemek için bir CNAME kaydı ekleyin.

`www.contoso.com` etki alanı örneğinde, `www` adını `<app_name>.azurewebsites.net` ile eşleyen bir CNAME kaydı ekleyin.

CNAME kaydını ekledikten sonra, DNS kayıtları sayfası aşağıdaki örnekte gösterildiği gibi görünür:

![Azure uygulamasına portal gezintisi](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Azure'da CNAME kaydı eşlemesini etkinleştirme

Azure Portal'daki uygulama sayfasının sol gezintisinde **Özel etki alanları**'nı seçin.

![Özel etki alanı menüsü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Uygulamanın **Özel etki alanları** sayfasında, tam özel DNS adını (`www.contoso.com`) listeye ekleyin.

**Özel etki alanı Ekle**' nin yanındaki **+** simgesini seçin.

![Konak adı ekleme](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

CNAME kaydı eklediğiniz tam etki alanı adını (örneğin, `www.contoso.com`) yazın.

**Doğrula**'yı seçin.

**Özel etki alanı Ekle** sayfası gösterilir.

**Ana bilgisayar adı kayıt türünün** **CNAME (www\.example.com veya herhangi bir alt etki alanı)** olarak ayarlandığından emin olun.

**Özel etki alanı ekle**'yi seçin.

![Uygulamaya DNS adı ekleme](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Yeni özel etki alanının uygulamanın **özel etki alanları** sayfasında yansıtılması biraz zaman alabilir. Verileri güncelleştirmek için tarayıcıyı yenilemeyi deneyin.

![CNAME kaydı eklenir](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

> [!NOTE]
> Özel etki alanınız için **güvenli olmayan** bir etiket, henüz bir SSL sertifikasına bağlanmamış ve tarayıcıya bağlı olarak bir tarayıcıdan özel etki alanınız için HERHANGI bir https isteği alacak ve hata ya da uyarı verecek. SSL bağlaması eklemek için, bkz. [Azure App Service BIR SSL bağlaması ile özel BIR DNS adını güvenli hale getirme](configure-ssl-bindings.md).

Daha önce bir adımı atladıysanız veya yazım hatası yaptıysanız, sayfanın en altında bir doğrulama hatası görürsünüz.

![Doğrulama hatası](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>A kaydını eşleme

Öğretici örneğinde, kök etki alanı (örneğin, `contoso.com`) için A kaydı eklersiniz.

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Uygulamanın IP adresini kopyalama

A kaydını eşlemek için, uygulamanın dış IP adresine ihtiyacınız vardır. Bu IP adresini, Azure Portal'da uygulamanın **Özel etki alanları** sayfasında bulabilirsiniz.

Azure Portal'daki uygulama sayfasının sol gezintisinde **Özel etki alanları**'nı seçin.

![Özel etki alanı menüsü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**Özel etki alanları** sayfasında, uygulamanın IP adresini kopyalayın.

![Azure uygulamasına portal gezintisi](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-domain-provider"></a>Etki alanı sağlayıcısı ile DNS kayıtlarına erişme

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>A kaydı oluşturma

A kaydını bir uygulamaya eşlemek için, App Service **iki** DNS kaydı gerektirir:

- Uygulamanın IP adresini eşlemek için bir **A** kaydı.
- Uygulamanın varsayılan etki alanı adına eşlemek için bir **txt** kaydı `<app_name>.azurewebsites.net`. App Service bu kaydı yalnızca yapılandırma sırasında, özel etki alanının sahibi olduğunuzu doğrulamak için kullanır. Özel etki alanınız doğrulandıktan ve App Service'te yapılandırıldıktan sonra, bu TXT kaydını silebilirsiniz.

`contoso.com` etki alanı örneğinde, A ve TXT kayıtlarını aşağıdaki tabloda gösterildiği gibi oluşturun (`@` normalde kök etki alanını temsil eder).

| Kayıt türü | Host | Değer |
| - | - | - |
| A | `@` | [Uygulamanın IP adresini kopyalama](#info) bölümünden IP adresi |
| TXT | `@` | `<app_name>.azurewebsites.net` |

> [!NOTE]
> Önerilen [CNAME kaydı](#map-a-cname-record)yerine bir kayıt kullanarak bir alt etki alanı (`www.contoso.com`gibi) eklemek Için, bir KAYDıNıZ ve txt kaydınız bunun yerine aşağıdaki tablo gibi görünmelidir:
>
> | Kayıt türü | Host | Değer |
> | - | - | - |
> | A | `www` | [Uygulamanın IP adresini kopyalama](#info) bölümünden IP adresi |
> | TXT | `www` | `<app_name>.azurewebsites.net` |
>

Kayıtlar eklendiğinde, DNS kayıtları sayfası aşağıdaki örnekte gösterildiği gibi görünür:

![DNS kayıtları sayfası](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Uygulamada A kaydı eşlemesini etkinleştirme

Azure Portal'da uygulamanın **Özel etki alanları** sayfasında dönün ve tam özel DNS adını (örneğin, `contoso.com`) listeye ekleyin.

**Özel etki alanı Ekle**' nin yanındaki **+** simgesini seçin.

![Konak adı ekleme](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

A kaydını yapılandırdığınız tam etki alanı adını (örneğin, `contoso.com`) yazın.

**Doğrula**'yı seçin.

**Özel etki alanı Ekle** sayfası gösterilir.

**Konak adı kayıt türü**'nün **A kaydı (example.com)** olarak ayarlandığından emin olun.

**Özel etki alanı ekle**'yi seçin.

![Uygulamaya DNS adı ekleme](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Yeni özel etki alanının uygulamanın **özel etki alanları** sayfasında yansıtılması biraz zaman alabilir. Verileri güncelleştirmek için tarayıcıyı yenilemeyi deneyin.

![A kaydı eklenir](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

> [!NOTE]
> Özel etki alanınız için **güvenli olmayan** bir etiket, henüz bir SSL sertifikasına bağlanmamış ve tarayıcıya bağlı olarak bir tarayıcıdan özel etki alanınız için HERHANGI bir https isteği alacak ve hata ya da uyarı verecek. SSL bağlaması eklemek için, bkz. [Azure App Service BIR SSL bağlaması ile özel BIR DNS adını güvenli hale getirme](configure-ssl-bindings.md).

Daha önce bir adımı atladıysanız veya yazım hatası yaptıysanız, sayfanın en altında bir doğrulama hatası görürsünüz.

![Doğrulama hatası](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Joker karakter etki alanını eşleme

Öğretici örneğinde, CNAME kaydı ekleyerek [joker karakter DNS adını](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (örneğin, `*.contoso.com`) App Service uygulamasına eşlersiniz.

#### <a name="access-dns-records-with-domain-provider"></a>Etki alanı sağlayıcısı ile DNS kayıtlarına erişme

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>CNAME kaydı oluşturma

Joker karakter adını uygulamanın varsayılan etki alanı adına (`<app_name>.azurewebsites.net`) eşlemek için bir CNAME kaydı ekleyin.

`*.contoso.com` etki alanı örneğinde, CNAME kaydı `*` adını `<app_name>.azurewebsites.net` ile eşler.

CNAME eklendiğinde, DNS kayıtları sayfası aşağıdaki örnekte gösterildiği gibi görünür:

![Azure uygulamasına portal gezintisi](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Uygulamada CNAME kaydı eşlemesini etkinleştirme

Artık joker karakterlerden oluşturulmuş adı uygulamayla eşleşen her alt etki alanını ekleyebilirsiniz (örneğin, `sub1.contoso.com` ve `sub2.contoso.com`, `*.contoso.com` ile eşleşir).

Azure Portal'daki uygulama sayfasının sol gezintisinde **Özel etki alanları**'nı seçin.

![Özel etki alanı menüsü](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**Özel etki alanı Ekle**' nin yanındaki **+** simgesini seçin.

![Konak adı ekleme](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Joker karakter etki alanıyla (örneğin, `sub1.contoso.com`) eşleşen bir tam etki alanı adı yazın ve **Doğrula**'yı seçin.

**Özel etki alanı Ekle** düğmesi etkinleştirilir.

**Ana bilgisayar adı kayıt türünün** **CNAME kaydı (www\.example.com veya herhangi bir alt etki alanı)** olarak ayarlandığından emin olun.

**Özel etki alanı ekle**'yi seçin.

![Uygulamaya DNS adı ekleme](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Yeni özel etki alanının uygulamanın **özel etki alanları** sayfasında yansıtılması biraz zaman alabilir. Verileri güncelleştirmek için tarayıcıyı yenilemeyi deneyin.

Joker karakter etki alanıyla eşleşen başka bir özel etki alanı eklemek için **+** simgesini yeniden seçin. Örneğin, `sub2.contoso.com` ekleyin.

![CNAME kaydı eklenir](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

> [!NOTE]
> Özel etki alanınız için bir **Not güvenli** etiketi, henüz bir SSL sertifikasına bağlanmamış ve tarayıcıya bağlı olarak bir tarayıcıdan özel etki alanınız için HERHANGI bir https isteğinin gönderileceği ve hata ya da uyarı alacağı anlamına gelir. SSL bağlaması eklemek için, bkz. [Azure App Service BIR SSL bağlaması ile özel BIR DNS adını güvenli hale getirme](configure-ssl-bindings.md).

## <a name="test-in-browser"></a>Tarayıcıda test

Daha önce yapılandırılmış olan DNS adlarına (örneğin, `contoso.com`,  `www.contoso.com`, `sub1.contoso.com` ve `sub2.contoso.com`) göz atın.

![Azure uygulamasına portal gezintisi](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>404 "Bulunamadı" sorununu çözme

Özel etki alanınızın URL'sine göz atarken HTTP 404 (Bulunamadı) hatası alırsanız, etki alanınızın <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a> kullanılarak uygulamanızın IP adresine çözümlendiğini doğrulayın. Çözümlenmiyorsa, bunun nedeni aşağıdakilerden biri olabilir:

- Yapılandırılan özel etki alanında bir A kaydı ve/veya CNAME kaydı eksiktir.
- Tarayıcı istemcisi etki alanınızın eski IP adresini önbelleğe almıştır. Önbelleği temizleyin ve DNS çözümlemesini yeniden test edin. Windows makinesinde önbelleği `ipconfig /flushdns` ile temizlersiniz.

<a name="virtualdir" aria-hidden="true"></a>

## <a name="migrate-an-active-domain"></a>Etkin bir etki alanını geçirme

Canlı siteyi ve onun DNS etki alanı adını hiçbir kesinti olmadan App Service'e geçirmek için, bkz. [Etkin DNS adını Azure App Service'e geçirme](manage-custom-dns-migrate-domain.md).

## <a name="redirect-to-a-custom-directory"></a>Özel bir dizine yeniden yönlendirme

Varsayılan olarak, App Service web isteklerini uygulama kodunuzun kök dizinine yönlendirir. Bununla birlikte, bazı web çerçeveleri kök dizinden başlamaz. Örneğin, [Laravel](https://laravel.com/) `public` alt dizininden başlar. `contoso.com` DNS örneğine devam edersek, bu tür bir uygulamaya `http://contoso.com/public` adresinden erişilebilir ama bunun yerine `http://contoso.com` isteğini gerçekten `public` dizinine yönlendirmek isteyebilirsiniz. Bu adın DNS çözümlemesi içermez; sanal dizini özelleştirme işlemi yapılır.

Bunu yapmak için, web uygulaması sayfanızın sol tarafındaki gezintisinde **Uygulama ayarları**'nı seçin. 

Sayfanın en altında, kök sanal dizin `/` varsayılan olarak `site\wwwroot` dizinine (uygulama kodunuzun kök dizini) işaret eder. Bunu, örneğin `site\wwwroot\public` dizinine işaret edecek şekilde değiştirin ve değişikliklerinizi kaydedin.

![Sanal dizini özelleştirme](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

İşlem tamamlandığında, uygulamanız kök yolda doğru sayfayı döndürmelidir (örneğin, `http://contoso.com`).

## <a name="automate-with-scripts"></a>Betiklerle otomatikleştirme

Özel etki alanlarının yönetimini, [Azure CLI](/cli/azure/install-azure-cli) veya [Azure PowerShell](/powershell/azure/overview) kullanarak betiklerle otomatik hale getirebilirsiniz. 

### <a name="azure-cli"></a>Azure CLI 

Aşağıdaki komut, App Service uygulamasına özel bir DNS adı yapılandırır. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Daha fazla bilgi için bkz. [Özel bir etki alanını web uygulamasıyla eşleme](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki komut, App Service uygulamasına özel bir DNS adı yapılandırır.

```powershell  
Set-AzWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net")
```

Daha fazla bilgi için bkz. [Özel bir etki alanını web uygulamasına atama](scripts/powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * CNAME kaydı kullanarak alt etki alanını eşleme
> * A kaydı kullanarak kök etki alanını eşleme
> * CNAME kaydı kullanarak joker karakter etki alanını eşleme
> * Varsayılan URL'yi özel bir dizine yeniden yönlendirme
> * Etki alanı eşlemesini betiklerle otomatikleştirme

Web uygulamasına özel bir SSL sertifikası bağlamayla ilgili bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure App Service 'de SSL bağlaması ile özel bir DNS adının güvenliğini sağlama](configure-ssl-bindings.md)
