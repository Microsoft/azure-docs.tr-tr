---
title: Azure Service Fabric sistem durumunu raporlama ve denetleme
description: Hizmet kodunuzda sistem durumu raporlarının nasıl gönderileceğini ve Azure Service Fabric tarafından sağlanan sistem durumu izleme araçlarını kullanarak hizmetinizin sistem durumunu nasıl denetleyeceğinizi öğrenin.
ms.topic: conceptual
ms.date: 02/25/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: dc15ea7f062d3912c297050ffac755990fdd3b7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625951"
---
# <a name="report-and-check-service-health"></a>Hizmet durumunu raporlama ve denetleme
Hizmetleriniz sorunlarla karşılaştığında, olayları ve kesintilere yanıt verme ve bunları çözme imkanını sorunları hızlı bir şekilde algılamanıza bağlıdır. Hizmet kodunuzda Azure Service Fabric Health Manager sorunlarını ve başarısızlıklarını raporlayabilir, sistem durumunu denetlemek için Service Fabric sağladığı standart sistem durumu izleme araçlarını kullanabilirsiniz.

Hizmetten sistem durumunu bildirebilmeniz için üç yol vardır:

* [Bölüm](/dotnet/api/system.fabric.istatefulservicepartition) veya [Codepackageactivationcontext](/dotnet/api/system.fabric.codepackageactivationcontext) nesneleri kullanın.  
  `Partition`Ve `CodePackageActivationContext` nesnelerini, geçerli bağlamın parçası olan öğelerin sistem durumunu raporlamak için kullanabilirsiniz. Örneğin, bir çoğaltmanın parçası olarak çalışan kod yalnızca o çoğaltma üzerinde sistem durumunu, ait olduğu bölümü ve bir parçası olan uygulamayı rapor edebilir.
* `FabricClient` komutunu kullanın.   
  `FabricClient`Küme [güvenli](service-fabric-cluster-security.md) değilse veya hizmet yönetici ayrıcalıklarıyla çalışıyorsa, hizmet kodundan sistem durumunu raporlamak için ' i kullanabilirsiniz. En gerçek dünyada senaryolar güvenli olmayan kümeler kullanmaz veya yönetici ayrıcalıkları sağlamaz. İle `FabricClient` , kümenin bir parçası olan herhangi bir varlıkta sistem durumunu rapor edebilirsiniz. Bununla birlikte, hizmet kodu yalnızca kendi sistem durumuyla ilgili raporları göndermelidir.
* Küme, uygulama, dağıtılan uygulama, hizmet, hizmet paketi, bölüm, çoğaltma veya düğüm düzeylerinde REST API 'Leri kullanın. Bu, bir kapsayıcı içinden sistem durumunu raporlamak için kullanılabilir.

Bu makalede, hizmet kodundan sistem durumunu raporlayan bir örnek adım adım açıklanmaktadır. Örnek ayrıca, Service Fabric tarafından belirtilen araçların sistem durumunu denetlemek için nasıl kullanılabileceğini gösterir. Bu makale, Service Fabric sistem durumu izleme yeteneklerine hızlı bir giriş yapmak için tasarlanmıştır. Daha ayrıntılı bilgi için, bu makalenin sonundaki bağlantıyla başlayan sistem durumu hakkında ayrıntılı makalelerin serisini okuyabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Aşağıdakilerin yüklü olması gerekir:

* Visual Studio 2015 veya Visual Studio 2019
* Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Yerel bir güvenli geliştirici kümesi oluşturmak için
* PowerShell 'i yönetici ayrıcalıklarıyla açın ve aşağıdaki komutları çalıştırın:

