---
title: Dize alanlarına dil Çözümleyicileri ekleme
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de Ingilizce olmayan sorgular ve dizinler için çok dilli sözlü analizler.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: ac11b7bc7e53c214f872d400565d50009479afcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604432"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Azure Bilişsel Arama dizinindeki dize alanlarına dil Çözümleyicileri ekleme

*Dil çözümleyici* , hedef dilin dil kurallarını kullanarak sözlü analiz gerçekleştiren belirli bir [metin Çözümleyicisi](search-analyzers.md) türüdür. Aranabilir her alanın bir **çözümleyici** özelliği vardır. İçeriğiniz, Ingilizce ve Çince metin için ayrı alanlar gibi çevrilmiş dizelerinden oluşuyorsa, her bir alanda bu çözümleyiciler için zengin dil özelliklerine erişmek üzere dil Çözümleyicileri belirtebilirsiniz.

## <a name="when-to-use-a-language-analyzer"></a>Dil Çözümleyicisi ne zaman kullanılır?

Sözcük veya tümce yapısının bir değeri, metin ayrıştırmaya değer eklediğinde bir dil Çözümleyicisi göz önünde bulundurmanız gerekir. Yaygın olarak kullanılan bir örnek, düzensiz fiil formlarının ("getir" ve "getirilen) ya da çoğul isimler (" fareler "ve" fare ") ilişkidir. Dil tanıma olmadan, bu dizeler tek başına fiziksel özelliklerle ayrıştırılır ve bu da bağlantıyı yakalayamaz. Büyük metin öbeklerinin bu içeriğe sahip olması daha olası olduğundan, açıklamalardan, incelemelerden veya özetlerden oluşan alanlar bir dil çözümleyici için iyi adaylardır.

Ayrıca, içerik Batı dili olmayan dizeler içeriyorsa dil Çözümleyicileri de göz önünde bulundurmanız gerekir. [Varsayılan çözümleyici](search-analyzers.md#default-analyzer) dilden bağımsız olsa da, dizeleri ayırmak için boşluk ve özel karakterler (tireler ve eğik çizgiler) kullanma kavramı, Batı dillerinin Batı dışındaki dillerde daha uygulanabilir. 

Örneğin, Çince, Japonca, Korece (ÇJK) ve diğer Asya dillerinde, bir boşluk bir sözcük sınırlayıcısı değildir. Aşağıdaki Japonca dizeyi göz önünde bulundurun. Boşluk içermediğinden, dilden bağımsız bir çözümleyici dizenin tamamını bir belirteç olarak analiz eder, aslında dize aslında bir tümceciktir.

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

Yukarıdaki örnekte, başarılı bir sorgunun tam belirteci içermesi veya bir sonek joker karakteri kullanan kısmi bir belirteç içermesi gerekir, bu, doğal olmayan ve sınırlı arama deneyimine neden olur.

Her bir kelime aramak için daha iyi bir deneyim vardır: 明るい (parlak), 私たちの (), 銀河系 (Galaxy). Bilişsel Arama bulunan Japonca çözümleyiciler kullanmanın, bu çözümleyicilerin metin öbeğini hedef dilde anlamlı sözcüklere bölmek daha iyi donatılmış olduğundan, bu davranışın kilidini açmak daha yüksektir.

## <a name="comparing-lucene-and-microsoft-analyzers"></a>Lucene ve Microsoft Çözümleyicileri karşılaştırması

Azure Bilişsel Arama, Lucene tarafından desteklenen 35 dil Çözümleyicileri ve Office ve Bing 'de kullanılan özel Microsoft doğal dil işleme teknolojisi tarafından desteklenen 50 dil Çözümleyicileri destekler.

Bazı geliştiriciler, Lucene 'in daha tanıdık, basit ve açık kaynaklı çözümünü tercih edebilir. Lucene dil Çözümleyicileri daha hızlıdır, ancak Microsoft Çözümleyicileri, Microsoft çözümleyicilerinin (Almanca, Danca, Felemenkçe, Isveççe, Norveççe, Estonca, son, Macarca, Slovakça) ve varlık tanıma (URL 'Ler, e-postalar, tarihler, sayılar) gibi gelişmiş özellikleri vardır. Mümkünse, hangisinin daha iyi bir şekilde uygun olduğuna karar vermek için hem Microsoft hem de Lucene Çözümleyicileri için karşılaştırmalar çalıştırmalısınız. 

Microsoft Çözümleyicileri ile dizin oluşturma, dile bağlı olarak, Lucene eşdeğerlerine kıyasla ortalama iki ile üç kat daha yavaştır. Ara performans, ortalama boyut sorgularında önemli ölçüde etkilenmemelidir. 

### <a name="english-analyzers"></a>İngilizce çözümleyiciler

Varsayılan çözümleyici, Ingilizce 'nin yanı sıra Lucene 'in Ingilizce Çözümleyicisi veya Microsoft 'un Ingilizce Çözümleyicisi için iyi bir şekilde çalışacak standart Lucene ' dir.

+ Lucene 'ın Ingilizce Çözümleyicisi standart çözümleyici 'yi genişletiyor. Sözcüklerdeki iyelik (sondaki) ' ı kaldırır, her Porter için sözcük kökü karartma algoritması olarak sözcük kökü ayırmayı uygular ve Ingilizce durdurma sözcüklerini kaldırır.  

+ Microsoft 'un Ingilizce Çözümleyicisi, sözcük kökü ayırmayı yerine katileştirme gerçekleştirir. Bu, daha fazla ilgili arama sonuçlarıyla sonuçlanarak ve düzensiz Word formlarının çok daha iyi işleyebileceği anlamına gelir 

## <a name="how-to-specify-a-language-analyzer"></a>Dil Çözümleyicisi belirtme

Alan tanımı sırasında Edm. String türünde "aranabilir" alanlarda bir dil Çözümleyicisi ayarlayın.

Alan tanımlarında, çözümleyici ile ilgili birkaç özellik olsa da, dil Çözümleyicileri için yalnızca "çözümleyici" özelliği kullanılabilir. "Çözümleyici" değeri, destek Çözümleyicileri listesinden dil çözümleyicilerinin biri olmalıdır.

```json
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
```

Dizin oluşturma ve alan özelliklerini ayarlama hakkında daha fazla bilgi için bkz. [Dizin oluşturma (REST)](/rest/api/searchservice/create-index). Metin analizi hakkında daha fazla bilgi için bkz. [Azure bilişsel arama Çözümleyicileri](search-analyzers.md).

<a name="language-analyzer-list"></a>

## <a name="supported-language-analyzers"></a>Desteklenen dil Çözümleyicileri

 Aşağıda, Lucene ve Microsoft çözümleyici adlarıyla desteklenen dillerin listesi verilmiştir.  

| Dil | Microsoft Çözümleyicisi adı | Lucene çözümleyici adı |
|----------|-------------------------|----------------------|
| Arapça   | ar. Microsoft | ar. Lucene |
| Ermenice |           | HY. Lucene |
| Bangla   | milyar TL. Microsoft |  |
| Baskça   |  | AB. Lucene |
| Bulgarca | bg. Microsoft | bg. Lucene |
| Katalanca  | CA. Microsoft | CA. Lucene |
| Basitleştirilmiş Çince | zh-Hans. Microsoft | zh-Hans. Lucene |
| Geleneksel Çince | zh-Hant. Microsoft | zh-Hant. Lucene |
| Hırvatça | HR. Microsoft |  |
| Çekçe | CS. Microsoft | CS. Lucene |
| Danca | da. Microsoft | da. Lucene |
| Felemenkçe | nl. Microsoft | nl. Lucene |
| İngilizce | en. Microsoft | en. Lucene |
| Estonya Dili | et. Microsoft |  |
| Fince | Fi. Microsoft | Fi. Lucene |
| Fransızca | fr. Microsoft | fr. Lucene |
| Galiçya Dili |  | GL. Lucene |
| Almanca | de Microsoft | de. Lucene |
| Yunanca | El. Microsoft | El. Lucene |
| Gucerat dili | Gu. Microsoft |  |
| İbranice | BT. Microsoft |  |
| Hintçe | Merhaba. Microsoft | Merhaba. Lucene |
| Macarca | Hu. Microsoft | Hu. Lucene |
| İzlandaca | . Microsoft |  |
| Endonezya dili (Bahasa) | kimliği. Microsoft | ID. Lucene |
| İrlandaca |  | ga. Lucene |
| İtalyanca | BT. Microsoft | BT. Lucene |
| Japonca | Ja. Microsoft | Ja. Lucene |
| Kannada dili | KN. Microsoft |  |
| Korece | Ko. Microsoft | Ko. Lucene |
| Letonca | LV. Microsoft | LV. Lucene |
| Litvanca | lt. Microsoft |  |
| Malayalam dili | ml. Microsoft |  |
| Malay dili (Latin) | MS. Microsoft |  |
| Marathi | Mr. Microsoft |  |
| Norveççe | NB. Microsoft | Hayır. Lucene |
| Farsça |  | FA. Lucene |
| Lehçe | pl. Microsoft | pl. Lucene |
| Portekizce (Brezilya) | pt-br. Microsoft | pt-br. Lucene |
| Portekizce (Portekiz) | PT-PT. Microsoft | PT-PT. Lucene |
| Pencap dili | PA. Microsoft |  |
| Rumence | ro. Microsoft | ro. Lucene |
| Rusça | ru. Microsoft | ru. Lucene |
| Sırpça (Kiril) | SR-Kiril. Microsoft |  |
| Sırpça (Latin) | SR-Latin. Microsoft |  |
| Slovakça | SK. Microsoft |  |
| Slovence | SL. Microsoft |  |
| İspanyolca | es. Microsoft | es. Lucene |
| İsveççe | ZF. Microsoft | ZF. Lucene |
| Tamil dili | ta. Microsoft |  |
| Telugu dili | te. Microsoft |  |
| Tayca | TH. Microsoft | TH. Lucene |
| Türkçe | tr. Microsoft | tr. Lucene |
| Ukraynaca | UK. Microsoft |  |
| Urduca | . Microsoft |  |
| Vietnamca | Vi. Microsoft |  |

 **Lucene** ile açıklama eklenmiş adlara sahip tüm çözümleyiciler [Apache Lucene 'in dil Çözümleyicileri](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )tarafından desteklenir.

## <a name="see-also"></a>Ayrıca bkz.  

+ [Dizin oluşturma](search-what-is-an-index.md)
+ [Çok dilli bir dizin oluşturma](search-language-support.md)
+ [Dizin Oluştur (REST API)](/rest/api/searchservice/create-index)  
+ [LexicalAnalyzerName sınıfı (.NET için Azure SDK)](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername)