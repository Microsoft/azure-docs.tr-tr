---
title: Otomatik ölçeklendirme için en iyi uygulamalar
description: Web Apps, sanal makine ölçek kümeleri ve Cloud Services için Azure 'da otomatik ölçeklendirme desenleri
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 9aaf9525f2fedee67a86011e938b8e995ccfe9fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100623948"
---
# <a name="best-practices-for-autoscale"></a>Otomatik ölçeklendirme için en iyi uygulamalar
Azure Izleyici otomatik ölçeklendirme yalnızca [Sanal Makine Ölçek Kümeleri](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)ve [API Management Hizmetleri](../../api-management/api-management-key-concepts.md)için geçerlidir.

## <a name="autoscale-concepts"></a>Otomatik ölçeklendirme kavramları

* Bir kaynakta yalnızca *bir* otomatik ölçeklendirme ayarı olabilir
* Bir otomatik ölçeklendirme ayarında bir veya daha fazla profil bulunabilir ve her profil bir veya daha fazla otomatik ölçeklendirme kuralına sahip olabilir.
* Bir otomatik ölçeklendirme ayarı, örnekleri yatay olarak ölçeklendirir ve örneklerin sayısını *azaltarak,* *içindeki ve ' ın* artması.
  Otomatik ölçeklendirme ayarı en yüksek, en düşük ve varsayılan örnek değerine sahiptir.
* Otomatik ölçeklendirme işi her zaman, ölçek genişletme veya ölçek genişletme için yapılandırılan eşiği geçtiğine bakarak, ölçeklendirilmesi için ilişkili ölçümü okur. Otomatik ölçeklendirme 'nin ölçeklendiği ölçümlerin listesini, [Azure izleyici otomatik ölçeklendirme ortak ölçümleri](autoscale-common-metrics.md)' nde görüntüleyebilirsiniz.
* Tüm eşikler bir örnek düzeyinde hesaplanır. Örneğin, "örnek sayısı 2 olduğunda ortalama CPU >% 80 oranında ölçeği bir örneğe göre ölçeklendirin, tüm örneklerde ortalama CPU %80 ' den fazla olduğunda ölçeği genişletme anlamına gelir.
* Tüm otomatik ölçeklendirme hatalarının etkinlik günlüğüne kaydedilir. Sonra bir otomatik ölçeklendirme hatası olduğunda e-posta, SMS veya Web kancaları aracılığıyla bildirim alabilmeniz için bir [etkinlik günlüğü uyarısı](../alerts/activity-log-alerts.md) yapılandırabilirsiniz.
* Benzer şekilde, tüm başarılı ölçeklendirme eylemleri etkinlik günlüğüne gönderilir. Sonra başarılı bir otomatik ölçeklendirme eylemi olduğunda e-posta, SMS veya Web kancaları aracılığıyla bildirim alabilmeniz için bir etkinlik günlüğü uyarısı yapılandırabilirsiniz. Ayrıca, otomatik ölçeklendirme ayarında Bildirimler sekmesi aracılığıyla başarılı ölçeklendirme eylemleri hakkında bildirim almak için e-posta veya Web kancası bildirimleri de yapılandırabilirsiniz.

## <a name="autoscale-best-practices"></a>Otomatik ölçeklendirme en iyi yöntemleri

Otomatik ölçeklendirme kullanırken aşağıdaki en iyi yöntemleri kullanın.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>En yüksek ve en düşük değerlerin farklı olduğundan emin olun ve bunların arasında yeterli bir boşluk bırakın

En düşük=2 ve en yüksek=2 ayarınız varsa, geçerli örnek sayısı da 2 ise, hiçbir ölçeklendirme işlemi gerçekleştirilemez. En yüksek ile en düşük örnek sayıları (belirtilen sayılar da dahildir) arasında uygun bir marj bırakın. Otomatik ölçeklendirme her zaman bu sınırlar arasında ölçeklendirir.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>El ile ölçeklendirme, otomatik ölçeklendirmenin en düşük ve en yüksek değerleriyle sıfırlanır

