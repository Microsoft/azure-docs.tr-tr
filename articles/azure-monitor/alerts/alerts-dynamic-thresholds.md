---
title: Azure Izleyici 'de dinamik eşiklerle uyarı oluşturma
description: Makine öğrenimi tabanlı dinamik eşiklerle uyarı oluşturma
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 9cf4caf77d14dd1296276ca48827c170ef265e9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101718072"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Azure Izleyici 'de dinamik eşiklerle ölçüm uyarıları

Dinamik Eşik algılama ile ölçüm uyarısı, ölçümlerin geçmiş davranışını öğrenmek için gelişmiş makine öğrenimi (ML) kullanır ve olası hizmet sorunlarını belirten desenleri ve anormallikleri belirler. Kullanıcıların, uyarı kurallarını Azure Resource Manager API aracılığıyla tam otomatik bir şekilde yapılandırmasına izin vererek hem basit bir kullanıcı arabirimi hem de işlemler için destek sağlar.

Bir uyarı kuralı oluşturulduktan sonra, yalnızca izlenen ölçüm, özel eşiklerine göre beklendiği gibi davranmıyorsa harekete geçmeyecektir.

Görüşlerinizi duymak için sevdiğimiz için seveceğiz <azurealertsfeedback@microsoft.com> .

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Neden ve ne zaman dinamik koşul türünü kullanıyor önerilir?

1. **Ölçeklenebilir** uyarı – dinamik eşik uyarı kuralları, tek seferde yüzlerce ölçüm serisi için özel eşikler oluşturabilir, ancak tek bir ölçüm üzerinde bir uyarı kuralı tanımlama kolaylığını sağlar. Oluşturma ve yönetme konusunda daha az uyarı verir. Bunları oluşturmak için Azure portal ya da Azure Resource Manager API 'sini kullanabilirsiniz. Ölçeklenebilir yaklaşım özellikle ölçüm boyutları ile ilgilenirken veya tüm abonelik kaynakları gibi birden fazla kaynağa uygulanırken yararlıdır.  [Şablonları kullanarak dinamik eşiklerle ölçüm uyarılarını yapılandırma hakkında daha fazla bilgi edinin](./alerts-metric-create-templates.md).

1. **Akıllı ölçüm deseni tanıma** – ml teknolojimizi kullanarak, ölçüm düzenlerini otomatik olarak tespit edebilir ve zaman içinde ölçüm değişikliklerine uyarlayabilirsiniz, bu da genellikle mevsimsellik (saatlik/günlük/haftalık) içerebilir. Her ölçüm için "sağ" eşiğini bilmenin yükünü sizi maliyetinden kurtarır, kendi örüntüsünün ölçülerine göre zaman içindeki davranışa ve uyarı hedefine uyarlamak. Dinamik eşikte kullanılan ML algoritması, beklenen bir kalıbı olmayan gürültülü (düşük duyarlılık) veya geniş (düşük geri çağırma) eşiklerini engellemek için tasarlanmıştır.

1. **Sezgisel yapılandırma** – dinamik eşikler, üst düzey kavramlar kullanılarak ölçüm uyarılarını ayarlamaya olanak tanır, hafifletmesini ölçüm hakkında kapsamlı etki alanı bilgisine sahip olması gerekir.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Uyarı kuralları dinamik eşiklerle nasıl yapılandırılır?

Dinamik eşiklere sahip uyarılar, Azure Izleyici 'de ölçüm uyarıları aracılığıyla yapılandırılabilir. [Ölçüm uyarılarını yapılandırma hakkında daha fazla bilgi edinin](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Eşikler nasıl hesaplanır?

Dinamik eşikler, Ölçüm serisinin verilerini sürekli öğrenir ve bir dizi algoritmaya ve yönteme göre modeli oluşturmaya çalışır. Veride mevsimsellik (saatlik/günlük/haftalık) gibi desenleri algılar ve gürültülü ölçümleri (makine CPU veya bellek gibi) ve düşük dağılım (kullanılabilirlik ve hata oranı gibi) ölçümlerle işleyebilir.

Eşikler, bu eşiklerden bir sapmanın ölçüm davranışında bir anomali olduğunu gösterdiği şekilde seçilir.

> [!NOTE]
> Dinamik eşikler saatlik, günlük veya haftalık desenler için mevsimsellik algılayabilir. İki saatlik veya yarı haftalık mevsimsellik gibi diğer desenler algılanmayabilir. Haftalık mevsimsellik düzeyini algılamak için, geçmiş verilerin en az üç haftası gereklidir. 

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Dinamik Eşikteki ' duyarlılık ' ayarı ne anlama geliyor?

