---
title: Sorgu deposu senaryoları-PostgreSQL için Azure veritabanı-esnek sunucu
description: Bu makalede PostgreSQL için Azure veritabanı 'nda (esnek sunucu) sorgu deposu için bazı senaryolar açıklanır.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: ac0374cf91229e7f1b3f84d967d9825e2a3090f2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559275"
---
# <a name="usage-scenarios-for-query-store---flexible-server"></a>Sorgu deposu için kullanım senaryoları-esnek sunucu

**Uygulama hedefi:** PostgreSQL için Azure veritabanı-tek sunucu sürümleri 11, 12

Sorgu deposunu, tahmin edilebilir iş yükü performansının izlenmesi ve sürdürülmesi açısından çok çeşitli senaryolarda kullanabilirsiniz. Aşağıdaki örnekleri inceleyin: 
- En yüksek maliyetli sorguları tanımlama ve ayarlama 
- A/B testi 
- Yükseltmeler sırasında performansın kararlı tutulması 
- Geçici iş yüklerini tanımlama ve geliştirme 

## <a name="identify-and-tune-expensive-queries"></a>Pahalı sorguları tanımla ve ayarla 

### <a name="identify-longest-running-queries"></a>En uzun çalışan sorguları tanımla 
En uzun çalışan sorguları hızlı bir şekilde tanımlamak için sunucunuzun azure_sys veritabanında sorgu deposu görünümlerini kullanın. Bu sorgular genellikle en fazla kaynağı tüketme eğilimindedir. En uzun çalışan sorgularınızı iyileştirmek, sisteminizde çalışan diğer sorgular tarafından kullanılan kaynakları boşaltarak performansı iyileştirebilir. 

### <a name="target-queries-with-performance-deltas"></a>Performans değişimleri ile hedef sorgular 
Sorgu deposu, performans verilerini zaman içinde dilimlerler, böylece bir sorgunun zaman içinde performansını izleyebilirsiniz. Bu, tam zamanında harcanan bir artışa hangi sorguların katkıda olduğunu belirlemenize yardımcı olur. Sonuç olarak, iş yükünüzün hedeflenen sorunlarını giderme işlemlerini gerçekleştirebilirsiniz.

### <a name="tuning-expensive-queries"></a>Maliyetli sorguları ayarlama 
Bir sorguyu, iyi performans performansı ile belirlediğinizde, yaptığınız eylem sorunun doğasına bağlıdır: 
- Sorgu tarafından kullanılan temel tablolar için istatistiklerin güncel olduğundan emin olun.
- Pahalı sorguları yeniden yazmayı düşünün. Örneğin, sorgu Parametreleştirme özelliğinden yararlanın ve dinamik SQL kullanımını küçültün. Uygulama tarafında değil, veritabanı tarafında veri filtreleme uygulama gibi verileri okurken en iyi mantığı uygulayın. 


## <a name="ab-testing"></a>A/B testi 
Geçiş veya daha önce ve sonrasında bir uygulama değişikliğinden önce ve sonra iş yükü performansını karşılaştırmak için sorgu deposu kullanın. İş yükü performansı değişikliklerinin etkisini değerlendirmek için sorgu deposunu kullanmaya yönelik örnek senaryolar: 
- PostgreSQL sürümleri arasında geçiş. 
- Uygulamanın yeni bir sürümünü kullanıma alma. 
- Sunucuya ek kaynaklar ekleniyor. 
- Pahalı sorgular tarafından başvurulan tablolarda eksik dizinler oluşturuluyor. 
- Tek sunucudan esnek sunucuya geçiş. 
 
Bu senaryolardan herhangi birinde, aşağıdaki iş akışını uygulayın: 
1. Performans temeli oluşturmak için planlı değişiklikten önce iş yükünüzü sorgu deposu ile çalıştırın. 
2. Uygulama değişiklik (ler) i denetimli anda uygulama değişikliği. 
3. Değişiklikten sonra sistemin performans görüntüsünü oluşturmak için yeterince uzun süren iş yükünü çalıştırmaya devam edin. 
4. Değişiklikten önceki ve sonraki sonuçları karşılaştırın. 
5. Değişiklik veya geri alma işleminin tutulup tutulmayacağını belirleyin. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Geçici iş yüklerini tanımla ve geliştir 
Bazı iş yükleri, genel uygulama performansını geliştirmek için ayarlayabildiğiniz bir baskın sorgu içermez. Bu iş yükleri genellikle görece çok sayıda benzersiz sorguyla belirlenir, her biri sistem kaynaklarının bir kısmını alır. Her benzersiz sorgu seyrek yürütülür, bu nedenle çalışma zamanının tek tek kullanımı kritik değildir. Diğer taraftan, uygulamanın her seferinde yeni sorgular üretmesine karşın, sistem kaynaklarının önemli bir kısmı sorgu derlemesi üzerinde harcanmıştır ve bu da en uygun değildir. Genellikle, uygulamanız sorgu oluşturduğunda (saklı yordamları veya parametreli sorgular kullanmak yerine) veya varsayılan olarak sorgu üreten nesne ilişkisel eşleme çerçevelerine dayanıyorsa bu durum oluşur. 
 
Uygulama kodunun denetimini kullanıyorsanız, saklı yordamları veya parametreli sorguları kullanmak için veri erişim katmanını yeniden yazmayı düşünebilirsiniz. Bununla birlikte, bu durum, tüm veritabanı (tüm sorgular) veya aynı sorgu karması olan bireysel sorgu şablonları için sorgu parametrelemeyi zorlayarak uygulama değişiklikleri olmadan da geliştirilebilir. 

## <a name="next-steps"></a>Sonraki adımlar
- [Sorgu deposunu kullanmaya yönelik en iyi uygulamalar](concepts-query-store-best-practices.md) hakkında daha fazla bilgi edinin
