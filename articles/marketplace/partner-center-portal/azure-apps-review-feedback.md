---
title: Azure Apps teklifleri için geri bildirimi gözden geçirme-Microsoft ticari Market
description: Azure Uygulama teklifiniz için Microsoft Azure Market gözden geçirme ekibinin geri bildirimini işleme. Azure DevOps 'daki geri bildirimde, Iş ortağı merkezi kimlik bilgilerinizle erişebilirsiniz.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: ede4e5dd781851c781407a8acea640e3e501f259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96436394"
---
# <a name="handling-review-feedback-for-azure-application-offers"></a>Azure Uygulama teklifleri için gözden geçirme geri bildirimi işleniyor

Bu makalede, [Azure DevOps](https://azure.microsoft.com/services/devops/)'daki Microsoft Azure Market gözden geçirme ekibinin geri bildirimlerine nasıl erişebileceğiniz açıklanır. **Microsoft gözden geçirme** adımı sırasında Azure Uygulama teklifinizdeki kritik sorunlar bulunursa, bu sorunlarla ilgili ayrıntılı bilgileri görüntülemek için bu sistemde oturum açabilirsiniz (geri bildirim gözden geçirin). Tüm sorunları düzelttikten sonra, Azure Marketi 'nde yayımlamaya devam etmek için teklifinizi yeniden göndermeniz gerekir. Aşağıdaki diyagramda, bu geri bildirim işleminin yayımlama işlemiyle nasıl ilişkili olduğu gösterilmektedir.

![Geri bildirim işlemini gözden geçir](./media/review-feedback-process.png)

Genellikle, gözden geçirme sorunları bir çekme isteği (PR) olarak başvurulur. Her bir çekme isteği, sorunla ilgili ayrıntıları içeren çevrimiçi bir Azure DevOps öğesine bağlıdır. Aşağıdaki görüntüde, İncelemeler sırasında sorunlar bulunursa Iş Ortağı Merkezi deneyiminin bir örneği görüntülenir. 

![Yayın durumu](./media/publishing-status.png)

Gönderim hakkındaki belirli ayrıntıları içeren PR, "sertifika raporunu görüntüle" bağlantısında bahsedilir. Karmaşık durumlarda, gözden geçirme ve destek takımları da size e-posta verebilir.

## <a name="azure-devops-access"></a>Azure DevOps erişimi

Iş Ortağı Merkezi 'nde "Geliştirici" rolüne erişimi olan tüm kullanıcıların, gözden geçirme geri bildirimde başvurulan PR öğelerini görüntüleme erişimi olur.

## <a name="reviewing-the-pull-request"></a>Çekme isteğini gözden geçirme

Çekme isteğinde belgelenen sorunları gözden geçirmek için aşağıdaki yordamı kullanın.

1. Yayımlama adımlarının **Microsoft gözden geçirme** bölümlerinde, tarayıcınızı başlatmak IÇIN bir PR bağlantısı seçin ve bu çekme Isteği Için **genel bakış** (giriş) sayfasına gidin. Aşağıdaki görüntüde, contoso örnek uygulama teklifinin kritik sorun giriş sayfasına bir örnek gösterilmektedir. Bu sayfa, Azure uygulamasında bulunan gözden geçirme sorunlarıyla ilgili yararlı Özet bilgiler içerir.

    [![Çekme isteği giriş sayfası](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Genişletmek için bu resme tıklayın.*

1. Seçim Pencerenin sağ tarafında, bölüm **ilkeleri**' nde, sorunun alt düzey ayrıntılarını araştırmak için, ilgili günlük dosyaları da dahil olmak üzere, sorun iletisini seçin (Bu örnekte, **ilke doğrulaması başarısız oldu**). Hatalar genellikle günlük dosyalarının altında görüntülenir.

1. Ana sayfanın sol tarafındaki menüde, bu teklif için teknik varlıkları oluşturan liste dosyalarını göstermek için **dosyalar** ' ı seçin. Microsoft gözden geçirenler, bulunan kritik sorunları açıklayan açıklamalar eklemiş olmalıdır. Aşağıdaki örnekte, iki sorun keşfedilmiştir.

    [![Dosyaları ve keşfedilen iki sorunu vurgulayan ekran görüntüsü.](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Genişletmek için bu resme tıklayın.*

1. Çevredeki kodun bağlamında açıklamaya gitmek için sol ağaçtaki her bir yorum düğümünü seçin. Açıklama tarafından açıklanan sorunu gidermek için takımınızın projesindeki kaynak kodunuzu düzeltin.

>[!Note]
>Teklifinizin teknik varlıklarını gözden geçirme ekibinin Azure DevOps ortamında düzenleyemezsiniz. Yayımcılar için, bu, içerilen kaynak kodu için salt okunurdur. Ancak, Microsoft gözden geçirme ekibinin avantajı için açıklamalara yanıt verebilirsiniz.

   Aşağıdaki örnekte, yayımcı ilk sorunu inceetti, düzeltti ve yanıtladı.

   ![İlk düzelme ve açıklama yanıtı](./media/first-comment-reply.png)

## <a name="next-steps"></a>Sonraki adımlar

Gözden geçirme çekme isteği 'nde belgelenen kritik sorunları düzelttikten sonra [Azure uygulama teklifinizi yeniden yayımlamanız](../create-new-azure-apps-offer.md)gerekir.
