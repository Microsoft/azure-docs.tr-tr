---
title: 'Öğretici: var olan özel DNS adını eşle'
description: Azure App Service bir Web uygulamasına, mobil uygulama arka ucuna veya API uygulamasına mevcut bir özel DNS etki alanı adı (Vanity etki alanı) ekleme hakkında bilgi edinin.
keywords: App Service, Azure App Service, etki alanı eşleme, etki alanı adı, var olan etki alanı, ana bilgisayar adı, Gösterim
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18, devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1fcf8c681f4fad65209c27663045d4974be633f7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833265"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Öğretici: mevcut bir özel DNS adını Azure App Service eşleme

Bu öğreticide, var olan her türlü eşleme <abbr title="GoDaddy gibi bir etki alanı kaydedicisi veya satın aldığınız etki alanınızın bir alt etki alanı adı.">özel DNS etki alanı adı</abbr> kullanıcısı <abbr title="Web uygulamalarını, REST API 'Leri ve mobil arka uç uygulamalarını barındırmak için HTTP tabanlı bir hizmet.">Azure App Service</abbr>.

Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:

> [!div class="checklist"]
> * Bir alt etki alanını kullanarak eşleme <abbr title="DNS kurallı ad kaydı, bir etki alanı adını diğerine eşler.">CNAME kaydı</abbr>.
> * Bir kök etki alanını kullanarak eşleme <abbr title="DNS 'deki bir adres kaydı, ana bilgisayar adını bir IP adresiyle eşleştirir.">A kaydı</abbr>.
> * CNAME kaydı kullanarak joker bir etki alanı eşleyin.
> * Varsayılan URL 'YI özel bir dizine yönlendirin.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. ortamınızı hazırlayın

* [Bir App Service uygulaması oluşturun](./index.yml) veya başka bir öğretici için oluşturduğunuz bir uygulamayı kullanın.
* Özel etki alanınız için DNS kayıtlarını düzenleyediğinizden emin olun. Henüz özel bir etki alanınız yoksa [bir App Service etki alanı satın](manage-custom-dns-buy-domain.md)alabilirsiniz.

    <details>
        <summary>DNS kayıtlarını düzenleyemem gerekir mi?</summary>
        , GoDaddy gibi etki alanı sağlayıcınız için DNS kayıt defterine erişim gerektirir. Örneğin, <code>contoso.com</code> ve <code>www.contoso.com</code> için DNS girdileri eklemek üzere <code>contoso.com</code> kök etki alanının DNS ayarlarını yapılandırabilmeniz gerekir.
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2. uygulamayı hazırlama

Özel bir DNS adını bir uygulamayla eşlemek için, uygulamanın <abbr title="Uygulamanızı barındıran Web sunucusu grubunun konumunu, boyutunu ve özelliklerini belirtir.">App Service planı</abbr> ücretli bir katman olmalıdır (değil <abbr title="Uygulamanızın diğer uygulamalar dahil diğer uygulamalarla aynı VM 'lerde çalıştığı bir Azure App Service katmanı. Bu katman geliştirme ve test amaçlıdır.">**Ücretsiz (F1)**</abbr>). Daha fazla bilgi için bkz. [Azure App Service planına genel bakış](overview-hosting-plans.md).

#### <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure Portal](https://portal.azure.com)açın ve Azure hesabınızla oturum açın.

#### <a name="select-the-app-in-the-azure-portal"></a>Azure portal uygulamayı seçin

