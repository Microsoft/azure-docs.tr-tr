---
title: Azure Uygulama teklifi marketi sekmesi
description: Azure Uygulama teklifinin pazarlama varlıklarını belirlemek için Market sekmesini kullanın.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 967b66a67d51b3a79bcf930ce977af48acc3dd63
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827573"
---
# <a name="azure-application-marketplace-tab"></a>Azure Uygulama marketi sekmesi

Azure uygulamanızı anlatmak ve pazarlama varlıkları sağlamak için Market sekmesini kullanın. Bu sekme aşağıdaki formları içerir: genel bakış, pazarlama yapıtları, müşteri adayı yönetimi ve yasal.

## <a name="overview-form"></a>Genel Bakış formu

Genel Bakış formu, sonraki ekran yakalama bölümünde gösterilen gerekli ve isteğe bağlı alanlara sahiptir. Gerekli alanlar bir yıldız işareti (*) ile tanımlanır.

![Genel Bakış formu](./media/azureapp-marketplace-overview.png)

Aşağıdaki tabloda, teklif için bir storefront oluşturmak üzere kullanılacak ayarlar açıklanmaktadır.   Bir yıldız işaretiyle eklenen alanlar gereklidir.

|      Alan         |    Açıklama    |
|  ---------------   |  ---------------  |
| **Başlık\***        | Teklifin başlığı. Market 'te göze çarpacak olarak görüntülenecektir. En fazla 50 karakter uzunluğunda olur. |
| **Özet\***      | Teklifin kısa özeti. En fazla 100 karakter uzunluğunda olur.           |
| **Uzun Özet\*** | Teklifin daha uzun Özeti (ancak Özet ile aynı olabilir). En fazla 256 karakter uzunluğunda olur.           |
| **Açıklama\***  | Teklifin açıklaması. En fazla 3000 karakter uzunluğunda olur. &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; ve üst bilgi etiketleri dahil basit HTML biçimlendirmesine izin verilir.  |
| **Pazarlama tanımlayıcısı\*** | Bu teklifle ilişkilendirilecek benzersiz bir URL, genellikle kuruluşunuzun ve çözüm adı, maksimum uzunluk 50 karakter içerir. Hizmetiniz için kısa ve kolay bir pazarlama tanımlayıcısı seçin. Bu, bu teklif için Market URL 'Lerinde kullanılacaktır. Örneğin, yayımcı KIMLIĞINIZ "contoso" ise ve pazarlama tanımınız "sampleApp" ise, Azure Marketi 'nde teklifinizin URL 'SI https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp olacaktır.  
| **Abonelik kimliklerini önizleme\*** | Ön görüntüleyicilerin bir den 100 abonelik tanımlayıcılarından birini ekleyin. Bu beyaz listeye alınan abonelikler, yayımlandıktan sonra, kullanıma hazır hale gelmeden önce, kullanıma alındıktan sonra teklifinizin erişimine sahip olur.          |
| **Faydalı bağlantılar**    | İsteğe bağlı olarak, tekliflerinizin kullanıcıları için destek, belgeler, forumlar vb. gibi çeşitli kaynaklara bağlantı sağlayabilirsiniz.  Belgelerinize en az bir bağlantı eklemeniz önerilir.            |
| **Önerilen Kategoriler (en fazla 5)\*** | Bir ila beş kategori seçin. Seçili kategoriler, teklifinizi Azure Marketi 'nde ve Azure portalında bulunan ürün kategorileriyle eşlemek için kullanılır. Bunlar, tarayıcı sayfalarında ve ürün ayrıntıları sayfasında gösterilir. |
|  |  |


## <a name="marketing-artifacts"></a>Pazarlama yapıtları

Pazarlama yapıtları formu, sonraki ekran yakalama bölümünde gösterilen gerekli ve isteğe bağlı alanlara sahiptir. Gerekli alanlar bir yıldız işareti (*) ile tanımlanır.

![Pazarlama yapıtları formu](./media/azureapp-marketplace-artifacts.png)

Aşağıdaki tabloda, pazarlama yapıtları açıklanmaktadır.

|      Alan         |    Açıklama    |
|  ---------------   |  ---------------  |
| **Küçük\***        | Küçük logo: PNG biçiminde 40x40 piksel     |
| **Orta\***       | Orta Logo: PNG biçiminde 90x90 piksel    |
| **Büyük\***        | Büyük Logo: 115x115 piksel, PNG biçiminde   |
| **Geniş\***         | Geniş amblem: 255x115 piksel, PNG biçiminde    |
| **Hero**           | İsteğe bağlı Hero logosu: 815x290 piksel PNG biçiminde. **Note:** Hero simgesi karşıya yüklendikten sonra silinemez. |
| **Ekran görüntüleri (en fazla 5)** |        Ekran görüntüleri, ürün ayrıntıları sayfanızda görüntülenir. Bu uygulamalar, uygulamanızın ne yaptığını ve nasıl çalıştığını görsel olarak iletmenin iyi bir yoludur. Örneğin, mimari diyagramlarını gösterebilir veya örnek durum çizimlerini kullanabilirsiniz. Ekran görüntüleri isteğe bağlıdır ve SKU başına 5 ile sınırlı olursunuz. Bir ekran görüntüsü eklemek için:<ul><li>Ekran görüntüsü penceresini açmak için **+ ekran görüntüsü Ekle** ' yi seçin</li><li>**Ad** -bir ad/başlık girin (en fazla 100 karakter uzunluğunda).</li><li>**Karşıya yükle** -görüntüyü karşıya yükleyin. Bu, PNG biçiminde olmalıdır ve boyut 533 x 324 pikseldir.</li></ul>           |
| **Video ekle**      | İsteğe bağlı, videolar ürün ayrıntıları sayfanızda görüntülenir. Uygulamanızın ne yaptığını ve nasıl çalıştığını görsel olarak iletmenin iyi bir yoludur. Video eklemek için: <ul><li>Video penceresini açmak için **+ video ekle** ' yi seçin</li><li>**Ad** -bir ad/başlık girin (en fazla 100 karakter uzunluğunda).</li><li>**Bağlantı** -videoyu barındıran sitenin URL 'sini girin (YouTube veya Vimeo)</li><li>**Küçük resim** -küçük resim yükleyin. Bu, PNG biçiminde olmalıdır ve boyut 533 x 324 pikseldir.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Azure Marketi 'ndeki yapıt örnekleri

