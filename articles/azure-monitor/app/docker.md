---
title: Azure Application Insights Docker uygulamalarını izleme | Microsoft Docs
description: Docker performans sayaçları, olaylar ve özel durumlar, Kapsayıcılı uygulamalardaki telemetri ile birlikte Application Insights ' de görüntülenebilir.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/14/2019
ms.openlocfilehash: 66a2481d25c863bbdbf4d72c4683a309918776db
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677929"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Application Insights (kullanım dışı) Docker uygulamalarını izleme

> [!NOTE]
> Bu çözüm kullanım dışı bırakıldı. Kapsayıcı izlemeyle ilgili mevcut yatırımlarımız hakkında daha fazla bilgi edinmek için [Azure izleyici 'yi kapsayıcılar için](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)kullanıma sunmamız önerilir.

[Docker](https://www.docker.com/) kapsayıcılarından gelen yaşam döngüsü olayları ve performans sayaçları Application Insights üzerinde grafiklenebilir. [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) görüntüsünü konaktaki bir kapsayıcıya yüklediğinizde, diğer görüntüler için de, ana bilgisayar için performans sayaçlarını görüntüler.

Docker sayesinde uygulamalarınızı basit kapsayıcılarda tüm bağımlılıklarla birlikte dağıtabilirsiniz. Bunlar, Docker altyapısını çalıştıran tüm ana makineler üzerinde çalışır.

Docker ana bilgisayarınızda [Application Insights görüntüsünü](https://hub.docker.com/r/microsoft/applicationinsights/) çalıştırdığınızda bu avantajları elde edersiniz:

* Konakta çalışan tüm kapsayıcılar hakkında yaşam döngüsü telemetrisi-başlatma, durdurma vb.
* Tüm kapsayıcılar için performans sayaçları. CPU, bellek, ağ kullanımı ve daha fazlası.
* Kapsayıcıda çalışan uygulamalarda [Java için APPLICATION INSIGHTS SDK 'yı yüklediyseniz](../../azure-monitor/app/java-get-started.md) , bu uygulamaların tüm telemetrisi, kapsayıcıyı ve ana makineyi tanımlayan ek özelliklere sahip olur. Örneğin, birden fazla konakta çalışan bir uygulamanın örneklerine sahipseniz, uygulama telemetrinizi ana bilgisayara göre kolayca filtreleyebilirsiniz.

## <a name="set-up-your-application-insights-resource"></a>Application Insights kaynağınızı ayarlama

1. [Microsoft Azure Portal](https://azure.com) oturum açın ve uygulamanız için Application Insights kaynağını açın; veya [Yeni bir tane oluşturun](../../azure-monitor/app/create-new-resource.md ). 
   
    *Hangi kaynağı kullanmalıyım?* Ana bilgisayarınızda çalıştırdığınız uygulamalar başkası tarafından geliştirilmişse, [Yeni bir Application Insights kaynağı oluşturmanız](../../azure-monitor/app/create-new-resource.md )gerekir. Telemetriyi görüntülediğiniz ve analiz ettiğiniz yerdir. (Uygulama türü için ' genel ' seçeneğini belirleyin.)
   
    Ancak uygulamaların geliştiricisiyseniz, her birine [Application Insights SDK 'sı eklemiş](../../azure-monitor/app/java-get-started.md) olduğunuzu umuyoruz. Tek bir iş uygulamasının tüm gerçekten bileşenleri varsa, bunların tümünü tek bir kaynağa telemetri gönderecek şekilde yapılandırabilir ve aynı kaynağı kullanarak Docker yaşam döngüsünü ve performans verilerini görüntüleyebilirsiniz. 
   
    Üçüncü senaryo birçok uygulamayı geliştirdik, ancak Telemetriyi göstermek için ayrı kaynaklar kullanıyorsunuz. Bu durumda muhtemelen Docker verileri için ayrı bir kaynak oluşturmak da isteyebilirsiniz.

2. **Temel** bileşenler açılan düğmesine tıklayın ve izleme anahtarını kopyalayın. Bunu SDK 'nın Telemetriyi nereye gönderileceğini söylemek için kullanırsınız.

Telemetrinize bakmak için yakında geri döneceksiniz, tarayıcı penceresini kullanışlı tutun.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Application Insights izleyicisini konakta çalıştırın

Telemetriyi görüntülemenin bir yere sahip olduğunuza göre, onu toplayıp gönderecek Kapsayıcılı uygulamayı ayarlayabilirsiniz.

1. Docker konağına bağlanın.
2. İzleme anahtarınızı bu komutla düzenleyin ve sonra çalıştırın:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Docker Konağı başına yalnızca bir Application Insights görüntüsü gerekir. Uygulamanız birden çok Docker konağında dağıtılmışsa, komutu her konakta yineleyin.

## <a name="update-your-app"></a>Uygulamanızı güncelleştirme
Uygulamanız [Java için Application Insights SDK 'sı](../../azure-monitor/app/java-get-started.md)ile birlikte işaretlenmiş ise, `<TelemetryInitializers>` öğesi altında, projenizdeki ApplicationInsights. xml dosyasına aşağıdaki satırı ekleyin:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Bu, uygulamanızdan gönderilen her telemetri öğesine kapsayıcı ve ana bilgisayar kimliği gibi Docker bilgilerini ekler.

## <a name="view-your-telemetry"></a>Telemetrinizi görüntüleme
Azure portal Application Insights kaynağına geri dönün.

Docker kutucuğuna tıklayın.

Özellikle de Docker altyapınız üzerinde çalışan başka kapsayıcılarınız varsa Docker uygulamasından gelen verileri görürsünüz.

### <a name="docker-container-events"></a>Docker kapsayıcı olayları
![Örneğinde](./media/docker/13.png)

Ayrı olayları araştırmak için [Ara](../../azure-monitor/app/diagnostic-search.md)' ya tıklayın. İstediğiniz olayları bulmak için arama yapın ve filtre uygulayın. Daha ayrıntılı bilgi almak için herhangi bir olaya tıklayın.

### <a name="exceptions-by-container-name"></a>Kapsayıcı adına göre özel durumlar
![Örneğinde](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Uygulama telemetrisine Docker bağlamı eklendi
AI SDK ile işaretlenmiş uygulamadan gönderilen istek telemetrisi, Docker bağlam bilgileriyle uyumlu.

## <a name="q--a"></a>Soru-Cevap
*Application Insights, Docker 'dan alınamayan bana ne sunar?*

* Kapsayıcıya ve görüntüye göre performans sayaçlarının ayrıntılı dökümü.
* Kapsayıcıyı ve uygulama verilerini tek bir panoda tümleştirin.
* Bir veritabanına, Power BI veya başka bir panoya daha fazla analiz için [telemetri dışarı aktarın](export-telemetry.md) .

*Nasıl yaparım? uygulamadan telemetri almak mı istiyorsunuz?*

* Application Insights SDK 'sını uygulamaya yükler. Şunları öğrenin: [Java Web Apps](../../azure-monitor/app/java-get-started.md), [Windows Web Apps](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Sonraki adımlar

* [Java için Application Insights](../../azure-monitor/app/java-get-started.md)
* [Node. js için Application Insights](../../azure-monitor/app/nodejs.md)
* [ASP.NET için Application Insights](../../azure-monitor/app/asp-net.md)
