---
title: Akustik Simülasyon ile Tasarım Kavramları
titlesuffix: Azure Cognitive Services
description: Bu kavramsal genel bakış, Project Acoustics 'ın ses tasarımı işlemine akustik simülasyonu nasıl eklediğini açıklar.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 11e1e3f45b5198ddedb6c31fcd354185adef445d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854303"
---
# <a name="project-acoustics-design-process-concepts"></a>Project Acoustics tasarım süreci kavramları

Bu kavramsal genel bakış, Project Acoustics 'ın ses tasarımı işlemine fiziksel akustik benzetim nasıl eklediğini açıklar.

## <a name="sound-design-with-audio-dsp-parameters"></a>Ses DSP parametreleri ile ses tasarımı

3B etkileşimli başlıklar, ses altyapısında barındırılan ses dijital sinyal işleme (DSP) bloklarını kullanarak belirli seslerini elde etmenizi ister. Bu bloklar basit karıştırma, geri alma, yankı, gecikme, eşitleme, sıkıştırma ve sınırlama ve diğer etkilere göre karmaşıklığa sahiptir. Bu efektlerde parametre seçme, düzenleme ve ayarlama, deneyimin Aesthetic Characteristics ve oyun deneyimini hedeflerini karşılayan bir ses grafiği oluşturan ses Tasarımcısı 'nın sorumluluğundadır.

Etkileşimli bir başlıkta, sesler ve dinleyici 3B alan boyunca hareket etmekte olduğundan, bu parametreler değişen koşullara nasıl uyum sağlar? Ses Tasarımcısı genellikle, geri alma efektlerinde değişikliklere ulaşmak üzere parametre değişikliklerini tetiklemek için programlanarak birimleri düzenler (örneğin, dinleyici sahnenin bir bölümünden diğerine hareket ettirildiğinde). Bu etkilerden bazılarını otomatik hale getirebilen Acoustics sistemleri de mevcuttur.

3B başlıklar, Immersion ve oyun Play hedeflerinin bir karışımını elde etmek üzere fizik veren ancak tasarımcı tarafından ayarlanmış aydınlatma ve Kinematic fizik sistemlerini kullanır. Görsel tasarımcı, tek bir piksel değeri yapmaz, ancak tüm fiziksel tabanlı 3B modelleri, malzemeleri ve açık aktarım sistemlerini, Visual Aesthetic ve CPU maliyetlerini dengeleyen şekilde ayarlar. Ses için eşdeğer işlem ne olacaktır? Project Acoustics, bu sorunun araştırmasının ilk adımıdır. İlk olarak, acoustical enerji tasarrufu için bir boşluk aracılığıyla ne anlama geldiğini inceleyeceğiz.

