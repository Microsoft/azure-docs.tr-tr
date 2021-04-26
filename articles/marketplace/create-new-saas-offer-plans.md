---
title: Microsoft Iş Ortağı Merkezi 'nde SaaS teklifiniz için planlar oluşturma
description: Iş Ortağı Merkezi 'nde Microsoft ticari Market portalı 'nı kullanarak yeni bir hizmet olarak yazılım (SaaS) teklifi için planlar oluşturma.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: fb1514c146dcd352451597e6182c6d99ed8836d6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044728"
---
# <a name="how-to-create-plans-for-your-saas-offer"></a>SaaS teklifiniz için planlar oluşturma

Microsoft Commercial Market aracılığıyla satılan teklifler en az bir plana sahip olmalıdır. Aynı teklifde farklı seçeneklere sahip çeşitli planlar oluşturabilirsiniz. Bu planlar (bazen SKU 'Lar olarak adlandırılır) sürüm, para veya hizmet katmanları bakımından farklılık gösterebilir. Planlar hakkında ayrıntılı yönergeler için bkz. [ticari Market teklifleri Için planlar ve fiyatlandırma](plans-pricing.md).

> [!NOTE]
> İşlemleri bağımsız olarak işlemeyi seçerseniz, bu seçeneği görmezsiniz. Bunun yerine, [SaaS teklifinizi satma](create-new-saas-offer-marketing.md)bölümüne atlayın.

## <a name="create-a-plan"></a>Plan oluşturma

1. **Plana genel bakış** sekmesinin üst kısmındaki **+ Yeni plan oluştur**' u seçin.

1. Görüntülenen iletişim kutusunda, **plan kimliği** kutusuna benzersiz BIR plan kimliği girin. En fazla 50 küçük alfasayısal karakter, çizgi veya alt çizgi kullanın. **Oluştur**' u seçtikten sonra plan kimliğini değiştiremezsiniz.

1. **Plan adı** kutusuna bu plan için benzersiz bir ad girin. En fazla 50 karakter kullanın.

1. **Oluştur**’u seçin.

## <a name="define-the-plan-listing"></a>Plan listesini tanımlama

**Plan listeleme** sekmesinde, plan adı ve açıklamasını ticari Market 'te görünmesini istediğiniz şekilde tanımlayabilirsiniz.

1. **Plan adı** kutusunda, bu plan için daha önce verdiğiniz ad burada görünür. İstediğiniz zaman değiştirebilirsiniz. Bu ad, ticari Market 'te teklifinizin yazılım planının başlığı olarak görünür.

1. **Plan açıklaması** kutusunda, bu yazılım planının ne kadar benzersiz olduğunu ve teklifinizdeki diğer planlardan herhangi bir farkı olduğunu açıklayın. Bu açıklama en fazla 500 karakter içerebilir.
1. Sonraki sekmeye geçmeden önce **Taslağı kaydet** ' i seçin: **fiyatlandırma ve kullanılabilirlik**.

## <a name="define-markets-pricing-and-availability"></a>Pazarlar, fiyatlandırma ve kullanılabilirliği tanımlama

Her plan en az bir pazarda kullanılabilir olmalıdır. **Fiyatlandırma ve kullanılabilirlik** sekmesinde, bu planın kullanılabilir olacağı pazarları, istenen ödeme modeli, Fiyat ve faturalandırma şartlarını yapılandırabilirsiniz. Ayrıca, planın herkese veya yalnızca belirli müşterilere (özel bir plan olarak da bilinir) görünür hale getirmek isteyip istemediğinizi belirtebilirsiniz.

1. **Pazarlar**' ın altında, **pazarlar Düzenle** bağlantısını seçin.
1. Görüntülenen iletişim kutusunda, planınızı kullanılabilir hale getirmek istediğiniz Pazar konumlarını seçin. En az bir adet 141 pazarı seçmeniz gerekir.

   > [!NOTE]
   > Bu iletişim kutusu, Microsoft remits Sales ve sizin adınıza vergi üzerinden yalnızca "vergi havalesi" ülkelerine filtre uygulamak için bir arama kutusu ve bir seçenek içerir.

1. İletişim kutusunu kapatmak için **Kaydet**' i seçin.

## <a name="define-a-pricing-model"></a>Fiyatlandırma modeli tanımlama

Fiyatlandırma modelini her planla ilişkilendirmeniz gerekir: _sabit oran_ ya da _Kullanıcı başına_. Aynı teklifteki tüm planlar aynı fiyatlandırma modelini kullanmalıdır. Örneğin, bir teklifin sabit fiyat ve Kullanıcı başına başka bir plan olan bir plana sahip olması gerekir. Daha fazla bilgi için bkz. [SaaS fiyatlandırma modelleri](plan-saas-offer.md#saas-pricing-models).

> [!IMPORTANT]
> Teklifiniz yayımlandıktan sonra, fiyatlandırma modelini değiştiremezsiniz. Ayrıca, aynı teklif için tüm planlar aynı fiyatlandırma modelini paylaşmalıdır.

### <a name="configure-flat-rate-pricing"></a>Düz oran fiyatlandırmasını yapılandırma

1. **Fiyatlandırma ve kullanılabilirlik** sekmesindeki **fiyatlandırma** bölümünde **sabit fiyat**' ı seçin.
1. **Aylık** veya **yıllık** onay kutusunu ya da her ikisini birden seçin ve ardından fiyatı girin.

### <a name="add-a-custom-meter-dimension"></a>Özel ölçüm boyutu Ekle

Bu seçenek yalnızca sabit oran fiyatlandırması ' nı seçtiğinizde kullanılabilir. Daha fazla bilgi için bkz. [ticari Market ölçüm hizmeti kullanılarak SaaS Için tarifeli faturalandırma](./partner-center-portal/saas-metered-billing.md).

1. **Market ölçüm hizmeti boyutları** altında **özel ölçüm boyutu Ekle (en fazla 30)** bağlantısını seçin.
1. **Kimlik** kutusuna, kullanım olaylarını yayırken sabit tanımlayıcı başvurusunu girin.
1. **Görünen ad** kutusuna boyutla ilişkili görünen adı girin. Örneğin, "gönderilen metin iletileri".
1. **Ölçü birimi** kutusuna faturalandırma biriminin açıklamasını girin. Örneğin, "metin başına ileti" veya "100 başına e-posta".
1. **USD cinsinden birim başına fiyat** kutusunda, boyutun bir biriminin fiyatını girin.
1. Temel kutuya **dahil edilen aylık miktar** ' da, yinelenen aylık ücreti ödeyen müşteriler için her ay dahil edilen boyutun miktarını (tamsayı olarak) girin. Sınırsız bir miktar ayarlamak için bunun yerine onay kutusunu işaretleyin.
1. Taban kutusuna **eklenen yıllık miktar** ' da, yinelenen yıllık ücreti ödeyen müşteriler için her ay dahil edilen boyutun miktarını (tamsayı olarak) girin. Sınırsız bir miktar ayarlamak için bunun yerine onay kutusunu işaretleyin.
1. Başka bir özel ölçüm boyutu eklemek için **başka bir boyut ekle** bağlantısını seçin ve ardından 1 ile 7 arasındaki adımları yineleyin.

### <a name="configure-per-user-pricing"></a>Kullanıcı başına fiyatlandırmayı yapılandırma

1. **Fiyatlandırma ve kullanılabilirlik** sekmesinde, **fiyatlandırma** altında **Kullanıcı başına**' yı seçin.
2. Uygulanabiliyorsa, **Kullanıcı sınırları** altında bu plan için en düşük ve en fazla kullanıcı sayısını belirtin.
3. **Fatura dönemi** altında, aylık fiyat, yıllık fiyat veya her ikisini de belirtin.

### <a name="validate-custom-prices"></a>Özel fiyatları doğrulama

Tek bir pazarda özel fiyatlar ayarlamak için, fiyatlandırma elektronik tablosunu dışarı aktarın, değiştirin ve içeri aktarın. Bu fiyatlandırmayı doğrulamadan ve bu ayarların sahibi olduğunuzdan siz sorumlusunuz. Ayrıntılı bilgi için bkz. [özel fiyatlar](plans-pricing.md#custom-prices).

1. Fiyatlandırma verilerinin dışarı aktarılmasını sağlamak için öncelikle fiyatlandırma değişikliklerinizi kaydetmeniz gerekir. **Fiyatlandırma ve kullanılabilirlik** sekmesinin altındaki **Taslağı kaydet**' i seçin.
1. **Fiyatlandırma** bölümünde **fiyatlandırma verilerini dışarı aktar** bağlantısını seçin.
1. exportedPrice.xlsx dosyasını Microsoft Excel 'de açın.
1. Elektronik tabloda, istediğiniz güncelleştirmeleri Fiyatlandırma bilgileriniz yapın ve ardından öğesini kaydedin. CSV dosyası.<br> Dosyayı güncelleştirebilmeniz için önce Excel 'de düzenlemesini etkinleştirmeniz gerekebilir.
2. Fiyatlandırma **ve kullanılabilirlik** sekmesindeki **fiyatlandırma** bölümünde **fiyatlandırma verilerini içeri aktar** bağlantısını seçin.
3. Görüntülenen iletişim kutusunda **Evet**' e tıklayın.
4. Güncelleştirdiğiniz exportedPrice.xlsx dosyasını seçin ve ardından **Aç**' a tıklayın.

### <a name="enable-a-free-trial"></a>Ücretsiz denemeyi etkinleştirin

Teklifinizdeki her plan için ücretsiz bir deneme yapılandırabilirsiniz. Bir aylık ücretsiz denemeye izin vermek için onay kutusunu işaretleyin. Bu onay kutusu Market ölçüm hizmetini kullanan planlar için kullanılamaz. Daha fazla bilgi için bkz. [ücretsiz deneme](plans-pricing.md#free-trials).

> [!IMPORTANT]
> Transactable teklifiniz ücretsiz bir deneme ile yayımlandıktan sonra, bu plan için devre dışı bırakılamaz. Planı yeniden oluşturmak zorunda kalmamak için teklifi yayımlamadan önce bu ayarın doğru olduğundan emin olun.

- **Ücretsiz deneme** bölümünde, **tek aylık ücretsiz denemeye izin ver** onay kutusunu seçin.

## <a name="choose-who-can-see-your-plan"></a>Planınızı kimlerin görebileceğini seçin

Her planı herkese veya yalnızca belirli bir hedef kitleye görünür olacak şekilde yapılandırabilirsiniz. Bir özel plana, atadığınız her kiracı KIMLIĞININ açıklamasını ekleme seçeneğiyle, kiracı kimliklerini kullanarak erişim izni verirsiniz. Bir kullanarak en fazla 10 Kiracı kimliği el ile veya 20.000 Kiracı kimliği ekleyebilirsiniz. CSV dosyası.

> [!NOTE]
> Özel bir plan yayımlarsanız, görünürlüğünü daha sonra herkese açık olarak değiştirebilirsiniz. Ancak, bir genel planı yayımladıktan sonra görünürlüğünü özel olarak değiştiremezsiniz.

### <a name="make-your-plan-public"></a>Planınızı genel yapın

1. **Plan görünürlüğü** altında **ortak** kutusunu seçin.
1. **Taslağı kaydet**' i seçin ve ardından sekmenin sol üst kısmında plana genel **bakış** ' ı seçerek **plana genel** bakış sekmesine dönün.
1. Bu teklif için, **plana genel bakış** sekmesinin üst kısmına yakın bir plan oluşturmak için **+ Yeni plan oluştur**' u seçin. Ardından [plan oluşturma](#create-a-plan) bölümündeki adımları tekrarlayın. Aksi takdirde, [planlarınızı görüntüleyin](#view-your-plans)sayfasına gidin.

### <a name="manually-add-tenant-ids-for-a-private-plan"></a>Özel bir plan için kiracı kimliklerini el ile ekleme 

1. **Plan görünürlüğü**' nın altında, **Bu özel bir plan** kutusunu seçin.
1. Görüntülenen **KIRACı kimliği** kutusunda, bu özel plana erişim vermek istediğiniz hedef kitlesinin Azure AD Kiracı kimliğini girin. En az bir kiracı KIMLIĞI gereklidir.
1. Seçim **Açıklama** kutusuna bu hedef kitle için bir açıklama girin.
1. Başka bir kiracı KIMLIĞI eklemek için adım 2 ve 3 ' ü tekrarlayın.
1. Kiracı kimlikleri ekleme işiniz bittiğinde, **Taslağı kaydet**' i seçin ve ardından sekmenin sol üst kısmında plana genel **bakış** ' ı seçerek **plana genel** bakış sekmesine dönün.
1. Bu teklif için, **plana genel bakış** sekmesinin üst kısmına yakın bir plan oluşturmak için **+ Yeni plan oluştur**' u seçin. Ardından [plan oluşturma](#create-a-plan) bölümündeki adımları tekrarlayın. Aksi takdirde, [planlarınızı görüntüleyin](#view-your-plans)sayfasına gidin.

### <a name="use-a-csv-file-for-a-private-plan"></a>Bir kullanın. Özel bir plan için CSV dosyası

1. **Plan görünürlüğü**' nın altında, **Bu özel bir plan** kutusunu seçin.
2. **Izleyiciyi dışarı aktar (CSV)** bağlantısını seçin.
3. Öğesini açın. CSV dosyası ve özel teklifin **kimlik** sütununa erişim izni vermek Istediğiniz Azure kimliklerini ekleyin.
4. İsteğe bağlı olarak, **Açıklama** sütunundaki her bir hedef kitle için bir açıklama girin.
5. Bir Azure KIMLIĞI olan her satır için **tür** sütununa "tenantıd" ekleyin.
6. Öğesini kaydedin. CSV dosyası.
7. **Fiyatlandırma ve kullanılabilirlik** sekmesindeki **plan görünürlüğü** altında, **izleyiciyi içeri aktar (CSV)** bağlantısını seçin.
8. Görüntülenen iletişim kutusunda **Evet**' i seçin.
9. Öğesini seçin. CSV dosyası ve sonra **Aç**' ı seçin.
10. **Taslağı kaydet**' i seçin ve ardından sekmenin sol üst kısmında plana genel **bakış** ' ı seçerek **plana genel** bakış sekmesine dönün.
11. Bu teklif için başka bir plan oluşturmak üzere **plana genel bakış** sekmesinin en üstünde **+ Yeni plan oluştur**' u seçin. Ardından [plan oluşturma](#create-a-plan) bölümündeki adımları tekrarlayın. Aksi takdirde, planlar oluşturmayı bitirdiğinizde sonraki bölüme gidin: **planlarınızı görüntüleyin**.

## <a name="view-your-plans"></a>Planlarınızı görüntüleyin

Bir veya daha fazla plan oluşturduktan sonra plan adı, plan KIMLIĞI, fiyatlandırma modeli, kullanılabilirlik (genel veya özel), geçerli yayımlama durumu ve **plana genel bakış** sekmesinde tüm kullanılabilir eylemler görüntülenir.

**Plana genel bakış** sekmesinin **eylem** sütununda bulunan Eylemler planınızın durumuna göre farklılık gösterir ve şunları içerebilir:

- Plan durumu **taslak** Ise, **eylem** sütunundaki bağlantı **taslak Sil**' i söylecektir.
- Plan durumu **canlı** Ise, Eylem sütunundaki bağlantı, satışı veya **özel Izleyiciyi eşitleme** **işlemini** **durdurur** . **Eşitleme özel hedef kitle** bağlantısı, yalnızca özel izleyicilerinizdeki değişiklikleri, teklifte yapmış olabileceğiniz herhangi bir güncelleştirmeyi yayımlamadan yayımlar.

## <a name="before-you-publish-your-offer"></a>Teklifinizi yayımlamadan önce

Henüz yapmadıysanız, üretim teklifinizi canlı yayımlamadan önce teklifinizi test etmek için bir geliştirme ve test (DEV) teklifi oluşturun. Daha fazla bilgi için bkz. [geliştirme ve test teklifi oluşturma](create-saas-dev-test-offer.md).

## <a name="next-steps"></a>Sonraki adımlar

- **Microsoft Ile ortak satış** aracılığıyla [SaaS teklifinizi satma](create-new-saas-offer-marketing.md) ve **CSP programlarını yeniden satma** hakkında bilgi edinin.
- [Ticari Market 'e SaaS teklifini test etme ve yayımlama](test-publish-saas-offer.md).
