---
title: Microsoft Commercial Market 'e yönelik bir teklifi İnceleme ve yayımlama
description: Teklifinizi önizlemek için Iş Ortağı Merkezi 'ni kullanın, teklifinizin önizlemesini yapın ve Microsoft ticari Market ' te yayımlayın.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: f8ddcbef6d2ce4a9cba625374db9908335954424
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102630903"
---
# <a name="how-to-review-and-publish-an-offer-to-the-commercial-marketplace"></a>Ticari Market 'e yönelik bir teklifi İnceleme ve yayımlama

Bu makalede, Iş Ortağı Merkezi 'ni kullanarak kendi teklifinizi yayınlama, teklifinizin önizlemesini oluşturma ve ardından ticari Market 'te yayımlama işlemlerinin nasıl yapılacağı gösterilmektedir. Yayımlama adımlarında ilerlerken yayımlama durumunuzu nasıl denetliyoruz de ele alınmaktadır. Yayımlamak istediğiniz bir teklifi önceden oluşturmuş olmanız gerekir.

## <a name="offer-status"></a>Teklif durumu

Teklif durumunuzu, [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)' nde ticari Market panosunun **genel bakış** sekmesinde gözden geçirebilirsiniz. Her teklifin **durum** sütununda aşağıdaki durum göstergelerden biri görüntülenir.

| Durum | Açıklama |
| ------------ | ------------- |
| Taslak | Teklif oluşturuldu ancak yayımlanmadı. |
| Yayımlama devam ediyor | Teklif, yayımlama sürecinde bu şekilde çalışır. |
| İlgilenilmesi gerekiyor | Sertifika sırasında veya başka bir yayımlama aşamasında kritik bir sorun tespit ettik. |
| Önizleme | Artık, yayımcı tarafından nihai bir doğrulamayı beklemiş olan teklifi sertifikalandırdık. Teklifi canlı yayımlamak için **canlı git** ' i seçin. |
| Canlı | Teklif, Market 'te canlı olur ve müşteriler tarafından görülebilir ve elde edilebilir. |
| Satışı durdur bekleniyor | Yayımcı bir teklif veya planda "satışı Durdur" seçeneğini seçti, ancak eylem henüz tamamlanmadı. |
| Market 'te yok | Market 'te daha önce yayınlanmış bir teklif kaldırılmıştır. |
|||

## <a name="validation-and-publishing-steps"></a>Doğrulama ve yayımlama adımları

Yayımlamaya yönelik bir teklif göndermeye hazırsanız, portalın sağ üst köşesinde **gözden geçir ve Yayımla** ' yı seçin. **İnceleme ve yayımlama** sayfasında, teklifiniz için her sayfanın durumu gösterilir ve bu, aşağıdakilerden biri olabilir:

   - **Başlatılmamış** – sayfa tamamlanmadı.
   - **Tamamlanmamış** – sayfada gerekli bilgiler eksik veya düzeltilmesi gereken hatalar var. Sayfaya dönüp güncelleştirmeniz gerekir.
   - **Tamam** – sayfa tamamlanmıştır. Gerekli tüm veriler sağlanmış ve hata yok.

Sayfaların herhangi birinde **tamamlanmamış** bir durum varsa, söz konusu sayfadaki sorunu düzeltmeniz ve sonra durumu şimdi **Tamam** olarak gösterir durumunu onaylamak için **İnceleme ve yayımlama** sayfasına geri dönmeniz gerekir. Bazı teklif türleri test gerektirir. Bu durumda, sertifika ekibine yönelik test yönergeleri ve uygulamanızı anlamak için yararlı olan tüm ek notlar sağlamanız gereken **sertifika alanı için bir not** görürsünüz.