![Yankı alanları ile çakışan Altalan sahnenin ekran görüntüsü](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Empulo yanıtları: Acoustically iki noktaya yer bağlamayı bağlama

Ses tasarımı hakkında bilginiz varsa, akustik yanıt yanıtlarını biliyor olabilirsiniz. Akustik bir yanıt, bir kaynaktan bir sesin bir dinleyiciye aktarımını modelleyen. Bu nedenle, bir empuli yanıtı, Oda Acoustics her ilginç efektini (örneğin, occlusiyon ve ters dönüş) yakalayabilir. Empulde yanıtları, ses DSP etkilerinin ölçeklendirilmesine izin veren bazı güçlü özelliklere sahiptir. İki ses sinyalini birlikte eklemek ve bir empulde yanıtla birlikte işlemek, ımpulo yanıtını her sinyalle ayrı olarak uygulama ve sonuçları ekleme ile aynı sonucu verir. Akustik yayma ve Empulse yanıtları, işlenen sese, yalnızca modellenen sahnede ve kaynak ve dinleyici konumlarına bağlı değildir. Kısacası, büyük bir yanıt, sahnenin ses yayma üzerindeki etkisini ayırt ediyor.

Etkileyici bir yanıt, her ilgi çekici odadaki etkiyi yakalar ve bunu bir filtreyle verimli bir şekilde uygulayabilir ve ölçülerden veya simülasyondan daha fazla yanıt alabilirsiniz. Ancak Acoustics 'in fizik ile tam olarak eşleşmesini kesinlikle istemiyorum, ancak bunun yerine bir sahnenin temel taleplerini eşleştirmek istiyor musunuz? Ancak çok sayıda piksel değeri, bir duyuluk yanıtı yalnızca binlerce sayıdan oluşan bir liste olduğundan, Aesthetic Characteristics ihtiyaçlarını karşılayacak şekilde nasıl ayarlayabiliriz? Doorways veya sonraki bir arka planda geçiş yaparken, sorunsuz bir şekilde bir etkiye sahip olmak için kaç tane ımpuli yanıtı elde etmemiz gerektiğini gösteren occlusiyon/düşüşe 'e sahip olmak istiyoruz. Kaynak hızlı bir şekilde taşınırsa ne olacak? Ne kadar geç?

Etkileşimli başlıklarda Acoustics 'in bazı yönleri için benzetim ve empulde yanıtları kullanmayı zorlaştırır. Ancak, tanıdık ses DSP efekt parametrelerimizle simülasyondan gelen kendi yanıtlarını bağlayabilmemiz için tasarımcı ayarlamalarını destekleyen bir ses taşıma sistemi oluşturmaya devam edebilirsiniz.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Bir simülasyonu, parametrelerle ses DSP 'ye bağlama

Bir ımpulo yanıtı, her ilgi çekici (ve ilginç olmayan) acoustical efektini içerir. Ses DSP blokları, parametreleri düzgün şekilde ayarlandığında ilginç acoustical efektini işleyebilir. Bir 3B sahnede ses taşımasını otomatik hale getirmek için acoustical simülasyonu kullanmak, yalnızca bir ımpulo yanıtından ses DSP parametreleri ölçmenin bir önemi vardır. Bu ölçüm, occlusiyon, düşüşe, portalling ve geri alma dahil olmak üzere belirli ortak ve önemli acoustical etkileri için iyi anlaşılmıştır.

Ancak simülasyon doğrudan ses DSP parametrelerine bağlandıysa, nerede tasarımcı ayarlamadır? Ne elde ediyoruz? Ayrıca, empuli yanıtlarını atarak ve birkaç DSP parametresini korurken önemli miktarda bellek elde ediyoruz. Tasarımcı 'yı nihai sonucun üzerinde biraz güç vermek için, yalnızca simülasyonu ve ses DSP 'si arasına tasarımcı eklemenin bir yolunu bulduk.

![Çakışan parametrelere sahip stilize bir Yanıt ile grafik](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Ses DSP parametrelerini simülasyondan dönüştürerek ses tasarımı

Dünyanın her yerindeki gerçek gözlerinizin etkisini göz önünde bulundurun. Parlak bir gün boyunca gözlük, daha rahat bir şekilde kısaltabilecek şekilde azalabilir. Koyu bir odada, hiçbir şeyi görmeyebilirsiniz. Gözlük, her durumda belirli bir parlaklık düzeyini ayarlama; yalnızca her şeyi daha koyu hale getirir.

Yük, occlusiyon ve geri alma parametreleri kullanarak ses DSP 'umuzu sağlamak için simülasyonu kullandığımızda, DSP 'nin ' gördüğü ' parametrelerini ayarlamak için benzeticiden sonra bir filtre ekleyebiliriz. Filtre belirli bir occlusiyon veya yankı kuyruğu uzunluğu uygulamaz; bu da güneş gözlüklerine benzer şekilde her odaya aynı parlaklığı vermez. Filtre her bir Occluder occlude daha az hale gelebilir. Veya occlude daha fazlasını yapın. Bir ' azat ' occlusiyon parametre filtresi ekleyerek ve ayarlayarak, büyük, açık odalar hala hiç bir etkiye sahip olmaya devam eder, ancak doorways bir ortamdan güçlü bir şekilde artarak etkili olur ve bu da düzfiyi efekt geçişlerde korur simülasyonu sağlar.

Bu paradigma, tasarımcı 'nın görevi her bir durum için akustik parametreleri seçme, simülasyonundan gelen en önemli DSP parametrelerine uygulanacak filtreleri seçmek ve ayarlamak için de değişir. Tasarımcı etkinliklerini, temizleme ve geri alma efektlerinin yoğunluğu ve karışımdaki kaynakların varlığı için sorunsuz geçişler ayarlamanın dar kaygılarından yükseltir. Kuşkusuz, durum talep edildiğinde, her zaman kullanılabilir bir filtre, belirli bir durumda belirli bir kaynak için DSP parametrelerini seçmeye geri dönmelidir.

## <a name="sound-design-in-project-acoustics"></a>Project Acoustics 'da ses tasarımı

Project Acoustics Package, yukarıda açıklanan bileşenlerden her birini tümleştirir: bir simülatör, parametreleri çıkaran ve Acoustics varlığını, ses DSP 'sini ve bir filtre seçimini oluşturan bir kodlayıcı. Project Acoustics ile ses tasarımı; simülasyondan türetilmiş ve ses DSP 'si ve ses motoru içinde kullanıma sunulan dinamik denetimlerle ses DSP 'si uygulanmış olan geri alma ve geri alma parametrelerini düzenleyen filtreler için parametreleri seçmeyi gerektirir.

## <a name="next-steps"></a>Sonraki adımlar
* [Unity Için Project Acoustics hızlı](unity-quickstart.md) başlangıcı 'Nı veya [gerçek zamanlı olmayan proje Acoustics hızlı](unreal-quickstart.md) başlangıcı 'ni kullanarak tasarım paradigmasını deneyin
* [Unity Için Project Acoustics tasarım denetimlerini](unity-workflow.md) veya [gerçek zamanlı olmayan proje Acoustics tasarım denetimlerini](unreal-workflow.md) keşfet

