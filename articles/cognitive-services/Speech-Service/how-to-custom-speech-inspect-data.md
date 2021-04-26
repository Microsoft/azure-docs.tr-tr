---
title: Özel Konuşma Tanıma konuşma hizmeti için veri kalitesini İnceleme
titleSuffix: Azure Cognitive Services
description: Özel Konuşma Tanıma, ses verilerini karşılık gelen tanıma sonucuyla karşılaştırarak bir modelin tanınma kalitesini görsel olarak incelemenize olanak sağlayan araçlar sağlar. Karşıya yüklenen sesi kayıttan yürütebilir ve belirtilen tanıma sonucunun doğru olup olmadığını belirleyebilirsiniz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: erhopf
ms.openlocfilehash: 9ce0d3a06846cbc3aa37ab836564150e6f2c34ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100362820"
---
# <a name="inspect-custom-speech-data"></a>Özel Konuşma verilerini inceleme

> [!NOTE]
> Bu sayfa, [özel konuşma tanıma için test verilerini hazırlama](./how-to-custom-speech-test-and-train.md) okuduğunuzu ve İnceleme için bir veri kümesi yüklediğinizi varsayar.

Özel Konuşma Tanıma, ses verilerini karşılık gelen tanıma sonucuyla karşılaştırarak bir modelin tanınma kalitesini görsel olarak incelemenize olanak sağlayan araçlar sağlar. [Konuşma Studio](https://speech.microsoft.com/customspeech)'dan karşıya yüklenen sesi kayıttan yürütebilir ve belirtilen tanıma sonucunun doğru olup olmadığını belirleyebilirsiniz. Bu araç, Microsoft 'un temel konuşma konuşmadan metin modelinin kalitesini incelemenize, eğitilen özel bir modeli incelemenize veya iki model ile döküm karşılaştırmanıza yardımcı olur.

Bu belgede, Microsoft 'un temel konuşma konuşmadan metin modelinin ve/veya eğitilen özel modellerin kalitesini görsel olarak incelemeyi öğreneceksiniz. Ayrıca, etiketli ses veri kümelerini oluşturmak ve iyileştirmek için çevrimiçi döküm Düzenleyicisi 'ni nasıl kullanacağınızı öğreneceksiniz.

## <a name="create-a-test"></a>Test oluşturma

Bir test oluşturmak için aşağıdaki yönergeleri izleyin:

1. [Konuşma Studio](https://speech.microsoft.com/customspeech)'da oturum açın.
2. **Test > > [proje adı] Özel Konuşma Tanıma > konuşmadan metne** gidin.
3. **Test Ekle**' ye tıklayın.
4. **İnceleme kalitesi (yalnızca ses verileri)** seçeneğini belirleyin. Teste bir ad, açıklama verin ve ses veri kümenizi seçin.
5. Test etmek istediğiniz en fazla iki model seçin.
6. **Oluştur**’a tıklayın.

Bir test başarıyla oluşturulduktan sonra, bir modelin belirttiğiniz ses veri kümesini nasıl oluşturduğunu veya iki modelden gelen sonuçları yan yana nasıl karşılaştırabileceğiniz hakkında bilgi alabilirsiniz.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Yan yana model karşılaştırmaları

Test durumu _başarılı_ olduğunda testin ayrıntılarını görmek için test öğesi adına tıklayın. Bu ayrıntı sayfasında, veri kümenizdeki tüm bildirimler listelenir ve karşılaştırdığınız iki modelin tanınma sonuçları gösterilir.

Yan yana karşılaştırmayı incelemeye yardımcı olması için ekleme, silme ve değiştirme gibi çeşitli hata türlerini değiştirebilirsiniz. Her sütunda ses dinlemek ve tanıma sonuçlarını karşılaştırmak yoluyla (insan tarafından etiketlenmiş bir konum ve iki konuşmadan metne ait sonuçları gösterir), hangi modelin ihtiyaçlarınıza uygun olduğunu ve iyileştirmelerin gerekli olduğunu belirtebilirsiniz.

Yan yana model testi, bir uygulama için en uygun konuşma tanıma modelini doğrulamak için kullanışlıdır. Bir doğruluğun bir amaç ölçüsü için, daha fazla ses kullanılmasını gerektiren [doğruluğu değerlendir](how-to-custom-speech-evaluate-data.md)bölümünde bulunan yönergeleri izleyin.

## <a name="online-transcription-editor"></a>Çevrimiçi döküm Düzenleyicisi

Çevrimiçi döküm Düzenleyicisi, Özel Konuşma Tanıma ' de ses dökümlerini kolayca çalışmanıza olanak sağlar. Düzenleyicinin ana kullanım durumları şunlardır: 

* Yalnızca ses verileriniz var, ancak model eğitiminde kullanılacak doğru ses ve insan etiketli veri kümelerini sıfırdan oluşturmak istiyorsunuz.
* Ses ve insan etiketli veri kümeleriniz zaten var, ancak bu durumda hatalar veya hatalar bulunuyor. Düzenleyici, en iyi eğitim doğruluğunu elde etmek için dökümü hızlı bir şekilde değiştirmenize olanak sağlar.

Döküm düzenleyicisini kullanmaya yönelik tek gereksinim, ses verilerinin karşıya yüklenmesini (yalnızca ses veya ses + döküm) sağlar.

### <a name="import-datasets-to-editor"></a>Veri kümelerini düzenleyiciye aktar

Verileri düzenleyiciye aktarmak için öncelikle **Özel Konuşma Tanıma > [projeniz] > düzenleyiciye** gidin.

![Düzenleyici sekmesi](media/custom-speech/custom-speech-editor-detail.png)

Ardından, verileri içeri aktarmak için aşağıdaki adımları kullanın.

1. **Verileri Içeri aktar** ' a tıklayın
1. Yeni bir veri kümesi oluşturun ve buna bir açıklama verin
1. Veri kümelerini seçin. Çoklu seçimler desteklenir ve ses ve insan etiketli verileri de yalnızca ses verileri ' ni seçebilirsiniz.
1. Yalnızca ses verileri için isteğe bağlı olarak, düzenleyiciye aktardıktan sonra makine dökümünü otomatik olarak oluşturmak üzere varsayılan modelleri kullanabilirsiniz
1. **Içeri aktar** ' a tıklayın

Veriler başarıyla alındıktan sonra veri kümelerine tıklayıp Düzenle ' ye tıklayabilirsiniz.

> [!TIP]
> Veri kümelerini seçerek ve **düzenleyiciye dışarı aktar** ' a tıklayarak veri kümelerini düzenleyiciye doğrudan de aktarabilirsiniz

### <a name="edit-transcription-by-listening-to-audio"></a>Sesi dinleyerek dökümü düzenleme

Karşıya veri yükleme başarılı olduktan sonra, verilerin ayrıntılarını görmek için her öğe adına tıklayın. Ayrıca, bir dosya arasında ilerlemek için **önceki** ve **İleri** ' ye de kullanabilirsiniz.

Ayrıntı sayfası, her ses dosyasındaki tüm kesimleri listeler ve istediğiniz şekilde tıklayabilirsiniz. Her bir söylük için, her türlü ekleme, silme veya değiştirme hatası bulursanız, sesi kayıttan yürütebilir ve dökümü inceleyebilir ve bu değişiklikleri düzenleyebilirsiniz. Hata türleri hakkında daha ayrıntılı bilgi için bkz. [veri değerlendirmesi nasıl yapılır](how-to-custom-speech-evaluate-data.md) .

![Düzenleyici sayfası](media/custom-speech/custom-speech-editor.png)

Düzenleme yaptıktan sonra **Kaydet** düğmesine tıklayın.

### <a name="export-datasets-from-the-editor"></a>Veri kümelerini düzenleyiciden dışarı aktarma

**Veri kümelerini veri** sekmesine geri aktarmak için veri ayrıntıları sayfasına gidin ve **dışarı aktar** düğmesine tıklayarak tüm dosyaları yeni bir veri kümesi olarak dışarı aktarın. Ayrıca, istenen dosyaları kısmen seçmek için dosyaları son düzenleme zamanına, ses sürelerine vb. göre de filtreleyebilirsiniz. 

![Verileri dışarı aktarma](media/custom-speech/custom-speech-editor-export.png)

Verilere aktarılmış dosyalar, yepyeni bir veri kümesi olarak kullanılır ve mevcut veri/eğitim/test varlıklarını etkilemez.

## <a name="next-steps"></a>Sonraki adımlar

- [Verilerinizi değerlendirin](how-to-custom-speech-evaluate-data.md)
- [Modelinizi eğitme](how-to-custom-speech-train-model.md)
- [Modelinizi geliştirme](./how-to-custom-speech-evaluate-data.md)
- [Modelinizi dağıtın](./how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Ek kaynaklar

- [Özel Konuşma Tanıma için test verilerini hazırlama](./how-to-custom-speech-test-and-train.md)