Sonraki ekranda yakalama bir Market arama sonucu örneğini gösterir.

![Market teklif arama sonucu](./media/azureapp-marketplace-example-browse.png)

Aşağıdaki görüntüde, bir müşteri, arama sonucunda teklifin kutucuğuna tıkladıktan sonra teklifin Market 'te nasıl görüntülendiğini gösterir.

![Market teklif arama sonucu ayrıntıları](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Azure portalında yapıt örnekleri

Aşağıdaki ekran yakalama, bir teklifin Azure portalında nasıl görüntülendiğini gösterir. Bu örnekteki uygulama teklifi, **> geliştirme ve Test > Jenkins > her şeyin Market**'e göz atarak bulunur. Jenkins teklifi bir logo, başlık ve yayımcı görünen adını gösterir.

![Azure portal tekliflere gözatın](./media/azureapp-portalbrowse-artifacts-jenkins.png)

Sonraki ekran yakalama, bir Kullanıcı Jenkins seçtiğinde uygulamayla ilgili ayrıntılı bilgileri gösterir.

![Azure portal 'de teklif ayrıntıları](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Logo yönergeleri

Bulut İş Ortağı Portalı yüklenen tüm logolar, yönergeleri izlemelidir:

- Azure tasarımının basit bir renk paleti vardır. Logonuz üzerinde birincil ve ikincil renk sayısını düşük tutun.
- Azure portalının Tema renkleri beyaz ve siyahtır. Bu renkleri logolarınızın arka plan rengi olarak kullanmaktan kaçının. Logolarınızın Azure portal göre önemli olmasını sağlayacak bir renk kullanın. Basit birincil renkleri öneririz. Saydam bir arka plan kullanıyorsanız, logoların/metnin beyaz, siyah veya mavi olmadığından emin olun.
- Logonuz üzerinde gradyan arka plan kullanmayın.
- Logonuz üzerinde, şirketinizin veya marka adınızın bile metin yerleştirmekten kaçının. Logonuzun görünüm "düz" olmalıdır ve gradyanları kullanmaktan kaçınmalıdır.
- Logoyu uzamayın.


#### <a name="hero-logo"></a>Kahraman logosu

Hero logosu isteğe bağlıdır.

>[!IMPORTANT]
>Karşıya yüklendikten sonra Hero logosunu silemezsiniz.

Bir Hero logosu için aşağıdaki yönergeleri kullanın:

- Siyah, beyaz ve saydam arka planlara izin verilmez.
- Amblem için arka plan olarak herhangi bir açık renk kullanmaktan kaçının. Yayımcının görünen adı, plan başlığı ve teklif uzun Özeti, beyaz yazı tipi renginde görüntülenir ve arka planda bir süre önce gelmelidir.
- Logoyu tasarlarken çoğu metni kullanmaktan kaçının. Teklif listelendiğinde yayımcı adı, plan başlığı, teklif uzun Özeti ve bir oluştur düğmesi, logo içine programlı bir şekilde katıştırılır.
- Hero logosunun sağ tarafında kullanılmayan dikdörtgen bir boşluk ekleyin. Bu boş alan 415x100 pikseldir ve soldan 370 piksel arasındadır.


## <a name="lead-management"></a>Müşteri adayı yönetimi

Lider Yönetimi formu, müşteri adayı yönetimini yapılandırmak için isteğe bağlı bir alana sahiptir. Müşteri adayı yönetimini yapılandırmak için, açılan listeden lider hedefini seçin. Sonraki ekran yakalama, kullanılabilir hedefleri gösterir.

![Müşteri adayı yönetim hedefini seçin](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Bu iletiyi görmek için bilgi simgesini seçin: "müşteri adaylarınızın depolanacağı sistemi seçin. CRM sisteminize [buradan](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) nasıl bağlanacağınızı öğrenin. "

Daha fazla bilgi için bkz. [müşteri adaylarını yapılandırma](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).


## <a name="legal"></a>Yasal Bilgiler

Her teklif için gereken yasal belgeleri sağlamak için yasal formu kullanın.

Şu bilgileri belirtin:

- **Gizlilik ILKESI URL\*** -uygulamanızın gizlilik ilkesine bir bağlantı girin.
- **Kullanım koşulları\*** -uygulamanız için kullanım koşulları 'nı girin. Müşterilerinizin uygulamanızı deneyebilmeleri için önce bu koşulları kabul etmesi gerekir.

![Yasal form](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>Sonraki adımlar

[Destek sekmesi](./cpp-support-tab.md)