Örnek sayısını maksimum değerin üzerinde veya altında bir değere el ile güncelleştirirseniz, otomatik ölçeklendirme motoru otomatik olarak en düşük (aşağıda varsa) veya en yüksek (yukarıda varsa) olarak yeniden ölçeklenir. Örneğin, 3 ile 6 arasındaki aralığı ayarlarsınız. Çalışan bir örneğiniz varsa, otomatik ölçeklendirme motoru bir sonraki çalıştırmasında üç örneğe ölçeklendirir. Benzer şekilde, ölçeği sekiz örneğe el ile ayarlarsanız, sonraki çalışma otomatik ölçeklendirme, sonraki çalıştırmada altı örneğe geri ölçeklendirecektir.  Otomatik ölçeklendirme kurallarını sıfırlamadıkça el ile ölçekleme geçicidir.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Her zaman artırma ve azaltma gerçekleştiren bir ölçek genişletme ve ölçekleme kuralı kombinasyonu kullanın
Birleşiminin yalnızca bir bölümünü kullanırsanız, otomatik ölçeklendirme, en yüksek değere veya profilde tanımlanan minimum örnek sayısına ulaşıncaya kadar yalnızca tek bir yönde (ölçeği genişletme veya içinde) eylem yapılır. Bu en iyi şekilde, kaynağın kullanılabilirliği sağlamak için yüksek kullanımlarda ölçeğini en iyi şekilde ölçeklendirmenizi ister. Benzer şekilde, düşük kullanım saatlerinde kaynağınızın ölçeğini, böylece maliyet tasarrufu elde edebilirsiniz.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Tanılama ölçümünüz için uygun istatistiği seçin
Tanılama ölçümleri için, ölçeklendirmek üzere bir ölçüm olarak *Ortalama*, *Minimum*, *Maksimum* ve *Toplam* arasından seçim yapabilirsiniz. En yaygın istatistik *ortalaması*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Tüm ölçüm türlerine yönelik eşikleri dikkatle seçin
Genişleme ve ölçeklendirme için farklı eşikleri pratik durumlara göre dikkatle seçmeyi öneririz.

Aşağıdaki örneklerde, çıkış ve koşullar için aynı veya benzer eşik değerleriyle birlikte otomatik ölçeklendirme ayarlarını *önermiyoruz* :

* İş Parçacığı Sayısı >= 600 olduğunda örnekleri 1 artır
* İş Parçacığı Sayısı <= 600 olduğunda örnekleri 1 azalt

Kafa karıştırıcı olabilecek bir davranışa neden olabilecek bir örneğe bakalım. Aşağıdaki sırayı göz önünde bulundurun.

1. İle başlayabileceğiniz iki örnek olduğunu varsayın ve örnek başına ortalama iş parçacığı sayısı 625 olarak artar.
2. Otomatik ölçeklendirme, üçüncü bir örnek eklemenin ölçeğini ölçeklendirir.
3. Ardından, örnek genelinde ortalama iş parçacığı sayısının 575 olduğunu varsayalım.
4. Ölçeklendirmeyi ölçeklendirmeden önce, otomatik ölçeklendirme, son durumun ölçeklendirildiğinde ne olacağını tahmin etmeye çalışır. Örneğin, 575 x 3 (geçerli örnek sayısı) = 1.725/2 (ölçeklendirildiğinde son örnek sayısı) = 862,5 iş parçacıkları. Bu, otomatik ölçeklendirmeyi, içinde ölçeklendirdikten sonra bile hemen ölçeklendirilmesi, ortalama iş parçacığı sayısı aynı kalırsa veya yalnızca küçük bir miktarda kalırsa. Ancak, yeniden ölçeği tekrar ölçeklendirirse, tüm işlem yinelenir ve sonsuz bir döngüye döner.
5. Bu durumdan kaçınmak için ("Flama"), otomatik ölçeklendirme tamamen ölçeklenmez. Bunun yerine, hizmet işinin bir sonraki açılışında koşulu atlar ve yeniden gerçekleştirir. Ortalama iş parçacığı sayısı 575 olduğunda otomatik ölçeklendirme iş olarak görünmediği için, düzletme durumu birçok kişiyi karıştırabilirler.

