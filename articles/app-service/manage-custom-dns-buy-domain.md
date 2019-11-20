---
title: Azure 'da özel etki alanı adı satın alma-App Service
description: Azure App Service ' de bir Web uygulamasıyla özel bir etki alanı adı satın almayı öğrenin.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/24/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1354f48843f8006b4a6180a2cea60e735413e446
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470423"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Azure App Service için özel etki alanı adı satın alma

App Service etki alanları doğrudan Azure 'da yönetilen en üst düzey etki alanlardır. [Azure App Service](overview.md)için özel etki alanlarını yönetmeyi kolaylaştırır. Bu öğreticide, bir App Service etki alanı satın alma ve Azure App Service DNS adlarını atama işlemlerinin nasıl yapılacağı gösterilmektedir.

Azure VM veya Azure depolama için bkz. [Azure VM veya Azure Storage 'a App Service etki alanı atama](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). Cloud Services için bkz. [Azure bulut hizmeti için özel etki alanı adı yapılandırma](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

* [Bir App Service uygulaması oluşturun](/azure/app-service/) veya başka bir öğretici için oluşturduğunuz bir uygulamayı kullanın.
* [Aboneliğinizdeki harcama limitini kaldırın](../billing/billing-spending-limit.md#remove). Ücretsiz abonelik kredilerine sahip App Service etki alanı satın alamaz.

## <a name="prepare-the-app"></a>Uygulamayı hazırlama

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Azure App Service özel etki alanlarını kullanmak için uygulamanızın [App Service planı](https://azure.microsoft.com/pricing/details/app-service/) ücretli bir katman (**paylaşılan**, **temel**, **Standart**veya **Premium**) olmalıdır. Bu adımda, uygulamanın desteklenen fiyatlandırma katmanında olduğundan emin olursunuz.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure Portal](https://portal.azure.com)'ı açın ve Azure hesabınızla oturum açın.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Azure Portal'da uygulamaya gitme

Sol menüden **Uygulama Hizmetleri**'ni ve ardından uygulamanın adını seçin.

![Azure uygulamasına portal gezintisi](./media/app-service-web-tutorial-custom-domain/select-app.png)

App Service uygulamasının yönetim sayfasını görüyorsunuz.  

### <a name="check-the-pricing-tier"></a>Fiyatlandırma katmanını denetleme

Uygulama sayfasının sol gezintisini **Ayarlar** bölümüne kaydırın ve **Ölçeği artır (App Service planı)** öğesini seçin.

![Ölçeği artır menüsü](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Uygulamanın geçerli katmanı mavi kenarlıkla vurgulanmıştır. Uygulamanın **F1** katmanında olmadığından emin olun. **F1** katmanında özel DNS desteklenmez. 

![Fiyatlandırma katmanını denetleyin](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

App Service planı **F1** katmanında değilse, **Ölçek artırma** sayfasını kapatın ve [etki alanını satın almak](#buy-the-domain)için atlayın.

### <a name="scale-up-the-app-service-plan"></a>App Service planının ölçeğini artırma

Ücretsiz olmayan katmanlardan birini seçin (**D1**, **B1**, **B2**, **B3** veya **Üretim** kategorisindeki herhangi bir katmanı). Ek seçenekler için **Ek seçeneklere bakın**’a tıklayın.

**Apply (Uygula)** düğmesine tıklayın.

![Fiyatlandırma katmanını denetleyin](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Aşağıdaki bildirimi gördüğünüzde, ölçeklendirme işlemi tamamlanmıştır.

![Ölçeklendirme işlemi onayı](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Etki alanını satın al

### <a name="pricing-information"></a>Fiyatlandırma bilgileri
Azure App Service etki alanları hakkında fiyatlandırma bilgileri için [App Service fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/app-service/windows/) ziyaret edin ve aşağı kaydırın App Service etki alanı.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma
[Azure Portal](https://portal.azure.com/)'ı açın ve Azure hesabınızla oturum açın.

### <a name="launch-buy-domains"></a>Satın alma etki alanlarını Başlat
**Uygulama hizmetleri** sekmesinde uygulamanızın adına tıklayın, **Ayarlar**' ı seçin ve ardından **özel etki alanları** ' nı seçin.
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

**Özel etki alanları** sayfasında, **etki alanı satın al**' a tıklayın.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> **App Service etki alanları** bölümünü göremiyorsanız, Azure hesabınızdaki harcama limitini kaldırmanız gerekir (bkz. [Önkoşullar](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Etki alanı satın almayı yapılandırma

**App Service etki** alanı sayfasında, **etki alanı ara** kutusuna satın almak istediğiniz etki alanı adını yazın ve `Enter`yazın. Önerilen kullanılabilir etki alanları, metin kutusunun hemen altında gösterilir. Satın almak istediğiniz bir veya daha fazla etki alanını seçin.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Aşağıdaki [üst düzey etki alanları](https://wikipedia.org/wiki/Top-level_domain) App Service etki alanları tarafından desteklenir: _com_, _net_, _Co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_ve _Co.in_.
>
>

**Iletişim bilgilerine** tıklayın ve etki alanının kişi bilgileri formunu doldurun. İşiniz bittiğinde App Service etki alanı sayfasına dönmek için **Tamam** ' ı tıklatın.

Gerekli tüm alanları mümkün olduğunca fazla doğrulukta doldurmanız önemlidir. İletişim bilgileri için yanlış veriler, etki alanı satın alma hatasına neden olabilir.

Ardından, etki alanınız için istenen seçenekleri seçin. Açıklamalar için aşağıdaki tabloya bakın:

| Ayar | Önerilen Değer | Açıklama |
|-|-|-|
|Gizlilik Koruması | Etkinleştirme | Satın alma fiyatına _ücretsiz_olarak dahil edilen "Gizlilik Koruması" nı kabul edin. Bazı üst düzey etki alanları, gizlilik korumasını desteklemeyen kayıt şirketlerinde tarafından yönetilir ve **Gizlilik Koruması** sayfasında listelenir. |
| Varsayılan konak adlarını ata | **www** ve **\@** | İsterseniz istenen konak adı bağlamalarını seçin. Etki alanı satın alma işlemi tamamlandığında, uygulamanıza seçili ana bilgisayar adları üzerinden erişilebilir. Uygulama [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)arkasındaysa, Traffic Manager bir kaydı desteklemediği için kök etki alanını (@) atama seçeneğini görmezsiniz. Etki alanı satın alma işlemi tamamlandıktan sonra ana bilgisayar adı atamaları üzerinde değişiklik yapabilirsiniz. |

### <a name="accept-terms-and-purchase"></a>Koşulları kabul edin ve satın alın

Hüküm ve ücretleri gözden geçirmek için **yasal koşullar** ' a tıklayın ve ardından **satın al**' a tıklayın.

> [!NOTE]
> App Service etki alanları, etki alanı kaydı için GoDaddy kullanır ve etki alanlarını barındırmak için Azure DNS. Etki alanı kayıt ücretine ek olarak Azure DNS için kullanım ücretleri uygulanır. Bilgi için bkz. [Azure DNS fiyatlandırması](https://azure.microsoft.com/pricing/details/dns/).
>
>

**App Service etki alanı** sayfasına dönün, **Tamam**' a tıklayın. İşlem devam ederken aşağıdaki bildirimleri görürsünüz:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Ana bilgisayar adlarını test etme

Uygulamanıza varsayılan ana bilgisayar adları atadıysanız, seçili her konak adı için bir başarı bildirimi de görürsünüz.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Ayrıca, özel **ana bilgisayar adları** bölümünde **özel etki alanları** sayfasında seçili konak adlarını görürsünüz.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> Özel etki alanınız için **güvenli olmayan** bir etiket, henüz bir SSL sertifikasına bağlı olmadığı ve bir tarayıcıdan özel etki ALANıNA ait https isteğinin, tarayıcıya bağlı olarak bir hata veya uyarı alacağı anlamına gelir. SSL bağlamasını yapılandırmak için, bkz. [Azure App Service BIR SSL bağlamasıyla özel BIR DNS adını güvenli hale getirme](configure-ssl-bindings.md).
>

Ana bilgisayar adlarını test etmek için tarayıcıda listelenen ana bilgisayar adları bölümüne gidin. Yukarıdaki ekran görüntüsündeki örnekte, _kontoso.net_ ve _www\.kontoso.net_sayfasına gidin.

## <a name="assign-hostnames-to-app"></a>Ana bilgisayar adlarını uygulamaya ata

Satın alma işlemi sırasında uygulamanıza bir veya daha fazla varsayılan konak adı atamayı tercih ederseniz veya listede bulunmayan bir konak adı atamanız gerekiyorsa, her zaman bir ana bilgisayar adı atayabilirsiniz.

App Service etki alanını başka bir uygulama için de atayabilirsiniz. Adımlar App Service etki alanı ve uygulamanın aynı aboneliğe ait olup olmadığına bağlıdır.

- Farklı abonelik: özel DNS kayıtlarını App Service etki alanından, dışarıdan satın alınan etki alanı gibi bir uygulamayla eşleyin. App Service etki alanına özel DNS adları ekleme hakkında daha fazla bilgi için bkz. [özel DNS kayıtlarını yönetme](#custom). Dış satın alınan bir etki alanını bir uygulamayla eşlemek için bkz. [var olan bir özel DNS adını Azure App Service eşleme](app-service-web-tutorial-custom-domain.md). 
- Aynı abonelik: aşağıdaki adımları kullanın.

### <a name="launch-add-hostname"></a>Ana bilgisayar adı Ekle 'yi Başlat
**Uygulama hizmetleri** sayfasında, konak adlarını atamak istediğiniz uygulamanın adını seçin, **Ayarlar**' ı seçin ve ardından **özel etki alanları**' nı seçin.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Satın alınan etki alanının **App Service etki alanları** bölümünde listelendiğinden emin olun, ancak seçmeyin. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Aynı abonelikteki tüm App Service etki alanları uygulamanın **özel etki alanları** sayfasında gösterilir. Etki alanınız uygulamanın aboneliğinden ise, ancak uygulamanın **özel etki alanları** sayfasında göremiyorsanız, **özel etki alanları** sayfasını yeniden açmayı deneyin veya Web sayfasını yenileyin. Ayrıca, ilerleme veya oluşturma hatalarıyla ilgili Azure portal en üstündeki bildirim zili ' nı kontrol edin.
>
>

**Konak adı ekle**'yi seçin.

### <a name="configure-hostname"></a>Ana bilgisayar adını Yapılandır
Konak adı **Ekle** iletişim kutusunda, App Service etki alanınızın veya herhangi bir alt etki alanının tam etki alanı adını yazın. Örneğin:

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

İşiniz bittiğinde **Doğrula**' yı seçin. Ana bilgisayar adı kayıt türü sizin için otomatik olarak seçilir.

**Konak adı ekle**'yi seçin.

İşlem tamamlandığında, atanan ana bilgisayar adı için bir başarı bildirimi görürsünüz.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Konak adı Ekle ' ye kapat
**Konak adı Ekle** sayfasında, uygulamanıza istediğiniz diğer ana bilgisayar adını atayın. İşiniz bittiğinde **konak adı Ekle** sayfasını kapatın.

Şimdi uygulamanızın **özel etki alanları** sayfasında yeni atanan ana bilgisayar adını görmeniz gerekir.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Ana bilgisayar adlarını test etme

Tarayıcıda listelenen ana bilgisayar adları bölümüne gidin. Yukarıdaki ekran görüntüsündeki örnekte, _ABC.kontoso.net_' a gezinmeyi deneyin.

## <a name="renew-the-domain"></a>Etki alanını yenileme

Satın aldığınız App Service etki alanı, satın alma zamanından itibaren bir yıl için geçerlidir. Varsayılan olarak, etki alanı, ödeme yönteminizin bir sonraki yıla göre doldurularak otomatik olarak yenilenecek şekilde yapılandırılmıştır. Etki alanı adınızı el ile yenileyebilirsiniz.

Otomatik yenilemeyi devre dışı bırakmak istiyorsanız veya etki alanınızı el ile yenilemek istiyorsanız buradaki adımları izleyin.

**Uygulama hizmetleri** sekmesinde uygulamanızın adına tıklayın, **Ayarlar**' ı seçin ve ardından **özel etki alanları**' nı seçin.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

**App Service etki alanları** bölümünde, yapılandırmak istediğiniz etki alanını seçin.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

Etki alanının sol gezinti noktasından **etki alanı yenileme**' yi seçin. Etki alanınızı otomatik olarak yenilemeyi durdurmak için **kapalı**' yı ve ardından **Kaydet**' i seçin.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Etki alanınızı el ile yenilemek için **etki alanını Yenile**' yi seçin. Ancak, bu düğme, [etki alanının süre sonundan 90 gün önce](#when-domain-expires)etkin değildir.

Etki alanı yenileme işlemi başarılı olursa, 24 saat içinde bir e-posta bildirimi alırsınız.

## <a name="when-domain-expires"></a>Etki alanının süresi dolarsa

Azure, süresi dolan veya süresi dolan App Service etki alanları ile aşağıdaki şekilde anlaşmalar:

* Otomatik yenileme devre dışıysa: etki alanı süre sonundan 90 gün önce, bir yenileme bildirimi e-postası gönderilir ve portalda **etki alanını Yenile** düğmesi etkinleştirilir.
* Otomatik yenileme etkinse: etki alanı sona erme tarihi dolduktan sonra Azure, etki alanı adı yenileme için sizi faturalandırma girişiminde bulunur.
* Otomatik yenileme sırasında bir hata oluşursa (örneğin, dosyanızdaki kartın süresi dolmuşsa) veya otomatik yenileme devre dışıysa ve etki alanının süresinin dolmasına izin verirseniz Azure, etki alanı süre sonunu ve etki alanı adınızı park süresini size bildirir. Etki alanınızı [el ile yenileyebilirsiniz](#renew-the-domain) .
* 4 ve 12. günün süresi dolduktan sonra Azure size ek bildirim e-postaları gönderir. Etki alanınızı [el ile yenileyebilirsiniz](#renew-the-domain) .
* Süre dolduktan sonra 19. günde, etki alanınız tutuluyor kalır, ancak bir teminat ücretine tabi olur. Uygun yenileme ve kullanım ücretlerine tabi olmak üzere etki alanı adınızı yenilemek için müşteri desteğini çağırabilirsiniz.
* Süre dolduktan sonra 25. günde Azure, etki alanı adı endüstri açık servisi olan bir etki alanını açık eksiltmeye geçirir. Uygun yenileme ve kullanım ücretlerine tabi olmak üzere etki alanı adınızı yenilemek için müşteri desteğini çağırabilirsiniz.
* Süresi dolduktan sonra 30 gün sonra, etki alanınızı artık kullanamayacak.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Özel DNS kayıtlarını yönetme

Azure 'da, bir App Service etki alanı için DNS kayıtları [Azure DNS](https://azure.microsoft.com/services/dns/)kullanılarak yönetilir. Yalnızca dışarıdan satın alınan etki alanı gibi DNS kayıtlarını ekleyebilir, kaldırabilir ve güncelleştirebilirsiniz.

### <a name="open-app-service-domain"></a>App Service etki alanı aç

Azure portal, sol menüden **tüm hizmetler** > **App Service etki alanları**' nı seçin.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Yönetilecek etki alanını seçin. 

### <a name="access-dns-zone"></a>DNS bölgesine erişme

Etki alanının sol menüsünde **DNS bölgesi**' ni seçin.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Bu eylem Azure DNS App Service etki alanının [DNS bölgesi](../dns/dns-zones-records.md) sayfasını açar. DNS kayıtlarını düzenleme hakkında daha fazla bilgi için, bkz. [Azure portal DNS bölgeleri yönetme](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Satınalmayı iptal et (etki alanını sil)

App Service etki alanını satın aldıktan sonra, satın alma işlemini tam geri ödeme için iptal etmeniz beş gününüz vardır. Beş günden sonra App Service etki alanını silebilirsiniz, ancak para iadesi alamazsınız.

### <a name="open-app-service-domain"></a>App Service etki alanı aç

Azure portal, sol menüden **tüm hizmetler** > **App Service etki alanları**' nı seçin.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

İptal etmek veya silmek istediğiniz etki alanını seçin. 

### <a name="delete-hostname-bindings"></a>Konak adı bağlamalarını Sil

Etki alanının sol menüsünde **konak adı bağlamaları**' nı seçin. Tüm Azure hizmetlerinden konak adı bağlamaları burada listelenmiştir.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Tüm konak adı bağlamaları silinene kadar App Service etki alanını silemezsiniz.

**...**  > **Sil**' i seçerek her bir konak adı bağlamayı silin. Tüm bağlamalar silindikten sonra **Kaydet**' i seçin.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>İptal et veya Sil

Etki alanının sol menüsünde **genel bakış**' ı seçin. 

Satın alınan etki alanındaki iptal süresi dolmuşsa, **satınalmayı Iptal et**' i seçin. Aksi takdirde, bunun yerine bir **Sil** düğmesi görürsünüz. Etki alanını para iadesi olmadan silmek için **Sil**' i seçin.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

İşlemi onaylamak için **Evet**' i seçin.

İşlem tamamlandıktan sonra, etki alanı aboneliğinizden serbest bırakılır ve herkes tarafından yeniden satın alınabilir. 

## <a name="direct-default-url-to-a-custom-directory"></a>Varsayılan URL'yi özel bir dizine yönlendirme

Varsayılan olarak, App Service web isteklerini uygulama kodunuzun kök dizinine yönlendirir. Onları `public`gibi bir alt dizine yönlendirmek için bkz. [varsayılan URL 'yi özel bir dizine doğrudan yönlendirme](app-service-web-tutorial-custom-domain.md#virtualdir).
