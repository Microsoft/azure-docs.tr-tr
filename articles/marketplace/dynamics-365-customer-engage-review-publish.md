---
title: Microsoft AppSource (Azure Marketi) için bir Dynamics 365 müşteri katılımı ve Power Apps teklifi inceleyin ve yayımlayın.
description: Microsoft AppSource (Azure Marketi) için bir Dynamics 365 müşteri katılımı ve Power Apps teklifi inceleyin ve yayımlayın.
ms.reviewer: emuench
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: c24baa6a89b70a8b0b6409be20e1a8d4923d12ae
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820494"
---
# <a name="review-and-publish-a-dynamics-365-cepa-offer"></a>Dynamics 365 CE&PA teklifini İnceleme ve yayımlama

Bu makalede, Iş Ortağı Merkezi 'nin müşteri katılımı ve Power Apps için taslak Dynamics 365 ' i nasıl önizlemesinin yapılacağı ve ardından ticari Market 'te yayımlayabileceği gösterilmektedir. Yayımlama adımlarında ilerlerken yayımlama durumunun nasıl denetlenmesi de ele alınmaktadır.

## <a name="offer-status"></a>Teklif durumu

Teklif durumunuzu, [Iş Ortağı Merkezi](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)' nde ticari Market panosunun **genel bakış** sekmesinde gözden geçirebilirsiniz. Her teklifin **durumu** aşağıdakilerden biri olacaktır:

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

Teklifinizin yayımlama durumu, yayın sürecinde gezindiğinde değişir. Bu işlemle ilgili ayrıntılı bilgi için bkz. [doğrulama ve yayımlama adımları](review-publish-offer.md#validation-and-publishing-steps).

Yayımlamaya yönelik bir teklif göndermeye hazırsanız, portalın sağ üst köşesinde **gözden geçir ve Yayımla** ' yı seçin. Teklifiniz için her sayfanın durumunu aşağıdakilerden biri olarak görürsünüz:

   - **Başlatılmamış** – sayfa tamamlanmadı.
   - **Tamamlanmamış** – sayfada gerekli bilgiler eksik veya düzeltilmesi gereken hatalar var. Sayfaya dönüp güncelleştirmeniz gerekir.
   - **Tamam** – sayfa tamamlanmıştır. Gerekli tüm veriler sağlanmış ve hata yok.

Sayfaların herhangi birinde **tamamlanmamış** bir durum varsa, söz konusu sayfadaki sorunu düzeltmeniz ve sonra durumu şimdi **Tamam** olarak gösterir durumunu onaylamak için **İnceleme ve yayımlama** sayfasına geri dönmeniz gerekir. Bazı teklif türleri test gerektirir. Bu durumda, sertifika ekibine yönelik test yönergeleri ve uygulamanızı anlamak için yararlı olan tüm ek notlar sağlamanız gereken **sertifika alanı için bir not** görürsünüz.

Tüm sayfalar tamamlandıktan ve ilgili test notlarını girdikten sonra, teklifinizi göndermek için **Yayımla** ' yı seçin. Teklifinizin bir önizleme sürümü onaylanmak üzere kullanılabilir olduğunda size e-posta göndereceğiz. Şu anda:

1. Iş Ortağı Merkezi 'ne geri dönün.
1. Sol gezinti menü çubuğunda **genel bakış** sekmesini seçin.
1. Teklifi seçin.
1. **Gözden geçir ve Yayımla '** yı seçin.
1. Teklifinizin herkese açık olması için **canlı git** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Ticari Market'te bulunan bir teklifi güncelleştirme](partner-center-portal/update-existing-offer.md)
