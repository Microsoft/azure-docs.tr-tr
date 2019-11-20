---
title: Dizin oluşturma sırasında yerleşik metin ve görüntü işleme
titleSuffix: Azure Cognitive Search
description: Veri ayıklama, doğal dil, görüntü işleme bilişsel becerileri, Azure Bilişsel Arama işlem hattındaki ham içeriğe semantik ve yapı ekler.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f419b19b576ebf6f7f853b41128908487f047df6
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113367"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Dizin oluşturma sırasında metin ve görüntü işleme için yerleşik bilişsel yetenekler (Azure Bilişsel Arama)

Bu makalede, Azure Bilişsel Arama ile sunulan bilişsel yetenekler hakkında bilgi edinirsiniz ve içeriği ve yapıyı ayıklamak için bir beceri ekleyebilirsiniz. Bilişsel *beceri* , içeriği bir şekilde dönüştüren bir modül veya işlemdir. Genellikle, veri veya devlet yapısını çıkaran ve bu nedenle giriş verilerini anlamamızı genişlettiğini sağlayan bir bileşendir. Neredeyse her zaman çıktı metin tabanlıdır. *Beceri* , enzenginleştirme işlem hattını tanımlayan yeteneklerin koleksiyonudur. 

> [!NOTE]
> İşlem sıklığını artırarak, daha fazla belge ekleyerek veya daha fazla AI algoritması ekleyerek kapsamı genişlettikten sonra faturalandırılabilir bilişsel [Hizmetler kaynağı](cognitive-search-attach-cognitive-services.md)eklemeniz gerekir. Bilişsel hizmetlerde API 'Leri çağırırken ve Azure Bilişsel Arama belge çözme aşamasının bir parçası olarak görüntü ayıklama için ücretler tahakkuk eder. Belgelerden metin ayıklama için herhangi bir ücret alınmaz.
>
> Yerleşik yeteneklerin yürütülmesi, mevcut bilişsel [Hizmetler Kullandıkça Öde fiyatı](https://azure.microsoft.com/pricing/details/cognitive-services/)üzerinden ücretlendirilir. Görüntü ayıklama fiyatlandırması, [Azure bilişsel arama fiyatlandırma sayfasında](https://go.microsoft.com/fwlink/?linkid=2042400)açıklanmaktadır.


## <a name="built-in-skills"></a>Yerleşik yetenekler

Birçok beceri, tüketildikleri veya ürettikleri şekilde esnektir. Genellikle, çoğu beceri önceden eğitilen modelleri temel alır. Bu, modeli kendi eğitim verilerinizi kullanarak eğitemeyeceğiniz anlamına gelir. Aşağıdaki tabloda Microsoft tarafından sunulan yetenekler numaralandırılır ve açıklanmaktadır. 

| İmde | Açıklama |
|-------|-------------|
| [Microsoft. yetenekler. Text. KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Bu beceri, terim yerleştirme, dil kuralları, diğer koşullara yakınlık ve dönem kaynak veride ne kadar olağandışı olduğunu temel alan önemli tümcecikleri algılamak için önceden eğitilen bir model kullanır. |
| [Microsoft. yetenekler. Text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Bu beceri, hangi dilin kullanıldığını (belge başına bir dil KIMLIĞI) algılamak için önceden eğitilen bir model kullanır. Aynı metin kesimlerinde birden çok dil kullanıldığında çıktı, ağırlıklı kullanılan dilin LCıD 'si olur.|
| [Microsoft. yetenekler. Text. Mergeskıll](cognitive-search-skill-textmerger.md) | Bir alan koleksiyonundan metni tek bir alanda birleştirir.  |
| [Microsoft. yetenekler. Text. Entityrecognitionbeceri](cognitive-search-skill-entity-recognition.md) | Bu beceri, sabit bir kategori kümesi için varlık oluşturmak üzere önceden eğitilen bir model kullanır: kişiler, konum, kuruluş, e-postalar, URL 'Ler, tarih saat alanları. |
| [Microsoft. yetenekler. Text. Sentimentbecerisi](cognitive-search-skill-sentiment.md)  | Bu beceri, kayıt temelinde bir kayıtta olumlu veya olumsuz bir yaklaşım almak için önceden eğitilen bir model kullanır. Puan 0 ile 1 arasındadır. Bağımsız puanlar, yaklaşım algılanmadığında hem null durum hem de nötr olarak kabul edilen metin için oluşur.  |
| [Microsoft. yetenekler. Text. Splitbeceri](cognitive-search-skill-textsplit.md) | İçeriği artımlı olarak zenginleştirmek veya genişletmek için metni sayfalara böler. |
| [Microsoft. yetenekler. Text. Translationbeceri](cognitive-search-skill-text-translation.md) | Bu beceri, normalleştirmeyi veya yerelleştirme kullanım örnekleri için giriş metnini çeşitli dillerde dönüştürmek üzere önceden eğitilen bir model kullanır. |
| [Microsoft. yetenekler. Vision. ımageanalysisbeceri](cognitive-search-skill-image-analysis.md) | Bu beceri bir görüntünün içeriğini tanımlamak ve bir metin açıklaması oluşturmak için bir görüntü algılama algoritması kullanır. |
| [Microsoft. yetenekler. Vision. Ocrbeceri](cognitive-search-skill-ocr.md) | Optik karakter tanıma. |
| [Microsoft. yetenekler. util. Conditionalbeceri](cognitive-search-skill-conditional.md) | Filtrelemeye, varsayılan bir değere atamaya ve verileri bir koşula göre birleştirmeye izin verir.|
| [Microsoft. yetenekler. util. Belgetextractionbeceri](cognitive-search-skill-document-extraction.md) | Zenginleştirme işlem hattının içindeki bir dosyadan içerik ayıklar. |
| [Microsoft. yetenekler. util. Shaperbeceri](cognitive-search-skill-shaper.md) | Çıktıyı bir karmaşık türe eşler (bir tam ad, çok satırlı bir adres veya soyadı ve bir kişisel tanımlayıcı birleşimi için kullanılabilen çok parçalı veri türü). |
| [Microsoft. yetenekler. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md) | Özel bir Web API 'sine HTTP çağrısı yaparak bir AI zenginleştirme işlem hattının genişletilebilirliğini sağlar |


[Özel bir yetenek](cognitive-search-custom-skill-web-api.md)oluşturma hakkında yönergeler için bkz. [özel bir arabirim tanımlama](cognitive-search-custom-skill-interface.md) ve [örnek: AI zenginleştirme için özel bir yetenek oluşturma](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Ayrıca bkz.

+ [Beceri tanımlama](cognitive-search-defining-skillset.md)
+ [Özel yetenekler arabirim tanımı](cognitive-search-custom-skill-interface.md)
+ [Öğretici: AI ile zenginleştirilmiş dizin oluşturma](cognitive-search-tutorial-blob.md)