Ölçek oluşturma sırasında tahmin, "Flama" durumlarından kaçınmaya yöneliktir ve bu durum, ölçek ve genişleme eylemlerinin sürekli olarak geri ve ileri bir yere gider. Ölçek genişletme için aynı eşikleri seçtiğinizde ve içinde bu davranışı aklınızda bulundurun.

Ölçek Genişletme ve eşikler arasında yeterli bir kenar boşluğu seçmeyi öneririz. Örnek olarak, aşağıdaki daha iyi kural birleşimini göz önünde bulundurun.

* CPU% >= 80 olduğunda örnekleri 1 sayıya göre artır
* CPU% <= 60 olduğunda örnekleri 1 sayıya küçültür

Bu durumda  

1. İle başlayan 2 örnek olduğunu varsayalım.
2. Örnekler arasında ortalama CPU yüzdesi 80 ise, otomatik ölçeklendirme üçüncü bir örnek eklemenin ölçeğini ölçeklendirir.
3. Artık% CPU %60 ' e denk gelir.
4. Otomatik ölçeklendirmeyi ölçekleme kuralı, ölçeklendirilmesi durumunda son durumu tahmin eder. Örneğin, 60 x 3 (geçerli örnek sayısı) = 180/2 (ölçeği azaltılabilen son örnek sayısı) = 90. Bu nedenle otomatik ölçeklendirme, ölçeği yeniden genişletmek zorunda olduğundan ölçeklendirmez. Bunun yerine, ölçeklendirmeyi aşağı atlar.
5. Otomatik ölçeklendirme sonraki sefer, CPU 50 'e düşmeye devam eder. Yeniden tahmin eder-50 x 3 örnek = 150/2 örnek = 75, bu, 80 genişletme eşiğinin altında, 2 örneğe başarıyla ölçeklenir.

> [!NOTE]
> Otomatik ölçeklendirme motoru, hedef örnek sayısına ölçeklendirilmesi sonucu olarak flama gerçekleşirse, aynı zamanda geçerli sayı ile hedef sayısı arasında farklı sayıda örneğe ölçeklendirmeye çalışır. Bu aralıkta Flam gerçekleşmezse, otomatik ölçeklendirme yeni hedefle ölçek işlemine devam eder.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Özel ölçümler için eşik değerleri ölçeklendirme konusunda dikkat edilmesi gerekenler
 Depolama veya Service Bus kuyruğu uzunluğu ölçümü gibi özel ölçümler için eşik, geçerli örnek sayısı başına kullanılabilen ortalama ileti sayısıdır. Bu ölçüm için eşik değerini dikkatle seçin.

Davranışı daha iyi anladığınızdan emin olmak için bunu bir örnekle gösterelim.

* Depolama kuyruğu ileti sayımında örnekleri 1 sayıya göre artır >= 50
* Depolama kuyruğu ileti sayımında örnekleri 1 sayıya göre azalt <= 10

Aşağıdaki sırayı göz önünde bulundurun:

