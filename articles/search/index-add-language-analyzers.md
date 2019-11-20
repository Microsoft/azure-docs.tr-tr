---
title: Dize alanlarına dil Çözümleyicileri ekleme
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de Ingilizce olmayan sorgular ve dizinler için çok dilli sözlü metin analizi.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: ebdbcdda4efd7fdf9eb0e3e04cfa4d1987e03716
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111816"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Azure Bilişsel Arama dizinindeki dize alanlarına dil Çözümleyicileri ekleme

*Dil çözümleyici* , hedef dilin dil kurallarını kullanarak sözlü analiz gerçekleştiren belirli bir [metin Çözümleyicisi](search-analyzers.md) türüdür. Aranabilir her alanın bir **çözümleyici** özelliği vardır. Dizininiz, Ingilizce ve Çince metin için ayrı alanlar gibi çevrilmiş dizeler içeriyorsa, bu çözümleyicilerin zengin dil özelliklerine erişmek için her bir alanda dil Çözümleyicileri belirtebilirsiniz.  

Azure Bilişsel Arama, Lucene tarafından desteklenen 35 Çözümleyicileri ve Office ve Bing 'de kullanılan özel Microsoft doğal dil işleme teknolojisi tarafından desteklenen 50 Çözümleyicileri destekler.

## <a name="comparing-analyzers"></a>Çözümleyiciler karşılaştırılıyor

Bazı geliştiriciler, Lucene 'in daha tanıdık, basit ve açık kaynaklı çözümünü tercih edebilir. Lucene dil Çözümleyicileri daha hızlıdır, ancak Microsoft Çözümleyicileri, Microsoft çözümleyicilerinin (Almanya, Danca, Felemenkçe, Isveççe, Norveççe, Estonca, son, Macarca, Slovakça) ve varlık gibi gelişmiş özellikleri vardır. tanıma (URL 'Ler, e-postalar, tarihler, sayılar). Mümkünse, hangisinin daha iyi bir şekilde uygun olduğuna karar vermek için hem Microsoft hem de Lucene Çözümleyicileri için karşılaştırmalar çalıştırmalısınız. 

Microsoft Çözümleyicileri ile dizin oluşturma, dile bağlı olarak, Lucene eşdeğerlerine kıyasla ortalama iki ile üç kat daha yavaştır. Ara performans, ortalama boyut sorgularında önemli ölçüde etkilenmemelidir. 

### <a name="english-analyzers"></a>İngilizce çözümleyiciler

Varsayılan çözümleyici, Ingilizce 'nin yanı sıra Lucene 'in Ingilizce Çözümleyicisi veya Microsoft 'un Ingilizce Çözümleyicisi için iyi bir şekilde çalışacak standart Lucene ' dir. 
 
+ Lucene 'ın Ingilizce Çözümleyicisi standart çözümleyici 'yi genişletiyor. Sözcüklerdeki iyelik (sondaki) ' ı kaldırır, her Porter için sözcük kökü karartma algoritması olarak sözcük kökü ayırmayı uygular ve Ingilizce durdurma sözcüklerini kaldırır.  

+ Microsoft 'un Ingilizce Çözümleyicisi, sözcük kökü ayırmayı yerine katileştirme gerçekleştirir. Bu, daha fazla ilgili arama sonuçlarına göre daha iyi ve düzensiz Word formlarını işleyebileceği anlamına gelir. 

## <a name="configuring-analyzers"></a>Çözümleyicileri yapılandırma

Dil Çözümleyicileri olduğu gibi kullanılır. Dizin tanımındaki her bir alan için, **çözümleyici** özelliğini dili ve Linguistics yığınını (Microsoft veya Lucene) belirten bir çözümleyici adı olarak ayarlayabilirsiniz. Aynı çözümleyici, bu alan için dizin oluştururken ve aranırken de uygulanır. Örneğin, aynı dizinde yan yana bulunan Ingilizce, Fransızca ve Ispanyolca otel açıklamaları için ayrı alanlara sahip olabilirsiniz. Alternatif olarak, **çözümleyici**yerine **ındexanalyzer** ve **searchAnalyzer** kullanarak dizin oluşturma sırasında ve sorgu sırasında farklı analiz kurallarına sahip olabilirsiniz. 

