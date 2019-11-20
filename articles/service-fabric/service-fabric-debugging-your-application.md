---
title: Visual Studio 'da uygulamanızda hata ayıklama | Microsoft Docs
description: Yerel bir geliştirme kümesinde Visual Studio 'da geliştirip hata ayıklaarak hizmetlerinizin güvenilirliğini ve performansını artırabilirsiniz.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: ee5354845efba0cce92ccf4f11eb3480684e7509
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947895"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Visual Studio 'Yu kullanarak Service Fabric uygulamanızda hata ayıklama
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Tutulma/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Yerel Service Fabric uygulamasında hata ayıklama
Azure Service Fabric uygulamanızı yerel bir bilgisayar geliştirme kümesinde dağıtarak ve hata ayıkladıktan sonra zamandan ve paradan tasarruf edebilirsiniz. Visual Studio 2019 veya 2015, uygulamayı yerel kümeye dağıtabilir ve hata ayıklayıcıyı otomatik olarak uygulamanızın tüm örneklerine bağlayın. Hata ayıklayıcıya bağlanmak için Visual Studio 'nun yönetici olarak çalıştırılması gerekir.

1. [Service Fabric geliştirme ortamınızı ayarlama](service-fabric-get-started.md)adımlarını izleyerek bir yerel geliştirme kümesi başlatın.
2. **F5** tuşuna basın veya **Hata Ayıkla** > **hata ayıklamayı Başlat**öğesine tıklayın.
   
    ![Bir uygulamada hata ayıklamayı başlatma][startdebugging]
3. Kodunuzda kesme noktaları ayarlayın ve **Hata Ayıkla** menüsündeki komutlar ' a tıklayarak uygulamada gezinin.
   
   > [!NOTE]
   > Visual Studio, uygulamanızın tüm örneklerine iliştirir. Kod üzerinden adımlarken, kesme noktaları, eşzamanlı oturumlara neden olan birden çok işlem tarafından bir isabet alabilir. Her bir kesme noktasını iş parçacığı KIMLIĞI üzerinde koşullu yaparak veya Tanılama olaylarını kullanarak, bu noktaları isabet ettikten sonra devre dışı bırakmayı deneyin.
   > 
   > 
4. Tanılama olaylarını gerçek zamanlı olarak görüntüleyebilmeniz için **Tanılama olayları** penceresi otomatik olarak açılır.
   
    ![Tanılama olaylarını gerçek zamanlı olarak görüntüleme][diagnosticevents]
5. Ayrıca, Cloud Explorer 'da **Tanılama olayları** penceresini açabilirsiniz.  **Service Fabric**altında herhangi bir düğüme sağ tıklayın ve **akış izlemelerini görüntüle**' yi seçin.
   
    ![Tanılama olayları penceresini açın][viewdiagnosticevents]
   
    İzlemelerinizi belirli bir hizmet veya uygulamaya göre filtrelemek istiyorsanız, söz konusu hizmet veya uygulamada akış izlemelerini etkinleştirin.
6. Tanılama olayları otomatik olarak oluşturulan **ServiceEventSource.cs** dosyasında görülebilir ve uygulama kodundan çağırılır.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. **Tanılama olayları** penceresi, olayları gerçek zamanlı olarak filtrelemeyi, duraklatmayı ve İnceleme işlemlerini destekler.  Filtre, olay iletisinin içeriği de dahil olmak üzere basit bir dize aramasından oluşur.
   
    ![Olayları gerçek zamanlı olarak filtreleyin, duraklatın ve devam ettirir veya inceleyin][diagnosticeventsactions]