1. İki depolama kuyruğu örneği vardır.
2. İletiler yakında devam ederse ve depolama kuyruğunu gözden geçirdikten sonra toplam sayı 50 okur. Otomatik ölçeklendirmeyi bir ölçek genişletme eylemi başlatması gerektiğini varsayabilirsiniz. Ancak, örnek başına hala 50/2 = 25 ileti olduğunu unutmayın. Bu nedenle, ölçeği genişletme gerçekleşmez. İlk genişleme için, depolama sırasındaki toplam ileti sayısı 100 olmalıdır.
3. Sonra, toplam ileti sayısının 100 ' a ulaştığını varsayın.
4. Bir ölçek genişletme eylemi nedeniyle üçüncü bir depolama kuyruğu örneği eklendi.  Sonraki genişleme eylemi, kuyruktaki toplam ileti sayısı 150/3 = 50 nedeniyle 150 ' a ulaşıncaya kadar gerçekleşmeyecektir.
5. Artık kuyruktaki ileti sayısı küçüktür. Üç örnek ile, tüm kuyruklardaki toplam ileti 30 ' a kadar, her bir örnek için (ölçek-ın eşiği) 30/3 = 10 ileti eklendiğinde ilk ölçeklendirme eylemi gerçekleşir.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Birden fazla profil otomatik ölçeklendirme ayarında yapılandırıldığında ölçeklendirme konusunda dikkat edilmesi gerekenler
Otomatik ölçeklendirme ayarında, her zaman zamanlama veya zaman bağımlılığı olmadan uygulanan bir varsayılan profil seçebilirsiniz veya bir yinelenen profil ya da tarih ve saat aralığı ile sabit bir dönem için bir profil seçebilirsiniz.

Otomatik Ölçeklendirme hizmeti onları işlediğinde, her zaman aşağıdaki sırayı denetler:

1. Sabit tarih profili
2. Yinelenen profil
3. Varsayılan ("Always") profili

Bir profil koşulu karşılanırsa, otomatik ölçeklendirme bunun altındaki sonraki profil koşulunu denetlemez. Otomatik ölçeklendirme tek seferde yalnızca bir profili işler. Bu, alt katman profilinden bir işlem koşulu da eklemek istiyorsanız, bu kuralları geçerli profile de dahil etmeniz gerekir.

Örnek kullanarak gözden geçirelim:

Aşağıdaki görüntüde, varsayılan en düşük örnek profili = 2 ve en fazla örnek = 10 olan bir otomatik ölçeklendirme ayarı gösterilmektedir. Bu örnekte, kuyruktaki ileti sayısı üçten az olduğunda, sıradaki ileti sayısı 10 ' dan büyük olduğunda ve ölçeğe göre ölçeği genişletmek için kurallar yapılandırılır. Böylece kaynak iki ile on örnek arasında ölçeklendirebilir.

Ayrıca, Pazartesi için yinelenen bir profil kümesi vardır. Minimum örnekler = 3 ve en fazla örnek = 10 için ayarlanır. Bu, Pazartesi günü, ilk kez otomatik ölçeklendirmeyi bu koşulu denetlediğinde, örnek sayısı iki ise, en az üç olarak ölçeklendirir. Otomatik ölçeklendirme, eşleşen bu profil koşulunu bulmaya devam ettiğinde (Pazartesi), yalnızca bu profil için yapılandırılmış CPU tabanlı ölçek genişletme/iade kurallarını işler. Şu anda, kuyruk uzunluğunu denetlemez. Bununla birlikte, aynı zamanda sıra uzunluğu koşulunun denetlenmesini istiyorsanız, bu kuralları varsayılan profilden ve Pazartesi profilinizde de eklemeniz gerekir.

Benzer şekilde, otomatik ölçeklendirme varsayılan profile geri geçtiğinde, önce en düşük ve en yüksek koşulların karşılanıp karşılanmadığını denetler. Zaman içindeki örneklerin sayısı 12 ise, varsayılan profil için izin verilen üst sınır olan 10 ' a ölçeklendirir.

