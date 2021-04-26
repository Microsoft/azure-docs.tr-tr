---
title: Karma varlık yükseltme-LUSıS
description: Karma varlığı, HALSıS portalındaki yükseltme işlemiyle makine öğrenimi varlığına yükseltin.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 46e9ece70d9f980065c719ee1205eb46591b45c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025251"
---
# <a name="upgrade-composite-entity-to-machine-learning-entity"></a>Bileşik varlığı makine öğrenimi varlığına yükseltme

Varlıkta hata ayıklama için daha iyi bir kullanılabilirlik elde eden daha fazla tahmine sahip bir varlık oluşturmak üzere bileşik varlığı makine öğrenimi varlığına yükseltin.

## <a name="current-version-model-restrictions"></a>Geçerli sürüm modeli kısıtlamaları

Yükseltme işlemi, uygulamanızda bulunan mevcut bileşik varlıkların uygulamanızın yeni bir sürümüne göre makine öğrenimi varlıkları oluşturur. Buna Bileşik varlık alt öğeleri ve rolleri dahildir. Bu işlem, Ayrıca, yeni varlığı kullanmak için de etiketleri örnek olarak değiştirir.

## <a name="upgrade-process"></a>Yükseltme işlemi

Yükseltme işlemi:
* Her bileşik varlık için yeni makine öğrenimi varlığı oluşturur.
* Alt varlıklar:
    * Alt varlık yalnızca bileşik varlıkta kullanılıyorsa, yalnızca makine öğrenimi varlığına eklenecektir.
    * Alt varlık bileşik içinde _ve_ ayrı bir varlık (örneğin, örnek olarak etiketlenir) olarak kullanılırsa, bir varlık olarak sürüme ve yeni makine öğrenimi varlığına bir alt varlık olarak eklenir.
    * Alt varlık bir rol kullanıyorsa, her rol aynı ada sahip bir alt varlığa dönüştürülür.
    * Alt varlık makine öğrenimi olmayan bir varlıktır (normal ifade, liste varlığı veya önceden oluşturulmuş varlık), aynı ada sahip yeni bir alt varlık oluşturulur ve yeni alt varlık, gerekli özelliği eklenen makine olmayan öğrenme varlığını kullanan bir özelliğe sahiptir.
* Adlar korunur, ancak aynı alt varlık/eşdüzey düzeyde benzersiz olmalıdır. [Benzersiz adlandırma sınırlarına](./luis-limits.md#name-uniqueness)bakın.
* Örnek söyleylerdeki Etiketler, alt varlıklar ile yeni makine öğrenimi varlığına geçiş yaptı.

Modelinizin değişikliğini anlamak için aşağıdaki grafiği kullanın:

|Eski nesne|Yeni nesne|Notlar|
|--|--|--|
|Bileşik varlık|yapıda makine öğrenimi varlığı|Her iki nesne de üst nesnelerdir.|
|Kompozit 'ın alt varlığı **basit bir varlıktır**|alt varlık|Her iki nesne de alt nesnelerdir.|
|Kompozit 'ın alt varlığı, sayı gibi **önceden oluşturulmuş bir varlıktır**|Sayı gibi önceden oluşturulmuş varlık adına sahip alt varlık ve alt varlık, kısıtlama seçeneği _true_ olarak ayarlanan önceden oluşturulmuş sayı varlığı _özelliğine_ sahiptir.|alt varlık, alt varlık düzeyinde kısıtlaması olan özelliği içerir.|
|Kompozit 'ın alt varlığı sayı gibi **önceden oluşturulmuş varlıktır** ve önceden oluşturulmuş varlık bir **role** sahiptir|rol adına sahip alt varlık ve alt varlık, kısıtlama seçeneği true olarak ayarlanan önceden oluşturulmuş sayı varlığı özelliğine sahiptir.|alt varlık, alt varlık düzeyinde kısıtlaması olan özelliği içerir.|
|Rol|alt varlık|Rol adı, alt varlık adı olur. Alt varlık, makine öğrenimi varlığının doğrudan bir alt öğesi.|

## <a name="begin-upgrade-process"></a>Yükseltme işlemini Başlat

Güncelleştirme yapmadan önce şunları yaptığınızdan emin olun:

* Yükseltmek için doğru sürümde değilseniz sürümü değiştirin


1. Bildirimden yükseltme işlemini başlatın veya bir sonraki zamanlanmış komut istemine kadar bekleyebilirsiniz.

    > [!div class="mx-imgBorder"]
    > ![Bildirimleri yükseltmeye başla](./media/update-composite-entity/notification-begin-update.png)

1. Açılır pencerede **Şimdi Yükselt**' i seçin.

1. Bilgileri **yükselttiğinizde neler olduğunu** gözden geçirin ve ardından **devam**' ı seçin.

1. Yükseltmek için listeden bileşik varlıkları seçin ve ardından **devam**' ı seçin.

1. Etkin olmayan tüm değişiklikleri, onay kutusunu seçerek geçerli sürümden yükseltilen sürüme taşıyabilirsiniz.

1. Yükseltme işlemine başlamak için **devam** ' ı seçin.

1. İlerleme çubuğu, yükseltme işleminin durumunu gösterir.

1. İşlem tamamlandığında, yeni makine öğrenimi varlıkları ile yeni bir eğitilen sürümü görürsünüz. Yeni varlığı görmek için **Yeni varlıklarınızı deneyin** ' i seçin.

    Yeni varlık için yükseltme veya eğitim başarısız olursa, bir bildirim daha fazla bilgi sağlar.

1. Varlık listesi sayfasında, yeni varlıklar tür adının yanında **Yeni** ile işaretlenir.

## <a name="next-steps"></a>Sonraki adımlar

* [Yazarlar ve ortak çalışanlar](luis-how-to-collaborate.md)