---
title: Akademik Bilgi API'si nedir?
titlesuffix: Azure Cognitive Services
description: Akademik Bilgi API'sini kullanarak kullanıcı sorgularını yorumlayabilir ve Academic Graph'ten zengin bilgiler alabilirsiniz.
services: cognitive-services
author: darrine
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: overview
ms.date: 10/30/2018
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 305d73ba4b1b955b731e34ee5da0b1dced6acddc
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704979"
---
# <a name="academic-knowledge-api"></a>Akademik Bilgi API'si

Akademik Bilgi API'sine hoş geldiniz. Bu hizmet ile kullanıcı sorgularındaki akademik amacı yorumlayabilir ve Microsoft Academic Graph’ten (MAG) buna uygun kapsamlı bilgiler getirebilirsiniz. MAG bilgi bankası, akademik faaliyetleri modelleyen varlıklardan oluşan web ölçeğinde heterojen bir varlık grafıdır: çalışma alanı, yazar, kurum, inceleme, mekan ve olay. 

MAG verileri Bing web dizinine ek olarak Bing tarafından sağlanan şirket içi bir bilgi bankasından toplanır. Bing tarafında dizine ekleme işlemleri sürekli devam ettiğinden bu API'de Bing tarafından yeni keşfedilen ve dizine eklenen Web sayfalarından alınan güncel bilgiler bulunacaktır. Akademik Bilgi API'si bu veri kümesini temel alarak belirli yayınlar ve ilgili varlıklar için reaktif aramayı proaktif öneri deneyimleriyle, zengin araştırma grafı arama sonuçlarıyla ve öznitelik değerlerinin histogram dağıtımlarıyla sorunsuz bir şekilde birleştiren bilgi tabanlı ve etkileşimli bir diyalog sunar.

Microsoft Academic Graph hakkında daha fazla bilgi için bkz. [https://aka.ms/academicgraph](https://aka.ms/academicgraph).

Akademik Bilgi API'si, Bilişsel Hizmetler Önizleme aşamasından Bilişsel Hizmetler Laboratuvarları aşamasına geçmiştir. Proje için yeni ana sayfa: [https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge). Var olan API anahtarınız 24 Mayıs 2018 tarihine kadar çalışmaya devam edecektir. Bu tarihten sonra lütfen yeni bir API anahtarı oluşturun. Var olan anahtarınızın kullanım süresi sona erdikten sonra ücretli önizlemenin kullanım dışı olacağını lütfen unutmayın. Ücretsiz API katmanı çalışmalarınız için yeterli değilse lütfen ekibimizle iletişime geçin. 

## <a name="features"></a>Özellikler
Akademik Bilgi API'si dört ilgili REST uç noktasından oluşur:  
  1. **interpret** – Doğal dildeki bir kullanıcı sorgu dizesini yorumlar. Ek açıklamalı yorumlar döndürerek, kullanıcının ne yazmakta olduğunun tahmin edildiği zengin bir arama kutusu otomatik tamamlama deneyimi sağlar.  
  2. **evaluate** – Bir sorgu ifadesini değerlendirir ve Akademik Bilgi varlık sonuçları döndürür.  
  3. **calchistogram** – Bir sorgu ifadesi tarafından döndürülen akademik varlıklara ait öznitelik değerleri dağılımının histogramını hesaplar. Örneğin, belirli bir yazar için yıla göre alıntıların dağılımı hesaplanabilir.  
  
Bu API metotları birlikte kullanıldığında zengin bir anlamsal arama deneyimi oluşturmanızı sağlar. Bir kullanıcı sorgusu belirtildiğinde **interpret** metodu sorgunun not eklenmiş sürümünü ve yapılandırılmış bir sorgu ifadesi sunarken kullanıcı sorgusunu isteğe bağlı olarak arka plandaki akademik verilerin anlamsal özelliklerine göre tamamlar. Örneği bir kullanıcı *latent s* dizesini yazarsa **interpret** metodu bir dizi derecelendirilmiş yorumlama sağlayarak kullanıcının *latent semantic analysis* alanında, *latent structure analysis* yayınını veya *latent s* ile başlayan diğer varlık ifadelerini kapsayan bir arama yapmak istiyor olabileceğini önerir. Bu bilgiler kullanıcıyı hızla istenen arama sonuçlarına ulaştırmak için kullanılabilir.

**evaluate** metodu, akademik bilgi bankasından eşleşen yayın varlıklarını almak için, **calchistogram** metodu ise, arama sonuçlarına filtre uygulamak için bir dizi yayın varlığının öznitelik değerlerinin dağılımını hesaplamak için kullanılabilir.        

## <a name="getting-started"></a>Başlarken 
Ayrıntılı belgeler için lütfen sol taraftaki alt konu başlıklarına bakın.  Örneklerin daha kolay okunmasını sağlamak için REST API çağrılarında URL'de kodlanmamış olan karakterlerin (boşluk gibi) bulunduğuna dikkat edin.  Kodunuzda uygun URL kodlama işlemlerini gerçekleştirmeniz gerekir.