Sorgularınızda aranacak dile özgü alanı belirtmek için **Searchfields** sorgu parametresini kullanın. [Arama belgelerinde](https://docs.microsoft.com/rest/api/searchservice/search-documents)çözümleyici özelliğini içeren sorgu örneklerini inceleyebilirsiniz. 

Dizin özellikleri hakkında daha fazla bilgi için bkz. [Create &#40;ındex Azure bilişsel arama&#41;REST API](https://docs.microsoft.com/rest/api/searchservice/create-index). Azure Bilişsel Arama 'de analiz hakkında daha fazla bilgi için bkz. [azure bilişsel arama 'de çözümleyiciler](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Dil Çözümleyicisi listesi 
 Aşağıda, Lucene ve Microsoft çözümleyici adlarıyla birlikte desteklenen dillerin listesi verilmiştir.  

|Dil|Microsoft Çözümleyicisi adı|Lucene çözümleyici adı|  
|--------------|-----------------------------|--------------------------|  
|Arapça|ar. Microsoft|ar. Lucene|  
|Ermenice||HY. Lucene|  
|Bangla|milyar TL. Microsoft||  
|Bask dili||AB. Lucene|  
|Bulgarca|bg. Microsoft|bg. Lucene|  
|Katalanca|CA. Microsoft|CA. Lucene|  
|Basitleştirilmiş Çince|zh-Hans. Microsoft|zh-Hans. Lucene|  
|Geleneksel Çince|zh-Hant. Microsoft|zh-Hant. Lucene|  
|Hırvatça|HR. Microsoft||  
|Çekçe|CS. Microsoft|CS. Lucene|  
|Danca|da. Microsoft|da. Lucene|  
|Felemenkçe|nl. Microsoft|nl. Lucene|  
|Türkçe|en. Microsoft|en. Lucene|  
|Estonca|et.microsoft||  
|Fince|Fi. Microsoft|Fi. Lucene|  
|Fransızca|fr. Microsoft|fr. Lucene|  
|Galiçya dili||GL. Lucene|  
|Almanca|de.microsoft|de. Lucene|  
|Yunanca|El. Microsoft|El. Lucene|  
|Gucerat dili|Gu. Microsoft||  
|İbranice|BT. Microsoft||  
|Hintçe|Merhaba. Microsoft|Merhaba. Lucene|  
|Macarca|Hu. Microsoft|Hu. Lucene|  
|İzlanda dili|. Microsoft||  
|Endonezya dili (Bahasa)|kimliği. Microsoft|ID. Lucene|  
|İrlanda dili||ga. Lucene|  
|İtalyanca|BT. Microsoft|BT. Lucene|  
|Japonca|Ja. Microsoft|Ja. Lucene|  
|Kannada dili|KN. Microsoft||  
|Korece|Ko. Microsoft|Ko. Lucene|  
|Letonca|LV. Microsoft|LV. Lucene|  
|Litvanca|lt. Microsoft||  
|Malayalam dili|ml. Microsoft||  
|Malay dili (Latin)|MS. Microsoft||  
|Marathi|Mr. Microsoft||  
|Norveççe|NB. Microsoft|Hayır. Lucene|  
|Farsça||FA. Lucene|  
|Lehçe|pl. Microsoft|pl. Lucene|  
|Portekizce (Brezilya)|pt-br. Microsoft|pt-br. Lucene|  
|Portekizce (Portekiz)|PT-PT. Microsoft|PT-PT. Lucene|  
|Pencap dili|PA. Microsoft||  
|Rumence|ro. Microsoft|ro. Lucene|  
|Rusça|ru. Microsoft|ru. Lucene|  
|Sırpça (Kiril)|SR-Kiril. Microsoft||  
|Sırpça (Latin)|SR-Latin. Microsoft||  
|Slovakça|SK. Microsoft||  
|Slovence|SL. Microsoft||  
|İspanyolca|es. Microsoft|es. Lucene|  
|İsveççe|ZF. Microsoft|ZF. Lucene|  
|Tamil dili|ta. Microsoft||  
|Telugu dili|te. Microsoft||  
|Tay Dili|TH. Microsoft|TH. Lucene|  
|Türkçe|tr. Microsoft|tr. Lucene|  
|Ukrayna dili|UK. Microsoft||  
|Urduca|. Microsoft||  
|Vietnam dili|Vi. Microsoft||  

 **Lucene** ile açıklama eklenmiş adlara sahip tüm çözümleyiciler [Apache Lucene 'in dil Çözümleyicileri](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )tarafından desteklenir.

## <a name="see-also"></a>Ayrıca bkz.  

+ [Dizin &#40;oluşturma Azure bilişsel arama REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [Analiz Zername sınıfı](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

