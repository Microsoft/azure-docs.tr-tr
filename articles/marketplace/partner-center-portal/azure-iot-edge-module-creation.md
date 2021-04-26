---
title: Azure Marketi 'nde Iş Ortağı Merkezi ile Azure IoT Edge bir modül teklifi oluşturma
description: Iş Ortağı Merkezi 'ni kullanarak Azure Marketi 'nde bir IoT Edge modülü teklifi oluşturmayı, yapılandırmayı ve yayımlamayı öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 08/07/2020
ms.openlocfilehash: 12600cadaa84ae116818eec06459d5db0c05304a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773429"
---
# <a name="create-an-iot-edge-module-offer"></a>IoT Edge modül teklifi oluşturma

Bu makalede, Azure Marketi için bir Nesnelerin İnterneti (IoT) Edge modülü teklifinin nasıl oluşturulacağı ve yayımlanacağı açıklanır. Başlamadan önce, henüz yapmadıysanız [Iş Ortağı Merkezi 'nde bir ticari Market hesabı oluşturun](../create-account.md) . Hesabınızın ticari Market programına kayıtlı olduğundan emin olun.

## <a name="create-a-new-offer"></a>Yeni teklif oluşturma

1. [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/home)' nde oturum açın.
2. Sol gezinti menüsünde **ticari Market**  >  **genel bakış**' ı seçin.
3. Genel Bakış sayfasında **+ yeni teklif**  >  **IoT Edge modülünü** seçin.

    ![Sol gezinti menüsünü gösterir.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> Bir teklif yayımlandıktan sonra, Iş Ortağı Merkezi 'nde yapılan düzenlemeler, teklif yeniden yayımlandıktan sonra yalnızca çevrimiçi mağazalarda görünür. Değişiklik yaptıktan sonra her zaman yeniden yayımlaytığınızdan emin olun.

### <a name="offer-id-and-alias"></a>Teklif KIMLIĞI ve diğer ad

Bir **TEKLIF kimliği** girin. Bu, hesabınızdaki her teklif için benzersiz bir tanımlayıcıdır.

- Bu KIMLIK, varsa Market teklifi ve Azure Resource Manager şablonları için Web adresinde müşteriler tarafından görülebilir.
- Yalnızca küçük harfleri ve rakamları kullanın. Kısa çizgi ve alt çizgi içerebilir, ancak boşluk içeremez ve 50 karakterle sınırlıdır. Örneğin, **Test-teklif-1** girerseniz, teklif Web adresi olacaktır `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Teklif KIMLIĞI, **Oluştur**' u seçtikten sonra değiştirilemez.

Bir **teklif diğer adı** girin. Bu, Iş Ortağı Merkezi 'nde teklif için kullanılan addır.

- Bu ad Market 'te kullanılmıyor ve teklif adından ve müşterilere gösterilen diğer değerlerden farklı.
- Bu, **Oluştur**' u seçtikten sonra değiştirilemez.

Teklifi oluşturmak için **Oluştur** ' u seçin ve devam edin.

## <a name="offer-overview"></a>Teklifin genel bakış

**Teklif genel bakış** sayfasında, bu teklifi yayımlamak için gereken adımların görsel bir gösterimi gösterilir (hem tamamlandı hem de yakında) ve her adımın tamamlanması gereken süre.

Bu sayfa, yaptığınız seçime bağlı olarak bu teklif üzerinde işlem gerçekleştirme bağlantılarını içerir. Örnek:

- Teklif bir taslak-silme taslağı tekliftir
- Teklif canlı ise, [teklifi satmaya](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Teklif önizleme sürümündedir- [canlı](../review-publish-offer.md#previewing-and-approving-your-offer)
- Yayımcı oturumu kapatma Işlemini tamamlamadıysanız [yayımlamayı Iptal edin.](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Teklif kurulumu

Teklifinizi ayarlamak için bu adımları izleyin.

### <a name="customer-leads"></a>Müşteri liderleri

Teklifinizi Iş Ortağı Merkezi ile Market 'te yayımlarken, isteğe bağlı olarak Müşteri Ilişkileri yönetimi (CRM) sisteminize bağlayabilirsiniz. Bu, bir kişi, ürününüzü ilgilendiren veya ürünü kullandığı anda müşteri iletişim bilgilerini almanızı sağlar.

1. Potansiyel müşterileri göndermemizi istediğiniz müşteri adayı hedefini seçin. İş Ortağı Merkezi aşağıdaki CRM sistemlerini destekler:

    - Müşteri katılımı için [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md)
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > CRM sisteminiz yukarıda listelenmiyorsa, müşteri adayı verilerini depolamak için [Azure tablosu](commercial-marketplace-lead-management-instructions-azure-table.md) veya [https uç noktasını](commercial-marketplace-lead-management-instructions-https.md) kullanın, ardından verileri CRM sisteminize dışarı aktarın.

2. Teklifinizi Iş Ortağı Merkezi 'nde yayımlarken lider hedefine bağlayın.
3. Müşteri adayı hedefi bağlantısının düzgün şekilde yapılandırıldığını doğrulayın. Iş Ortağı Merkezi 'nde yayımladıktan sonra, bağlantıyı doğrulayacağız ve size bir test lideri göndereceğiz. Teklifi canlı olmadan önce önizlerken, teklifi önizleme ortamında satın almayı deneyerek lider bağlantınızı da test edebilirsiniz.
4. Müşteri adayı hedefine olan bağlantının güncelleştirildiğinden emin olun, böylece müşteri adaylarını kaybetmezsiniz.

Bazı ek müşteri yönetimi kaynakları aşağıda verilmiştir:

- [Ticari market teklifinizden elde edilen potansiyel müşteriler](commercial-marketplace-get-customer-leads.md)
- [Müşteri adayı yönetimi hakkında sık sorulan sorular](../lead-management-faq.md#common-questions-about-lead-management)
- [Müşteri adayı yapılandırma hatalarını giderme](../lead-management-faq.md#publishing-config-errors)
- [Müşteri adayı yönetimine genel bakış](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (açılır pencere engelleyicinizin kapalı olduğundan emin olun).

Devam etmeden önce **Taslağı kaydet** ' i seçin.

### <a name="properties"></a>Özellikler

Bu sayfa, teklifinizi Market 'te gruplandırmak için kullanılan kategorileri ve teklifinizi destekleyen yasal sözleşmeleri tanımlamanızı sağlar.

#### <a name="category"></a>Kategori

Teklifinizi uygun Market arama alanlarında yerleştirmek için kategoriler ve alt kategoriler ' i seçin. Teklifinizin teklif açıklamasında bu kategorileri nasıl desteklediğini açıkladığınızdan emin olun. Seçin:

- Birincil ve ikincil kategori dahil olmak üzere en az bir ve en fazla iki kategori (isteğe bağlı).
- Her birincil ve/veya ikincil kategori için en fazla iki alt kategori. Teklifiniz için geçerli bir alt kategori yoksa, **geçerli değil**' i seçin.

[Teklif listesi En Iyi uygulamalarında](../gtm-offer-listing-best-practices.md)kategorilerin ve alt kategorilerin tam listesini görebilirsiniz. Market 'te IoT Edge modüller her zaman  **nesnelerin interneti**  >  **IoT Edge modül**   kategorisi altında gösterilir.

#### <a name="legal"></a>Yasal Bildirim

Teklif için hüküm ve koşullar sağlamanız gerekir. İki seçeneğiniz vardır:

- Microsoft ticari Market için standart sözleşmeyi kullanın.
- Kendi hüküm ve koşullarınızı belirtin.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft ticari Market için standart sözleşme

Ticari Market 'teki işlemleri kolaylaştırmaya yardımcı olmak için standart bir sözleşme şablonu sunuyoruz. Çözümünüzü standart sözleşme altında sunmayı tercih edebilirsiniz; Bu, müşterilerin yalnızca bir kez denetlemesi ve kabul etmesi gereken bir çözümdür. Özel hüküm ve koşullar yapmak istemiyorsanız bu iyi bir seçenektir.

Standart Sözleşme hakkında daha fazla bilgi edinmek için bkz. [Microsoft ticari Market Için standart sözleşme](../standard-contract.md). Ayrıca [standart sözleşme](https://go.microsoft.com/fwlink/?linkid=2041178) PDF 'sini de indirebilirsiniz (açılır pencere engelleyicisinin kapalı olduğundan emin olun).

Standart sözleşmeyi kullanmak için **Microsoft 'un ticari Market Için standart sözleşmeyi kullan** onay kutusunu işaretleyin ve ardından **kabul et**' e tıklayın.

> [!NOTE]
> Microsoft ticari Market için standart sözleşmeyi kullanarak bir teklifi yayımladıktan sonra, kendi özel hüküm ve koşullarınızı kullanamazsınız. Çözümünüzü standart sözleşme kapsamında ya da kendi hüküm ve koşullarınızın altına sunun.

![Microsoft 'un ticari Market onay kutusu için standart sözleşmeyi kullanmayı gösterir.](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Kendi hüküm ve koşullarınız

Kendi özel hüküm ve koşullarınızı sağlamak için **hüküm ve koşullar** kutusuna girin. Bu kutuya metnin sınırsız miktarda karakterini girebilirsiniz. Müşterilerinizin teklifinizi deneyebilmeleri için önce bu koşulları kabul etmesi gerekir.

Bir sonraki bölüme devam etmeden önce **Taslağı kaydet** ' i seçin, teklif listesi.

## <a name="offer-listing"></a>Teklif listesi

Burada, Market 'te görüntülenen teklif ayrıntılarını tanımlayacaksınız. Buna teklif adı, açıklama, görüntüler vb. dahildir. Bu teklifi yapılandırırken Microsoft 'un ilke sayfasında ayrıntılı ilkeleri izlediğinizden emin olun.

> [!NOTE]
> Teklif açıklaması, "Bu uygulama yalnızca [Ingilizce olmayan dil] bölümünde kullanılabilir" ifadesi ile başlıyorsa, teklif ayrıntılarının Ingilizce olması gerekmez. Ayrıca, teklif listesi ayrıntılarından farklı bir dilde içerik sunmak için faydalı bir bağlantı sağlamak da yararlı olabilir.

### <a name="name"></a>Name

Buraya girdiğiniz ad teklifinizin başlığı olarak görüntülenir. Bu alan, teklifi oluştururken **teklif diğer adı** kutusuna girdiğiniz metin ile önceden doldurulur. Bu adı daha sonra değiştirebilirsiniz.

Ad:

- Trademarked olabilir (ve ticari marka veya telif hakkı sembolleri dahil edebilirsiniz).
- 50 karakterden uzun olamaz.
- Emojıs dahil olamaz.

### <a name="search-results-summary"></a>Arama sonuçları Özeti

Teklifinizin kısa bir açıklamasını sağlayın. Bu, 100 karakter uzunluğunda olabilir ve Market arama sonuçlarında kullanılır.

### <a name="long-summary"></a>Uzun Özet

Teklifinizin daha ayrıntılı bir açıklamasını sağlayın. Bu, 256 karakter uzunluğunda olabilir ve Market arama sonuçlarında kullanılır.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

IoT Edge modül teklifleri, açıklamanın en altında bulunan en düşük donanım gereksinimleri paragrafını içermelidir, örneğin:

- En düşük donanım gereksinimleri: Linux x64 ve ARM32 OS, 1 GB RAM, 500 MB depolama

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>Gizlilik ilkesi URL 'SI

Kuruluşunuzun gizlilik ilkesinin Web adresini girin. Teklifinizin gizlilik yasaları ve yönetmeliklerle uyumlu olmasını sağlamaktan sorumlu olursunuz. Ayrıca, Web sitenizde geçerli bir gizlilik ilkesi göndermekten de sorumlusunuz.

#### <a name="useful-links"></a>Yararlı bağlantılar

Teklifiniz hakkında ek çevrimiçi belgeler sağlayın. En fazla 25 bağlantı ekleyebilirsiniz. Bir bağlantı eklemek için **+ bağlantı ekle** ' yi seçin ve ardından aşağıdaki alanları doldurun:

- **Başlık** -müşteriler, teklifin ayrıntılar sayfasında başlığı görür.
- **Bağlantı (URL)** -müşterilerin çevrimiçi belgenizi görüntülemesi için bir bağlantı girin. Bağlantı veya ile başlamalıdır `http://` `https://` .

Belgelerinize en az bir bağlantı ve [Azure IoT cihaz kataloğundan](https://devicecatalog.azure.com/)uyumlu IoT Edge cihazlara bir bağlantı eklediğinizden emin olun.

### <a name="contact-information"></a>İletişim bilgileri

Bir **destek kişisi** ve **mühendislik ilgili kişisi** için ad, e-posta ve telefon numarası sağlamanız gerekir. Bu bilgiler müşterilere gösterilmez. Microsoft tarafından kullanılabilir ve bulut çözümü sağlayıcısı (CSP) iş ortakları için sağlanmış olabilir.

- Destek kişisi (gerekli): genel destek soruları Için.
- Mühendislik ilgili kişisi (gerekli): Teknik sorular ve sertifika sorunları Için.
- CSP program kişisi (isteğe bağlı): CSP programıyla ilgili satıcı soruları Için.

**Destek kişisi** bölümünde, ortağın küresel Azure 'Da, Azure Kamu 'da veya her ikisinde de kullanılabilir olup olmadığına bağlı olarak iş ortaklarının teklif desteğini bulabileceği **Destek Web sitesinin** Web adresini sağlayın.

**CSP program kişisi** bölümünde, CSP iş ortaklarının teklifiniz için pazarlama malzemeleri bulabileceği bağlantıyı (**CSP program pazarlama malzemeleri**) sağlayın.

#### <a name="additional-marketplace-listing-resources"></a>Ek Market listeleme kaynakları

Teklif listeleri oluşturma hakkında daha fazla bilgi için bkz. [teklif listesi en iyi uygulamaları](../gtm-offer-listing-best-practices.md).

### <a name="marketplace-images"></a>Market görüntüleri

Teklifinizle birlikte kullanılacak logo ve görüntüler sağlayın. Tüm görüntülerin PNG biçiminde olması gerekir. Bulanık görüntüler reddedilir.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Dosya yükleme sorununuz varsa, yerel ağınızın https://upload.xboxlive.com Iş Ortağı Merkezi tarafından kullanılan hizmeti engellemediğinden emin olun.

#### <a name="store-logos"></a>Mağaza logoları

**Büyük** boyutlu amblem IÇIN bir PNG dosyası sağlayın. İş Ortağı Merkezi, **küçük** ve **Orta ölçekli** bir amblem oluşturmak için bunu kullanacaktır. İsterseniz bunları daha sonra farklı görüntülerle değiştirebilirsiniz.

- **Büyük** (216 x 216-350 x 350 px, gereklidir)
- **Orta** (90 x 90 piksel, isteğe bağlı)
- **Küçük** (48 x 48 piksel, isteğe bağlı)

Bu amblemler, listede farklı yerlerde kullanılır:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Ekran görüntüleri (isteğe bağlı)

Teklifinizin nasıl çalıştığını gösteren en fazla beş ekran görüntüsü ekleyin. Her biri, boyut ve PNG biçiminde 1280 x 720 piksel olmalıdır.

#### <a name="videos-optional"></a>Videolar (isteğe bağlı)

Teklifinizi gösteren en fazla beş video ekleyin. Videonun adını, Web adresini ve videonun küçük resim PNG görüntüsünü 1280 x 720 piksel boyutunda girin.

#### <a name="marketplace--examples"></a>Market örnekleri

Azure Marketi 'nde teklif bilgilerinin nasıl göründüğünü gösteren bir örnek aşağıda verilmiştir:

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="Bu teklifin Azure Marketi 'nde nasıl göründüğünü gösterir.":::

#### <a name="call-out-descriptions"></a>Çağrı açıklamaları

1. Büyük logo
2. Kategoriler
3. Destek adresi (bağlantı)
4. hüküm ve koşullar
5. Gizlilik ilkesi adresi (bağlantı)
6. Name
7. Özet
8. Description
9. Yararlı bağlantılar
10. Ekran görüntüleri/videolar

<br>Azure Marketi arama sonuçlarında teklif bilgilerinin nasıl göründüğünü gösteren bir örnek aşağıda verilmiştir:

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="Bu teklifin Azure Marketi arama sonuçlarında nasıl göründüğünü gösterir.":::

#### <a name="call-out-descriptions"></a>Çağrı açıklamaları

1. Küçük logo
2. Teklif adı
3. Arama sonuçları Özeti

<br>Teklif bilgilerinin Azure portal nasıl göründüğünü gösteren bir örnek aşağıda verilmiştir:

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="Bu teklifin Azure portal nasıl göründüğünü gösterir.":::

#### <a name="call-out-descriptions"></a>Çağrı açıklamaları

1. Ad
2. Açıklama
3. Yararlı bağlantılar
4. Ekran görüntüleri

<br>Teklif bilgilerinin Azure portal arama sonuçlarında nasıl göründüğünü gösteren bir örnek aşağıda verilmiştir:

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="Bu teklifin Azure portal arama sonuçlarında nasıl göründüğünü gösterir.":::

#### <a name="call-out-descriptions"></a>Çağrı açıklamaları

1. Küçük logo
2. Teklif adı
3. Arama sonuçları Özeti

<br>Sonraki bölüme devam etmeden önce **Taslağı kaydet** ' i seçin, önizleme.

## <a name="preview"></a>Önizleme

**Önizleme sekmesinde**, daha geniş Market hedef kitlesi için canlı yayımlamadan önce teklifinizi doğrulamak üzere sınırlı bir **Önizleme hedef kitlesi** seçebilirsiniz.

> [!IMPORTANT]
> Teklifinizi önizlemede güncelleştirdikten sonra teklifinizi herkese açık bir şekilde yayımlamak için **canlı git** ' i seçmeniz gerekir.

Azure abonelik KIMLIĞI GUID 'Lerini kullanarak önizleme Dinleyicilerinizi, her biri için isteğe bağlı bir açıklama ile birlikte belirtin. Bu alanlardan hiçbiri müşteriler tarafından görülenebilir.

> [!NOTE]
> Azure abonelik KIMLIĞINIZI Azure portal abonelikler sayfasında bulabilirsiniz.

Tek tek (10 ' a kadar) veya bir CSV dosyası karşıya yükleyerek (100 'e kadar) en az bir Azure abonelik KIMLIĞI ekleyin. Bu abonelik kimliklerini ekleyerek teklifinizin canlı olarak yayınlanmadan önce ne kadar önizleyebilir tanımlar. Teklifiniz zaten canlı ise, teklifinizdeki değişiklikleri veya güncelleştirmeleri test etmek için bir önizleme izleyiciyi tanımlayabilirsiniz.

Sonraki bölüme geçmeden önce **Taslağı kaydet** ' i seçin, plana genel bakış ' a tıklayın.

## <a name="plan-overview"></a>Plana genel bakış

Bu sekme, Iş Ortağı Merkezi 'nde aynı teklif dahilinde farklı plan seçenekleri sağlamanıza olanak tanır. Planlar (eskiden SKU olarak adlandırılır), genel bulutlar, kamu bulutları ve plan tarafından başvurulan görüntü gibi hangi bulutların kullanılabildiği konusunda farklılık gösterebilir. Teklifinizi Market 'te listelemek için en az bir plan ayarlamanız gerekir.

Her teklif için en fazla 100 plan oluşturabilirsiniz: bunların en fazla 45 ' e kadar özel olabilir. [Microsoft ticari Market 'Teki özel tekliflerle](../private-offers.md)ilgili özel planlar hakkında daha fazla bilgi edinin.

Planlarınızı oluşturduktan sonra **plana genel bakış** sekmesi şunları gösterir:

- Plan adları
- Fiyatlandırma modeli
- Azure bölgeleri (küresel veya kamu)
- Geçerli yayımlama durumu
- Kullanılabilir eylemler

Plana genel bakışta sunulan eylemler planınızın geçerli durumuna bağlı olarak farklılık gösterir. Bunlara aşağıdakiler dahildir:

- **Taslağı Sil**: plan durumu bir tasladır.
- **Satışı durdur plan**: plan durumu canlı olarak yayımlandı.

### <a name="create-new-plan"></a>Yeni plan oluştur

**Yeni plan oluştur**' u seçin. **Yeni plan** iletişim kutusu görüntülenir.

**Plan kimliği** kutusunda, bu teklifte her plan için benzersiz BIR plan kimliği oluşturun. Bu KIMLIK, ürün web adresinde müşterilere görünür olacaktır. Yalnızca küçük harf ve rakam, tire veya alt çizgi ve en fazla 50 karakter kullanın.

**Plan adı** kutusuna bu plan için bir ad girin. Müşteriler, teklifiniz dahilinde hangi planın seçeceğinize karar verirken bu adı görür. Bu teklifte her plan için benzersiz bir ad oluşturun. Örneğin, Windows Server **2016** ve **Windows Server 2019** planlarına **sahip bir** teklif adı kullanabilirsiniz.

> [!NOTE]
> Plan KIMLIĞI, **Oluştur**' u seçtikten sonra değiştirilemez.

**Oluştur**’u seçin.

### <a name="plan-setup"></a>Planı ayarla

Bu sekme, planın hangi bulutlarda kullanılabilir olduğunu yapılandırmanızı sağlar. Bu sekmedeki yanıtlarınız, diğer sekmelerde hangi alanların görüntülendiğini etkiler.

#### <a name="azure-regions"></a>Azure bölgeleri

IoT Edge modül teklifleri için tüm planlar otomatik olarak **Azure küresel**' te kullanıma sunulmuştur.  Planınız, Market kullanan tüm genel Azure bölgelerinde bulunan müşteriler tarafından kullanılabilir. Ayrıntılar için bkz. [coğrafi kullanılabilirlik ve para birimi desteği](../marketplace-geo-availability-currencies.md).

Çözümünüzü burada görünmesini sağlamak için [Azure Kamu](../../azure-government/documentation-government-welcome.md) seçeneğini belirleyin. Bu, ABD Federal, eyalet ve yerel ya da üç devlet kurumlarının yanı sıra onlara hizmeti sunmaya uygun iş ortakları için denetimli erişime sahip bir kamu topluluk bulutuna sahiptir. Yayımcı olarak, bu bulut topluluğu için tüm uyumluluk denetimleri, güvenlik ölçüleri ve en iyi uygulamalardan siz sorumlusunuz. Azure Kamu fiziksel olarak yalıtılmış veri merkezleri ve ağlar (yalnızca ABD 'de bulunur) kullanır. Azure Kamu 'da [yayımlamadan](../../azure-government/documentation-government-manage-marketplace-partners.md) önce, sonuçlar farklı olabileceğinden çözümünüzü test edin ve onaylayın. Çözümünüzü hazırlamak ve test etmek için [Microsoft Azure Kamu deneme](https://azure.microsoft.com/global-infrastructure/government/request/)sürümünden bir deneme hesabı isteyin.

> [!NOTE]
> Planınız yayımlandıktan ve belirli bir bölgede kullanılabilir olduktan sonra bu bölgeyi kaldıramazsınız.

#### <a name="azure-government-certifications"></a>Azure Kamu sertifikaları

Bu seçenek yalnızca Azure **kamu** , Azure **bölgeleri** kapsamında seçili olduğunda görülebilir.

Azure Kamu Hizmetleri, belirli kamu düzenlemelerine ve gereksinimlerine tabi olan verileri işler. Örneğin, Fedrampa, NıST 800,171 (DIB), ıTAR, ıRS 1075, DoD L4 ve CJıS. Bu programların sertifikalarına yönelik olarak bir açıklama getirmek için, sertifikalarınızı tanımlayan en fazla 100 bağlantı sağlayabilirsiniz. Bunlar program üzerinde doğrudan veya kendi web sitenizde yer alan dökümlerinizin bağlantısı olabilir. Bu bağlantılar yalnızca Azure Kamu müşterileri tarafından görülebilir.

### <a name="plan-listing"></a>Planı listeleme

Bu sekme, aynı teklif içindeki her farklı plan için belirli bilgileri görüntüler.

### <a name="plan-name"></a>Plan adı

Bu, planınızı oluşturduğunuz sırada verdiğiniz adla önceden doldurulmuştur. Bu adı gerektiği şekilde değiştirebilirsiniz. En fazla 50 karakter uzunluğunda olabilir. Bu ad, Azure Marketi 'nde bu planın başlığı ve Azure portal görüntülenir. Plan kullanılmak üzere hazır olduktan sonra varsayılan modül adı olarak kullanılır.

### <a name="plan-summary"></a>Plan Özeti

Planınızın kısa bir özetini sağlayın (teklif değil). Bu Özet, Azure Marketi arama sonuçlarında görünür ve en fazla 100 karakter içerebilir.

### <a name="plan-description"></a>Plan açıklaması

Bu planın ne olduğunu ne yaptığını ve teklifinizdeki planlar arasındaki farkları betimleyebilirsiniz. Teklifi değil, yalnızca plan. Bu açıklama, Azure Marketi 'nde ve teklif listesi sayfasındaki Azure portal görüntülenir. Plan özetinde girdiğiniz içerik aynı olabilir ve en fazla 2.000 karakter içerebilir.

Bu alanları tamamladıktan sonra **Taslağı kaydet** ' i seçin.

#### <a name="plan-examples"></a>Plan örnekleri

Azure Market planı ayrıntılarına bir örnektir (listelenen tüm fiyatlar yalnızca örnek amaçlıdır ve gerçek maliyetlerin yansıtılmasından sorumlu değildir):

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="Azure Market planı ayrıntılarını gösterir.":::

#### <a name="call-out-descriptions"></a>Çağrı açıklamaları

1. Teklif adı
2. Plan adı
3. Plan açıklaması

<br>Aşağıda, Azure portal planı ayrıntılarına bir örnek verilmiştir (listelenen tüm fiyatlar yalnızca örnek amaçlıdır ve gerçek maliyetlerin yansıtılmasından sorumlu değildir):

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="Azure portal planı ayrıntılarını gösterir.":::

#### <a name="call-out-descriptions"></a>Çağrı açıklamaları

1. Teklif adı
2. Plan adı
3. Plan açıklaması

### <a name="availability"></a>Kullanılabilirlik

Müşterilerin Market 'te arayabilmesi, gözatmasını veya satın almasını sağlamak için yayınlanmış teklifinizi gizlemek istiyorsanız, kullanılabilirlik sekmesinde **planı Gizle** onay kutusunu seçin.

Bu alan genellikle şu durumlarda kullanılır:

- Teklif, başka bir uygulama olarak başvuruluyorsa yalnızca dolaylı olarak kullanılmak üzere tasarlanmıştır.
- Teklif ayrı olarak satın alınmamalıdır.
- Plan ilk test için kullanıldı ve artık ilgili değil.
- Plan, geçici veya dönemsel teklifler için kullanıldı ve artık sunulmamalıdır.

## <a name="technical-configuration"></a>Teknik yapılandırma

**IoT Edge modülü** teklif türü, bir IoT Edge cihazında çalışan belirli bir kapsayıcı türüdür. **Teknik yapılandırma** sekmesinde, [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)içinde kapsayıcı görüntü deponuz için başvuru bilgilerini ve müşterilerin modülü kolayca kullanmasına izin veren yapılandırma ayarlarını sağlarsınız.

Teklif yayımlandıktan sonra, IoT Edge kapsayıcı resminiz belirli bir ortak kapsayıcı kayıt defterinde Azure Marketi 'ne kopyalanır. Azure kullanıcılarından modülünüzü kullanmaya yönelik tüm istekler, özel kapsayıcı Kayıt defterinizden değil, Azure Marketi ortak kapsayıcı kayıt defterinden sunulur.

Birden çok platformu hedefleyebilir ve Etiketler kullanarak modül kapsayıcı resminizin çeşitli sürümlerini sağlayabilirsiniz. Etiketler ve sürüm oluşturma hakkında daha fazla bilgi için bkz. [IoT Edge modülünüzü hazırlama teknik varlıkları](create-iot-edge-module-asset.md).

### <a name="image-repository-details"></a>Görüntü deposu ayrıntıları

**Görüntü deposu ayrıntıları** sekmesinde aşağıdaki bilgileri sağlarız.

**Görüntü kaynağını seçin**: **Azure Container Registry** seçeneğini belirleyin.

**Azure ABONELIK kimliği**: kaynak kullanımının BILDIRILDIĞI abonelik kimliğini ve kapsayıcı görüntünüzü içeren Azure Container Registry için hizmetlerin faturalandırıldığını belirtin. Bu KIMLIĞI, Azure portal [abonelikler sayfasında](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) bulabilirsiniz.

**Azure Kaynak grubu adı**: kapsayıcı yansımanıza Azure Container Registry içeren [kaynak grubu](../../azure-resource-manager/management/manage-resource-groups-portal.md) adını sağlayın. Kaynak grubuna abonelik KIMLIĞINDE erişilebilir olması gerekir (yukarıda). Adı Azure portal [kaynak grupları](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) sayfasında bulabilirsiniz.

**Azure Container kayıt defteri adı**: kapsayıcı görüntünüzü içeren [Azure Container Registry](../../container-registry/container-registry-intro.md) adını belirtin. Kapsayıcı kayıt defterinin, daha önce belirttiğiniz Azure Kaynak grubunda mevcut olması gerekir. Tam oturum açma sunucusu adını değil yalnızca kayıt defteri adını sağlayın. Adından **azurecr.io** attığınızdan emin olun. Kayıt defteri adını Azure portal [kapsayıcı kayıt defterleri sayfasında](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) bulabilirsiniz.

**Azure Container Registry Için Yönetici Kullanıcı adı**: kapsayıcı yansımanıza sahip Azure Container Registry ilişkili [Yönetici Kullanıcı adı](../../container-registry/container-registry-authentication.md#admin-account). Şirketinizin kayıt defterine erişiminin olduğundan emin olmak için Kullanıcı adı ve parola gereklidir. Yönetici Kullanıcı adını ve parolasını almak için, Azure Command-Line arabirimi (CLı) kullanarak **yönetici özellikli** özelliği **true** olarak ayarlayın. İsteğe bağlı olarak, Azure portal **yönetici kullanıcıyı** **etkinleştirmek** için ayarlayabilirsiniz.

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="Kapsayıcı kayıt defterini Güncelleştir iletişim kutusunu gösterir.":::

#### <a name="call-out-description"></a>Çağrı açıklaması

1. Yönetici kullanıcı

<br>**Azure Container Registry parolası**: Azure Container Registry ilişkili Yönetici Kullanıcı adı için parola belirtin ve kapsayıcı görüntünüzü içerir. Şirketinizin kayıt defterine erişiminin olduğundan emin olmak için Kullanıcı adı ve parola gereklidir. Azure Portal parolayı **Container Registry**  >  **erişim anahtarlarına** giderek veya [göster komutunu](/cli/azure/acr/credential#az_acr_credential_show) kullanarak Azure CLI ile öğrenebilirsiniz.

:::image type="content" source="media/example-iot-access-keys.png" alt-text="Azure portal erişim tuşu ekranını gösterir.":::

#### <a name="call-out-descriptions"></a>Çağrı açıklamaları

1. Erişim tuşları
2. Kullanıcı adı
3. Parola

**Azure Container Registry Içinde Depo adı**. Yansımanıza sahip Azure Container Registry deposunun adını sağlayın. Görüntüyü kayıt defterine gönderdiğinizde deponun adını belirtirsiniz. Deponun adını [Container Registry](https://azure.microsoft.com/services/container-registry/)  >  **depoları sayfasına** giderek bulabilirsiniz. Daha fazla bilgi için bkz. [Azure Portal kapsayıcı kayıt defteri depolarını Görüntüleme](../../container-registry/container-registry-repositories.md). Ad ayarlandıktan sonra değiştirilemez. Hesabınızdaki her teklif için benzersiz bir ad kullanın.

> [!NOTE]
> Edge modülü sertifikası için şifrelenmiş Azure Container Registry desteklemiyoruz. Azure Container Registry şifreleme etkin olmadan oluşturulmalıdır.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Teklifinizin yeni sürümleri için görüntü etiketleri

Bir güncelleştirme yayımladığınızda müşterilerin Azure Marketi 'nden güncelleştirmeleri otomatik olarak alabilmesi gerekir. Güncelleştirmek istemlerse, görüntünüzün belirli bir sürümünde kalabilmeleri gerekir. Görüntüde her güncelleştirme yaptığınızda bunu yeni resim etiketleri ekleyerek yapabilirsiniz.

**Resim etiketi**. Bu alan, tüm desteklenen platformlarda görüntünüzün en son sürümüne işaret eden **en son** bir etiketi içermelidir. Ayrıca, sürüm etiketi de içermelidir (örneğin, xx. xx. xx ile başlayarak xx bir sayıdır). Müşteriler birden çok platformu hedeflemek için [bildirim etiketlerini](https://github.com/estesp/manifest-tool) kullanmalıdır. Bir bildirim etiketi tarafından başvurulan tüm Etiketler de karşıya yüklenebilmemiz için eklenmelidir. Tüm bildirim etiketleri (en son etiketi hariç), X. Y-veya X. Y. Z ile başlamalı; burada X, Y ve Z tamsayılardır. Örneğin, en son bir etiket 1.0.1-Linux-x64, 1.0.1-Linux-ARM32 ve 1.0.1-Windows-ARM32 ' y i gösteriyorsa, bu altı etiket bu alana eklenmelidir. Etiketler ve sürüm oluşturma hakkında daha fazla bilgi için bkz [. IoT Edge modülünüzü hazırlama teknik varlıkları.](create-iot-edge-module-asset.md)

### <a name="default-deployment-settings-optional"></a>Varsayılan dağıtım ayarları (isteğe bağlı)

IoT Edge modülünüzü dağıtmak için en yaygın ayarları tanımlayın. Bu varsayılan ayarlarla IoT Edge modülünüzü kullanıma hazır hale getirerek müşteri dağıtımlarını iyileştirin.

**Varsayılan yollar**. IoT Edge hub, modüller, IoT Hub ve cihazlar arasındaki iletişimi yönetir. Modüller ve IoT Hub arasında veri girişi ve çıkış için yollar ayarlayabilir ve bu, iletileri işlemek veya ek kod yazmak için ek hizmetlere gerek kalmadan gitmeleri gereken yerlere ileti gönderme esnekliği sağlar. Yollar ad/değer çiftleri kullanılarak oluşturulur. En fazla beş varsayılan yol adı tanımlayabilirsiniz, her biri 512 karakter uzunluğunda olabilir.

Yol değerindeki doğru [yol söz dizimini](../../iot-edge/module-composition.md#declare-routes)kullandığınızdan emin olun (genellikle from/Message/* $upstream olarak tanımlanmıştır). Diğer bir deyişle, herhangi bir modül tarafından gönderilen tüm iletiler IoT Hub gider. Modülize başvurmak için, varsayılan modül adını kullanın, bu ad, boşluk veya özel karakterler olmadan **teklif adınız** olacaktır. Henüz bilinen diğer modüllere başvurmak için, <FROM_MODULE_NAME> kuralını kullanarak müşterilerinizin bu bilgileri güncelleştirmesi gerektiğini bilmesini sağlayın. IoT Edge yolları hakkında daha fazla bilgi için bkz. [yolları bildirme](../../iot-edge/module-composition.md#declare-routes)).

Örneğin, ContosoModule, ContosoOutput 'da Contosoınput ve Output verilerinde giriş yapıyorsa, aşağıdaki iki varsayılan yolu tanımlamak mantıklı olur:

- Ad #1: ToContosoModule
- Değer #1:/messages/modules/<FROM_MODULE_NAME>/Outputs/* BrokeredEndpoint ("/modules/ContosoModule/inputs/ContosoInput")
- Ad #2: FromContosoModuleToCloud
- Değer #2:/messages/modules/ContonsoModule/outputs/ContosoOutput 'den $upstream

**Varsayılan modül ikizi istenen özellikleri**. Modül ikizi, istenen özellikler dahil olmak üzere bir modül örneği için durum bilgilerini depolayan IoT Hub JSON belgesidir. İstenen özellikler, modül yapılandırmalarını veya koşullarını eşitlemeye yönelik bildirilen özelliklerle birlikte kullanılır. Çözüm arka ucu istenen özellikleri ayarlayabilir ve modül bunları okuyabilir. Modül, istenen özelliklerde değişiklik bildirimleri de alabilir. İstenen özellikler en fazla beş ad/değer çifti kullanılarak oluşturulur ve her varsayılan değer 512 karakterden kısa olmalıdır. En fazla beş ad/değer ikizi istenen özellikleri tanımlayabilirsiniz. İkizi Desired özelliklerinin değerleri, en yüksek iç içe geçmiş dört düzey hiyerarşisine sahip diziler olmadan geçerli bir JSON, kaçışsız olmalıdır. Varsayılan değer için gereken bir parametrenin anlamlı olmadığı bir senaryoda (örneğin, bir müşterinin sunucusunun IP adresi), varsayılan değer olarak bir parametre ekleyebilirsiniz. İkizi istenen özellikler hakkında daha fazla bilgi edinmek için bkz. [istenen özellikleri tanımlama veya güncelleştirme](../../iot-edge/module-composition.md#define-or-update-desired-properties).

Örneğin, bir modül ikizi istenen özellikleri kullanarak dinamik olarak yapılandırılabilir yenileme oranını destekliyorsa, aşağıdaki varsayılan ikizi istenen özelliği tanımlamak mantıklı olur:

- Ad #1: RefreshRate
- Değer #1:60

**Varsayılan ortam değişkenleri**. Ortam değişkenleri, yapılandırma işlemine yardımcı olan bir modüle ek bilgiler sağlar. Ortam değişkenleri ad/değer çiftleri kullanılarak oluşturulur. Her varsayılan ortam değişkeni adı ve değeri 512 karakterden kısa olmalıdır ve en fazla beş karakter tanımlayabilirsiniz. Varsayılan bir değer için gereken bir parametre anlamlı olmadığında (örneğin, bir müşterinin sunucusunun IP adresi), varsayılan değer olarak bir parametre ekleyebilirsiniz.

Örneğin, bir modülün başlamadan önce kullanım koşullarını kabul etmesi gerekiyorsa, aşağıdaki ortam değişkenini tanımlayabilirsiniz:

- Ad #1: ACCEPT_EULA
- Değer #1: Y

**Varsayılan kapsayıcı oluşturma seçenekleri**. Kapsayıcı oluşturma seçenekleri IoT Edge modülü Docker kapsayıcısının oluşturulmasını doğrudan yönlendirir. IoT Edge Docker Engine API 'SI kapsayıcı oluşturma seçeneklerini destekler. [Liste kapsayıcılarındaki](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) tüm seçeneklere bakın. Oluşturma seçenekleri alanı geçerli bir JSON, kaçışsız ve 512 karakterden daha az olmalıdır.

Örneğin, bir modül bağlantı noktası bağlamayı gerektiriyorsa, aşağıdaki oluşturma seçeneklerini tanımlayın:

"HostConfig": {"PortBindings": {"5012/TCP": [{"HostPort": "5012"}]}

## <a name="review-and-publish"></a>İnceleme ve yayımlama

Teklifin tüm gerekli bölümlerini tamamladıktan sonra, bunu gözden geçirmek ve yayımlamak için gönderebilirsiniz.

Portalın sağ üst köşesinde, **gözden geçir ve Yayımla**' yı seçin.

İnceleme sayfasında yayımlama durumunu görebilirsiniz:

- Teklifin her bölümü için tamamlanma durumuna bakın. Teklifin tüm bölümleri Tamam olarak işaretlenene kadar yayımlayamazsınız.
    - **Başlatılmamış** -bölüm başlatılmamış ve tamamlanması gerekiyor.
    - **Tamamlanmamış** -bölümde düzeltilmesi gereken hatalar var veya daha fazla bilgi sağlamanız gerekiyor. Rehberlik için bu belgenin önceki bölümlerine bakın.
    - **Tamamlandı** -bölümde tüm gerekli veriler var ve hata yok. Teklifi gönderebilmeniz için teklifin tüm bölümlerinin tamamlanmış olması gerekir.
- Teklifinizin doğru şekilde test edilmesini sağlamak için sertifika ekibine test yönergeleri sağlayın. Ayrıca, teklifinizi anlamak için yararlı olan tüm ek notları sağlayın.

Teklifi yayımlamaya göndermek için **Yayımla**' yı seçin.

Teklifin bir önizleme sürümünün gözden geçirilmesi ve onaylanmak üzere kullanılabilir olduğunu size bildirmek için size bir e-posta göndereceğiz. Teklifinizi herkese açık bir şekilde yayımlamak için Iş Ortağı Merkezi 'ne gidin ve **Git-Live**' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Ticari Market 'te mevcut bir teklifi güncelleştirme](update-existing-offer.md)