Tüm sayfalar tamamlandıktan ve ilgili test notlarını girdikten sonra, doğrulama ve yayımlama işlemlerine başlamak için **Yayımla** ' yı seçin. Aşamalar ve genel sıra, yayımladığınız teklifin türüne bağlı olarak farklılık gösterebilir. Aşağıdaki tabloda, olası bir yayımlama akışı gösterilmektedir. Her aşama aşağıdaki bölümlerde daha ayrıntılı olarak açıklanmıştır.

| Aşama | Ne olur? |
| ------------ | ------------- | ------------- |
| [Otomatik doğrulama](#automated-validation-phase) | Otomatik doğrulama kümesini işliyoruz. |
| [Sertifikasyon](#certification-phase) | El ile doğrulama yaptık. |
| [Önizleme oluşturma](#preview-creation-phase) | Teklif önizlemeniz için liste sayfası önizleme bağlantısına sahip olan herkes tarafından kullanılabilir. Teklifiniz Microsoft (transactable) üzerinden satılıyorsa, yalnızca teklifinizin **ön izleme İzleyicisi** sayfasında belirttiğiniz hedef kitle, test için teklif satın alabilir ve bu teklifi erişebilir. |
| [Yayımcı oturumu kapatma](#publisher-sign-off-phase) | Teklifinizi önizlemenizi ve onaylamanıza yönelik bir istek içeren bir e-posta göndereceğiz. |
| [Yayımla](#publish-phase) | Önizleme teklifinin ticari Market 'e canlı olarak yayımlandığını doğrulamak için bir dizi adımı çalıştırdık. |
|||

## <a name="automated-validation-phase"></a>Otomatik doğrulama aşaması

Yayımlama işlemindeki ilk adım bir otomatik doğrulama kümesidir. Her doğrulama adımı, teklifi oluştururken seçtiğiniz bir özelliğe karşılık gelir. Teklifin yayımlama işlemindeki bir sonraki adıma geçebilmesi için her doğrulama denetiminin tamamlanmış olması gerekir.

- **Teklif satın alma akışı kurulumu** (<10 dk)

   Teklifinizin, Azure portal aracılığıyla müşteriler tarafından satın alındığında karşılanabileceği güvence altına alınır. Bu adım yalnızca Microsoft ile satılan teklifler için geçerlidir.

- **Sınama sürücüsü veri doğrulaması** (~ 5 dakika)

   Teklifin teknik yapılandırma sayfasında verdiğiniz verileri doğrulamamız gerekir. Test sürücüsü işlevlerini test etme ve onaylama. Bu adım yalnızca bir test sürücüsü etkin olan teklifler için geçerlidir.

- **Sınama sürücüsü sağlama** (~ 30 dakika)

    Önceki adımda test sürücünüzün verileri ve işlevlerini doğruladıktan sonra, test sürücünüzün örneklerini dağıtıp çoğaltarak müşteri kullanımına hazırlarız. Bu adım yalnızca bir test sürücüsü etkin olan teklifler için geçerlidir.

- **Müşteri adayı yönetimi doğrulama ve kayıt** (<15 dk)

    Müşteri adayı yönetim sisteminizin müşteri adaylarını, teklif Kurulumu sayfasında verdiğiniz ayrıntılara göre **alacağından** emin veriyoruz. Bu adım yalnızca, lider yönetimi etkinleştirilmiş teklifler için geçerlidir.

- **Teklif doğrulaması** (<30 dakika)

    Yalnızca SaaS teklifleri için içerik ve teknik yapılandırmalara yönelik hızlı doğrulamalar gerçekleştiririz. Bu adımda algılanan doğrulama sorunlarında Önizleme oluşturma engellenmeyecektir. Sertifikalamamasına neden olan herhangi bir sorun bulduk, **teklif genel bakış** sayfasında bir **Görünüm doğrulama raporu** bağlantısı görüntülenir. Rapor, herhangi bir ilke ihlallerinin açıklamalarını içerir.

    > [!IMPORTANT]
    > **Canlı çalış** düğmesini seçmeden önce teklif doğrulama adımındaki tüm uyarıları gözden geçirip adreslediğinizden emin olun. Aksi takdirde, teklifinizin canlı olmaya devam edecek şekilde hata yaratması riski vardır.

## <a name="certification-phase"></a>Sertifika aşaması

Ticari Market 'e gönderilen tekliflerin yayımlanmadan önce sertifikalı olması gerekir. Daha ayrıntılı bir test, bazı otomatikleştirilmiş ve diğerleri el ile daha kapsamlı testler sunar. Daha fazla bilgi için bkz. [ticari Market sertifika ilkeleri](/legal/marketplace/certification-policies).

### <a name="types-of-validation-that-take-place-during-certification"></a>Sertifika sırasında gerçekleşen doğrulama türleri

Gönderilen her teklif için sertifika işlemine dahil edilen üç doğrulama düzeyi vardır.

- Yayımcı iş uygunluğu
- İçerik doğrulama
- Teknik doğrulama

#### <a name="publisher-business-eligibility"></a>Yayımcı iş uygunluğu

Her teklif türü, gerekli bir temel uygunluk ölçütü kümesini denetler. Bu ölçütler, yayımcı MPN durumu, Uzmanlıklar, uzmanlık düzeyleri vb. içerebilir.

#### <a name="content-validation"></a>İçerik doğrulama

Teklifinizi oluştururken girilen bilgiler kalite ve ilgi açısından denetlenir. Bu denetimler Market listeleme ayrıntıları, fiyatlandırma, kullanılabilirlik, ilişkili planlar vb. için girdilerinizi gözden geçirir. Microsoft AppSource ve Azure Marketi 'nin liste ölçütlerini karşılamak için teklifinizin şunları içerdiğini doğrulayacağız:

- Teklifi doğru şekilde açıklayan bir başlık
- Kapsamlı bir genel bakış ve değer teklifi sağlayan iyi yazılmış açıklamalar
- Kaliteli ekran görüntüleri ve videolar
- Teklifin Microsoft platformlarını ve araçlarını nasıl kullandığını gösteren bir açıklama.

[Genel listeleme ilkelerini](/legal/marketplace/certification-policies#100-general)okuyarak içerik doğrulama ölçütleriyle ilgili daha fazla bilgi edinin.

#### <a name="technical-validation"></a>Teknik doğrulama

Teknik doğrulama sırasında, teklif (paket veya ikili) aşağıdaki denetimleri altına girer.

- Kötü amaçlı yazılım için tarandı
- İzlenen ağ çağrıları
- Paket çözümlendi
- Teklifin işlevselliğinin tam olarak taranması

Teklif, güçlü olduğundan emin olmak için çeşitli platformlar ve sürümler arasında test edilir.

### <a name="certification-failure-report"></a>Sertifika hata raporu

Teklifiniz herhangi bir liste, teknik veya ilke denetimi başarısız olursa veya bu tür bir teklif göndermeye uygun değilse, size bir sertifika hatası raporu göndereceğiz.

Bu rapor, gözden geçirme notlarıyla birlikte başarısız olan ilkelerin açıklamalarını içerir. Bu e-posta raporunu gözden geçirin, herhangi bir sorunu çözün, sizin teklifinizde gereken güncelleştirmeleri yapın ve Iş Ortağı Merkezi ' nde [ticari Market portalı](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) 'nı kullanarak teklifi yeniden gönderin. Sertifikayı geçirmeden önce teklifi gerektiği kadar yeniden gönderebilirsiniz.

## <a name="preview-creation-phase"></a>Önizleme oluşturma aşaması

Önizleme oluşturma aşamasında, teklifinizin yalnızca teklifinizin **ön izleme İzleyicisi** sayfasında belirttiğiniz kitlelerin erişebileceği bir sürümünü oluşturacağız. Teklifinizi canlı olarak yayımlaana kadar, teklifinizin önizleme sürümü, önizleme hedef kitlesi dışındaki herkese açık olmayacaktır.

> [!NOTE]
> Kuruluşunuzun dışındaki kişilere bir teklifle görünürlük sağlamak için Önizleme kitleyi kullanmayın. Bunun yerine özel teklif seçeneğini kullanın. Bu noktada, teklifiniz tam olarak sınanmamıştır ve doğrulanmaz ve dağıtım dışı için hazırlanma.

## <a name="cancel-publishing"></a>Yayımlamayı iptal et

**Yayımlama sürüyor** durumunda olan bir teklifi iptal etmek için:

1. Teklif **genel bakış** sayfasını açmak için teklif adını seçin.
1. Sayfanın sağ üst köşesinden **yayımlamayı Iptal et** ' i seçin.
1. Teklifin yayımlanmasını durdurmak istediğinizi onaylayın.

Teklifi daha sonra yayınlamak istiyorsanız, Yayımlama sürecini üzerinde başlatmanız gerekir.

> [!NOTE]
> Bir teklifin yayımlanmasını, yalnızca teklif yayımcı oturumu kapatma adımına ilerlemedi. **Canlı git**' i seçtikten sonra, yayımlamayı daha uzun süre iptal etme seçeneğiniz olmayacaktır.

## <a name="publisher-sign-off-phase"></a>Yayımcı oturum kapatma aşaması

Teklif gözden geçirmeniz ve oturumunuz devre dışı bırakıldığında size, teklif önizlemeinizi gözden geçirip onaylamanıza yönelik bir e-posta göndereceğiz. Teklifinizin Yayımcı oturum açma aşamasına ulaşmış olup olmadığını görmek için tarayıcınızda **teklif genel bakış** sayfasını da yenileyebilirsiniz. Varsa, **canlı çalış** düğmesi ve önizleme bağlantıları kullanılabilir hale gelir.

Aşağıdaki ekran görüntüsünde, Iş Ortağı Merkezi 'nde ticari Market programında **teklif genel bakış** sayfası gösterilmektedir. Bu sayfada göreceğiniz doğrulama adımları, teklif türüne ve teklifi oluştururken yaptığınız seçimlere bağlı olarak değişir. Sertifika aşamasının, teklif türüne bağlı olarak önizleme veya canlı gönderim sırasında gerçekleşebileceğini unutmayın.

![Iş Ortağı Merkezi 'nde teklif için teklif genel bakış sayfasını gösterir. Canlı çalış düğmesi ve önizleme bağlantıları gösterilir.](./media/publish-status-publisher-signoff.png)

### <a name="previewing-and-approving-your-offer"></a>Teklifinizi önizleme ve onaylama

> [!IMPORTANT]
> Uçtan uca satın alma ve kurulum akışını doğrulamak için, önizleme aşamasında olan teklifinizi satın alın. Bir ücreti işlememenizi sağlamak için öncelikle Microsoft 'un bir [destek bileti](https://aka.ms/marketplacesupport) olduğunu bildirin. Teklifiniz bir _kişi benimle ilgili_ ise, önizleme sırasında ilgili kişi ayrıntılarını sağlayarak bir müşteri adayının beklenen şekilde oluşturulduğunu test edin.

**Teklif genel bakış** sayfasında, **canlı çalış** düğmesinin altında Önizleme bağlantıları görüntülenir. Teklifinizi oluştururken seçtiğiniz seçeneklere bağlı olarak, AppSource Preview, Azure Marketi önizlemesi veya her ikisinin de bir bağlantısı olacaktır. Teklifinizi Microsoft aracılığıyla satmayı seçerseniz, önizleme izleyicisine eklenmiş olan herkes, bu aşamada gereksinimlerinizi karşıladığından emin olmak için teklifinizin Alım ve dağıtımını test edebilir.

> [!IMPORTANT]
> **Canlı çalış** düğmesini seçmeden önce _teklif doğrulama_ adımındaki tüm uyarıları gözden geçirip adreslediğinizden emin olun. Aksi takdirde, teklifinizin canlı olmaya devam edecek şekilde hata yaratması riski vardır.

Önizlemeyi onayladıktan sonra, teklifinizi ticari Market 'te canlı yayımlamak için **canlı ol** ' u seçin. 

Teklifin önizlemesini yaptıktan sonra değişiklik yapmak istiyorsanız, yayın isteğinizi düzenleyebilir ve yeniden gönderebilirsiniz. Teklifiniz zaten canlı ve Market 'te herkese açık ise, yaptığınız güncelleştirmeler **canlı git*' i seçene kadar canlı olmaz. Daha fazla bilgi için bkz [. ticari Market 'te mevcut teklifi güncelleştirme](partner-center-portal/update-existing-offer.md)

## <a name="publish-phase"></a>Yayımlama aşaması

Teklifinizle canlı yapmayı seçtiniz ve bu, ticari Market 'te kullanılabilir hale getirir. canlı teklifin yalnızca teklifin önizleme sürümü gibi yapılandırıldığından emin olmak için bir dizi son doğrulama denetimi gerçekleştirdik.

- **Teklif satın alma akışı kurulumu** (>10 dk)

    Teklifinizin, Azure portal aracılığıyla müşteriler tarafından satın alındığında karşılanabileceği güvence altına alınır. Bu adım yalnızca Microsoft ile satılan teklifler için geçerlidir.

- **Sınama sürücüsü veri doğrulaması** (~ 5 dakika)

    Teklifin teknik yapılandırma sayfasında verdiğiniz verileri doğrulamamız gerekir. Test sürücüsü işlevlerini test etme ve onaylama. Bu adım yalnızca bir test sürücüsü etkin olan teklifler için geçerlidir.

- **Sınama sürücüsü sağlama** (~ 30 dakika)

    Test sürücünüzün örneklerini dağıtıp çoğalttık ve bu sayede müşteri kullanımı için hazırlanın. Bu adım yalnızca bir test sürücüsü etkin olan teklifler için geçerlidir.

- **Müşteri adayı yönetimi doğrulama ve kayıt** (>15 dk)

    Müşteri adaylarını teklif kurulum sayfanızda sunulan ayrıntılara göre müşteri adaylarını **alacağından** emin veriyoruz. Bu adım yalnızca, lider yönetimi etkinleştirilmiş teklifler için geçerlidir.

- **Son yayımlama (>30 dakika)**

    Teklifinizin Market 'te herkese açık hale geldiğini güvence altına aldığınızdan emin veriyoruz.

Bu doğrulama denetimleri tamamlandıktan sonra teklifiniz Market 'te canlı olacaktır.

## <a name="publishing-history"></a>Yayımlama geçmişi

Iş ortağı merkezindeki **Geçmiş** sayfası, ticari Market tekliflerinizin yayımlama olaylarını gösterir. Her olay için, sayfada eylemi başlatan kullanıcı, olay türü ve olay tarih ve saati görüntülenir. [Doğrulama ve yayımlama adımları](#validation-and-publishing-steps) tamamlanma tarihi ve saati ile listelenir.

Teklifinizin geçmişini görüntülemek için:

1.    [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/home)' nde oturum açın.
2.    Sol taraftaki gezinti menüsünde **ticari Market**  >  **genel bakış**' ı seçin.
3.    Teklifinizi seçin.
4.    Sol taraftaki gezinti menüsünde **Geçmiş** sekmesini seçin. Bu sayfa, teklifiniz için geçerli olan aşağıdaki olayların ayrıntılarını sağlar:

|Olay    |Description    |
|---------|---------------|
|Teklif oluşturuldu    |Teklif Iş Ortağı Merkezi 'nde oluşturulmuştur. Bir Kullanıcı, **ticari Market**  >  **'e genel bakış** bölümünde teklif türü, teklif kimliği ve teklif diğer adını seçti.    |
|Plan oluşturuldu: *Plan adı*    |Kullanıcı plana **genel bakış** SEKMESINDE plan kimliği ve plan adı girerek yeni bir plan oluşturdu.</br>*Bu olay yalnızca planları destekleyen teklif türleri için geçerlidir*.    |
|Plan silindi    |Kullanıcı **plana genel bakış** sayfasında **taslağı Sil** ' i seçerek yayımlanmamış bir taslak planı sildi.</br>*Bu olay yalnızca planları destekleyen teklif türleri için geçerlidir*.    |
|Başlatılan plan satışı Durdur: *Plan adı*    |Kullanıcı **plana genel bakış** sayfasından **satışı durdur** ' ı seçerek bir planı başlattı.</br>*Bu olay yalnızca planları destekleyen teklif türleri için geçerlidir*.    |
|Plan durdur satışı al: *Plan adı*    |Kullanıcı **plana genel bakış** sayfasında **satışı durdur** ' ı seçerek bir planı durdur-satışı iptal etti.</br>*Bu olay yalnızca planları destekleyen teklif türleri için geçerlidir*.    |
|Önizlemeye gönderilen teklif    |Bir Kullanıcı, **Gözden geçirme ve yayımlama** sayfasından **Yayımla** ' yı seçerek, teklifi önizlemeye gönderdi.    |
|Önizleme iptaline gönderim başlatıldı    |Bir Kullanıcı, önizlemeye gönderim sonrasında **teklif genel bakış** sayfasında **yayımlamayı iptal et** ' i seçerek önizleme için teklif yayınını iptal etmek istedi.</br>*Bu olay, iptal isteği işlendiği için görüntülenir*.    |
|Önizlemeye gönderim iptal edildi    |Kullanıcı, önizlemeye gönderim sonrasında **teklif genel bakış** sayfasında **yayımlamayı iptal et** seçeneğini belirleyerek teklif yayınını önizlemek üzere iptal etti.</br>*Bu olay, iptal isteği başarıyla işlendikten sonra görüntülenir*.    |
|Canlı gitmek için oturum kapatma    |**Teklif genel bakış** sayfasından **canlı ol** ' ı seçerek bir Kullanıcı teklifi ticari Market 'e yayımladı.    |
|Market 'te yayımlama iptali başlatıldı    |Oturum kapattıktan sonra, bir Kullanıcı, teklif **genel bakış** sayfasında **yayımlamayı iptal et** ' i seçerek teklif yayınını iptal etmek istedi.</br>*Bu olay, iptal isteği işlendiği için görüntülenir*.    |
|Ticari Market 'te yayımlamayı iptal etti    |Bir Kullanıcı, oturum açma işleminden sonra, canlı çalışmaya başladıktan sonra **teklif genel bakış** sayfasında **yayımlamayı iptal et** ' i seçerek teklif yayınını iptal etti.</br>*Bu olay, iptal isteği başarıyla işlendikten sonra görüntülenir*.    |
|Özel izleyiciyi Eşitle    |Bir Kullanıcı **plana genel bakış** sayfasında veya **fiyatlandırma & kullanılabilirliği** sayfasında özel izleyiciyi **Eşitle** seçeneğini belirleyerek özel izleyiciyi güncelleştirmiş ve eşitledi.</br>*Bu olay yalnızca özel planları destekleyen teklif türleri için geçerlidir*.    |
|Satış teklifini durdur    |Bir kullanıcı teklif **genel bakış** sayfasından satışı Durdur ' ı seçerek teklifi **satmaya** durdurdu.    |

> [!NOTE]
> Bir teklif taslağı kaydedildiğinde geçmiş sayfası söydeğildir.

### <a name="filter-options"></a>Filtre seçenekleri

Belirli yayımlama olaylarına teklifinizin tam geçmişini daraltmak için filtreleri kullanabilirsiniz:

1.    Sayfanın sağ üst köşesinde bulunan filtre düğmesini seçin.
2.    Bir filtre seçin ve seçtiğiniz ölçütlere karşılık gelen geçmiş olaylarını görmek için **Uygula** ' yı seçin.
3.    Teklifinizin tam geçmişine geri dönmek için **filtreleri temizle** ' yi seçin.

Dört filtre vardır:
* Ekinlikler
* Kullanıcılar
* Tarih
* Sayfalar

**Sayfalar** filtresini seçtiğinizde, teklif türü için geçerli olan Iş Ortağı Merkezi sayfalarından herhangi birini seçebilirsiniz. Uygulandığında **, sayfa filtresi,** seçtiğiniz sayfadaki değişikliklerle ilgili olayları **önizlemek için gönderilen tüm teklifi** gösterir.

* Tüm teklifler için, **teklif kurulumu** sayfası her gönderim olayı için varsayılan olarak dahil edilir.
* Planları destekleyen teklifler için, her gönderim olayına **plana genel bakış** sayfası dahildir.
* Sınama sürücüsünü destekleyen teklifler için, her gönderim olayına **test sürücüsü** sayfası dahildir.

### <a name="users"></a>Kullanıcılar

Bir olay kullanıcı tarafından başlatılmışsa, geçmiş sayfası kullanıcıyı aşağıdaki senaryolara göre gösterir:

#### <a name="the-event-was-initiated-by-the-publisher"></a>Olay yayımcı tarafından başlatıldı

Bir teklif için yayımlama izinlerine sahip olan kullanıcıların, başlattıkları yayımlama olayları için adı görüntülenir.

[![Geçmiş sayfasında, yayımlama izinlerine sahip kullanıcıların adı görüntülenir.](./media/review-publish-offer/event-initiated-by-publisher.png)](./media/review-publish-offer/event-initiated-by-publisher.png#lightbox)

#### <a name="the-event-was-initiated-by-microsoft"></a>Olay Microsoft tarafından başlatıldı

Microsoft yöneticilerine, sizin adınıza eylem başlatabilir veya beklenmeyen bir sistem hatasından sonra düzeltici eylemler uygulayabilmeniz için izinler verebilirsiniz. Microsoft adı ve logosu, hesabınız adına Microsoft tarafından başlatılan olayları yayımlamak için görüntülenir.

[![Geçmiş sayfasının Microsoft tarafından başlatılan olayları görüntüleme örneği.](./media/review-publish-offer/event-initiated-by-microsoft.png)](./media/review-publish-offer/event-initiated-by-microsoft.png#lightbox)

#### <a name="the-event-was-initiated-by-an-unidentified-user"></a>Olay tanımlanmamış bir kullanıcı tarafından başlatıldı

Artık bir hesapla ilişkili olmayan kullanıcıların adı, yayımlama izinleri geri alındıktan sonra **tarafından başlatılan** sütundan kaldırılır.

[![Geçmiş sayfasının tanımlanmamış kullanıcılar tarafından başlatılan olayları görüntüleme örneği.](./media/review-publish-offer/event-initiated-by-unidentified-user.png)](./media/review-publish-offer/event-initiated-by-unidentified-user.png#lightbox)

#### <a name="validation-and-publishing-steps"></a>Doğrulama ve yayımlama adımları

[Doğrulama ve yayımlama adımlarına](#validation-and-publishing-steps)karşılık gelen sistem işlemleri için hiçbir Kullanıcı gösterilmez. Bu olaylar, olayın tamamlanma durumuna göre renk kodludur.

[![Geçmiş sayfasının doğrulama ve yayımlama adımlarını görüntüleme örneği.](./media/review-publish-offer/validation-and-publishing-event.png)](./media/review-publish-offer/validation-and-publishing-event.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

[Iş Ortağı Merkezi 'nde ticari Market için analitik raporlara erişin](partner-center-portal/analytics.md)
