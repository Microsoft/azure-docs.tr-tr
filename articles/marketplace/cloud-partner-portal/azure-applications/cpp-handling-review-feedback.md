---
title: Azure Uygulama incelemesi geri bildirimi işleme | Azure Marketi
description: Azure Market için Azure Uygulama tekliflerine yönelik gözden geçirme geri bildirimini işlemek üzere Azure DevOps 'ın nasıl kullanılacağını açıklar.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pabutler
ms.openlocfilehash: ef4aff57948034fb369bd74564306b7b8674b377
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827595"
---
# <a name="handling-review-feedback"></a>Gözden geçirme geri bildirimini işleme

Bu makalede, Microsoft Azure Market gözden geçirme ekibi tarafından kullanılan Azure DevOps ortamına nasıl erişebileceğiniz açıklanır.  **Microsoft gözden geçirme** adımı sırasında Azure Uygulama teklifinizdeki kritik sorunlar bulunursa, bu sorunlarla ilgili ayrıntılı bilgileri görüntülemek için bu sistemde oturum açabilirsiniz (geri bildirim gözden geçirin).  Tüm bu sorunları düzelttikten sonra, Azure Marketi 'nde yayımlamaya devam etmek için teklifinizi yeniden göndermeniz gerekir.  Aşağıdaki diyagramda, bu geri bildirim işleminin yayımlama işlemiyle nasıl ilişkili olduğu gösterilmektedir.

![Azure DevOps geri bildirimi ile adımları yayımlama](./media/pub-flow-vsts-access.png)

Genellikle, gözden geçirme sorunları çekme isteği (PR) olarak başvurulur.  Her bir çekme isteği, sorunla ilgili ayrıntıları içeren çevrimiçi bir [Azure DevOps](https://azure.microsoft.com/services/devops/) (daha önce adlandırılmış VISUAL STUDIO Team SERVICES (VSTS)) öğesine bağlıdır.  Aşağıdaki görüntüde bir gözden geçirme çekme isteği başvurusunun bir örneği görüntülenmektedir.  Karmaşık durumlarda, gözden geçirme ve destek takımları da size e-posta verebilir. 

![İnceleme geri bildirimini görüntüleyen durum sekmesi](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Azure DevOps erişimi

Gözden geçirme geri bildirimde başvurulan PR öğelerini görüntülemek için, yayımcıların öncelikle uygun yetkilendirme verilmelidir.  Aksi halde, yeni yayımcılar PR 'ler görüntülemeye çalışırken bir `401 - Not Authorized` yanıt sayfası alır.  Bu Azure DevOps deposuna erişim istemek için aşağıdaki adımları gerçekleştirin:

1. Aşağıdaki bilgileri toplayın:
    - Yayımcı adınız ve KIMLIĞINIZ
    - Teklif türü (Azure uygulaması), teklif adı ve SKU KIMLIĞI
    - Çekme isteği bağlantısı, örneğin: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` bu URL 'nin bildirim iletisinden veya 401 yanıt sayfasının adresinden elde edilebilir.
    - Yayımlama kuruluşunuzdaki erişim izni verilmesini istediğiniz kişilerin e-posta adresleri.  Bu liste, Bulut İş Ortağı Portalı yayımcı olarak kaydederken verdiğiniz sahip adreslerini (es) içermelidir.
2. Destek olayı oluşturun.  Bulut İş Ortağı Portalı başlık çubuğunda, **Yardım** düğmesini seçin ve menüden **destek** ' i seçin.  Varsayılan Web tarayıcısının başlatılması ve Microsoft yeni destek olay sayfasına gitmeniz gerekir.  (Önce oturum açmanız gerekebilir.)
3. **Sorun türünü** **Market ekleme** ve **Kategori** olarak **erişim sorunu**olarak belirtip **isteği Başlat**' ı seçin.

    ![Destek bileti kategorisi](./media/support-incident1.png)

4. **Adım 1/2** sayfasında, iletişim bilgilerinizi girip **devam**' ı seçin.
5. 2\. **adım** sayfasında bir olay başlığı belirtin (örneğin `Request Azure DevOps access`) ve ilk adımda topladığınız bilgileri (yukarıdaki) sağlayın.  Sözleşmeyi okuyup kabul edin ve ardından **Gönder**' i seçin.

Olay oluşturma başarılı olduysa, bir onay sayfası görüntülenir.  Bu sayfadaki onay bilgilerini başvurunuz için kaydedin.  Microsoft Desteği takım, erişim isteğinize birkaç iş günü içinde yanıt vermesi gerekir.


## <a name="reviewing-the-pull-request"></a>Çekme isteğini gözden geçirme 

Çekme isteğinde belgelenen sorunları gözden geçirmek için aşağıdaki yordamı kullanın.

1. **Yayımlama adımları** formunun **Microsoft gözden geçirme** bölümünde, bir PR bağlantısına tıklayarak tarayıcınızı başlatın ve bu çekme isteği için **genel bakış** (giriş) sayfasına gidin.  Aşağıdaki görüntüde, contoso örnek uygulama teklifi için örnek bir kritik sorun giriş sayfası gösterilmektedir.  Bu sayfa, Azure uygulamasında bulunan gözden geçirme sorunlarıyla ilgili yararlı Özet bilgiler içerir.  

    [çekme isteği giriş sayfası
   ![](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)  <br/> *Genişletilecek resme tıklayın.*
    
2. Seçim Pencerenin sağ tarafında, bölüm **ilkeleri**' nde, sorunun alt düzey ayrıntılarını araştırmak için, ilgili günlük dosyaları da dahil olmak üzere, sorun iletisi ' ne (Bu örnekte, **ilke doğrulaması başarısız oldu**) tıklayın.  Hatalar genellikle günlük dosyalarının altında görüntülenir.

3. Ana sayfanın sol tarafındaki menüde, bu teklif için teknik varlıkları oluşturan liste dosyalarını göstermek için **dosyalar** ' ı seçin.  Microsoft gözden geçirenler, bulunan kritik sorunları açıklayan açıklamalar eklemiş olmalıdır.  Aşağıdaki örnekte, iki sorun keşfedilmiştir. 

    [çekme isteği giriş sayfası
   ![](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)  <br/> *Genişletilecek resme tıklayın.*

4. Çevredeki kodun bağlamında açıklamaya gitmek için sol ağaçtaki her bir açıklama düğümüne tıklayın.  Açıklama tarafından açıklanan sorunu gidermek için takımınızın projesindeki kaynak kodunuzu düzeltin.

> [!Note]
> Teklifinizin teknik varlıklarını gözden geçirme ekibinin Azure DevOps ortamında düzenleyemezsiniz.  Yayımcılar için, bu, içerilen kaynak kodu için salt okunurdur.  Ancak, Microsoft gözden geçirme ekibinin avantajı için açıklamalara yanıt verebilirsiniz.

   Aşağıdaki örnekte, yayımcı ilk sorunu inceetti, düzeltti ve yanıtladı.

   ![İlk düzelme ve açıklama yanıtı](./media/first-comment-reply.png)


## <a name="next-steps"></a>Sonraki adımlar

Gözden geçirme çekme isteği 'nde belgelenen kritik sorunları düzelttikten sonra [Azure uygulama teklifinizi yeniden yayımlamanız](./cpp-publish-offer.md)gerekir.
