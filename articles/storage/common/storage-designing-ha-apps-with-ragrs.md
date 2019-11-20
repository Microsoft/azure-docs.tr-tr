---
title: Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GZRS veya RA-GRS) kullanarak yüksek oranda kullanılabilir uygulamalar tasarlama | Microsoft Docs
description: Yüksek oranda kullanılabilir bir uygulamayı kesintileri işleyecek kadar esnek bir şekilde mimarmak için Azure RA-GZRS veya RA-GRS depolama alanı kullanma.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: a6d724f834fb8a4c54cd613c61ca90a77a36bdea
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673122"
---
# <a name="designing-highly-available-applications-using-read-access-geo-redundant-storage"></a>Okuma Erişimli Coğrafi olarak yedekli depolamayı kullanarak yüksek oranda kullanılabilir uygulamalar tasarlama

Azure depolama gibi bulut tabanlı altyapılardan oluşan ortak bir özellik, uygulamaları barındırmak için yüksek oranda kullanılabilir bir platform sağlamalarına yöneliktir. Bulut tabanlı uygulamaların geliştiricileri, kullanıcılarına yüksek düzeyde kullanılabilir uygulamalar sunmak için bu platformun nasıl yararlanacağınızı dikkatle düşünmelidir. Bu makalede, geliştiricilerin Azure depolama uygulamalarının yüksek oranda kullanılabilir olmasını sağlamak için coğrafi olarak yedekli çoğaltma seçeneklerinden birini nasıl kullanabilecekleri açıklanır.

Coğrafi olarak yedekli çoğaltma için yapılandırılan depolama hesapları, birincil bölgede zaman uyumlu olarak çoğaltılır ve sonra yüzlerce mil olan ikincil bir bölgeye zaman uyumsuz olarak çoğaltılır. Azure depolama, iki tür coğrafi olarak yedekli çoğaltma sunar:

* [Coğrafi bölge yedekli depolama (GZRS) (Önizleme)](storage-redundancy-gzrs.md) , hem yüksek kullanılabilirlik hem de maksimum dayanıklılık gerektiren senaryolar için çoğaltma sağlar. Veriler, bölgesel olarak yedekli depolama (ZRS) kullanılarak birincil bölgedeki üç Azure kullanılabilirlik alanı arasında zaman uyumlu olarak çoğaltılır, ardından ikincil bölgeye zaman uyumsuz olarak çoğaltılır. İkincil bölgedeki verilere yönelik okuma erişimi için, Okuma Erişimli Coğrafi bölge yedekli depolamayı (RA-GZRS) etkinleştirin.
* [Coğrafi olarak yedekli depolama (GRS)](storage-redundancy-grs.md) , bölgesel kesintilere karşı koruma sağlamak için çapraz bölgesel çoğaltma sağlar. Veriler yerel olarak yedekli depolama (LRS) kullanılarak birincil bölgede eşzamanlı olarak üç kez çoğaltılır ve ardından zaman uyumsuz olarak ikincil bölgeye çoğaltılır. İkincil bölgedeki verilere yönelik okuma erişimi için Okuma Erişimli Coğrafi olarak yedekli depolamayı (RA-GRS) etkinleştirin.

Bu makalede, uygulamanızı birincil bölgedeki bir kesinti işleyecek şekilde nasıl tasarlayacağız. Birincil bölge kullanılamaz duruma gelirse, uygulamanız bunun yerine ikincil bölgeye karşı okuma işlemleri gerçekleştirmeye uyarlayabilir. Başlamadan önce depolama hesabınızın RA-GRS veya RA-GZRS için yapılandırıldığından emin olun.

