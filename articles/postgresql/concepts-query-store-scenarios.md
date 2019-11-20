---
title: PostgreSQL için Azure veritabanı 'nda sorgu deposu kullanım senaryoları-tek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı-tek sunucu 'da sorgu deposu için bazı senaryolar açıklanmaktadır.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3cdb0d4e00e667b0369cdf612662830f18dc5fb8
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764268"
---
# <a name="usage-scenarios-for-query-store"></a>Sorgu deposu için kullanım senaryoları

**Şunlara uygulanır:** PostgreSQL için Azure veritabanı-tek sunuculu sürümler 9,6, 10, 11

Sorgu deposunu, tahmin edilebilir iş yükü performansının izlenmesi ve sürdürülmesi açısından çok çeşitli senaryolarda kullanabilirsiniz. Aşağıdaki örnekleri göz önünde bulundurun: 
- En yüksek maliyetli sorguları tanımlama ve ayarlama 
- A / B testi 
- Yükseltmeler sırasında performansın kararlı tutulması 
- Geçici iş yüklerini tanımlama ve geliştirme 

## <a name="identify-and-tune-expensive-queries"></a>Pahalı sorguları tanımla ve ayarla 

### <a name="identify-longest-running-queries"></a>En uzun çalışan sorguları tanımla 
En uzun çalışan sorguları hızlı bir şekilde tanımlamak için Azure portal [sorgu performansı içgörüleri](concepts-query-performance-insight.md) görünümünü kullanın. Bu sorgular tipik olarak önemli miktarda kaynak tüketme eğilimindedir. En uzun çalışan sorularınızı iyileştirmek, kaynakları sisteminizde çalışan diğer sorgular tarafından kullanılmak üzere boşaltarak performansı iyileştirebilir. 

### <a name="target-queries-with-performance-deltas"></a>Performans değişimleri ile hedef sorgular 
Sorgu deposu, performans verilerini zaman içinde dilimlerler, böylece bir sorgunun zaman içinde performansını izleyebilirsiniz. Bu, tam zamanında harcanan bir artışa hangi sorguların katkıda olduğunu belirlemenize yardımcı olur. Sonuç olarak, iş yükünüzün hedeflenen sorunlarını giderme işlemlerini gerçekleştirebilirsiniz.

### <a name="tuning-expensive-queries"></a>Maliyetli sorguları ayarlama 
Bir sorguyu, iyi performans performansı ile belirlediğinizde, yaptığınız eylem sorunun doğasına bağlıdır: 
- Önerilen dizinlerin olup olmadığını anlamak için [performans önerilerini](concepts-performance-recommendations.md) kullanın. Yanıt Evet ise, dizini oluşturun ve ardından sorgu deposunu kullanarak dizini oluşturduktan sonra sorgu performansını değerlendirin. 
- Sorgu tarafından kullanılan temel tablolar için istatistiklerin güncel olduğundan emin olun.
- Pahalı sorguları yeniden yazmayı düşünün. Örneğin, sorgu Parametreleştirme özelliğinden yararlanın ve dinamik SQL kullanımını küçültün. Uygulama tarafında değil, veritabanı tarafında veri filtreleme uygulama gibi verileri okurken en iyi mantığı uygulayın. 


## <a name="ab-testing"></a>A / B testi 
Kullanmayı planladığınız bir uygulama değişikliğinden önce ve sonra iş yükü performansını karşılaştırmak için sorgu deposu kullanın. Ortamın veya uygulamanın etkisini değerlendirmek için, iş yükü performansına yönelik değişiklik yapmak üzere sorgu deposu kullanmaya yönelik senaryolar örnekleri: 
- Uygulamanın yeni bir sürümünü kullanıma alma. 
- Sunucuya ek kaynaklar ekleniyor. 
- Pahalı sorgular tarafından başvurulan tablolarda eksik dizinler oluşturuluyor. 
 
Bu senaryolardan herhangi birinde, aşağıdaki iş akışını uygulayın: 
1. Performans temeli oluşturmak için planlı değişiklikten önce iş yükünüzü sorgu deposu ile çalıştırın. 
2. Uygulama değişiklik (ler) i denetimli anda uygulama değişikliği. 
3. Değişiklikten sonra sistemin performans görüntüsünü oluşturmak için yeterince uzun süren iş yükünü çalıştırmaya devam edin. 
4. Değişiklikten önceki ve sonraki sonuçları karşılaştırın. 
5. Değişiklik veya geri alma işleminin tutulup tutulmayacağını belirleyin. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Geçici iş yüklerini tanımla ve geliştir 
Bazı iş yükleri, genel uygulama performansını geliştirmek için ayarlayabildiğiniz bir baskın sorgu içermez. Bu iş yükleri genellikle görece çok sayıda benzersiz sorguyla belirlenir, her biri sistem kaynaklarının bir kısmını alır. Her benzersiz sorgu seyrek yürütülür, bu nedenle çalışma zamanının tek tek kullanımı kritik değildir. Diğer taraftan, uygulamanın her seferinde yeni sorgular üretmesine karşın, sistem kaynaklarının önemli bir kısmı sorgu derlemesi üzerinde harcanmıştır ve bu da en uygun değildir. Genellikle, uygulamanız sorgu oluşturduğunda (saklı yordamları veya parametreli sorgular kullanmak yerine) veya varsayılan olarak sorgu üreten nesne ilişkisel eşleme çerçevelerine dayanıyorsa bu durum oluşur. 
 
Uygulama kodunun denetimini kullanıyorsanız, saklı yordamları veya parametreli sorguları kullanmak için veri erişim katmanını yeniden yazmayı düşünebilirsiniz. Bununla birlikte, bu durum, tüm veritabanı (tüm sorgular) veya aynı sorgu karması olan tek tek sorgu şablonları için sorgu parametrelemeyi zorlayarak uygulama değişiklikleri olmadan da geliştirilebilir. 

## <a name="next-steps"></a>Sonraki adımlar
- [Sorgu deposunu kullanmaya yönelik en iyi uygulamalar](concepts-query-store-best-practices.md) hakkında daha fazla bilgi edinin