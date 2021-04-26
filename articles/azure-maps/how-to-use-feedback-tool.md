---
title: Azure haritalar 'a veri geri bildirimi sağlama | Microsoft Azure haritaları
description: Microsoft Azure Maps geri bildirim aracını kullanarak veri geri bildirimi sağlayın.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4be775d8a6a8c476bcc659a902c7f43c12a6d6d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905341"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Azure haritalar 'a veri geri bildirimi sağlama

Azure haritalar 2018 Mayıs 'tan itibaren kullanılabilir. Azure haritalar, yeni harita verileri, kullanımı kolay REST API 'Leri ve kurumsal müşterilerimizi farklı türde iş kullanım örnekleri ile desteklemeye yönelik güçlü SDK 'Lar sağlıyor. Gerçek dünya her saniye değiştiriliyor ve müşterilerimiz için dijital bir gösterim sağlamamız önemlidir. Açık veya kapalı tesisler için planlama yapan müşterilerimiz, haritalarımıza hemen güncelleştirmek için gerekli olmalıdır. Bu nedenle, doğru tesislerde teslim, bakım veya müşteri hizmetini verimli bir şekilde planlayabilirler. Müşterilerin doğrudan veri geri bildirimi sağlaması için Azure haritalar veri geri bildirim sitesini oluşturduk. Müşterilerin veri geri bildirimi doğrudan veri sağlayıcılarımıza ve harita düzenleyicilerimize gider. Bunlar, eşleme ürünlerimize hızlı bir şekilde değerlendirme yapabilir ve geri bildirim ekleyebilir.  

[Azure haritalar verileri (Önizleme) geri bildirim sitesi](https://feedback.azuremaps.com) , müşterilerin, özellikle ilgi çekici ve yöresel adresler halinde harita verileri geri bildirimi sağlaması için kolay bir yol sunar. Bu makalede, Azure Maps geri bildirim sitesini kullanarak farklı türlerde geri bildirimde bulunmak için size kılavuzluk eder.

## <a name="add-a-business-place-or-a-residential-address"></a>Bir iş yeri veya mesken adresi ekleyin 

Eksik bir ilgi noktası veya mesken adresi hakkında geri bildirim sağlamak isteyebilirsiniz. Bunu iki şekilde yapabilirsiniz. Azure Map veri geri bildirim sitesini açın, eksik konumun koordinatlarını arayın ve "bir konum Ekle" ye tıklayın.

  ![eksik konumu ara](./media/how-to-use-feedback-tool/search-poi.png)

Ya da eşlemesiyle etkileşime geçebilirsiniz. Bir PIN 'i koordinat üzerinde bırakmak için konuma tıklayın ve "bir konum Ekle" ye tıklayın.

  ![PIN Ekle](./media/how-to-use-feedback-tool/add-poi.png)

Tıklandıktan sonra, yer için ilgili ayrıntıları sağlamak üzere bir forma yönlendirilirsiniz.

  ![bir yer ekleyin](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>Bir iş yerini veya mesken adresini çözün 

Geri bildirim sitesi ayrıca bir iş yeri veya adres aramanızı ve bulmanızı sağlar. Doğru değilse, adresi veya PIN konumunu düzeltmek için geri bildirim sağlayabilirsiniz. Adresi gidermek için geri bildirim sağlamak üzere, arama çubuğunu kullanarak bir iş yeri veya mesken adresi arayın. Sonuçlar listesinden ilgilendiğiniz konuma tıklayın. "Bu yeri çözme" seçeneğine tıklayın.

  ![düzeltilmesi için arama yeri](./media/how-to-use-feedback-tool/fix-place.png)

Adresi gidermek için geri bildirimde bulunmak için, "bir yeri çözme" formunu doldurun ve ardından "Gönder" düğmesine tıklayın.

  ![formu onarma](./media/how-to-use-feedback-tool/fix-form.png)

Yer için PIN konumu yanlış ise, "PIN konumu yanlış" şeklindeki "bir yeri sabitle" formundaki onay kutusunu işaretleyin. PIN 'i doğru konuma taşıyın ve ardından "Gönder" düğmesine tıklayın.

  ![PIN konumunu taşı](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>Yorum ekle 

Geri bildirim aracı, bir konumu aramanıza izin vermenin yanı sıra, konum ile ilgili ayrıntılar için de ücretsiz form metni açıklaması eklemenize olanak tanır. Bir açıklama eklemek için konumu arayın veya konuma tıklayın. "Açıklama Ekle" ye tıklayın, bir açıklama yazın ve ardından "Gönder" e tıklayın.

  ![Açıklama Ekle](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>Durumu izleme 

Ayrıca, "durumu izlemek istiyorum" kutusunu işaretleyerek ve bir istek yaparken e-postanızı sağlamak için isteğinizin durumunu izleyebilirsiniz. E-postada, isteğinizin güncel durumunu sağlayan bir izleme bağlantısı alacaksınız. 

  ![geri bildirim durumu](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>Sonraki adımlar

Azure eşlemeleriyle ilgili teknik sorular göndermek için şu adresi ziyaret edin:

* [Microsoft Q & A](/answers/topics/azure-maps.html)