Uyarı eşiği duyarlılığı, bir uyarının tetiklenmesi için gereken ölçüm davranışından sapma miktarını denetleyen üst düzey bir kavramdır.
Bu seçenek statik eşik gibi bir ölçüm hakkında etki alanı bilgisi gerektirmez. Şu seçenekleri kullanabilirsiniz:

- Yüksek – eşikler sıkı ve ölçüm serisi düzenine yakın olacaktır. En küçük sapma üzerinde bir uyarı kuralı tetiklenir, bu da daha fazla uyarı elde edilir.
- Orta-daha az sıkı ve daha dengeli eşikler, yüksek duyarlığa kıyasla daha az uyarı (varsayılan).
- Düşük – eşikler, ölçüm serisi düzeninden daha fazla mesafeyle gevşek olacaktır. Uyarı kuralı yalnızca büyük sapmalarla tetiklenecek ve daha az uyarı oluşmasına neden olur.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Dinamik Eşikteki ' operator ' ayar seçenekleri nelerdir?

Dinamik eşikler uyarıları kuralı, aynı uyarı kuralını kullanarak hem üst hem de alt sınır için ölçüm davranışına göre özel eşikler oluşturabilir.
Aşağıdaki üç koşuldan birini izleyerek tetiklenecek uyarıyı seçebilirsiniz:

- Üst eşikten büyük veya alt eşikten düşük (varsayılan)
- Üst eşikten büyük
- Alt eşikten düşük.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Dinamik Eşikteki Gelişmiş ayarlar ne anlama geliyor?

**Başarısız dönemler** -dinamik eşikler Ayrıca "uyarıyı tetiklemek için sayı ihlalleri", sistemin bir uyarı oluşturması için belirli bir zaman penceresi içinde gerekli olan en az sayıda sapmaları yapılandırmanıza olanak tanır (varsayılan zaman penceresi 20 dakika içinde dört sapdır). Kullanıcı başarısız dönemleri yapılandırabilir ve başarısız dönemleri ve zaman penceresini değiştirerek ne uyarılmak istediğinizi seçebilir. Bu özellik, geçici ani artışlar tarafından oluşturulan uyarı gürültüsünü azaltır. Örnek:

Sorun 20 dakika boyunca sürekli olduğunda bir uyarı tetiklemek için, belirli bir dönemde 5 dakikalık gruplandırmada 4 kez, aşağıdaki ayarları kullanın:

![5 dakikalık belirli bir dönemde sürekli olarak 20 dakikalık, 4 ardışık sorun için dönem ayarları](media/alerts-dynamic-thresholds/0008.png)

5 dakikalık sürede son 30 dakikadan itibaren 20 dakika içinde dinamik eşiklerden bir ihlal olduğunda bir uyarı tetiklemek için aşağıdaki ayarları kullanın:

![5 dakikalık dönem gruplandırmasına sahip son 30 dakikadan kısa bir süre sonra sorun için dönemler ayarları başarısız oluyor](media/alerts-dynamic-thresholds/0009.png)

Daha **önce verileri yoksay** -kullanıcılar Ayrıca isteğe bağlı olarak, sistemin eşiklerini hesaplamaya başlayacağı başlangıç tarihini de tanımlayabilir. Bir kaynak sınama modunda çalışırken tipik bir kullanım durumu gerçekleşebilir ve artık bir üretim iş yüküne yönelik olarak yükseltilip, test aşamasında herhangi bir ölçümün davranışı yok sayılamayabilir.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Dinamik Eşik uyarısının neden tetiklendiğini nasıl bulabilirim?