![Güvenli bir geliştirici kümesi oluşturmayı gösteren komutlar](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Bir uygulamayı dağıtmak ve durumunu denetlemek için
1. Visual Studio 'Yu yönetici olarak açın.
1. **Durum bilgisi olan hizmet** şablonunu kullanarak bir proje oluşturun.
   
    ![Durum bilgisi olan hizmet ile Service Fabric uygulaması oluşturma](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Uygulamayı hata ayıklama modunda çalıştırmak için **F5** tuşuna basın. Uygulama yerel kümeye dağıtılır.
1. Uygulama çalıştırıldıktan sonra, bildirim alanında Yerel Küme Yöneticisi simgesine sağ tıklayın ve Service Fabric Explorer açmak için kısayol menüsünden **Yerel kümeyi Yönet** ' i seçin.
   
    ![Bildirim alanından Service Fabric Explorer aç](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. Uygulama durumu bu görüntüde olduğu gibi görüntülenmelidir. Bu sırada, uygulamanın hatasız olması gerekir.
   
    ![Service Fabric Explorer sağlıklı uygulama](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Ayrıca, PowerShell kullanarak sistem durumunu kontrol edebilirsiniz. ```Get-ServiceFabricApplicationHealth```Uygulamanın sistem durumunu denetlemek için öğesini kullanabilir ve ```Get-ServiceFabricServiceHealth``` bir hizmetin sistem durumunu denetlemek için ' i kullanabilirsiniz. Bu görüntüde, PowerShell 'deki aynı uygulama için sistem durumu raporu bulunur.
   
    ![PowerShell 'de sağlıklı uygulama](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Hizmet kodunuza özel sistem durumu olayları eklemek için
Visual Studio 'daki Service Fabric proje şablonları örnek kod içerir. Aşağıdaki adımlarda, hizmet kodunuzda özel sistem durumu olaylarını nasıl bildirekullanabileceğiniz gösterilmektedir. Bu raporlar, Service Fabric Explorer, Azure portal sistem durumu görünümü ve PowerShell gibi Service Fabric sağladığı sistem durumu izleme için standart araçlarda otomatik olarak gösterilir.

1. Daha önce Visual Studio 'da oluşturduğunuz uygulamayı yeniden açın veya **durum bilgisi olan hizmet** Visual Studio şablonunu kullanarak yeni bir uygulama oluşturun.
1. Stateful1. cs dosyasını açın ve `myDictionary.TryGetValueAsync` yöntemi içinde çağrısını bulun `RunAsync` . Bu yöntemin, `result` Bu uygulamadaki anahtar mantığı bir sayıyı çalışır durumda tutacağından, bu yöntemin, sayacın geçerli değerini tutan bir döndürür. Bu uygulama gerçek bir uygulamadır ve sonucun bulunmaması bir hatayı gösteriyorsa, bu olayı işaretlemek istersiniz.
1. Sonucun olmaması bir başarısızlığı temsil ettiğinde bir sistem durumu olayı raporlamak için aşağıdaki adımları ekleyin.
   
    a. `System.Fabric.Health`Stateful1. cs dosyasına ad alanını ekleyin.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Çağrıdan sonra aşağıdaki kodu ekleyin `myDictionary.TryGetValueAsync`
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Durum bilgisi olan bir hizmetten bildirildiği için çoğaltma sistem durumu raporlıyoruz. `HealthInformation`Parametresi, bildirilen sistem durumu sorunuyla ilgili bilgileri depolar.
   
    Durum bilgisi olmayan bir hizmet oluşturduysanız, aşağıdaki kodu kullanın
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Hizmetiniz yönetici ayrıcalıklarıyla çalışıyorsa veya küme [güvenli](service-fabric-cluster-security.md)değilse, `FabricClient` Aşağıdaki adımlarda gösterildiği gibi sistem durumunu raporlamak için de kullanabilirsiniz.  
   
    a. `FabricClient`Bildirimden sonra örneği oluşturun `var myDictionary` .
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Çağrıdan sonra aşağıdaki kodu ekleyin `myDictionary.TryGetValueAsync` .
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
1. Bu hatanın benzetimini yapmanızı sağlar ve sistem durumu izleme araçlarında göster bölümüne bakın. Hatanın benzetimini yapmak için, daha önce eklediğiniz sistem durumu raporlama kodundaki ilk satırı açıklama olarak değerlendirin. İlk satırı yorumladıktan sonra, kod aşağıdaki örneğe benzer şekilde görünür.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Bu kod, her çalıştırıldığında sistem durumu raporunu tetikler `RunAsync` . Değişikliği yaptıktan sonra **F5** tuşuna basarak uygulamayı çalıştırın.
1. Uygulama çalıştırıldıktan sonra uygulamanın sistem durumunu denetlemek için Service Fabric Explorer açın. Bu kez Service Fabric Explorer, uygulamanın sağlıksız olduğunu gösterir. Daha önce eklediğimiz koddan bildirilen hata nedeniyle uygulama sağlıksız olarak gösterilir.
   
    ![Service Fabric Explorer sağlıksız uygulama](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Service Fabric Explorer ağaç görünümünde birincil çoğaltmayı seçerseniz, **sistem durumunun** bir hata olduğunu görürsünüz. Service Fabric Explorer ayrıca, koddaki parametreye eklenen sistem durumu raporu ayrıntılarını görüntüler `HealthInformation` . Aynı sistem durumu raporlarını PowerShell ve Azure portal görebilirsiniz.
   
    ![Service Fabric Explorer çoğaltma durumu](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Bu rapor, başka bir rapor tarafından değiştirilene veya bu çoğaltma silinene kadar sistem durumu yöneticisinde kalır. `TimeToLive`Nesnedeki bu sistem durumu raporu için ayarlanmadığı için `HealthInformation` , raporun süresi dolmadı.

Bu durumda çoğaltma olan en ayrıntılı düzeyde sistem durumunun bildirilmesi önerilir. Ayrıca sistem durumunu da rapor edebilirsiniz `Partition` .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

, Ve üzerinde sistem durumunu raporlamak için `Application` `DeployedApplication` `DeployedServicePackage` kullanın  `CodePackageActivationContext` .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Sonraki adımlar
* [Service Fabric sistem durumu hakkında ayrıntılı bilgi](service-fabric-health-introduction.md)
* [Raporlama hizmeti durumu için REST API](/rest/api/servicefabric/report-the-health-of-a-service)
* [Uygulama durumunu raporlamak için REST API](/rest/api/servicefabric/report-the-health-of-an-application)
