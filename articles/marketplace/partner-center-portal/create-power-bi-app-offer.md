---
title: Microsoft AppSource Power BI uygulama teklifi oluşturma
description: Microsoft AppSource için Power BI uygulama teklifi oluşturmayı ve yayımlamayı öğrenin.
author: navits09
ms.author: navits
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/22/2020
ms.openlocfilehash: d5eb253fb24f463106866f8b0fe17f634e805cbb
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107486"
---
# <a name="create-a-power-bi-app-offer"></a>Power BI uygulaması teklifi oluşturma

Bu makalede, [Microsoft AppSource](https://appsource.microsoft.com/)için Power BI uygulama teklifinin nasıl oluşturulacağı ve yayımlanacağı açıklanır.

Başlamadan önce, henüz yapmadıysanız [Iş Ortağı Merkezi 'nde bir ticari Market hesabı oluşturun](../create-account.md) . Hesabınızın ticari Market programına kayıtlı olduğundan emin olun.

## <a name="create-a-new-offer"></a>Yeni teklif oluşturma

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/home)' nde oturum açın.
2. Sol gezinti menüsünde **ticari Market**  >  **genel bakış**' ı seçin.
3. Genel Bakış sayfasında **+ yeni teklif**  >  **Power BI hizmet uygulaması**' nı seçin.

   ![Sol gezinti menüsünü gösterir.](./media/new-offer-power-bi-app.png)

> [!NOTE]
> Bir teklif yayımlandıktan sonra, Iş Ortağı Merkezi 'nde yapılan düzenlemeler, teklif yeniden yayımlandıktan sonra yalnızca çevrimiçi mağazalarda görünür. Değişiklik yaptıktan sonra her zaman yeniden yayımlaytığınızdan emin olun.

> [!IMPORTANT]
> **Power BI hizmet uygulaması** gösterilmemişse veya etkinleştirilmemişse, hesabınız bu teklif türünü oluşturmak için izne sahip olmaz. Lütfen bir Geliştirici hesabına kaydolma dahil olmak üzere bu teklif türü için tüm [gereksinimleri](create-power-bi-app-overview.md) karşıladığınızı kontrol edin.

## <a name="new-offer"></a>Yeni teklif

Bir **TEKLIF kimliği** girin. Bu, hesabınızdaki her teklif için benzersiz bir tanımlayıcıdır.

- Bu KIMLIK, varsa Market teklifi ve Azure Resource Manager şablonları için Web adresinde müşteriler tarafından görülebilir.
- Yalnızca küçük harfleri ve rakamları kullanın. Kısa çizgi ve alt çizgi içerebilir, ancak boşluk içeremez ve 50 karakterle sınırlıdır. Örneğin, burada **Test-teklif-1** girerseniz, teklif Web adresi olacaktır `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Teklif KIMLIĞI, **Oluştur**' u seçtikten sonra değiştirilemez.

Bir **teklif diğer adı** girin. Bu, Iş Ortağı Merkezi 'nde teklif için kullanılan addır.

- Bu ad Market 'te kullanılmıyor ve teklif adından ve müşterilere gösterilen diğer değerlerden farklı.
- Teklif diğer adı, **Oluştur**' u seçtikten sonra değiştirilemez.

Teklifi oluşturmak için **Oluştur** ' u seçin ve devam edin.

## <a name="offer-overview"></a>Teklifin genel bakış

Bu sayfa, bu teklifi yayımlamak için gereken adımların görsel bir gösterimini gösterir (hem tamamlandı hem de yaklaşan) ve her adımın tamamlanması gereken süre.

Bu teklif üzerinde yaptığınız seçime bağlı olarak işlem gerçekleştirme bağlantılarını içerir. Örnek:

- Teklif bir taslak-silme taslağı tekliftir
- Teklif canlı ise, [teklifi satmaya](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Teklif önizleme sürümündedir- [canlı](../review-publish-offer.md#previewing-and-approving-your-offer)
- Yayımcı oturumu kapatma Işlemini tamamlamadıysanız [yayımlamayı Iptal edin.](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Teklif kurulumu

### <a name="customer-leads"></a>Müşteri liderleri

Teklifinizi Iş Ortağı Merkezi ile Market 'te yayımlarken, Müşteri Ilişkileri yönetimi (CRM) sisteminize bağlamanız gerekir. Bu, bir kişi, ürününüzü ilgilendiren veya ürünü kullandığı anda müşteri iletişim bilgilerini almanızı sağlar.

1. Potansiyel müşterileri göndermemizi istediğiniz müşteri adayı hedefini seçin. İş Ortağı Merkezi aşağıdaki CRM sistemlerini destekler:

    - Müşteri katılımı için [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md)
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > CRM sisteminiz bu listede yoksa, müşteri adayı verilerini depolamak için [Azure tablosu](commercial-marketplace-lead-management-instructions-azure-table.md) veya ve [https uç noktasını](commercial-marketplace-lead-management-instructions-https.md) kullanın. Ardından verileri CRM sisteminize dışarı aktarın.

2. Teklifinizi Iş Ortağı Merkezi 'nde yayımlarken lider hedefine bağlayın.
3. Müşteri adayı hedefi bağlantısının düzgün şekilde yapılandırıldığını doğrulayın. Iş Ortağı Merkezi 'nde yayımladıktan sonra, bağlantıyı doğrulayacağız ve size bir test lideri göndereceğiz. Teklifi canlı olmadan önce önizlerken, teklifi önizleme ortamında satın almayı deneyerek lider bağlantınızı da test edebilirsiniz.
4. Müşteri adayı hedefine olan bağlantının güncelleştirildiğinden emin olun, böylece müşteri adaylarını kaybetmezsiniz.

Bazı ek müşteri yönetimi kaynakları aşağıda verilmiştir:

- [Ticari market teklifinizden elde edilen potansiyel müşteriler](commercial-marketplace-get-customer-leads.md)
- [Müşteri adayı yönetimi hakkında sık sorulan sorular](../lead-management-faq.md#common-questions-about-lead-management)
- [Müşteri adayı yapılandırma hatalarını giderme](../lead-management-faq.md#publishing-config-errors)
- [Müşteri adayı yönetimine genel bakış](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (açılır pencere engelleyicinizin kapalı olduğundan emin olun).

Devam etmeden önce **Taslağı kaydet** ' i seçin.

## <a name="properties"></a>Özellikler

Bu sayfa, teklifinizi Market 'te, uygulama sürümünüzde ve teklifinizi destekleyen yasal sözleşmelerde gruplandırmak için kullanılan kategorileri ve endüstrileri tanımlamanızı sağlar.

### <a name="category"></a>Kategori

Teklifinizi uygun Market arama alanlarında yerleştirmek için kategoriler ve alt kategoriler ' i seçin. Teklifinizin teklif açıklamasında bu kategorileri nasıl desteklediğini açıkladığınızdan emin olun. Seçin:

- Birincil ve ikincil kategori dahil olmak üzere en az bir ve en fazla iki kategori (isteğe bağlı).
- Her birincil ve/veya ikincil kategori için en fazla iki alt kategori. Teklifiniz için geçerli bir alt kategori yoksa, **geçerli değil**' i seçin.

[Teklif listesi En Iyi uygulamalarında](../gtm-offer-listing-best-practices.md)kategorilerin ve alt kategorilerin tam listesini görebilirsiniz.

### <a name="industry"></a>Sektör

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="legal"></a>Yasal Bildirim

#### <a name="terms-and-conditions"></a>hüküm ve koşullar

Kendi özel hüküm ve koşullarınızı sağlamak için **hüküm ve koşullar** kutusuna en fazla 10.000 karakter girin. Müşterilerinizin teklifinizi deneyebilmeleri için önce bu koşulları kabul etmesi gerekir.

Bir sonraki bölüme devam etmeden önce **Taslağı kaydet** ' i seçin, teklif listesi.

## <a name="offer-listing"></a>Teklif listesi

Burada, Market 'te görüntülenen teklif ayrıntılarını tanımlayacaksınız. Buna teklif adı, açıklama, görüntüler vb. dahildir.

### <a name="language"></a>Dil

Teklifinizin listelenecek dili seçin. Şu anda **İngilizce (Birleşik Devletler)** kullanılabilir tek seçenektir.

Her dil/Pazar için Market ayrıntılarını (teklif adı, açıklama ve görüntüler gibi) tanımlayın. Bu bilgileri sağlamak için dil/Pazar adını seçin.

> [!NOTE]
> Teklif açıklaması, "Bu uygulama yalnızca [Ingilizce olmayan dil] bölümünde kullanılabilir" ifadesi ile başlıyorsa, teklif ayrıntılarının Ingilizce olması gerekmez. Ayrıca, teklif listesinde kullanılan bir dilde içerik sunmak için faydalı bir bağlantı sağlamak da yararlı olabilir.

Teklif bilgilerinin Microsoft AppSource nasıl göründüğünü gösteren bir örnek aşağıda verilmiştir (listelenen tüm fiyatlar yalnızca örnek amaçlıdır ve gerçek maliyetlerin yansıtılmasına yönelik değildir):

:::image type="content" source="media/example-power-bi-app.png" alt-text="Bu teklifin Microsoft AppSource nasıl göründüğünü gösterir.":::

#### <a name="call-out-descriptions"></a>Çağrı açıklamaları

1. Logo
2. Ürünler
3. Kategoriler
4. Sektörler
5. Destek adresi (bağlantı)
6. Kullanım koşulları
7. Gizlilik ilkesi
8. Teklif adı
9. Özet
10. Description
11. Ekran görüntüleri/videolar

### <a name="name"></a>Name

Buraya girdiğiniz ad teklifinizin başlığı olarak görüntülenir. Bu alan, teklifi oluştururken **teklif diğer adı** kutusuna girdiğiniz metin ile önceden doldurulur. Bu adı daha sonra değiştirebilirsiniz.

Ad:

- Trademarked olabilir (ve ticari marka veya telif hakkı sembolleri dahil edebilirsiniz).
- 50 karakterden uzun olamaz.
- Emojıs dahil olamaz.

### <a name="search-results-summary"></a>Arama sonuçları Özeti

Teklifinizin kısa bir açıklamasını sağlayın. Bu, 100 karakter uzunluğunda olabilir ve Market arama sonuçlarında kullanılır.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="search-keywords"></a>Anahtar sözcükleri ara

Müşterilerin teklifinizi Market 'te bulmasına yardımcı olmak için en fazla üç isteğe bağlı arama anahtar sözcüğü girin. En iyi sonuçlar için, tanımlarınızda bu anahtar sözcükleri de kullanın.

### <a name="helpprivacy-web-addresses"></a>Yardım/Gizlilik Web adresleri

Müşterilerinizin teklifinizi daha iyi anlamasına yardımcı olacak bağlantılar sağlar.

#### <a name="help-link"></a>Yardım bağlantısı

Müşterilerinizin teklifiniz hakkında daha fazla bilgi sağlayabildiği Web adresini girin.

#### <a name="privacy-policy-url"></a>Gizlilik ilkesi URL 'SI

Kuruluşunuzun gizlilik ilkesine Web adresini girin. Teklifinizin gizlilik yasaları ve yönetmeliklerle uyumlu olmasını sağlamaktan sorumlu olursunuz. Ayrıca, Web sitenizde geçerli bir gizlilik ilkesi göndermekten de sorumlusunuz.

### <a name="contact-information"></a>İletişim Bilgileri

Bir **destek kişisi** ve **mühendislik ilgili kişisi** için ad, e-posta ve telefon numarası sağlamanız gerekir. Bu bilgiler müşterilere gösterilmez. Microsoft tarafından kullanılabilir ve bulut çözümü sağlayıcısı (CSP) iş ortakları için sağlanmış olabilir.

- Destek kişisi (gerekli): genel destek soruları Için.
- Mühendislik ilgili kişisi (gerekli): Teknik sorular ve sertifika sorunları Için.
- CSP program kişisi (isteğe bağlı): CSP programıyla ilgili satıcı soruları Için.

**Destek kişisi** bölümünde, iş ortaklarının teklifiniz için destek bulabileceği **Destek Web sitesinin** Web adresini belirtin.

### <a name="supporting-documents"></a>Destekleyici belgeler

PDF biçiminde en az bir ve en çok üç ilgili pazarlama belgesi sağlayın. Örneğin, teknik incelemeler, broşürler, denetim listeleri veya sunular.

### <a name="marketplace-images"></a>Market görüntüleri

Teklifinizle birlikte kullanılacak logo ve görüntüler sağlayın. Tüm görüntülerin PNG biçiminde olması gerekir. Bulanık görüntüler reddedilir.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Dosya yükleme sorununuz varsa, yerel ağınızın `https://upload.xboxlive.com` Iş Ortağı Merkezi tarafından kullanılan hizmeti engellemediğinden emin olun.

#### <a name="store-logos"></a>Mağaza logoları

**Büyük** boyutlu amblem IÇIN bir PNG dosyası sağlayın. İş Ortağı Merkezi, **küçük** bir logo oluşturmak için bunu kullanacaktır. İsterseniz bunu daha sonra farklı bir görüntüyle değiştirebilirsiniz.

- **Büyük** (216 x 216-350 x 350 px, gereklidir)
- **Küçük** (48 x 48 piksel, isteğe bağlı)

Bu amblemler, listede farklı yerlerde kullanılır:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Ekran görüntüleri

Teklifinizin nasıl çalıştığını gösteren en az bir ve en fazla beş ekran görüntüsü ekleyin. Her biri, boyut ve PNG biçiminde 1280 x 720 piksel olmalıdır.

#### <a name="videos-optional"></a>Videolar (isteğe bağlı)

Teklifinizi gösteren en fazla beş video ekleyin. Videonun adını, Web adresini ve videonun küçük resim PNG görüntüsünü 1280 x 720 piksel boyutunda girin.

#### <a name="additional-marketplace-listing-resources"></a>Ek Market listeleme kaynakları

Teklif listeleri oluşturma hakkında daha fazla bilgi için bkz. [teklif listesi en iyi uygulamaları](../gtm-offer-listing-best-practices.md).

## <a name="technical-configuration"></a>Teknik yapılandırma

Uygulamanızı Power BI hizmetine yükseltin ve müşterilerin uygulamanızı yüklemesini sağlayan Power BI uygulama yükleyicisi bağlantısını sağlayın. Daha fazla bilgi için bkz. [Power BI içindeki panoları ve raporları kullanarak uygulama yayımlama](/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Ek içerik

Sağlamamıza yardımcı olması için teklifiniz hakkında ek bilgiler sağlayın. Bu bilgiler müşterilere gösterilmez veya Market 'te yayımlanır.

### <a name="validation-assets"></a>Doğrulama varlıkları

İsteğe bağlı olarak, Microsoft doğrulama ekibinin uygulamanızı yapılandırmasına, bağlanmasına ve test etmenize yardımcı olmak için yönergeler (en fazla 3.000 karakter) ekleyebilirsiniz. Verileri bağla seçeneğini test etmek için kullanılabilecek tipik yapılandırma ayarlarını, hesapları, parametreleri veya diğer bilgileri ekleyin. Bu bilgiler yalnızca doğrulama ekibi tarafından görülebilir ve yalnızca doğrulama amaçlarıyla kullanılır.

## <a name="review-and-publish"></a>İnceleme ve yayımlama

Teklifin tüm gerekli bölümlerini tamamladıktan sonra, teklifinizi gözden geçirme ve yayımlama için gönderebilirsiniz.

Portalın sağ üst köşesinde, **gözden geçir ve Yayımla**' yı seçin.

İnceleme sayfasında şunları yapabilirsiniz:

- Teklifin her bölümü için tamamlanma durumuna bakın. Teklifin tüm bölümleri Tamam olarak işaretlenene kadar yayımlayamazsınız.
  - **Başlatılmamış** -bölüm başlatılmamış ve tamamlanması gerekiyor.
  - **Tamamlanmamış** -bölümde düzeltilmesi gereken hatalar var veya daha fazla bilgi sağlamanız gerekiyor. Rehberlik için bu belgenin önceki bölümlerine bakın.
  - **Tamamlandı** -bölümde tüm gerekli veriler var ve hata yok. Teklifi gönderebilmeniz için teklifin tüm bölümlerinin tamamlanmış olması gerekir.
- Uygulamanızın doğru şekilde test edilmesini sağlamak için sertifika ekibine test yönergeleri sağlayın. Ayrıca, teklifinizi anlamak için yararlı olan tüm ek notları sağlayın.

Teklifi yayımlamaya göndermek için **Yayımla**' yı seçin.

Teklifin bir önizleme sürümünün gözden geçirilmesi ve onaylanmak üzere kullanılabilir olduğunu size bildirmek için size bir e-posta göndereceğiz. Teklifinizi herkese açık bir şekilde yayımlamak için Iş Ortağı Merkezi 'ne gidin ve **Git-Live**' ı seçin.