8. Hata ayıklama Hizmetleri başka bir uygulamanın hatalarını ayıklamaya benzer. Daha kolay hata ayıklama için, normalde kesme noktalarını Visual Studio aracılığıyla ayarlayacaksınız. Güvenilir koleksiyonlar birden çok düğüm arasında çoğaltılsa da, yine de IEnumerable uygularlar. Bu uygulama, içinde neleri depoladığınızı görmek için hata ayıkladığınızda, Visual Studio 'da sonuçlar görünümünü kullanabileceğiniz anlamına gelir. Bunu yapmak için, kodunuzun herhangi bir yerinden bir kesme noktası ayarlayın.
   
    ![Bir uygulamada hata ayıklamayı başlatma][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Hata ayıklamanın bir parçası olarak komut dosyası çalıştırma
Belirli senaryolarda hata ayıklama oturumu başlatma işleminin parçası olarak bir komut dosyası çalıştırmanız gerekebilir (örn. varsayılan hizmetler kullanmıyorsanız).

Visual Studio 'da, Service Fabric uygulama projesinin (. sfproj) **Scripts** klasöründe **Start-Service. ps1** adlı bir dosya ekleyebilirsiniz. Bu betik, uygulama yerel kümede oluşturulduktan sonra çağrılacaktır.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Uzak Service Fabric uygulamasında hata ayıklama
Service Fabric uygulamalarınız Azure 'da bir Service Fabric kümesinde çalışıyorsa, doğrudan Visual Studio 'dan Bu uygulamalarda hata ayıklaması yapabilirsiniz.

> [!NOTE]
> Özelliği, .NET 2,9 için [SERVICE fabrıc SDK 2,0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) ve [Azure SDK 'sını](https://azure.microsoft.com/downloads/)gerektirir.    

<!-- -->
> [!WARNING]
> Uzaktan hata ayıklama, çalışan uygulamalar üzerindeki etkisi nedeniyle geliştirme ve test senaryolarına yöneliktir ve üretim ortamlarında kullanılmamalıdır.

1. **Cloud Explorer**'da kümenize gidin. Sağ tıklayın ve **hata ayıklamayı etkinleştir** ' i seçin
   
    ![Uzaktan hata ayıklamayı etkinleştir][enableremotedebugging]
   
    Bu eylem, Küme düğümlerinizin ve gerekli ağ yapılandırmalarının uzaktan hata ayıklama uzantısını etkinleştirme işlemini etkinleştirir.
2. **Cloud Explorer**'da küme düğümüne sağ tıklayın ve **hata ayıklayıcı Ekle** ' yi seçin.
   
    ![Hata ayıklayıcı Ekle][attachdebugger]
3. **Işleme İliştir** iletişim kutusunda, hata ayıklamak istediğiniz işlemi seçin ve **Ekle** ' ye tıklayın.
   
    ![İşlem seçin][chooseprocess]
   
    Eklemek istediğiniz işlemin adı, hizmet projesi derleme adınızın adına eşit.
   
    Hata ayıklayıcı, işlemi çalıştıran tüm düğümlere eklenir.
   
   * Durum bilgisi olmayan bir hizmette hata ayıklaması yaptığınız durumda, tüm düğümlerdeki hizmetin tüm örnekleri hata ayıklama oturumunun bir parçasıdır.
   * Durum bilgisi olan bir hizmette hata ayıklaması yapıyorsanız, yalnızca herhangi bir bölümün birincil çoğaltması etkin olur ve bu nedenle hata ayıklayıcı tarafından yakalanır. Birincil çoğaltma, hata ayıklama oturumu sırasında taşınırsa, bu çoğaltmanın işlenmesi hata ayıklama oturumunun bir parçası olmaya devam eder.
   * Yalnızca belirli bir hizmetin ilgili bölümlerini veya örneklerini yakalamak için, koşullu kesme noktalarını yalnızca belirli bir bölümü veya örneği bölmek için kullanabilirsiniz.
     
     ![Koşullu kesme noktası][conditionalbreakpoint]
     
     > [!NOTE]
     > Şu anda aynı hizmet çalıştırılabilir adının birden fazla örneğiyle Service Fabric kümede hata ayıklamayı desteklemiyoruz.
     > 
     > 
4. Uygulamanızda hata ayıklamayı tamamladıktan sonra, **Cloud Explorer** 'da kümeye sağ tıklayıp **hata ayıklamayı devre dışı bırak** ' ı seçerek uzaktan hata ayıklama uzantısını devre dışı bırakabilirsiniz
   
    ![Uzaktan hata ayıklamayı devre dışı bırak][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Uzak küme düğümünden akış izlemeleri
Ayrıca, izlemeleri doğrudan uzak bir küme düğümünden Visual Studio 'ya de aktarabilirsiniz. Bu özellik, bir Service Fabric kümesi düğümünde oluşturulan ETW izleme olaylarını akışla oluşturmanıza olanak sağlar.

> [!NOTE]
> Bu özellik, [.net 2,9 için](https://azure.microsoft.com/downloads/) [Service Fabric SDK 2,0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) ve Azure SDK 'sını gerektirir.
> Bu özellik yalnızca Azure 'da çalışan kümeleri destekler.
> 
> 

<!-- -->
> [!WARNING]
> Akış izlemeleri geliştirme ve test senaryolarına yöneliktir ve çalışan uygulamalardaki etki nedeniyle üretim ortamlarında kullanılmamalıdır.
> Bir üretim senaryosunda, Azure Tanılama kullanarak olayları iletme ' yi kullanmanız gerekir.

1. **Cloud Explorer**'da kümenize gidin. Sağ tıklayın ve **akış Izlemelerini etkinleştir** ' i seçin
   
    ![Uzak akış izlemelerini etkinleştir][enablestreamingtraces]
   
    Bu eylem, Küme düğümlerinizin yanı sıra gereken ağ yapılandırmalarının akış izlemeleri uzantısını etkinleştirme sürecini öngörür.
2. **Cloud Explorer**'da **düğümler** öğesini genişletin, izlemelerin akışını yapmak istediğiniz düğüme sağ tıklayın ve **akış izlemelerini görüntüle** ' yi seçin.
   
    ![Uzak akış izlemelerini görüntüleme][viewremotestreamingtraces]
   
    İzlemelerini görmek istediğiniz kadar düğüm için 2. adımı tekrarlayın. Her düğüm akışı, ayrılmış bir pencerede gösterilir.
   
    Artık Service Fabric ve hizmetlerinize göre yayılan izlemeleri görebileceksiniz. Olayları yalnızca belirli bir uygulamayı gösterecek şekilde filtrelemek istiyorsanız, filtrenin içinde uygulamanın adını yazmanız yeterlidir.
   
    ![Akış izlemelerini görüntüleme][viewingstreamingtraces]
3. Kümenizdeki izleme akışını tamamladıktan sonra, **Cloud Explorer** 'da kümeye sağ tıklayıp **akış Izlemelerini devre dışı bırak** ' ı seçerek uzak akış izlemelerini devre dışı bırakabilirsiniz.
   
    ![Uzak akış izlemelerini devre dışı bırak][disablestreamingtraces]

## <a name="next-steps"></a>Sonraki adımlar
* [Bir Service Fabric hizmetini test](service-fabric-testability-overview.md)edin.
* [Visual Studio 'da Service Fabric uygulamalarınızı yönetin](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
