---
title: Ticari Market teklifinizin Yayımlanma durumunu kontrol edin
description: Microsoft Iş Ortağı Merkezi 'nde ticari Market aracılığıyla bir teklif yayımlamak için gereken doğrulama, sertifika ve önizleme adımlarının durumunu kontrol edin.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 14290e000d5c6251216a6b0a0fc68c8a0897210a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824029"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Ticari Market teklifinizin Yayımlanma durumunu kontrol edin

Geçerli **Yayımlama durumunuzu** Iş Ortağı Merkezi 'Nde [ticari Market portalının](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) **teklif genel bakış** sekmesinde görüntüleyebilirsiniz.

Her teklif için aşağıdaki durum göstergelerden biri görüntülenmelidir.

| **Durum**    | **Açıklama**  |
| :---------- | :-------------------|
| **Görünümündeyken** | Teklif oluşturuldu ancak yayımlanmadı. |
| **Yayımlama devam ediyor** | Teklif/plan, yayımlama sürecinin adımları aracılığıyla kendi yolunda çalışmaktadır. |
| **İlgilenilmesi gerekiyor** | Microsoft tarafından veya herhangi bir yayımlama adımında sertifika sırasında kritik bir sorun bulundu. |
| **Önizleme** | Teklif Microsoft tarafından sertifikalandıralındı ve şimdi yayımcı tarafından nihai bir doğrulamayı bekler. Teklifi canlı hale getirmek için canlı git ' i seçin. |
| **Lı** | Teklif, Market 'te canlı olur ve müşteriler tarafından görülebilir ve elde edilebilir. |
| **Satışı durdur bekleniyor** | Yayımcı, teklif veya planda "satışı Durdur" seçeneğini seçti, ancak eylem henüz tamamlanmadı. |
| **Market 'te yok** | Market 'te daha önce yayınlanmış bir teklif/plan kaldırılmıştır. |

## <a name="automated-validation"></a>Otomatik doğrulama

Yayımlama işlemindeki ilk adım bir otomatik doğrulama kümesidir. Her doğrulama adımı, teklifinizin oluşturulmasında etkinleştirmeyi seçtiğiniz bir özelliğe karşılık gelir. Bu özellik etkinleştirilmemişse, doğrulama sonraki yayımlama adımından önce atlar. Yayımlama durumu onaylanmadan önce her doğrulama denetiminin tamamlanması gerekir.

- **Teklif satın alma akışı kurulumu (< 10 dk)**

Bu adımda, teklif Azure portal aracılığıyla müşteriler tarafından satın alındığında teklifinizin karşılanabileceği güvence altına alınır. Bu adım yalnızca Microsoft ile satılan teklifler için geçerlidir.

- **Sınama sürücüsü veri doğrulaması (~ 5 dakika)**

Bu adımda, teklifinin test sürücüsü teknik yapılandırma bölümünde belirttiğiniz verileri doğrulayacağız. Sınama sürücüsü işlevselliği test edilir ve onaylanır. Bu adım yalnızca bir test sürücüsü etkin olan teklifler için geçerlidir.

- **Sınama sürücüsü sağlama (~ 30 dakika)**

Bu adımda, önceki adımda test sürücünüzün verileri ve işlevlerini doğruladıktan sonra, test sürücünüzün örneklerini dağıtıp çoğaltarak müşteri kullanımına hazırlarız.  Bu adım yalnızca bir test sürücüsü etkin olan teklifler için geçerlidir.

- **Müşteri adayı yönetimi doğrulama ve kayıt (< 15 dk)**

Bu adımda, müşteri adayı yönetim sisteminizin müşteri adaylarını teklif kurulumunda belirtilen ayrıntılara göre alabileceği onaylanır. Bu adım yalnızca, lider yönetimi etkinleştirilmiş teklifler için geçerlidir.

## <a name="certification"></a>Sertifika

