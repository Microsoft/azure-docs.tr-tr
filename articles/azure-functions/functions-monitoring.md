---
title: Azure İşlevlerini İzleme
description: İşlev yürütmeyi izlemek için Azure Application Insights Azure Işlevleri 'ni kullanmayı öğrenin.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 10/14/2020
ms.custom: devx-track-csharp, fasttrack-edit, contperf-fy21q2, devx-track-js
ms.openlocfilehash: 637f09c5ee52928631b965dfa6caea9368b44991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550768"
---
# <a name="monitor-azure-functions"></a>Azure İşlevlerini İzleme

[Azure işlevleri](functions-overview.md) , işlevleri Izlemek için [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) ile yerleşik tümleştirme sunar. Bu makalede Azure Işlevleri 'ni izlemek için Azure tarafından sağlanan izleme özelliklerine genel bir bakış sunulmaktadır.

Application Insights günlük, performans ve hata verilerini toplar. Performans sorunlarını otomatik olarak algılayarak ve güçlü analiz araçları sunarak, sorunları daha kolay bir şekilde tanılayabilir ve işlevlerinizin nasıl kullanıldığını daha iyi anlayabilirsiniz. Bu araçlar, işlevlerinizin performansını ve kullanılabilirliğini sürekli olarak iyileştirmenize yardımcı olmak için tasarlanmıştır. Application Insights, yerel işlev uygulama projesi geliştirme sırasında bile kullanabilirsiniz. Daha fazla bilgi için bkz. [Application Insights nedir?](../azure-monitor/app/app-insights-overview.md).