1. **Uygulama hizmetleri**' ni arayıp seçin.

   ![Uygulama Hizmetleri seçimini gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. **Uygulama hizmetleri** sayfasında, Azure uygulamanızın adını seçin.

   ![Azure uygulamasına Portal gezintisi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/select-app.png)

    App Service uygulamasının yönetim sayfasını görüyorsunuz.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>Fiyatlandırma katmanını denetleme

1. Uygulama sayfasının sol bölmesinde **Ayarlar** bölümüne gidin ve **ölçeği büyütme (App Service planı)** seçeneğini belirleyin.

   ![Ölçeği yukarı (App Service planı) menüsünü gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Uygulamanın geçerli katmanı mavi kenarlıkla vurgulanmıştır. Uygulamanın **F1** katmanında olmadığından emin olun. Özel DNS **F1** katmanında desteklenmez.

   ![Önerilen Fiyatlandırma katmanlarını gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. App Service planı **F1** katmanında değilse, **Ölçek artırma** sayfasını kapatın ve 3 ' e atlayın [. Bir etki alanı doğrulama KIMLIĞI alın](#3-get-a-domain-verification-id).

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>App Service planının ölçeğini artırma

1. Ücretsiz olmayan katmanlardan birini seçin (**D1**, **B1**, **B2**, **B3** veya **Üretim** kategorisindeki herhangi bir katmanı). Ek seçenekler için **ek seçenekleri göster**' i seçin.

1. **Uygula**’yı seçin.

   ![Fiyatlandırma katmanını denetlemeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Aşağıdaki bildirimi gördüğünüzde, ölçeklendirme işlemi tamamlanmıştır.

   ![Ölçek işlemi onayını gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3. bir etki alanı doğrulama KIMLIĞI al

Uygulamanıza özel bir etki alanı eklemek için etki alanı sağlayıcınızla bir TXT kaydı olarak bir doğrulama KIMLIĞI ekleyerek etki alanının sahipliğinin doğrulanması gerekir. 

1. Uygulama sayfanızın sol bölmesinde **özel etki alanları**' nı seçin. 
1. Bir sonraki adım için **özel etki alanları** sayfasındaki **özel etkı alanı doğrulama kimliği** kutusunda kimliği kopyalayın.

    ![Özel etki alanı doğrulama KIMLIĞI kutusunda KIMLIĞI gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>Buna neden ihtiyacım var?</summary>
        Özel etki alanına etki alanı doğrulama kimlikleri eklemek, DNS girdilerinin tehlikellerini önleyebilir ve alt etki alanı devrallarından kaçınmanıza yardımcı olur. Daha önce bu doğrulama KIMLIĞI olmadan yapılandırdığınız özel etki alanları için, doğrulama KIMLIĞINI DNS kaydınızdan ekleyerek aynı riskten korumanız gerekir. Bu genel yüksek öneme sahip tehdit hakkında daha fazla bilgi için bkz. alt <a href="/azure/security/fundamentals/subdomain-takeover">etki alanı</a>.
    </details>
    
<a name="info"></a>

3. **(Yalnızca bir kayıt)** Eşlemek için <abbr title="DNS 'deki bir adres kaydı, ana bilgisayar adını bir IP adresiyle eşleştirir.">A kaydı</abbr>, uygulamanın dış IP adresine sahip olmanız gerekir. **Özel etki alanları** sayfasında, **IP adresi** değerini kopyalayın.

   ![Bir Azure uygulamasına Portal gezintisi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4. DNS kayıtlarını oluşturma

1. Etki alanı sağlayıcınızın web sitesinde oturum açın.

    <details>
        <summary>Azure kullanarak etki alanı sağlayıcımda DNS 'i yönetebilir miyim?</summary>
        İsterseniz, etki alanınız için DNS kayıtlarını yönetmek ve Azure App Service için özel bir DNS adı yapılandırmak üzere Azure DNS kullanabilirsiniz. Daha fazla bilgi için bkz. <a href="/azure/dns/dns-delegate-domain-azure-dns">öğretici: etki alanınızı Azure DNS>barındırın </a>.
    </details>

1. DNS kayıtlarını yönetme sayfasını bulun. 

    <details>
        <summary>Sayfayı bulmak Nasıl yaparım? mı?</summary>
        <p>Her etki alanı sağlayıcısının kendi DNS kayıtları arabirimi vardır, bu nedenle sağlayıcının belgelerine başvurun. Sitede <strong>Domain Name</strong>, <strong>DNS</strong> veya <strong>Name Server Management</strong> etiketli alanları bulun.</p>
        <p>Genellikle, hesap bilgilerinizi görüntüleyerek ve <strong>etki alanlarım</strong>gibi bir bağlantı arayarak DNS kayıtları sayfasını bulabilirsiniz. Bu sayfaya gidin ve <strong>bölge dosyası</strong>, <strong>DNS kayıtları</strong>veya <strong>Gelişmiş yapılandırma</strong>gibi bir bağlantı adlı bağlantıyı arayın.</p>
    </details>

   DNS kayıtları sayfasının bir örneğini aşağıdaki ekran görüntüsünde görebilirsiniz:

   ![Örnek bir DNS kayıtları sayfasını gösteren ekran görüntüsü.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Bir kayıt oluşturmak için **Ekle** ' yi veya uygun pencere öğesini seçin. 

1. Oluşturulacak kayıt türünü seçin ve yönergeleri izleyin. Bunlardan birini kullanabilirsiniz <abbr title="DNS 'deki kurallı bir ad kaydı, bir etki alanı adını (diğer ad) diğerine (kurallı ad) eşler.">CNAME kaydı</abbr> veya bir <abbr title="DNS 'deki bir adres kaydı, ana bilgisayar adını bir IP adresiyle eşleştirir.">A kaydı</abbr> özel bir DNS adını App Service eşlemek için. 

    <details>
        <summary>Hangi kaydı seçmem gerekir?</summary>
        <div>
            <ul>
            <li>Kök etki alanını (örneğin,) eşlemek için <code>contoso.com</code> bir kayıt kullanın. Kök kaydı için CNAME kaydını kullanmayın (bilgi için bkz. <a href="https://en.wikipedia.org/wiki/CNAME_record">Vikipedi entry</a>).</li>
            <li>Bir alt etki alanını (örneğin,) eşlemek için <code>www.contoso.com</code> BIR CNAME kaydı kullanın.</li>
            <li>Bir alt etki alanını uygulamanın IP adresine doğrudan bir kayıtla eşleyebilirsiniz, ancak <a href="/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">IP adresinin değiştirilmesi</a>mümkündür. CNAME, uygulamanın ana bilgisayar adına eşlenir, bu da değişikliğe karşı daha düşüktür.</li>
            <li><a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">Joker bir etki alanını</a> (örneğin,) eşlemek IÇIN <code>*.contoso.com</code> bir CNAME kaydı kullanın.</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

İçindeki gibi bir alt etki alanı için `www` `www.contoso.com` aşağıdaki tabloya göre iki kayıt oluşturun:

| Kayıt türü | Ana bilgisayar | Değer | Yorumlar |
| - | - | - |
| CNAME | `<subdomain>` (örneğin, `www` ) | `<app-name>.azurewebsites.net` | Etki alanı eşlemesi. |
| TXT | `asuid.<subdomain>` (örneğin, `asuid.www` ) | [Daha önce aldığınız doğrulama KIMLIĞI](#3-get-a-domain-verification-id) | App Service, `asuid.<subdomain>` özel etki alanının sahipliğini doğrulamak için txt kaydına erişir. |

![Bir Azure uygulamasına Portal gezintisi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

Gibi bir kök etki alanı için `contoso.com` aşağıdaki tabloya göre iki kayıt oluşturun:

| Kayıt türü | Ana bilgisayar | Değer | Yorumlar |
| - | - | - |
| A | `@` | [Uygulamanın IP adresini kopyalama](#3-get-a-domain-verification-id) bölümünden IP adresi | Etki alanı eşlemesi ( `@` genellikle kök etki alanını temsil eder). |
| TXT | `asuid` | [Daha önce aldığınız doğrulama KIMLIĞI](#3-get-a-domain-verification-id) | App Service, `asuid.<subdomain>` özel etki alanının sahipliğini doğrulamak için txt kaydına erişir. Kök etki alanı için kullanın `asuid` . |

![DNS kayıtları sayfasını gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>Bir alt etki alanını A kaydıyla eşlemek istersem ne yapmalıyım?</summary>
Bir alt etki alanını `www.contoso.com` ÖNERILEN CNAME kaydı yerine a kaydıyla eşlemek için, bir kaydınız ve txt kaydınız bunun yerine aşağıdaki tablo gibi görünmelidir:

<div class="table-scroll-wrapper"><table class="table"><caption class="visually-hidden">Tablo 3</caption>
<thead>
<tr>
<th>Kayıt türü</th>
<th>Ana bilgisayar</th>
<th>Değer</th>
</tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td><code>&lt;subdomain&gt;</code> (örneğin, <code>www</code> )</td>
<td><a href="#info" data-linktype="self-bookmark">Uygulamanın IP adresini kopyalama</a> bölümünden IP adresi</td>
</tr>
<tr>
<td>TXT</td>
<td><code>asuid.&lt;subdomain&gt;</code> (örneğin, <code>asuid.www</code> )</td>
<td><a href="#3-get-a-domain-verification-id" data-linktype="self-bookmark">Daha önce aldığınız doğrulama KIMLIĞI</a></td>
</tr>
</tbody>
</table></div>
</details>

# <a name="wildcard-cname"></a>[Joker karakter (CNAME)](#tab/wildcard)

İçindeki gibi bir joker karakter adı için `*` `*.contoso.com` aşağıdaki tabloya göre iki kayıt oluşturun:

| Kayıt türü | Ana bilgisayar | Değer | Yorumlar |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Etki alanı eşlemesi. |
| TXT | `asuid` | [Daha önce aldığınız doğrulama KIMLIĞI](#3-get-a-domain-verification-id) | App Service, `asuid` özel etki alanının sahipliğini doğrulamak için txt kaydına erişir. |

![Bir Azure uygulamasına gezinmeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>Sayfadan ayrıldıktan sonra değişikliklerimi silinir.</summary>
<p>GoDaddy gibi bazı sağlayıcılarda, DNS kayıtlarında yapılan değişiklikler ayrı bir <strong>Değişiklikleri Kaydet</strong> bağlantısı seçilene kadar geçerlilik kazanmaz.</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5. uygulamanızda eşlemeyi etkinleştirin

1. Azure portal uygulama sayfasının sol bölmesinde **özel etki alanları**' nı seçin.

    ![Özel etki alanları menüsünü gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **Özel etki alanı ekle**'yi seçin.

    ![Konak adı Ekle öğesini gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. CNAME kaydı eklediğiniz tam etki alanı adını (örneğin, `www.contoso.com`) yazın.

1. **Doğrula**'yı seçin. **Özel etki alanı Ekle** sayfası görüntülenir.

1. **Ana bilgisayar adı kayıt türünün** **CNAME (www \. example.com veya herhangi bir alt etki alanı)** olarak ayarlandığından emin olun. **Özel etki alanı ekle**'yi seçin.

    ![Özel etki alanı Ekle düğmesini gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Yeni özel etki alanının uygulamanın **özel etki alanları** sayfasında yansıtılması biraz zaman alabilir. Verileri güncelleştirmek için tarayıcıyı yenileyin.

    ![CNAME kaydını eklemeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary><strong>Güvenli olmayan</strong> uyarı etiketi nedir?</summary>
        Özel etki alanınız için bir uyarı etiketi henüz bir TLS/SSL sertifikasına bağlanmamış demektir. Bir tarayıcıdan özel etki alanınızı HTTPS istekleri, tarayıcıya bağlı olarak bir hata veya uyarı alır. TLS bağlama eklemek için, bkz. <a href="/azure/app-service/configure-ssl-bindings">Azure App Service BIR TLS/SSL bağlaması ile özel BIR DNS adını güvenli hale getirme</a>.
    </details>

    Bir adım kaçırdıysanız veya daha önce bir yerde yazım hatası yaptıysanız, sayfanın alt kısmında bir doğrulama hatası görüntülenir.

    ![Doğrulama hatası gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. A kaydını yapılandırdığınız tam etki alanı adını (örneğin, `contoso.com`) yazın. 

1. **Doğrula**'yı seçin. **Özel etki alanı Ekle** sayfası gösterilir.

1. **Konak adı kayıt türü**'nün **A kaydı (example.com)** olarak ayarlandığından emin olun. **Özel etki alanı ekle**'yi seçin.

    ![Uygulamaya bir DNS adı eklemeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Yeni özel etki alanının uygulamanın **özel etki alanları** sayfasında yansıtılması biraz zaman alabilir. Verileri güncelleştirmek için tarayıcıyı yenileyin.

    ![Bir kayıt eklemeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary><strong>Güvenli olmayan</strong> uyarı etiketi nedir?</summary>
        Özel etki alanınız için bir uyarı etiketi henüz bir TLS/SSL sertifikasına bağlanmamış demektir. Bir tarayıcıdan özel etki alanınızı HTTPS istekleri, tarayıcıya bağlı olarak bir hata veya uyarı alır. TLS bağlama eklemek için, bkz. <a href="/azure/app-service/configure-ssl-bindings">Azure App Service BIR TLS/SSL bağlaması ile özel BIR DNS adını güvenli hale getirme</a>.
    </details>
    
    Bir adım kaçırdıysanız veya daha önce bir yerde yazım hatası yaptıysanız, sayfanın alt kısmında bir doğrulama hatası görüntülenir.
    
    ![Doğrulama hatasını gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[Joker karakter (CNAME)](#tab/wildcard)

3. Joker karakter etki alanıyla eşleşen bir tam etki alanı adı yazın. Örneğin, örneğin,,, `*.contoso.com` `sub1.contoso.com` `sub2.contoso.com` `*.contoso.com` veya joker karakter düzeniyle eşleşen herhangi bir dizeyi kullanabilirsiniz. Ardından **Doğrula**' yı seçin.

    **Özel etki alanı Ekle** düğmesi etkinleştirilir.

1. **Ana bilgisayar adı kayıt türünün** **CNAME kaydı (www \. example.com veya herhangi bir alt etki alanı)** olarak ayarlandığından emin olun. **Özel etki alanı ekle**'yi seçin.

    ![Uygulamaya bir DNS adı eklenmesini gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Yeni özel etki alanının uygulamanın **özel etki alanları** sayfasında yansıtılması biraz zaman alabilir. Verileri güncelleştirmek için tarayıcıyı yenileyin.

    <details>
        <summary><strong>Güvenli olmayan</strong> uyarı etiketi nedir?</summary>
        Özel etki alanınız için bir uyarı etiketi henüz bir TLS/SSL sertifikasına bağlanmamış demektir. Bir tarayıcıdan özel etki alanınızı HTTPS istekleri, tarayıcıya bağlı olarak bir hata veya uyarı alır. TLS bağlama eklemek için, bkz. <a href="/azure/app-service/configure-ssl-bindings">Azure App Service BIR TLS/SSL bağlaması ile özel BIR DNS adını güvenli hale getirme</a>.
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6. bir tarayıcıda test etme

Daha önce yapılandırdığınız DNS adlarına gidin.

![Bir Azure uygulamasına gezinmeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>Bir HTTP 404 (bulunamadı) hatası alıyorum.</summary>
<ul>
<li>Yapılandırılmış özel etki alanında bir kayıt veya CNAME kaydı eksik.</li>
<li>Tarayıcı istemcisi etki alanınızın eski IP adresini önbelleğe almıştır. Önbelleği temizleyin ve DNS çözümlemesini yeniden test edin. Windows makinesinde önbelleği <code>ipconfig /flushdns</code> ile temizlersiniz.</li>
</ul>
</details>

<hr/> 

## <a name="migrate-an-active-domain"></a>Etkin bir etki alanını geçirme

Canlı siteyi ve onun DNS etki alanı adını hiçbir kesinti olmadan App Service'e geçirmek için, bkz. [Etkin DNS adını Azure App Service'e geçirme](manage-custom-dns-migrate-domain.md).

<hr/> 

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Özel bir dizine yeniden yönlendirme

<details>
<summary>Bunun için gerekli midir?</summary>
<p>Uygulamanıza bağlıdır. Varsayılan olarak, App Service web isteklerini uygulama kodunuzun kök dizinine yönlendirir. Ancak bazı Web çerçeveleri kök dizinde&#39;. Örneğin, <a href="https://laravel.com/">Laravel</a><code>public</code> alt dizininden başlar. DNS örneğine devam etmek için <code>contoso.com</code> , bu tür bir uygulamaya adresinden erişilebilir <code>http://contoso.com/public</code> , ancak <code>http://contoso.com</code> bunun yerine dizine doğrudan yönlendirmek istersiniz <code>public</code> . </p>
</details>

Bu, yaygın bir senaryo olsa da aslında özel etki alanı eşlemesi içermez, ancak uygulamanızdaki sanal dizini özelleştirmek üzere olur.

1. Web uygulaması sayfanızın sol bölmesinde **uygulama ayarları** ' nı seçin.

1. Sayfanın en altında, kök sanal dizin `/` varsayılan olarak `site\wwwroot` dizinine (uygulama kodunuzun kök dizini) işaret eder. Bunu, örneğin `site\wwwroot\public` dizinine işaret edecek şekilde değiştirin ve değişikliklerinizi kaydedin.

    ![Sanal dizini özelleştirmeyi gösteren ekran görüntüsü.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. İşlem bittikten sonra, tarayıcıda uygulamanızın kök yoluna (örneğin, veya) giderek emin olun `http://contoso.com` `http://<app-name>.azurewebsites.net` .

<hr/> 

## <a name="automate-with-scripts"></a>Betiklerle otomatikleştirme

[Azure CLI](/cli/azure/install-azure-cli) veya [Azure PowerShell](/powershell/azure/)kullanarak betiklerle özel etki alanlarının yönetimini otomatik hale getirebilirsiniz.

#### <a name="azure-cli"></a>Azure CLI

Aşağıdaki komut, App Service uygulamasına özel bir DNS adı yapılandırır.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Daha fazla bilgi için bkz. [Özel bir etki alanını web uygulamasıyla eşleme](scripts/cli-configure-custom-domain.md).

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki komut, App Service uygulamasına özel bir DNS adı yapılandırır.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Daha fazla bilgi için bkz. [Özel bir etki alanını web uygulamasına atama](scripts/powershell-configure-custom-domain.md).

<hr/> 

## <a name="next-steps"></a>Sonraki adımlar

Özel bir TLS/SSL sertifikasını bir Web uygulamasına bağlamayı öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure App Service 'de TLS/SSL bağlaması ile özel bir DNS adının güvenliğini sağlama](configure-ssl-bindings.md)