Yayımlanmadan önce, Iş Ortağı Merkezi 'nde ticari Market 'e gönderilen tekliflerin sertifikalı olması gerekir. Gönderilen, [Azure Marketi sertifika ilkelerine](https://docs.microsoft.com/legal/marketplace/general-policies)yönelik bir denetim de dahil olmak üzere, daha kapsamlı bir test, bazı otomatikleştirilmiş ve diğer el ile bir kılavuz sunar Teklif gönderimlerinin, yayımlama akışında bir sonraki adıma geçmeden önce sertifika için uygun olarak işaretlenmesi gerekir.

### <a name="types-of-validation-that-take-place-during-certification"></a>Sertifika sırasında gerçekleşen doğrulama türleri

Gönderilen her teklif için sertifika işlemine dahil edilen üç doğrulama düzeyi vardır.

- Yayımcı iş uygunluğu
- İçerik doğrulama
- Teknik doğrulama

#### <a name="publisher-business-eligibility"></a>Yayımcı iş uygunluğu

Her teklif türü, yayımcının karşılaması gereken bir temel uygunluk ölçütü kümesini denetler. Uygunluk ölçütleri, yayımcının MPN durumunu, Uzmanlıklar, Uzmanlıklar düzeylerini, vb. içerebilir.

#### <a name="content-validation"></a>İçerik doğrulama

İçerik doğrulaması sırasında, teklifinizi oluştururken girilen bilgiler kalite ve ilgi açısından denetlenir. Bu denetimler Market listeleme ayrıntıları, fiyatlandırma, kullanılabilirlik, ilişkili planlar vb. için girdilerinizi gözden geçirir. Azure Marketi ve/veya AppSource listeleme ölçütlerini karşılamak için teklifinizin şunları içerdiğini doğrulayacağız:

- teklifi doğru şekilde açıklayan bir başlık;
- kapsamlı bir genel bakış ve değer teklifi sağlayan iyi yazılmış açıklamalar;
- kaliteli ekran görüntüleri ve ilgili videolar; '
- Teklifin Microsoft platformlarını ve araçlarını nasıl kullandığını gösteren bir açıklama.

[Genel listeleme ilkelerini](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general)okuyarak içerik doğrulama ölçütleriyle ilgili daha fazla bilgi edinin.

#### <a name="technical-validation"></a>Teknik doğrulama

Teknik doğrulama sırasında, teklif (paket veya ikili) aşağıdaki denetimleri altına girer.
- Kötü amaçlı yazılım için tarandı
- İzlenen ağ çağrıları
- Paket çözümlendi
- Teklifin gerçek işlevlerinin tam taranması

Teklif, güçlü olduğundan emin olmak için çeşitli platformlar ve sürümler arasında test edilir.

Bu belgenin teknik yapılandırma bölümünde teklifiniz için gereken belirli yapılandırma ayrıntılarını gözden geçirin.

### <a name="certification-failure-report"></a>Sertifika hata raporu

Gözden geçirmeyi tamamladıktan sonra, teklifiniz sertifikayı geçirirse yayımlama sürecinde bir sonraki adıma geçer. Teklifiniz hiçbir liste, teknik veya ilke denetimini başarısız olduysa ya da bu türden bir teklif göndermeye uygun değilseniz, bir sertifika hatası raporu oluşturulur ve size gönderilir.

Bu rapor, gözden geçirme notlarıyla birlikte başarısız olan ilkelerin açıklamalarını içerir. Bu e-posta raporunu gözden geçirin, herhangi bir sorunu çözün, sizin teklifinizde gereken güncelleştirmeleri yapın ve teklifi Iş Ortağı Merkezi ' nde [ticari Market portalı](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) 'nı kullanarak yeniden gönderin. (Sertifikayı, sertifika geçirmeden önce gerektiği kadar çok kez yeniden gönderebilirsiniz).

## <a name="preview-creation"></a>Önizleme oluşturma

**Önizleme oluşturma** adımı sırasında teklifinizin yalnızca teklifinizin Önizleme bölümünde belirttiğiniz kitlelerin erişebileceği bir sürümünü oluşturacağız.

>[!Note]
> Bu adımı, kuruluşunuzun dışındaki kişileri bir teklifle görünürlüğe yönelik olarak sunmak için kullanmayın. Bunun yerine **özel teklif** seçeneğini kullanın. Bu noktada, teklifiniz tam olarak sınanmamıştır ve doğrulanmaz ve dağıtım dışı için hazırlanma.

## <a name="publisher-approval"></a>Yayımcı onayı

Bu adımda, son yayımlama adımından önce teklif önizlemeyi gözden geçirmeniz ve onaylamanız için bir istek ile e-postayla gönderilir.

Teklifinizi Microsoft aracılığıyla satmayı seçtiyseniz, bu Önizleme onay aşaması sırasında gereksinimlerinizi karşıladığından emin olmak için teklifinizin Alım ve dağıtımını test edebilirsiniz. Teklifiniz henüz genel Market 'te kullanılamayacak. Bu önizlemeyi test edin ve onayladıktan sonra [**teklif genel bakış**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) panosunda **Go-Live** ' ı seçmeniz gerekir.

Bu önizleme aşamasında teklifte değişiklik yapmak istiyorsanız, yeni bir önizleme yayınlamak için düzenleyebilir ve yeniden gönderebilirsiniz. Daha fazla değişiklik hakkında daha fazla bilgi için [mevcut Market tekliflerini güncelleştirme](#update-existing-marketplace-offers) makalesine bakın.

Teklifiniz zaten canlı ve Market 'te herkese açık ise, [**teklif genel bakış**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) panosundaki canlı **çalış** ' ı seçene kadar yaptığınız tüm güncelleştirmeler canlı olmaz.

### <a name="publish-offer-to-the-public"></a>Teklifi herkese Yayımla

Iş Ortağı Merkezi ' nde oturum açın ve teklifine erişin. **Teklif genel bakış** sayfasına yönlendirilirsiniz. Bu sayfanın en üstünde, **canlı go**için bir seçenek görürsünüz. **Canlı git '** i seçin ve onayladıktan sonra teklif herkese yayınlanmasına başlayacaktır. Teklif canlı olduğunda bir e-posta bildirimi alacaksınız.

## <a name="publish"></a>Yayımlama

Teklifinizle **canlı çalışmaya** ve Market 'te kullanılabilir hale getirmeyi seçtiğinizden, canlı teklifin teklifin önizleme sürümü gibi yapılandırıldığından emin olmak için bir dizi son doğrulama denetimi vardır.

- **Teklif satın alma akışı kurulumu (> 10 dk)**

Bu adımda, teklif Azure portal aracılığıyla müşteriler tarafından satın alındığında teklifinizin karşılanabileceği güvence altına alınır. Bu adım yalnızca Microsoft ile satılan teklifler için geçerlidir.

- **Sınama sürücüsü veri doğrulaması (~ 5 dakika)**

Bu adımda, teklifinin test sürücüsü teknik yapılandırma bölümünde belirttiğiniz verileri doğrulayacağız. Sınama sürücüsü işlevselliği test edilir ve onaylanır. Bu adım yalnızca bir test sürücüsü etkin olan teklifler için geçerlidir.

- **Sınama sürücüsü sağlama (~ 30 dakika)**

Bu adımda, test sürücünüzün örneklerini dağıtıp çoğaltarak müşteri kullanımına hazırlarız.  Bu adım yalnızca bir test sürücüsü etkin olan teklifler için geçerlidir.

- **Müşteri adayı yönetimi doğrulama ve kayıt (> 15 dk)**

Bu adımda, müşteri adayı yönetim sisteminizin müşteri adaylarını teklif kurulumunda belirtilen ayrıntılara göre alabileceği onaylanır. Bu adım yalnızca, lider yönetimi etkinleştirilmiş teklifler için geçerlidir.

- **Son yayımlama (> 30 dakika)**

Bu adımda, teklifinizin Market 'te herkese açık hale geldiğini güvence altına sunuyoruz.

## <a name="update-existing-marketplace-offers"></a>Mevcut Market tekliflerini güncelleştir

Önceden yayımladığınız bir teklifte değişiklik yapmak istiyorsanız, önce mevcut teklifi güncelleştirmeniz ve sonra yeniden yayımlamanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Ticari Market 'te mevcut bir teklifi güncelleştirme](./update-existing-offer.md)
