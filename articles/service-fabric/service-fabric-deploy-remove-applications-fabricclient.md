---
title: FabricClient ile Azure Service Fabric dağıtımı
description: Service Fabric uygulamaları dağıtmak ve kaldırmak için FabricClient API 'Lerini kullanın.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: atsenthi
ms.openlocfilehash: cdb5ae4efbd4119422101eb8a05ce71e7b58d51f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013304"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>FabricClient kullanarak uygulama dağıtma ve kaldırma
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient API’leri](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Bir [uygulama türü paketlendikten][10]sonra Azure Service Fabric kümesine dağıtıma hazırız. Dağıtım aşağıdaki üç adımdan oluşur:

1. Uygulama paketini görüntü deposuna yükleyin
2. Uygulama türünü kaydetme
3. Uygulama paketini görüntü deposundan kaldır
4. Uygulama örneğini oluşturma

Bir uygulamayı dağıttıktan ve kümede bir örneği çalıştırdıktan sonra, uygulama örneğini ve uygulama türünü silebilirsiniz. Aşağıdaki adımları izleyerek bir uygulamayı kümeden tamamen kaldırın:

1. Çalışan uygulama örneğini Kaldır (veya Sil)
2. Artık gerekmiyorsa uygulama türünün kaydını silin

Yerel geliştirme kümenizdeki uygulamaları dağıtmak ve hata ayıklamak için Visual Studio kullanıyorsanız, önceki adımların tümü bir PowerShell betiği aracılığıyla otomatik olarak işlenir.  Bu betik, uygulama projesinin *betikler* klasöründe bulunur. Bu makalede, Visual Studio dışında aynı işlemleri yapabilmeniz için betiğin ne yaptığını gösteren arka plan sunulmaktadır. 
 
## <a name="connect-to-the-cluster"></a>Kümeye bağlanma
Bu makaledeki kod örneklerinden hiçbirini çalıştırmadan önce bir [FabricClient](/dotnet/api/system.fabric.fabricclient) örneği oluşturarak kümeye bağlanın. Yerel bir geliştirme kümesine veya Azure Active Directory, x509 sertifikaları veya Windows Active Directory kullanılarak güvenliği sağlanmış bir uzak kümeye veya kümeye bağlanma örnekleri için bkz. [güvenli bir kümeye bağlanma](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Yerel geliştirme kümesine bağlanmak için aşağıdaki örneği çalıştırın:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Uygulama paketini karşıya yükle
Visual Studio 'da *MyApplication* adlı bir uygulama oluşturup paketlediğinizi varsayalım. Varsayılan olarak, ApplicationManifest. xml dosyasında listelenen uygulama türü adı "MyApplicationType" dir.  Gerekli uygulama bildirimi, hizmet bildirimleri ve kod/yapılandırma/veri paketleri içeren uygulama paketi, *C:\Users\&lt; username&gt;\ist Studio 2019 \ Projects\MyApplication\MyApplication\pkg\Debug*konumunda bulunur.

Uygulama paketini karşıya yüklemek, iç Service Fabric bileşenleri tarafından erişilebilen bir konuma koyar. Service Fabric uygulama paketinin kaydı sırasında uygulama paketini doğrular. Ancak, uygulama paketini yerel olarak doğrulamak istiyorsanız (diğer bir deyişle, karşıya yüklemeden önce), [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet 'ini kullanın.

[CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API 'si, uygulama paketini küme görüntü deposuna yükler. 

Uygulama paketi büyükse ve/veya çok sayıda dosya içeriyorsa, [bu dosyayı sıkıştırabilir](service-fabric-package-apps.md#compress-a-package) ve PowerShell 'i kullanarak görüntü deposuna kopyalayabilirsiniz. Sıkıştırma, boyut ve dosya sayısını azaltır.

Görüntü deposu ve görüntü deposu bağlantı dizesi hakkında ek bilgi için bkz. [görüntü deposu bağlantı dizesi](service-fabric-image-store-connection-string.md) .

## <a name="register-the-application-package"></a>Uygulama paketini Kaydet
Uygulama bildiriminde belirtilen uygulama türü ve sürümü, uygulama paketi kaydedildiğinde kullanım için kullanılabilir hale gelir. Sistem, önceki adımda karşıya yüklenen paketi okur, paketi doğrular, paket içeriğini işler ve işlenen paketi bir iç sistem konumuna kopyalar.  

[Provisionapplicationasync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API 'si, uygulama türünü kümeye kaydeder ve dağıtımı için kullanılabilir hale getirir.

[Getapplicationtypelistasync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API 'si başarıyla kaydedilen tüm uygulama türleri hakkında bilgi sağlar. Kaydın ne zaman yapıldığını öğrenmek için bu API 'YI kullanabilirsiniz.

## <a name="remove-an-application-package-from-the-image-store"></a>Bir uygulama paketini görüntü deposundan kaldırma
Uygulama başarıyla kaydedildikten sonra uygulama paketini kaldırmanız önerilir.  Uygulama paketlerini görüntü deposundan silme sistem kaynaklarını boşaltır.  Kullanılmayan uygulama paketlerinin tutulması disk depolama alanı tüketir ve uygulama performans sorunlarına yol açar. [Removeapplicationpackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) API 'sini kullanarak uygulama paketini görüntü deposundan silin.

## <a name="create-an-application-instance"></a>Uygulama örneği oluşturma
[Createapplicationasync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) API kullanılarak başarıyla kaydedilmiş herhangi bir uygulama türünden bir uygulama örneği oluşturabilirsiniz. Her uygulamanın adı *"Fabric:"* şeması ile başlamalı ve her uygulama örneği (bir küme içinde) için benzersiz olmalıdır. Hedef uygulama türünün uygulama bildiriminde tanımlanan varsayılan hizmetler de oluşturulur.

Kayıtlı uygulama türünün belirli bir sürümü için birden çok uygulama örneği oluşturulabilir. Her uygulama örneği, kendi çalışma diziniyle ve işlem kümesiyle birlikte yalıtımda çalışır.

Kümede hangi adlandırılmış uygulamaların ve hizmetlerin çalıştığını görmek için [Getapplicationlistasync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) ve [getservicelistasync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) API 'lerini çalıştırın.

## <a name="create-a-service-instance"></a>Hizmet örneği oluşturma
[CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API 'sini kullanarak hizmet türünden bir hizmet örneği oluşturabilirsiniz.  Hizmet, uygulama bildiriminde varsayılan bir hizmet olarak bildirilirse, uygulama örneği oluşturulduğunda hizmet örneği oluşturulur.  Zaten örneği oluşturulan bir hizmet için [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API 'Sini çağırmak fabricexception türünde bir özel durum döndürür. Özel durum FabricErrorCode değeri olan bir hata kodu içerecektir. ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Hizmet örneğini kaldır
Bir hizmet örneğine artık ihtiyaç duyulmadığında, [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API 'sini çağırarak çalışan uygulama örneğinden kaldırabilirsiniz.  

> [!WARNING]
> Bu işlem ters çevrilemez ve hizmet durumu kurtarılamıyor.

## <a name="remove-an-application-instance"></a>Uygulama örneğini kaldır
Bir uygulama örneğine artık gerek kalmadığında, [Deleteapplicationasync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) API 'sini kullanarak dosyayı adıyla kalıcı olarak kaldırabilirsiniz. [Deleteapplicationasync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) , tüm hizmet durumlarını kalıcı olarak kaldırarak uygulamaya ait tüm hizmetleri otomatik olarak kaldırır.

> [!WARNING]
> Bu işlem ters çevrilemez ve uygulama durumu kurtarılamıyor.

## <a name="unregister-an-application-type"></a>Uygulama türünün kaydını silme
Uygulama türünün belirli bir sürümüne artık ihtiyaç duyulmadığında, [kayıt silme-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) API 'sini kullanarak uygulama türünün belirli bir sürümünün kaydını kaldırmanız gerekir. Uygulama türlerinin kullanılmayan sürümlerinin kaydı, görüntü deposu tarafından kullanılan depolama alanını yayınlar. Uygulama türü sürümüne göre hiçbir uygulama örneklenmedikçe, uygulama türünün bir sürümü kaydı silinir. Ayrıca, uygulama türünde bekleyen uygulama yükseltmeleri, uygulama türü sürümüne başvurmamaktadır.

## <a name="troubleshooting"></a>Sorun giderme
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage, ımatoreconnectionstring ister
Service Fabric SDK ortamında doğru varsayılanlar ayarlanmış olmalıdır. Ancak gerekirse, tüm komutlar için ımatoreconnectionstring, Service Fabric kümesinin kullandığı değerle eşleşmelidir. Imabtoreconnectionstring ' i küme bildiriminde bulabilirsiniz, [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) ve Get-ImageStoreConnectionStringFromClusterManifest komutlarını kullanarak elde edebilirsiniz:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Service Fabric SDK PowerShell modülünün bir parçası olan **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet 'i, görüntü deposu bağlantı dizesini almak için kullanılır.  SDK modülünü içeri aktarmak için şunu çalıştırın:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


Imatoreconnectionstring, küme bildiriminde bulunur:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Görüntü deposu ve görüntü deposu bağlantı dizesi hakkında ek bilgi için bkz. [görüntü deposu bağlantı dizesi](service-fabric-image-store-connection-string.md) .

### <a name="deploy-large-application-package"></a>Büyük uygulama paketini dağıtma
Sorun: büyük bir uygulama paketi (GB miktarı) için [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API zaman aşımına uğruyor.
Deneyin:
- [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) yöntemi için `timeout` parametresi ile daha büyük bir zaman aşımı belirtin. Varsayılan olarak, zaman aşımı 30 dakikadır.
- Kaynak makineniz ve kümeniz arasındaki ağ bağlantısını kontrol edin. Bağlantı yavaşsa, daha iyi bir ağ bağlantısına sahip bir makine kullanmayı düşünün.
İstemci makine kümeden başka bir bölgedeyse, kümeyle daha yakın bir bölgede bir istemci makine kullanmayı düşünün.
- Dış azaltmayı vurarak göz atın. Örneğin, görüntü deposu Azure Storage 'ı kullanacak şekilde yapılandırıldığında karşıya yükleme kısıtlanmış olabilir.

Sorun: yükleme paketi başarıyla tamamlandı, ancak [Provisionapplicationasync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API zaman aşımına uğruyor. Almaya
- Görüntü deposuna kopyalamadan önce [paketi sıkıştırın](service-fabric-package-apps.md#compress-a-package) .
Sıkıştırma, dosyanın boyutunu ve sayısını azaltır ve bu da Service Fabric olması gereken trafik miktarını ve çalışmayı azaltır. Karşıya yükleme işlemi daha yavaş olabilir (özellikle, sıkıştırma süresini dahil ediyorsanız), ancak uygulama türünün kaydını kaydedip kaydı daha hızlıdır.
- [Provisionapplicationasync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) apı için `timeout` parametresiyle daha büyük bir zaman aşımı belirtin.

### <a name="deploy-application-package-with-many-files"></a>Birçok dosya içeren uygulama paketini dağıtma
Sorun: çok sayıda dosya (binlerce sırada) olan bir uygulama paketi için [Provisionapplicationasync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) zaman aşımına uğruyor.
Deneyin:
- Görüntü deposuna kopyalamadan önce [paketi sıkıştırın](service-fabric-package-apps.md#compress-a-package) . Sıkıştırma, dosya sayısını azaltır.
- [Provisionapplicationasync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) için `timeout` parametresiyle daha büyük bir zaman aşımı belirtin.

## <a name="code-example"></a>Kod örneği
Aşağıdaki örnek, bir uygulama paketini görüntü deposuna kopyalar ve uygulama türünü hazırlar. Daha sonra örnek, bir uygulama örneği oluşturur ve bir hizmet örneği oluşturur. Son olarak, örnek uygulama örneğini kaldırır, uygulama türünün sağlamasını kaldırır ve görüntü deposundan uygulama paketini siler.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2019\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>Sonraki adımlar
[Uygulama yükseltmesini Service Fabric](service-fabric-application-upgrade.md)

[Service Fabric sistem durumu tanıtımı](service-fabric-health-introduction.md)

[Service Fabric hizmeti Tanılama ve sorunlarını giderme](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric bir uygulama modelleme](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