![Otomatik ölçeklendirme ayarları](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Birden fazla kural bir profilde yapılandırıldığında ölçeklendirme konusunda dikkat edilmesi gerekenler

Bir profilde birden çok kural ayarlamanız gerektiği durumlar vardır. Aşağıdaki otomatik ölçeklendirme kuralları, birden çok kural ayarlandığında otomatik ölçeklendirme motoru tarafından kullanılır.

*Ölçek Genişletme* sırasında, herhangi bir kural karşılanıyorsa otomatik ölçeklendirme çalışır.
*Ölçeklendirmede*, otomatik ölçeklendirme tüm kuralların karşılanmasını gerektirir.

Göstermek için, aşağıdaki dört otomatik ölçeklendirme kuralına sahip olduğunu varsayalım:

* CPU <% 30, ölçeği 1 ' de
* Bellek <% 50, ölçeği 1
* CPU >% 75, ölçeği genişletme 1
* Bellek >% 75, ölçeği genişletme 1

Ardından, aşağıdaki durum oluşur:

* CPU %76 ise ve bellek %50 ise ölçeği ölçeklendiririz.
* CPU %50 ise ve bellek %76 ise ölçeği ölçeklendiririz.

Öte yandan, CPU %25 ise ve bellek %51 ise ölçek ölçeklendirme **yapmaz** . Ölçeği ölçeklendirmek için CPU %29 ve bellek %49 olmalıdır.

### <a name="always-select-a-safe-default-instance-count"></a>Her zaman güvenli bir varsayılan örnek sayısı seçin
Varsayılan örnek sayısı, ölçümler kullanılabilir olmadığında hizmetinize otomatik ölçeklendirme bu sayıya ölçeklenirken önemlidir. Bu nedenle, iş yükleriniz için güvenli olan bir varsayılan örnek sayısı seçin.

### <a name="configure-autoscale-notifications"></a>Otomatik ölçeklendirme bildirimlerini yapılandırma
Aşağıdaki koşullardan biri gerçekleşirse otomatik ölçeklendirme etkinlik günlüğüne gönderilir:

* Otomatik ölçeklendirme bir ölçeklendirme işlemi verir.
* Otomatik Ölçeklendirme hizmeti bir ölçeklendirme eylemini başarıyla tamamlar.
* Otomatik Ölçeklendirme hizmeti bir ölçeklendirme eylemi alamaz.
* Otomatik Ölçeklendirme hizmeti için ölçümler, ölçek kararı vermek üzere kullanılamaz.
* Ölçümler, bir ölçek kararı vermek için yeniden kullanılabilir (kurtarma).
* Otomatik ölçeklendirme, ölçeklendirme denemesini algılar ve iptal eder. Bu durumda bir günlük türü görürsünüz `Flapping` . Bunu görürseniz, eşiklerinizin çok dar olup olmadığını göz önünde bulundurun.
* Otomatik ölçeklendirme flamı algılar, ancak hala başarıyla ölçeklenebiliyor. Bu durumda bir günlük türü görürsünüz `FlappingOccurred` . Bunu görürseniz, otomatik ölçeklendirme motoru ölçeklendirmeyi denedi (örn. 4 örneklerden 2 ' ye kadar), ancak bunun flama neden olacağını belirledi. Bunun yerine, otomatik ölçeklendirme motoru farklı sayıda örneğe ölçeklendirildi (ör. 2 yerine 3 örnek kullanılıyor), bu nedenle bu sayıda örneğe ölçeklendirildi.

Otomatik ölçeklendirme altyapısının sistem durumunu izlemek için bir etkinlik günlüğü uyarısı da kullanabilirsiniz. Aboneliğinizde [tüm otomatik ölçeklendirme motoru işlemlerini izlemek için bir etkinlik günlüğü uyarısı oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) veya [aboneliğinizde başarısız olan tüm otomatik ölçeklendirme ölçeği ölçeğini/ölçeği genişletme işlemlerini izlemek](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)Için bir etkinlik günlüğü uyarısı oluşturma örnekleri verilmiştir.

Etkinlik günlüğü uyarılarını kullanmanın yanı sıra, otomatik ölçeklendirme ayarında Bildirimler sekmesi aracılığıyla başarılı ölçeklendirme eylemleri hakkında bildirim almak için e-posta veya Web kancası bildirimleri de yapılandırabilirsiniz.

## <a name="next-steps"></a>Sonraki Adımlar
- [Aboneliğinizdeki tüm otomatik ölçeklendirme motoru işlemlerini izlemek için bir etkinlik günlüğü uyarısı oluşturun.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Aboneliğinizdeki tüm başarısız otomatik ölçeklendirme ölçeği ölçeğini/ölçeği genişletme işlemlerini izlemek için bir etkinlik günlüğü uyarısı oluşturun](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