Uyarı görünümünü, Azure portal uyarı görünümünü görmek için e-posta veya kısa mesaj veya tarayıcıdaki bağlantıya tıklayarak, uyarılar görünümünde inceleyebilirsiniz. [Uyarılar görünümü hakkında daha fazla bilgi edinin](./alerts-overview.md#alerts-experience).

Uyarı görünümü şunu görüntüler:

- Dinamik Eşik uyarısının tetiklenme andaki tüm ölçüm ayrıntıları.
- Bu noktada kullanılan dinamik eşikleri içeren, uyarının tetiklendiği dönemin bir grafiği.
- Dinamik eşikler uyarısı ve Uyarılar görünümü deneyimi hakkında geri bildirim sağlayabilme olanağı, bu da gelecekteki algılamaları iyileştirebilir.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>Ölçüm içindeki davranış değişikliklerinin bir uyarı tetiklenmesi gerekir mi?

Büyük olasılıkla gerekmez. Dinamik eşikler, yavaş gelişen sorunlar yerine önemli sapmaları saptamak için uygundur.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Eşikleri önizlemek ve daha sonra hesaplamak için ne kadar veri kullanıldı?

Bir uyarı kuralı ilk oluşturulduğunda, grafikte görünen eşikler saat veya günlük dönemsel desenleri (10 gün) hesaplamak için yeterli geçmiş verileri temel alarak hesaplanır. Bir uyarı kuralı oluşturulduktan sonra dinamik eşikler tüm gerekli geçmiş verileri kullanır ve eşiklerin daha doğru olmasını sağlamak için sürekli olarak yeni verilere göre öğrenve uyarlanacaktır. Bu, bu hesaplamadan sonra grafiğin haftalık desenleri de görüntüleyeceği anlamına gelir.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Uyarı tetiklenmesi için ne kadar veri gerekiyor?

Yeni bir kaynağınız varsa veya ölçüm verileriniz eksikse, dinamik eşikler, doğru eşikleri sağlamak için üç gün önce ve en az 30 örnek Ölçüm verisi kullanılabilir olduğunda uyarı tetiklemez.
Yeterli ölçüm verileri olan mevcut kaynaklar için dinamik eşikler, uyarıları hemen tetikleyebilirler.

## <a name="dynamic-thresholds-best-practices"></a>Dinamik eşikler en iyi uygulamaları

Dinamik eşikler, Azure Izleyici 'deki çoğu platforma ve özel ölçümlere uygulanabilir ve ayrıca ortak uygulama ve altyapı ölçümleri için de ayarlanmıştır.
Aşağıdaki öğeler, dinamik eşikler kullanarak bu ölçülerden bazılarının uyarılarını yapılandırma konusunda en iyi uygulamalardan oluşan deneyimlerdir.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Sanal makine CPU yüzdesi ölçümlerinde dinamik eşikler

1. [Azure Portal](https://portal.azure.com), **izleyici**' ye tıklayın. Izleyici görünümü tüm izleme ayarlarınızı ve verilerinizi tek bir görünümde birleştirir.

2. **Uyarılar** ' a ve ardından **+ Yeni uyarı kuralı**' na tıklayın.

    > [!TIP]
    > Çoğu kaynak dikey penceresinde, **izleme** altındaki kaynak menüsünde **da uyarılar bulunur** . Ayrıca, burada da uyarı oluşturabilirsiniz.

3. **Hedef Seç**' e tıklayın, yüklenen bağlam bölmesinde, uyarmak istediğiniz hedef kaynağı seçin. İzlemek istediğiniz kaynağı bulmak için **aboneliği** ve **' sanal makineler ' kaynak türü** açılan listelerini kullanın. Kaynağı bulmak için arama çubuğunu da kullanabilirsiniz.

4. Bir hedef kaynak seçtikten sonra **Koşul Ekle**' ye tıklayın.

5. **' CPU yüzdesi**'ni seçin.

6. İsteğe bağlı olarak, **dönemi** ve **toplamayı** ayarlayarak ölçüyü daraltın. Daha az davranış temsili olduğundan, bu ölçüm türü için ' en yüksek ' toplama türü kullanılması önerilmez. ' Maksimum ' toplama türü statik eşiği daha uygun olabilir.

7. Son 6 saat için ölçüm için bir grafik görürsünüz. Uyarı parametrelerini tanımlayın:
    1. **Koşul türü** -' Dynamic ' seçeneğini belirleyin.
    1. **Duyarlılık** -uyarı gürültüsünü azaltmak için orta/düşük duyarlılık seçeneğini belirleyin.
    1. **İşleç** -uygulama kullanımını temsil etmediği takdirde ' büyüktür ' seçeneğini belirleyin.
    1. **Sıklık** -uyarının iş etkisi temelinde kısmayı düşünün.
    1. **Başarısız dönemler** (Gelişmiş seçenek)-geri arama penceresi en az 15 dakika olmalıdır. Örneğin, dönem beş dakikaya ayarlanırsa, başarısız olan dönemler en az üç veya daha fazla olmalıdır.

8. Ölçüm grafiği, hesaplanan eşikleri son verilere göre görüntüler.

9. **Bitti**’ye tıklayın.

10. **Uyarı kuralı adı**, **açıklaması** ve **önem derecesi** gibi **uyarı ayrıntılarını** girin.

11. Var olan bir eylem grubunu seçerek veya yeni bir eylem grubu oluşturarak uyarıya bir eylem grubu ekleyin.

12. Ölçüm uyarı kuralını kaydetmek için **bitti** ' ye tıklayın.

> [!NOTE]
> Portal üzerinden oluşturulan ölçüm uyarısı kuralları, hedef kaynakla aynı kaynak grubunda oluşturulur.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Application Insights HTTP istek yürütme süresi üzerinde dinamik eşikler

1. [Azure Portal](https://portal.azure.com), **izleyici**' ye tıklayın. Izleyici görünümü tüm izleme ayarlarınızı ve verilerinizi tek bir görünümde birleştirir.

2. **Uyarılar** ' a ve ardından **+ Yeni uyarı kuralı**' na tıklayın.

    > [!TIP]
    > Çoğu kaynak dikey penceresinde, **izleme** altındaki kaynak menüsünde **da uyarılar bulunur** . Ayrıca, burada da uyarı oluşturabilirsiniz.

3. **Hedef Seç**' e tıklayın, yüklenen bağlam bölmesinde, uyarmak istediğiniz hedef kaynağı seçin. İzlemek istediğiniz kaynağı bulmak için **aboneliği** ve **' Application Insights ' kaynak türü** açılan listelerini kullanın. Kaynağı bulmak için arama çubuğunu da kullanabilirsiniz.

4. Bir hedef kaynak seçtikten sonra **Koşul Ekle**' ye tıklayın.

5. **' Http istek yürütme süresi**'ni seçin.

6. İsteğe bağlı olarak, **dönemi** ve **toplamayı** ayarlayarak ölçüyü daraltın. Daha az davranış temsili olduğundan, bu ölçüm türü için ' en yüksek ' toplama türü kullanılması önerilmez. ' Maksimum ' toplama türü statik eşiği daha uygun olabilir.

7. Son 6 saat için ölçüm için bir grafik görürsünüz. Uyarı parametrelerini tanımlayın:
    1. **Koşul türü** -' Dynamic ' seçeneğini belirleyin.
    1. **İşleç** -süre içinde geliştirme sırasında tetiklenen uyarıları azaltmak Için ' büyüktür ' seçeneğini belirleyin.
    1. **Sıklık** -uyarının iş etkisi temelinde kısmayı düşünün.

8. Ölçüm grafiği, hesaplanan eşikleri son verilere göre görüntüler.

9. **Bitti**’ye tıklayın.

10. **Uyarı kuralı adı**, **açıklaması** ve **önem derecesi** gibi **uyarı ayrıntılarını** girin.

11. Var olan bir eylem grubunu seçerek veya yeni bir eylem grubu oluşturarak uyarıya bir eylem grubu ekleyin.

12. Ölçüm uyarı kuralını kaydetmek için **bitti** ' ye tıklayın.

> [!NOTE]
> Portal üzerinden oluşturulan ölçüm uyarısı kuralları, hedef kaynakla aynı kaynak grubunda oluşturulur.

## <a name="interpreting-dynamic-threshold-charts"></a>Dinamik Eşik grafiklerini yorumlama

Aşağıda, bir ölçümü, dinamik eşik sınırlarını ve değer izin verilen eşiklerin dışında olduğunda tetiklenen bazı uyarıları gösteren bir grafik verilmiştir.

![Ölçüm uyarılarını yapılandırma hakkında daha fazla bilgi edinin](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Önceki grafiği yorumlamak için aşağıdaki bilgileri kullanın.

- **Mavi çizgi** -zaman içindeki gerçek ölçülen ölçüm.
- **Mavi gölgeli alan** -ölçüm için izin verilen aralığı gösterir. Ölçüm değerleri bu aralıkta olduğu sürece, hiçbir uyarı oluşmaz.
- **Mavi noktalar** -grafiğin bir bölümüne sağ tıklayıp mavi çizginin üzerine geldiğinizde, imlecinizin altında ayrı bir toplu ölçüm değeri gösteren mavi bir nokta görüntülenir.
- **Mavi noktayla açılan pencerede** , ölçülen ölçüm değerini (mavi nokta) ve izin verilen aralığın üst ve alt değerlerini gösterir.  
- **Siyah daire olan kırmızı nokta** -izin verilen aralıktan ilk ölçüm değerini gösterir. Bu değer, bir ölçüm uyarısını harekete geçirir ve bunu etkin bir duruma getirir.
- **Kırmızı noktalar**-izin verilen aralığın dışında diğer ölçülen değerleri gösterir. Bunlar ek ölçüm uyarıları tetiketmez, ancak uyarı etkin kalır.
- **Kırmızı alan** -ölçüm değerinin izin verilen aralığın dışında olduğu zamanı gösterir. Sonraki ölçülen değerler izin verilen aralığın dışında olduğu sürece, uyarı etkin durumda kalır, ancak yeni bir uyarı tetiklenmez.
- **Red alanının sonu** -mavi çizgi izin verilen değerlerin içinde olduğunda, Red alanı duraklar ve ölçülen değer çizgisi maviyi kapatır. Siyah anahatlı kırmızı nokta sırasında tetiklenen ölçüm uyarısının durumu Çözüldü olarak ayarlanır.