Application Insights izleme Azure Işlevleri 'nde yerleşik olduğundan, işlev uygulamanızı bir Application Insights kaynağına bağlamak için geçerli bir izleme anahtarına ihtiyacınız vardır. Azure 'da işlev uygulaması kaynağını oluştururken, izleme anahtarı uygulama ayarlarınıza eklenir. İşlev uygulamanız zaten bu anahtara sahip değilse, [el ile ayarlayabilirsiniz](configure-monitoring.md#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Fiyatlandırma ve limitleri Application Insights

Azure Işlevleri ile Application Insights tümleştirmeyi, ücretsiz olarak ne kadar veri işlendiği hakkında günlük bir sınıra sahip olmak için ücretsiz olarak deneyebilirsiniz.

Geliştirme sırasında uygulama öngörülerini etkinleştirirseniz, sınama sırasında bu sınıra ulaşırsınız. Günlük sınırınıza yaklaşdığınızda Azure, Portal ve e-posta bildirimleri sağlar. Bu uyarıları kaçırırsanız ve Sınıra ulaşırsanız yeni Günlükler Application Insights sorgularda görünmez. Gereksiz sorun giderme zamanından kaçının limiti göz önünde bulundurun. Daha fazla bilgi için bkz. [Application Insights fiyatlandırma ve veri hacmini yönetme](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> Application Insights, yoğun yük saatlerinde tamamlanan yürütmeler üzerinde çok fazla telemetri verisi üretmenin bir [örnekleme](../azure-monitor/app/sampling.md) özelliğine sahiptir. Örnekleme varsayılan olarak etkindir. Eksik veri olduğunu görürseniz, örnekleme ayarlarını belirli izleme senaryonuza uyacak şekilde ayarlamanız gerekebilir. Daha fazla bilgi için bkz. [örneklemesi yapılandırma](configure-monitoring.md#configure-sampling).

İşlev uygulamanız için kullanılabilen Application Insights özelliklerinin tam listesi, [Azure işlevleri tarafından desteklenen özellikler için Application Insights](../azure-monitor/app/azure-functions-supported-features.md)ayrıntılı olarak açıklanmıştır.

## <a name="application-insights-integration"></a>Application Insights tümleştirme

Genellikle, işlev uygulamanızı oluştururken bir Application Insights örneği oluşturursunuz. Bu durumda, tümleştirme için gerekli olan izleme anahtarı zaten *APPINSIGHTS_INSTRUMENTATIONKEY* adlı bir uygulama ayarı olarak ayarlanmıştır. Bir nedenden dolayı, işlev uygulamanızın izleme anahtarı ayarlanmamışsa, [Application Insights tümleştirmesini etkinleştirmeniz](configure-monitoring.md#enable-application-insights-integration)gerekir.  

## <a name="collecting-telemetry-data"></a>Telemetri verilerini toplama

Application Insights tümleştirme etkinken, telemetri verileri bağlı Application Insights örneğinize gönderilir. Bu veriler, Işlevler ana bilgisayarı tarafından oluşturulan günlükleri, işlevlerinizin kodınızdan yazılmış izlemeleri ve performans verilerini içerir. 

>[!NOTE]
>İşlevlerinizin ve Işlevler ana bilgisayarındaki verilerin yanı sıra, [işlevler ölçek denetleyicisinden](#scale-controller-logs)de veri toplayabilirsiniz.   

### <a name="log-levels-and-categories"></a>Günlük düzeyleri ve Kategoriler

Uygulama kodunuzun izlemelerini yazdığınızda, izlemelere bir günlük düzeyi atamanız gerekir. Günlük düzeyleri, izlemelerinizi toplanan veri miktarını sınırlandırmanız için bir yol sağlar.  

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Günlük düzeyleri hakkında daha fazla bilgi için bkz. [günlük düzeylerini yapılandırma](configure-monitoring.md#configure-log-levels).

Günlüğe kaydedilmiş öğeleri bir kategoriye atayarak, işlev uygulamanızda belirli kaynaklardan oluşturulan telemetri üzerinde daha fazla denetime sahip olursunuz. Kategoriler, toplanan veriler üzerinde analiz çalıştırmanızı kolaylaştırır. İşlev kodunuzdan yazılan izlemeler, işlev adına göre ayrı kategorilere atanır. Kategoriler hakkında daha fazla bilgi için bkz. [kategorileri yapılandırma](configure-monitoring.md#configure-categories).

### <a name="custom-telemetry-data"></a>Özel telemetri verileri

[C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions) ve [JavaScript](functions-reference-node.md#log-custom-telemetry)'te, özel telemetri verileri yazmak için bir Application Insights SDK kullanabilirsiniz.

### <a name="dependencies"></a>Bağımlılıklar

Çalışma zamanı, sürüm 2. x Işlevlerinden başlayarak, belirli istemci SDK 'larını kullanan bağlamalardaki verileri otomatik olarak toplar. Application Insights aşağıdaki bağımlılıklarda verileri toplar:

+ Azure Cosmos DB 
+ Azure Event Hubs
+ Azure Service Bus
+ Azure depolama hizmetleri (blob, kuyruk ve tablo)

Kullanılarak yapılan HTTP istekleri ve veritabanı çağrıları `SqlClient` da yakalanır. Application Insights tarafından desteklenen bağımlılıkların tüm listesi için bkz. [otomatik olarak izlenen bağımlılıklar](../azure-monitor/app/asp-net-dependencies.md#automatically-tracked-dependencies).

Application Insights, toplanan bağımlılık verilerinin _uygulama haritasını_ oluşturur. Aşağıda, bir kuyruk depolama çıkış bağlaması olan bir HTTP tetikleyici işlevinin uygulama Haritası örneği verilmiştir.  

![Bağımlılık ile uygulama Haritası](./media/functions-monitoring/app-map.png)

Bağımlılıklar, `Information` düzeyinde yazılır. Veya üzerine filtre yaparsanız `Warning` , bağımlılık verilerini görmezsiniz. Ayrıca, bağımlılıkların otomatik toplanması Kullanıcı olmayan bir kapsamda gerçekleşir. Bağımlılık verilerini yakalamak için, düzeyin `Information` ana bilgisayarınızdaki Kullanıcı kapsamının () dışında en az olarak ayarlandığından emin olun `Function.<YOUR_FUNCTION_NAME>.User` .

Otomatik bağımlılık veri toplamaya ek olarak, Özel bağımlılık bilgilerini günlüklere yazmak için dile özgü Application Insights SDK 'Lardan birini de kullanabilirsiniz. Özel bağımlılıkların nasıl yazılacağı bir örnek için, aşağıdaki dile özgü örneklerden birine bakın:

+ [C# işlevlerinde özel telemetriyi günlüğe kaydetme](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions)
+ [JavaScript işlevlerinde özel telemetriyi günlüğe kaydetme](functions-reference-node.md#log-custom-telemetry) 

## <a name="writing-to-logs"></a>Günlüklere yazma 

Günlükleri ve kullandığınız API 'Leri yazma yöntemi, işlev uygulaması projenizin diline bağlıdır.   
İşlevlerinizin günlüklerini yazma hakkında daha fazla bilgi edinmek için dilinize yönelik geliştirici kılavuzuna bakın.

+ [C# (.NET sınıf kitaplığı)](functions-dotnet-class-library.md#logging)
+ [Java](functions-reference-java.md#logger)
+ [JavaScript](functions-reference-node.md#write-trace-output-to-logs) 
+ [PowerShell](functions-reference-powershell.md#logging)
+ [Python](functions-reference-python.md#logging)

## <a name="analyze-data"></a>Verileri çözümleme

Varsayılan olarak, işlev uygulamanızdan toplanan veriler Application Insights depolanır. [Azure Portal](https://portal.azure.com), Application Insights telemetri verilerinizin kapsamlı bir görselleştirme kümesini sağlar. Hata günlükleri ve sorgu olayları ve ölçümleri detaylarına gidebilirsiniz. Toplanan verilerinizi görüntüleme ve sorgulama hakkında temel örnekler de dahil olmak üzere daha fazla bilgi edinmek için bkz. [Application Insights Azure işlevleri telemetrisini çözümleme](analyze-telemetry-data.md). 

## <a name="streaming-logs"></a>Akış günlükleri

Bir uygulama geliştirirken genellikle Azure 'da çalışırken neredeyse gerçek zamanlı olarak günlüklere ne yazıldığını görmek istersiniz.

İşlev yürütmeleri tarafından oluşturulan günlük verilerinin akışını görüntülemenin iki yolu vardır.

* **Yerleşik günlük akışı**: App Service platformu, uygulama günlüğü dosyalarınızın akışını görüntülemenize olanak sağlar. Bu akış, [yerel geliştirme](functions-develop-local.md) sırasında işlevlerinizi hata ayıkladığınızda ve portalda **Test** sekmesini kullandığınızda görülen çıkış ile eşdeğerdir. Günlük tabanlı tüm bilgiler görüntülenir. Daha fazla bilgi için bkz. [akış günlükleri](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Bu akış yöntemi yalnızca tek bir örneği destekler ve bir tüketim planında Linux üzerinde çalışan bir uygulamayla birlikte kullanılamaz.

* **Canlı ölçüm akışı**: işlev uygulamanız [Application Insights bağlandığı](configure-monitoring.md#enable-application-insights-integration)zaman, Azure Portal [canlı ölçüm akışı](../azure-monitor/app/live-stream.md)kullanarak günlük verilerini ve diğer ölçümleri neredeyse gerçek zamanlı olarak görebilirsiniz. Bir tüketim planında birden çok örnek veya Linux üzerinde çalışan işlevleri izlerken bu yöntemi kullanın. Bu yöntem [örneklenmiş verileri](configure-monitoring.md#configure-sampling)kullanır.

Günlük akışları hem portalda hem de birçok yerel geliştirme ortamında görüntülenebilir. Günlük akışlarını etkinleştirmeyi öğrenmek için bkz. [Azure işlevlerinde akış yürütme günlüklerini etkinleştirme](streaming-logs.md).

## <a name="diagnostic-logs"></a>Tanılama günlükleri

_Bu özellik önizleme aşamasındadır._ 

Application Insights, telemetri verilerini uzun süreli depolamaya veya diğer Analysis Services 'e aktarmanıza olanak tanır.  

Işlevler aynı zamanda Azure Izleyici ile tümleştiğinden, Azure Izleyici günlükleri de dahil olmak üzere çeşitli hedeflere telemetri verileri göndermek için de tanılama ayarlarını kullanabilirsiniz. Daha fazla bilgi edinmek için bkz. Azure [Izleme günlükleri Ile Azure Işlevlerini izleme](functions-monitor-log-analytics.md).

## <a name="scale-controller-logs"></a>Denetleyici günlüklerini ölçeklendirme

_Bu özellik önizleme aşamasındadır._ 

[Azure işlevleri ölçek denetleyicisi](./event-driven-scaling.md#runtime-scaling) , uygulamanızın çalıştırıldığı Azure işlevleri ana bilgisayarının örneklerini izler. Bu denetleyici, geçerli performansa göre örnek ekleme veya kaldırma hakkında kararlar verir. Ölçek denetleyicisinin işlev uygulamanız için yapmakta olduğu kararları daha iyi anlamak için ölçek denetleyicisinin günlükleri Application Insights ' ye yaymasına sahip olabilirsiniz. Oluşturulan günlükleri başka bir hizmet tarafından analiz edilmek üzere BLOB depolama alanında da saklayabilirsiniz. 

Bu özelliği etkinleştirmek için, işlev uygulaması ayarlarınıza adlı bir uygulama ayarı eklersiniz `SCALE_CONTROLLER_LOGGING_ENABLED` . Nasıl yapılacağını öğrenmek için bkz. [Ölçek denetleyicisi günlüklerini yapılandırma](configure-monitoring.md#configure-scale-controller-logs).

## <a name="report-issues"></a>Sorun bildirme

Işlevlerde Application Insights tümleştirmeyle ilgili bir sorun bildirmek veya bir öneri veya istek yapmak için [GitHub 'da bir sorun oluşturun](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Uygulama Bilgileri](/azure/application-insights/)
* [Günlüğe kaydetme ASP.NET Core](/aspnet/core/fundamentals/logging/)