Hangi birincil bölgelerin hangi ikincil bölgelere eşleştirildiği hakkında bilgi için bkz. [Iş sürekliliği ve olağanüstü durum kurtarma (BCDR): Eşleştirilmiş Azure Bölgeleri](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Bu makaleye dahil kod parçacıkları vardır ve sonunda, indirebileceğiniz ve çalıştırabileceğiniz bir bütün örneğe yönelik bir bağlantı bulunur.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>İkincil sunucudan okurken uygulama tasarımında dikkat edilmesi gerekenler

Bu makalenin amacı, birincil veri merkezinde önemli bir olağanüstü durum oluşması durumunda bile çalışmaya devam edecek bir uygulamayı (sınırlı bir kapasiteye göre) nasıl tasarlayacağınızı gösterir. Birincil bölgeden okumayı kesintiye uğratan bir sorun olduğunda, ikincil bölgeden okuma yaparak, geçici veya uzun süreli sorunları işleyecek şekilde uygulamanızı tasarlayabilirsiniz. Birincil bölge yeniden kullanılabilir olduğunda, uygulamanız birincil bölgeden okuma 'ya dönebilir.

Uygulamanızı RA-GRS veya RA-GZRS için tasarlarken bu önemli noktaları aklınızda bulundurun:

* Azure depolama, birincil bölgenizde depoladığınız verilerin salt bir kopyasını bir ikincil bölgeye tutar. Yukarıda belirtildiği gibi, depolama hizmeti ikincil bölgenin konumunu belirler.

* Salt okuma kopyası sonunda birincil bölgedeki verilerle [tutarlıdır](https://en.wikipedia.org/wiki/Eventual_consistency) .

* Bloblar, tablolar ve kuyruklar için ikincil bölgeyi, birincil sunucudan ikincil bölgeye yapılan son çoğaltmanın ne zaman oluştuğunu bildiren bir *son eşitleme zamanı* değeri için sorgulayabilirsiniz. (Bu, şu anda RA-GRS yedekliği olmayan Azure dosyaları için desteklenmez.)

* Birincil veya ikincil bölgede veri okumak ve yazmak için depolama Istemci kitaplığını kullanabilirsiniz. Ayrıca, birincil bölgeye yönelik okuma isteği zaman aşımına uğrarsa okuma isteklerini otomatik olarak ikincil bölgeye yönlendirebilirsiniz.

* Birincil bölge kullanılamaz duruma gelirse, hesap yük devretmesini başlatabilirsiniz. İkincil bölgeye yük devretmek için, birincil bölgeye işaret eden DNS girdileri, ikincil bölgeyi gösterecek şekilde değiştirilir. Yük devretme işlemi tamamlandıktan sonra, GRS ve RA-GRS hesapları için yazma erişimi geri yüklenir. Daha fazla bilgi için bkz. [Azure Storage 'Da olağanüstü durum kurtarma ve depolama hesabı yük devretmesi (Önizleme)](storage-disaster-recovery-guidance.md).

> [!NOTE]
> Müşteri tarafından yönetilen hesap yük devretmesi (Önizleme) GZRS/RA-GZRS destekleyici bölgelerde henüz kullanılamadığından, müşteriler şu anda GZRS ve RA-GZRS hesaplarıyla hesap yük devretme olaylarını yönetemez. Önizleme sırasında, Microsoft GZRS/RA-GZRS hesaplarını etkileyen tüm yük devretme olaylarını yönetir.

### <a name="using-eventually-consistent-data"></a>Sonuçta tutarlı verileri kullanma

Önerilen çözüm, olası eski verileri çağıran uygulamaya döndürmek için kabul edilebilir olduğunu varsayar. İkincil bölgedeki veriler sonunda tutarlı olduğundan, İkincil bölgedeki bir güncelleştirmenin çoğaltma işlemi tamamlanmadan önce birincil bölge erişilemez hale gelebilir.

Örneğin, müşterinizin bir güncelleştirmeyi başarıyla gönderdiğini varsayalım, ancak güncelleştirme ikincil bölgeye yayılmadan önce birincil bölge başarısız olur. Müşteri verileri geri okumayı istediğinde, güncelleştirilmiş veriler yerine ikincil bölgeden eski verileri alırlar. Uygulamanızı tasarlarken, bu kabul edilebilir olup olmadığına ve varsa müşteriyi nasıl mesaj vereceğinize karar vermelisiniz. 

Bu makalenin ilerleyen kısımlarında, ikincil veriler için son eşitleme zamanının, ikincinin güncel olup olmadığını kontrol etmek için nasıl kontrol eteceğiz.

### <a name="handling-services-separately-or-all-together"></a>Hizmetleri ayrı ayrı veya birlikte işleme

Büyük olasılıkla, diğer hizmetler hala tamamen işlevsel olduğu sürece bir hizmetin kullanılamaz hale gelmesi mümkündür. Her hizmet için yeniden denemeler ve salt okuma modunu ayrı ayrı (blob 'lar, kuyruklar, tablolar) işleyebilir veya tüm depolama hizmetleri için yeniden denemeleri idare edebilirsiniz.

Örneğin, uygulamanızda kuyruklar ve Bloblar kullanıyorsanız, bunların her biri için yeniden denenebilir hataları işlemek üzere ayrı bir kod koymaya karar verebilirsiniz. Daha sonra blob hizmetinden yeniden deneme yaparsanız, ancak kuyruk hizmeti hala çalışıyorsa, yalnızca uygulamanızın Blobları işleyen kısmı etkilenecektir. Tüm depolama hizmeti yeniden denemeleri genel olarak işlemeye karar verirseniz ve BLOB hizmeti çağrısı yeniden denenebilir bir hata döndürürse, hem blob hizmetine hem de kuyruk hizmetine yönelik istekler etkilenecektir.

Sonuçta bu, uygulamanızın karmaşıklığına bağlıdır. Sorunları hizmet 'e göre işleyememeye karar verebilir, ancak tüm depolama hizmetleri için okuma isteklerini ikincil bölgeye yeniden yönlendirmek ve birincil bölgedeki herhangi bir depolama hizmetiyle ilgili bir sorun tespit ettiğinizde uygulamayı salt okuma modunda çalıştırmak isteyebilirsiniz.

### <a name="other-considerations"></a>Dikkat edilecek diğer noktalar

Bunlar, bu makalenin geri kalanında tartıştığımız diğer önemli noktalardır.

* Devre kesici modelini kullanarak okuma isteklerinin yeniden denemelerini işleme

* Sonuçta tutarlı veriler ve son eşitleme zamanı

* Test Etme

## <a name="running-your-application-in-read-only-mode"></a>Uygulamanızı salt okuma modunda çalıştırma

Birincil bölgedeki bir kesinti için etkin bir şekilde hazırlanması için, hem başarısız okuma isteklerini hem de başarısız güncelleştirme isteklerini işleyebilmelisiniz (Bu durumda, bu örnekte bu durum, güncelleştirmeler ve silinmeler). Birincil bölge başarısız olursa, okuma istekleri ikincil bölgeye yeniden yönlendirilebilir. Ancak, ikincil salt okunurdur, güncelleştirme istekleri ikinciye yeniden yönlendirilemiyor. Bu nedenle, uygulamanızı salt okuma modunda çalışacak şekilde tasarlamanız gerekir.

Örneğin, Azure depolama 'ya herhangi bir güncelleştirme isteği gönderilmeden önce denetlenen bir bayrak ayarlayabilirsiniz. Güncelleştirme isteklerinden biri aracılığıyla geldiğinde, bunu atlayabilir ve müşteriye uygun bir yanıt döndürebilirsiniz. Sorun çözümlenene kadar belirli özellikleri devre dışı bırakmak ve kullanıcılara bu özelliklerin geçici olarak kullanılamadığını bildirmek isteyebilirsiniz.

Her hizmet için hataları ayrı olarak işlemeye karar verirseniz, uygulamanızı hizmet tarafından salt okuma modunda çalıştırma özelliğini de gerçekleştirmeniz gerekir. Örneğin, etkinleştirilebilen ve devre dışı bırakılabilirler her hizmet için salt okuma bayraklarınız olabilir. Daha sonra, bu bayrağı kodunuzda uygun yerlerde işleyebilirsiniz.

Uygulamanızı salt okuma modunda çalıştırmak, bir yandan büyük bir uygulama yükseltmesi sırasında sınırlı işlevsellik sağlama olanağı sunar. Uygulamanızı salt okuma modunda çalışacak şekilde tetikleyip ikincil veri merkezini işaret ederken, yükseltme yaparken birincil bölgedeki verilere hiç kimsenin erişemeyeceğinden emin olabilirsiniz.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Salt okuma modunda çalışırken güncelleştirmeleri işleme

Salt okuma modunda çalışırken güncelleştirme isteklerini işlemenin birçok yolu vardır. Bu kavrama kapsamlı bir şekilde ele alınmayacağız, ancak genellikle göz önünde bulundurmanız gereken birkaç desen vardır.

1. Kullanıcıya yanıt verebilir ve şu anda güncelleştirmeleri kabul etmelerinizi söyleyebilirsiniz. Örneğin, bir iletişim yönetimi sistemi müşterilerin iletişim bilgilerine erişmesini sağlayabilir, ancak güncelleştirme yapamaz.

2. Güncelleştirmelerinizi başka bir bölgede sıraya alabilirsiniz. Bu durumda, bekleyen güncelleştirme isteklerinizi farklı bir bölgedeki bir kuyruğa yazar ve ardından birincil veri merkezi yeniden çevrimiçi olduktan sonra bu istekleri işlemek için bir yola sahip olursunuz. Bu senaryoda, müşterinin, istenen güncelleştirmenin daha sonra işlenmek üzere kuyruğa alındığından emin olmanız gerekir.

3. Güncelleştirmelerinizi, başka bir bölgedeki bir depolama hesabına yazabilirsiniz. Ardından, birincil veri merkezi yeniden çevrimiçi olduğunda, verilerin yapısına bağlı olarak, bu güncelleştirmeleri birincil verilerle birleştirmenin bir yolu olabilir. Örneğin, adında bir tarih/saat damgasıyla ayrı dosyalar oluşturuyorsanız, bu dosyaları birincil bölgeye geri kopyalayabilirsiniz. Bu, günlüğe kaydetme ve IoT verileri gibi bazı iş yükleri için geçerlidir.

## <a name="handling-retries"></a>Yeniden denemeleri işleme

Azure Storage istemci kitaplığı, hangi hataların yeniden deneneceği tespit etmenize yardımcı olur. Örneğin, bir 404 hatası (kaynak bulunamadı) yeniden denenebilecek ve bu durum başarılı bir şekilde sonuçlandığı için yeniden denenebilir. Diğer taraftan, bir 500 hatası bir sunucu hatası olduğundan yeniden denenemez ve yalnızca geçici bir sorun olabilir. Daha fazla ayrıntı için .NET depolama istemci kitaplığındaki [üs Alretry sınıfına yönelik açık kaynak kodunu](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) inceleyin. (ShouldRetry yöntemini arayın.)

### <a name="read-requests"></a>Okuma istekleri

Birincil depolamayla ilgili bir sorun varsa okuma istekleri ikincil depolamaya yönlendirilebilir. En [sonunda tutarlı verileri kullanma](#using-eventually-consistent-data)bölümünde belirtildiği gibi, uygulamanızın eski verileri okuyabilmesi için kabul edilebilir olması gerekir. İkincili verilere erişmek için depolama istemci kitaplığını kullanıyorsanız, **Locationmode** özelliği için bir değer ayarlayarak aşağıdakilerden birine bir okuma isteğinin yeniden deneme davranışını belirtebilirsiniz:

* **Yalnızca Primary(** varsayılan)

* **PrimaryThenSecondary**

* **Yalnızca Secondary**

* **SecondaryThenPrimary**

**Locationmode** öğesini **primarythensecondary**olarak belirlediğinizde, birincil uç noktaya ilk okuma isteği yeniden denenebilecek bir hata vererek başarısız olursa istemci, ikincil uç noktaya otomatik olarak başka bir okuma isteği oluşturur. Hata bir sunucu zaman aşımı ise, istemci, hizmetten yeniden denenebilir bir hata vermeden önce zaman aşımı süresinin dolmasını beklemek zorunda kalır.

Yeniden denenebilir bir hataya nasıl yanıt vereceğinize karar verirken dikkate alınması gereken temel olarak iki senaryo vardır:

* Bu yalıtılmış bir sorundur ve birincil uç noktaya yapılan sonraki istekler yeniden denenebilir bir hata döndürmez. Bunun gerçekleşebileceği bir örnek, geçici bir ağ hatası olduğunda gerçekleşir.

    Bu senaryoda, yalnızca seyrek olduğu için **Locationmode** 'U **primarythensecondary** olarak ayarlanmış önemli bir performans cezası yoktur.

* Bu, birincil bölgedeki depolama hizmetlerinden en az birinde ve birincil bölgedeki bu hizmete yapılan tüm sonraki isteklerin bir süre için yeniden denenebilir hata döndürmesine neden olabilir. Bunun bir örneği, birincil bölgenin tamamen erişilemez olması durumunda oluşur.

    Bu senaryoda, tüm okuma istekleriniz öncelikle birincil uç noktayı deneyeceğinden, zaman aşımının süresinin dolmasını bekleyip ikincil uç noktaya geçiş yapmak için bir performans cezası vardır.

Bu senaryolarda, birincil uç noktada devam eden bir sorun olduğunu ve tüm okuma isteklerini doğrudan ikincil uç noktaya göndererek **Locationmode** özelliğini **yalnızca secondaryolarak**ayarlayarak belirlemeniz gerekir. Şu anda, uygulamayı salt okuma modunda çalışacak şekilde de değiştirmelisiniz. Bu yaklaşım, [devre kesici stili](/azure/architecture/patterns/circuit-breaker)olarak bilinir.

### <a name="update-requests"></a>Güncelleştirme istekleri

Devre kesici stili, güncelleştirme isteklerine de uygulanabilir. Ancak, güncelleştirme istekleri, salt okunurdur olan ikincil depolamaya yeniden yönlendirilemez. Bu istekler için **Locationmode** özelliği **primaryonly** (varsayılan) olarak ayarlanmalıdır. Bu hataları işlemek için, bu isteklere bir ölçü (örneğin, bir satırda 10 hata) uygulayabilir ve eşik karşılandığında, uygulamayı salt okuma moduna geçirin. Güncelleştirme moduna geri dönmek için, devre kesici deseninin sonraki bölümünde açıklandığı gibi aynı yöntemleri kullanabilirsiniz.

## <a name="circuit-breaker-pattern"></a>Devre Kesici düzeni

Uygulamanızdaki devre kesici deseninin kullanılması, tekrar tekrar başarısız olan bir işlemi yeniden denemesini engelleyebilir. İşlem üstel olarak yeniden denenirken uygulamanın çalışmaya devam etmesini sağlar. Ayrıca hata düzeltildiğinde, uygulamanın işlemi yeniden denemesini de algılar.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Devre kesici modelini uygulama

Birincil uç noktayla devam eden bir sorun olduğunu belirlemek için, istemcinin yeniden denenebilir hatalar ile ne sıklıkta karşılaşduğunu izleyebilirsiniz. Her durum farklı olduğundan, ikincil uç noktaya geçiş yapmak ve uygulamayı salt okunurdur modunda çalıştırmak için kullanmak istediğiniz eşiğe karar vermeniz gerekir. Örneğin, başarılı olmayan bir satırda 10 başarısızlık varsa anahtarı gerçekleştirmeye karar verebilirsiniz. 2 dakikalık bir dönemdeki isteklerin% 90 ' i başarısız olursa başka bir örnek de bu bir örnektir.

İlk senaryo için yalnızca hataların sayısını tutabilir ve en büyük sayıya ulaşmadan önce bir başarı varsa, sayıyı tekrar sıfır olarak ayarlayın. İkinci senaryo için, bunu uygulamak için bir yol MemoryCache nesnesini kullanmaktır (.NET 'te). Her bir istek için önbelleğe bir CacheItem ekleyin, değeri Success (1) veya fail (0) olarak ayarlayın ve sona erme süresini şimdi 2 dakika olarak ayarlayın (ya da zaman kısıtlamaınız olsun). Bir girdinin süre sonu zamanına ulaşıldığında, giriş otomatik olarak kaldırılır. Bu, 2 dakikalık bir pencere sağlar. Depolama hizmeti için her istek yaptığınızda, ilk olarak bellek yüzdesini hesaplamak için MemoryCache nesnesi genelinde bir LINQ sorgusu kullanırsınız ve sayı ile ayırarak toplam yüzde değerini hesaplayabilirsiniz. Toplam yüzde değeri bir eşiğin altına düştüğünde (örneğin% 10), okuma istekleri için **Locationmode** özelliğini **secondaryonly** olarak ayarlayın ve devam etmeden önce uygulamayı salt okuma moduna geçirin.

Anahtarın uygulamanızda hizmetten hizmete ne zaman değişebileceğini belirlemede kullanılan hata eşiği, bu nedenle bunları yapılandırılabilir parametreler yapmayı düşünmelisiniz. Bu, daha önce anlatıldığı gibi, her bir hizmetten ayrı ayrı veya tek bir yeniden denenebilir hata işlemeye karar vereceğiniz yerdir.

Diğer bir deyişle, bir uygulamanın birden çok örneğini işleme ve her bir örnekteki yeniden denenebilir hataları algılamadığınızda yapmanız gerekenler. Örneğin, aynı uygulama yüklü olan 20 VM çalıştırıyor olabilirsiniz. Her örneği ayrı olarak işlemeyin misiniz? Bir örnek sorunla karşılaşmaya başlarsa, yanıtı yalnızca bir örneğe sınırlamak ister veya bir örnek bir sorun olduğunda tüm örneklerin aynı şekilde yanıt vermesini denemek istiyor musunuz? Örneklerin ayrı olarak işlenmesi, yanıtları bunlar genelinde koordine edilmeye çalışırken çok daha basittir, ancak bunu yapma yöntemi uygulamanızın mimarisine bağlıdır.

### <a name="options-for-monitoring-the-error-frequency"></a>Hata sıklığını izlemeye yönelik seçenekler

Birincil bölgedeki yeniden deneme sıklığını izlemek için üç ana seçeneğiniz vardır ve bu, ikincil bölgeye ne zaman geçip uygulamayı salt okuma modunda çalışacak şekilde değiştirebilir.

* Depolama isteklerinizi geçirdiğiniz [**OperationContext**](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) nesnesinde yeniden [**deneme**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) olayı için bir işleyici ekleyin; Bu yöntem bu makalede görüntülenir ve birlikte gelen örnekte kullanılır. Bu olaylar istemci bir isteği yeniden denediğinde, istemcinin bir birincil uç noktada yeniden denenebilir hata ile karşılaşacağını izlemenize olanak sağlar.

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

* Özel bir yeniden deneme ilkesindeki [**değerlendir**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) yönteminde, her yeniden deneme gerçekleştiğinde özel kod çalıştırabilirsiniz. Bir yeniden deneme gerçekleştiğinde kayda ek olarak, bu da yeniden deneme davranışınızı değiştirme fırsatını sağlar.

    ```csharp 
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```

* Üçüncü yaklaşım, uygulamanızda birincil depolama uç noktanıza (küçük bir blobu okumak gibi) sistem durumunu öğrenmek için sürekli okuma istekleri (örneğin, küçük bir blob okuma) ile sürekli olarak ping bir özel izleme bileşeni uygulamaktır. Bu, bazı kaynakları ele geçirebilir, ancak önemli miktarda değildir. Eşiğine ulaşan bir sorun keşfedildiğinde, anahtarı **yalnızca Secondaryve** salt okunurdur modunda gerçekleştirirsiniz.

Bir noktada, birincil uç noktayı kullanmaya ve güncelleştirmelere izin vermeye geri dönmek isteyeceksiniz. Yukarıda listelenen ilk iki yöntemden birini kullanıyorsanız, yalnızca birincil uç noktaya geçiş yapmanız ve rastgele seçilmiş bir süre veya işlem sayısı gerçekleştirildikten sonra güncelleştirme modunu etkinleştirmeniz yeterlidir. Daha sonra yeniden deneme mantığı aracılığıyla yeniden çalışmaya devam edebilirsiniz. Sorun giderilmediyse, birincil uç noktayı kullanmaya ve güncelleştirmelere izin vermeyi sürdürür. Hala bir sorun varsa, ayarlamış olduğunuz ölçütlerle başarısız olduktan sonra ikincil uç noktaya ve salt okunurdur moduna daha fazla geçiş yapar.

Üçüncü senaryoda, birincil depolama uç noktasının ping işlemi yeniden başarılı hale geldiğinde, anahtarı **yalnızca primaryto** 'a tetikleyip güncelleştirmelere izin vermeye devam edebilirsiniz.

## <a name="handling-eventually-consistent-data"></a>Sonuçta tutarlı verileri işleme

Coğrafi olarak yedekli depolama, işlemleri birincil sunucudan ikincil bölgeye çoğaltarak işe yarar. Bu çoğaltma işlemi, İkincil bölgedeki verilerin *sonunda tutarlı*olmasını güvence altına alır. Bu, birincil bölgedeki tüm işlemlerin son olarak ikincil bölgede görüneceği, ancak görüntülenmeden önce bir gecikme olabileceği ve ikincil bölgede işlemlerin ikincil bölgeye ait oldukları ve bunların aynı sırada olduğu garantisi olmaması durumunda İlk olarak birincil bölgeye uygulandı. İşlemleriniz ikincil bölgeye sıra dışında geldiğinde, İkincil bölgedeki verilerinizi hizmet bitene kadar tutarsız bir durumda olacak *şekilde düşünebilirsiniz.*

Aşağıdaki tabloda, bir çalışanın ayrıntılarını *Yöneticiler* rolünün bir üyesi haline getirmek için güncelleştirdiğinizde neler gerçekleşebileceğini gösteren bir örnek gösterilmektedir. Bu örneğin, bu örnek için **çalışan** varlığını güncelleştirmenizi ve bir **yönetici rolü** varlığını, toplam yönetici sayısı sayısıyla güncelleştirmeniz gerekir. Güncelleştirmelerin ikincil bölgede nasıl uygulandığına dikkat edin.

| **saat** | **İşlem**                                            | **Çoğaltma**                       | **Son eşitleme zamanı** | **Sonuç** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | İşlem A: <br> Çalışan Ekle <br> birincil varlıktaki varlık |                                   |                    | Birincil öğesine ekli işlem<br> henüz çoğaltılmamıştır. |
| T1       |                                                            | İşlem A <br> çoğaltma<br> ikincil | T1 | İşlem ikinciye çoğaltılır. <br>Son eşitleme zamanı güncelleştirildi.    |
| T2       | İşlem B:<br>Güncelleştirme<br> Çalışan varlığı<br> birincil  |                                | T1                 | Birincil diske yazılan işlem B<br> henüz çoğaltılmamıştır.  |
| T3       | İşlem C:<br> Güncelleştirme <br>yönetici<br>içindeki rol varlığı<br>birincil |                    | T1                 | Birincil öğesine yazılan işlem C,<br> henüz çoğaltılmamıştır.  |
| *T4*     |                                                       | İşlem C <br>çoğaltma<br> ikincil | T1         | İşlem C, ikinciye çoğaltıldı.<br>LastSyncTime güncelleştirilmedi, çünkü <br>işlem B henüz çoğaltılmamıştır.|
| *T5*     | Varlıkları oku <br>ikincili                           |                                  | T1                 | Çalışan için eski değeri alırsınız <br> işlem B işlemi olmadığı için varlık <br> henüz çoğaltıldı. İçin yeni bir değer alırsınız<br> Yönetici rolü varlığı çünkü C<br> çoğaltılamaz. Son eşitleme saati hala değil<br> işlem B nedeniyle güncelleştirildi<br> çoğaltılmadı. Şunu yapabilirsiniz<br>Yönetici rolü varlığı tutarsız <br>varlık tarih/saat sonra olduğu için <br>Son eşitleme zamanı. |
| *T6*     |                                                      | İşlem B<br> çoğaltma<br> ikincil | T6                 | *T6* – C ile tüm işlemler <br>çoğaltılan, son eşitleme zamanı<br> güncelleştirildi. |

Bu örnekte, istemci, T5 adresindeki ikincil bölgeden okuma yapmak için anahtar olduğunu varsayalım. Şu anda **yönetici rolü** varlığını başarıyla okuyabilir, ancak varlık ikincil üzerinde yönetici olarak işaretlenen **çalışan** varlık sayısıyla tutarlı olmayan yönetici sayısı için bir değer içerir bölgesi şu anda. İstemciniz bu değeri, tutarsız bilgiler olması riskiyle tek bir şekilde görüntüleyebilir. Alternatif olarak, istemci, güncelleştirmeler sıralı olmadığından ve kullanıcıyı bu olguyu bilgilendirdiğinden, **yönetici rolünün** potansiyel olarak tutarsız bir durumda olduğunu belirlemeyi deneyebilir.

İstemci potansiyel olarak tutarsız veriler olduğunu tanımak için, bir depolama hizmetini sorgulayarak istediğiniz zaman alabileceğiniz *son eşitleme zamanının* değerini kullanabilir. Bu, İkincil bölgedeki verilerin en son tutarlı olduğu ve hizmetin bu noktadan önce tüm işlemleri uyguladığı zamanı gösterir. Yukarıdaki örnekte, hizmet **çalışan** varlığı ikincil bölgeye eklendikten sonra, son eşitleme zamanı *T1*olarak ayarlanır. Hizmet, *T6*olarak ayarlandığında, İkincil bölgedeki **çalışan** varlığını güncelleştirene kadar *T1* konumunda kalır. İstemci *T5*adresinde varlığı okurken son eşitleme saatini alıyorsa, varlığı varlığındaki zaman damgasıyla karşılaştırabilir. Varlıktaki zaman damgası son eşitleme zamanından daha sonra ise, varlık potansiyel olarak tutarsız bir durumda olur ve uygulamanız için uygun eylemi gerçekleştirebilirsiniz. Bu alanın kullanılması için son birincil güncelleştirmenin ne zaman tamamlandığını bilmeniz gerekir.

## <a name="getting-the-last-sync-time"></a>Son eşitleme zamanı alınıyor

Verilerin ikinciye en son ne zaman yazıldığını tespit etmek üzere PowerShell veya Azure CLı 'yi son eşitleme zamanını almak için kullanabilirsiniz.

### <a name="powershell"></a>PowerShell

PowerShell kullanarak depolama hesabının son eşitleme zamanını almak için, coğrafi çoğaltma istatistiklerini almayı destekleyen bir Azure depolama önizleme modülü yükler. Örneğin:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

Ardından depolama hesabının **Georeplicationstats. LastSyncTime** özelliğini denetleyin. Yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

### <a name="azure-cli"></a>Azure CLI

Azure CLı kullanarak depolama hesabının son eşitleme zamanını almak için depolama hesabının **Georeplicationstats. lastSyncTime** özelliğini denetleyin. **Georeplicationstats**altında iç içe yerleştirilmiş özelliklerin değerlerini döndürmek için `--expand` parametresini kullanın. Yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

## <a name="testing"></a>Test Etme

Yeniden denenebilir hata ile karşılaştığında uygulamanızın beklendiği gibi davrandığını test etmek önemlidir. Örneğin, bir sorun algıladığında uygulamanın ikinciye ve salt okuma moduna geçiş yapması ve birincil bölge yeniden kullanılabilir olduğunda geri geçiş yapmanız gerekir. Bunu yapmak için yeniden denenebilir hata benzetimi yapmak ve ne sıklıkta gerçekleştikleri denetlemek için bir yol gerekir.

Bir betikteki HTTP yanıtlarını kesme ve değiştirme için [Fiddler](https://www.telerik.com/fiddler) kullanabilirsiniz. Bu betik, birincil uç noktanıza gelen yanıtları tanımlayabilir ve HTTP durum kodunu depolama Istemci kitaplığının yeniden denenebilir hatası olarak tanıdığı bir hata olarak değiştirebilir. Bu kod parçacığı, 502 durumunu döndürmek için **employeedata** tablosuna yönelik okuma isteklerine yapılan yanıtları Izleyen bir Fiddler betiğinin basit bir örneğini gösterir:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Bu örneği, daha geniş bir istek aralığını ele geçirebilir ve yalnızca bir miktar üzerinde **yanıt kodunu** değiştirerek gerçek bir senaryoya daha iyi benzetim yapabilirsiniz. Fiddler betikleri özelleştirme hakkında daha fazla bilgi için, bkz. Fiddler belgelerindeki [bir isteği veya yanıtı değiştirme](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) .

Uygulamanızı, Salt okunabilir moda yapılandırılabilir hale getirmeye yönelik eşikler yaptıysanız, bu davranışı üretim dışı işlem birimleri ile test etmek daha kolay olacaktır.

## <a name="next-steps"></a>Sonraki Adımlar

* Son eşitleme zamanı özelliğinin nasıl ayarlandığı hakkında başka bir örnek de dahil olmak üzere ikincil bölgeden okuma hakkında daha fazla bilgi için bkz. [Azure Storage artıklık seçenekleri ve Okuma Erişimli Coğrafi olarak yedekli depolama](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Anahtarın birincil ve ikincil uç noktalar arasında geri ve ileri nasıl yapılacağını gösteren tam bir örnek için, bkz. [Azure örnekleri – RA-GRS depolama Ile devre kesici modelini kullanma](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
