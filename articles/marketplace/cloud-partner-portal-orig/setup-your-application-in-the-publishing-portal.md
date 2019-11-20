---
title: Yayımlama Portalı 'nda uygulamanızı ayarlama
description: Uygulamanızı bulut Yayımlama portalında ayarlama yönergeleri.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 1bf89f94d91e0d809428cf8098db0fb37afdb47a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814690"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Yayımlama Portalı 'nda uygulamanızı ayarlama

Artık uygulamanızı Yayımlama portalında ayarlamaya hazırsınız.

## <a name="login-and-create-a-new-offer"></a>Oturum açın ve yeni bir teklif oluşturun

1. [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.
2. Sol gezinti çubuğunda "+ yeni teklif" e tıklayın ve "müşteri katılımı için Dynamics 365" seçeneğini belirleyin.

![Yeni bir teklif seçme](./media/CRMScreenShot14.png)

1. Yeni bir teklif "Düzenleyici" görünümü artık sizin için açılmıştır ve yazmaya başlamak için hazır hale gelmiştir.

![Yeni teklif ekranı](./media/CRMScreenShot15.png)

1. Doldurulması gereken "Formlar", "Düzenleyici" görünümü içinde sol tarafta görünür. Her "form" doldurulacak bir alan kümesinden oluşur. Gerekli alanlar kırmızı yıldız işaretiyle (\*) işaretlenir.

Bir Dynamics 365 for Customer Engagement teklifini yazmak için dört ana form mevcuttur

* Teklif ayarları
* Teknik bilgi
* Storefront ayrıntıları
* Kişiler

## <a name="fill-out-the-offer-settings-form"></a>Teklif ayarları formunu doldurun

Teklif ayarları formu, teklif ayarlarını belirtmek için temel bir formdur. Farklı alanlar aşağıda açıklanmıştır.

### <a name="offer-id"></a>Teklif KIMLIĞI

Bu, bir yayımcı profili içindeki teklif için benzersiz bir tanımlayıcıdır. Bu KIMLIK, ürün URL 'Lerinde görünür olacaktır. Yalnızca küçük harfli alfasayısal karakterler veya tirelerden (-) oluşabilir. KIMLIK bir kısa çizgi ile bitemez ve en fazla 50 karakter uzunluğunda olabilir. Bir teklif canlı olduktan sonra bu alan kilitlenir.

Örneğin, bir yayımcının " **contoso"** yayımcıları **"örnek-WebApp"** teklif kimliği ile bir teklif oluşturursa, appsource 'ta "https:\//appsource.Microsoft.com/Marketplace/Apps/contoso.Sample-WebApp?Tab=Overview" olarak görünür

### <a name="publisher-id"></a>Yayımcı KIMLIĞI

Bu açılan liste, bu teklifi yayımlamak istediğiniz yayımcı profilini seçmenizi sağlar. Bir teklif canlı olduktan sonra bu alan kilitlenir.

### <a name="name"></a>Ad

Bu, teklifiniz için görünen addır. Bu, [Appsource](https://appsource.microsoft.com/)'ta görüntülenecek addır. En fazla 50 karakter olabilir.

İlerleme durumunu kaydetmek için "Kaydet" e tıklayın. Sonraki adım teklifiniz için teknik bilgiler eklemektir.

## <a name="fill-out-the-technical-info-form"></a>Teknik bilgi formunu doldurun


Teknik bilgi formu, müşteri için Dynamics 365 katılım çözümüne özgü bilgileri doldurmanızı sağlar. Üzerine gelindiğinde daha fazla bilgi sunulacaktır. Aşağıdaki örneğe bakın.

![Teknik bilgiler ekranı](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Uygulama bilgileri

Çoğu Yayımcı, yukarıdaki ekran görüntüsüne göre bu alanları varsayılan değerler, Kullanıcı, Hayır, hayır ve boş bir uygulama yapılandırma URL 'SI ile bırakır.

### <a name="crm-package"></a>CRM paketi

![CRM paket bilgileri](./media/CRMScreenShot17.png)

Bu alanlar için bir açıklama aşağıda verilmiştir:

* Paketinizin dosya adı: CRM AppSource paketinizin ZIP dosyasını oluştururken Yukarıdaki adımda oluşturduğunuz dosya adı. Yukarıdaki örnekte, "Microsoft\_SamplePackage. zip" olur.
* Paket konumunuz URL 'si: Bu, yukarıda belirtilen paket dosyası adını içeren Azure depolama hesabının URL 'sidir. Yukarıdaki bölümde 9. adımda oluşturulan URL 'dir.
* Paket dosyanızda birden fazla CRM paketi var: **yalnızca** farklı paketlerle birden çok CRM sürümünü destekliyorsanız Evet ' i seçin. Çoğu iş ortağı için bu "Hayır" olacaktır. Evet ' i seçerseniz, çözümünüzün her sürümü için AppSource paketleri oluşturmanız gerekir. _Not: Bu, birden çok **ZIP** dosyasına sahip olup OLMADıĞıNı sormamalıdır. Birden çok çözüm. zip dosyası varsa ancak yalnızca bir sürümseniz yine de "Hayır" ı seçmeniz gerekir. Paketleme aracı bunları otomatik olarak bir araya getirir._

### <a name="crm-package-availability"></a>CRM paketi kullanılabilirliği

Bu bölümde, paketinizin hangi CRM bölgelerine kullanılabilir olacağını seçin. Hangi CRM bölgelerinin hangi ülkelere/bölgelere kullanıldığı hakkında bilgi için, lütfen şu bağlantıya bakın: [https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Note: Almanya 'ya dağıtma "Sovereign ve US Gov Cloud" Sovereign, SERTIFIKA SıRASıNDA özel ızın ve doğrulama gerektırır

## <a name="storefront-details"></a>Storefront ayrıntıları

### <a name="offer-summary"></a>Teklif Özeti

Bu, teklifinizin değer teklifinin bir özetidir. Bu, teklifinizin arama sayfasında görünecektir. En fazla 100 karakter olmalıdır.

### <a name="offer-description"></a>Teklif açıklaması

Bu, uygulama ayrıntısı sayfanızda görüntülenecek olan açıklamadır. İzin verilen en fazla 1300 karakter

### <a name="industries"></a>Sektörler

Uygulamanızın en iyi hizalandığı sektöri seçin. Uygulamanız birden çok sektör ile ilişkili ise, bu alanı boş bırakabilirsiniz.

### <a name="categories"></a>Kategoriler

Uygulamanız için uygun olan kategorileri seçin. En fazla 3 seçin.

### <a name="app-type"></a>Uygulama türü

Uygulamanızın AppSource 'ta etkinleşolacağı deneme türünü seçin. ' Ücretsiz ', uygulamanızın ücretsiz olduğu anlamına gelir. ' Deneme sürümü, müşterilerin uygulamanızı AppSource 'ta kısa bir süre için deneyebileceği anlamına gelir. Müşteri katılım uygulamaları için Dynamics 365 için ' deneme için istek ' desteklenmez. Bu seçeneği seçmeyin.

### <a name="help-link-for-your-app"></a>Uygulamanız için yardım bağlantısı

Uygulamanız için yardım ile ilgili bilgilere sahip bir sayfanın URL 'sini girin.

### <a name="supported-countriesregions"></a>Desteklenen ülkeler/bölgeler

Bu alan, teklifinizin deneme için kullanılabilir olacağı ülkeleri/bölgeleri belirler.

### <a name="supported-languages"></a>Desteklenen diller

Uygulamanızın desteklediği dilleri seçin. Uygulamanız bu listede yer alan ek dilleri destekliyorsa, bize bildirmek için teklifinizin ve e-postanızı bize yayımlamaya devam edin: [appsource@microsoft.com](mailto:appsource@microsoft.com) .

### <a name="app-version"></a>Uygulama sürümü

Uygulamanız için sürüm numarasını girin

### <a name="app-release-date"></a>Uygulama Yayın tarihi

Uygulamanız için sürüm tarihini girin

### <a name="products-your-app-works-with-max-3"></a>Uygulamanızın birlikte çalışma ürünleri (en fazla 3)

Uygulamanızın birlikte kullandığı, listeye özgü ürünler. En fazla üç ürün listeleyebilirsiniz. Bir ürünü listelemek için artı işaretine tıklayın (yeni ' nin yanında) ve uygulamanızın birlikte çalışması için bir ürünün adını girmeniz için yeni bir açık metin alanı oluşturulur.

### <a name="search-keywords-max-3"></a>Anahtar sözcük ara (en fazla 3)

AppSource, müşterilerin anahtar sözcüklere göre arama yapmasına izin verir. Uygulamanızın müşterilere gösterilecek anahtar sözcük kümesini girebilirsiniz.

Örneğin, uygulama "e-posta sunucum" e-posta, posta, posta hizmeti bazı anahtar sözcükler olabilir. Kullanıcıların, uygulamanızın uygulama aramak için kullandığı kelimeleri AppSource arama kutusunda seçin.

### <a name="hide-key"></a>Anahtarı gizle

Bu, genel görünümden gizlemek için teklif önizleme URL 'siyle birleştirilecek bir anahtardır. Bu bir parola değil. Burada herhangi bir dize girebilirsiniz.

### <a name="offer-logo-png-format-48x48"></a>Teklif logosu (PNG biçimi, 48x48)

Bu, uygulamanızın arama sayfasında görünür. **Yalnızca PNG biçimine izin veriliyor.** 48 PIKSEL\*48PX çözünürlüklü bir PNG görüntüsünü karşıya yükleme

### <a name="offer-logo-png-format-216x216"></a>Teklif logosu (PNG biçimi, 216x216)

Bu, uygulamanızın ayrıntı sayfasında görünecektir. **Yalnızca PNG biçimine izin veriliyor.** 216PX çözünürlükte bir PNG görüntüsünü karşıya yükleme\*216PX

### <a name="videos"></a>Videolar

En fazla dört video yükleyebilirsiniz. Karşıya yüklemek istediğiniz her video için video adını, URL 'yi (YouTube veya Vimeo) ve video ile ilişkilendirilecek küçük resmi doldurmanız gerekir. Küçük resim png biçiminde olmalıdır ve 1280PX\*720PX olmalıdır. Yeni video eklemek için artı işaretine tıklayın. Video küçük resimleri uygulamanızın ayrıntı sayfasında görünecektir.

### <a name="documents"></a>Belgeler

En fazla üç belgeyi PDF biçiminde karşıya yükleyebilirsiniz. Karşıya yüklemek istediğiniz her belge için belge adını doldurmanız ve belgeyi yüklemeniz gerekir. Belge PDF biçiminde olmalıdır.

Yeni belge (ler) eklemek için artı işaretine tıklayın

### <a name="screenshots"></a>Ekran görüntüleri

Bunlar, uygulamanızın AppSource ayrıntı sayfasında görünecek ekran görüntülerinde yer alır.

### <a name="privacy-policy"></a>Gizlilik İlkesi

Uygulamanızın gizlilik ilkesinin URL 'sini girin

### <a name="terms-of-use"></a>Kullanım koşulları

Uygulamanızın kullanım koşullarını girin. AppSource müşterilerinin, uygulamanızı denemeden önce bu koşulları kabul etmesi gerekir

### <a name="support-url"></a>Destek URL 'SI

Uygulamanızın destek URL 'sini girin.

### <a name="lead-destination"></a>Müşteri adayı hedefi

Müşteri adayının depolanacağı bir CRM sistemi seçin. Aşağıdaki CRM sistemlerinden birine sahipseniz, burada "Azure tablosu" nu seçin: Salesforce, Marketo, Microsoft Dynamics CRM. Burada seçtiğiniz CRM sistemi, uygulamanızı AppSource 'ta (müşteri adayları) deneyen son kullanıcıların ayrıntılarını yazacağız. Seçtiğiniz CRM sistemine bağlı olarak, sonraki alan kümesinin nasıl tamamlanacağı hakkında bilgi için aşağıdaki ilgili URL 'ye tıklayın

* [Azure tablosu](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Storefront ayrıntıları

İletişim ayrıntıları, iş ortağı ve yalnızca Microsoft arasındaki dahili iletişim için kullanılır. Note: Bu alanlarda izlenen bir e-posta adresi kullanmak önemlidir. Bu e-postayı AppSource 'ta yayımlama aşamasında ilerlemede sizinle iletişim kurmak için kullanacağız. Yalnızca destek URL 'SI müşterilere görünür olur.
