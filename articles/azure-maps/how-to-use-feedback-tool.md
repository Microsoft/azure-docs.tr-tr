---
title: Azure haritalar 'a veri geri bildirimi sağlama | Microsoft Docs
description: Azure haritalar geri bildirim aracını kullanarak veri geri bildirimi sağlayın.
author: walsehgal
ms.author: v-musehg
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 076f98cb240014bcc88a395902203413e31fe0f1
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642129"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Azure haritalar 'a veri geri bildirimi sağlama

Azure Maps, 2018 Mayıs 'dan itibaren, yeni harita verileri, kullanımı kolay REST API 'Leri ve kurumsal müşterilerimizi çeşitli iş kullanımı durumlarında desteklemek için güçlü SDK 'Lar sağlamak için de kullanılabilir. Gerçek dünya her saniye değişmekte ve müşterilerimiz için dijital bir gösterim sağlamamız önemlidir. Açık veya kapalı tesisler için planlama yapan müşterilerimiz, doğru tesislerde teslim, bakım veya müşteri hizmetini verimli bir şekilde planlayabilmeleri için haritalarımızın hemen güncelleştirilmesini sağlamaktır. Müşterilerin doğrudan veri geri bildirimi sağlaması için Azure haritalar veri geri bildirim aracı 'nı oluşturduk. Müşterilerin veri geri bildirimi, doğrudan veri sağlayıcılarımıza ve eşleme ürünlerimize hızlı bir şekilde değerlendirme ve geri bildirim birleştirebilen harita düzenleyicilerimize gider.  

[Azure haritalar veri geri bildirim aracı](https://feedback.azuremaps.com) , müşterilerin, özellikle ilgi çekici ve mesken adreslerdeki harita verilerine geri bildirim sağlaması için kolay bir yol sunar. Bu makalede, Azure Maps geri bildirim aracını kullanarak farklı türlerde geri bildirimde bulunmak için size kılavuzluk eder.

## <a name="add-a-business-place-or-a-residential-address"></a>Bir iş yeri veya mesken adresi ekleyin 

Haritada eksik bir ilgi veya konut adresi noktası için geri bildirim sağlamak isteyebilirsiniz. Bunu iki şekilde yapabilirsiniz, Azure harita verileri geri bildirim aracını açın ve eksik konumun koordinatlarını aratıp "bir konum Ekle" ye tıklayın.

  ![eksik konumu ara](./media/how-to-use-feedback-tool/search-poi.png)

Ya da eşlemle etkileşim kurabilir ve bir PIN 'i koordine etmek için konuma tıklayabilir ve "yer Ekle" seçeneğine tıklayabilirsiniz. 

  ![PIN Ekle](./media/how-to-use-feedback-tool/add-poi.png)

Tıklandıktan sonra, yer için ilgili ayrıntıları sağlamak üzere bir forma yönlendirilirsiniz.

  ![bir yer ekleyin](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>Bir iş yerini veya mesken adresini çözün 

Geri bildirim aracı Ayrıca, bir iş yerinin veya bir adresin aranmasını ve yerini bulmanıza ve doğru olmadıkları takdirde adresi veya PIN konumunu düzeltmeye yönelik geribildirim sağlamanıza olanak tanır. Adresi gidermek için geri bildirim sağlamak üzere, arama çubuğunu kullanarak bir iş yeri veya mesken adresi arayın. Sonuçlar listesinden ilgilendiğiniz konuma tıklayın ve "Bu yeri düzeltir" düğmesine tıklayın.

  ![düzeltilmesi için arama yeri](./media/how-to-use-feedback-tool/fix-place.png)

Adresi gidermek için geri bildirim sağlamak üzere "bir yeri çözün" formu doldurun ve "Gönder" düğmesine tıklayın.

  ![formu onarma](./media/how-to-use-feedback-tool/fix-form.png)

Konumun PIN konumu yanlış ise, "PIN konumu yanlış" şeklindeki "bir yeri düzelt" formundaki onay kutusunu işaretleyin ve PIN 'i doğru konuma taşıyın ve "Gönder" düğmesine tıklayın.

  ![PIN konumunu taşı](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>Açıklama ekle 

Ayrıca, bir konum aramanıza izin veren geri bildirim aracı, konum ile ilgili ayrıntılar için de ücretsiz form metni açıklaması eklemenize olanak tanır. Konum için bir açıklama araması eklemek veya konuma tıklayın ve "Açıklama Ekle" ye tıklayın, yorum yazıp "Gönder" seçeneğine tıklayın. 

  ![Açıklama Ekle](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>Durumu izle 

Ayrıca, "durumu izlemek istiyorum" kutusunu işaretleyerek ve bir istek yaparken e-postanızı sağlamak için isteğinizin durumunu izleyebilirsiniz. E-postada, isteğiniz için güncel durum sağlayan bir izleme bağlantısı alacaksınız. 

  ![geri bildirim durumu](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>Sonraki adımlar

Azure eşlemeleriyle ilgili teknik sorular göndermek için şu adresi ziyaret edin:

* [Azure haritalar Stack Overflow](https://stackoverflow.com/questions/tagged/azure-maps)
* [Azure haritalar geri bildirim Forumu](https://feedback.azure.com/forums/909172-azure-maps)