---
title: Tek başına Azure Service Fabric kümesi oluşturma | Microsoft Docs
description: Şirket içinde veya herhangi bir bulutta olsun, Windows Server çalıştıran herhangi bir makinede (fiziksel veya sanal) bir Azure Service Fabric kümesi oluşturun.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/21/2019
ms.author: dekapur
ms.openlocfilehash: 6fce1957101050c6ff3a2c3aba2b4b87d4f66f1d
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554656"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Windows Server 'da çalışan tek başına küme oluşturma
Azure Service Fabric 'yi, Windows Server çalıştıran tüm sanal makinelerde veya bilgisayarlarda Service Fabric kümeler oluşturmak için kullanabilirsiniz. Bu, birbirine bağlı bir Windows Server bilgisayarları kümesi içeren herhangi bir ortamda Service Fabric uygulamaları dağıtabileceğiniz ve çalıştırabileceğiniz anlamına gelir. Bu durumda, şirket içinde veya herhangi bir bulut sağlayıcısıyla olabilirsiniz. Service Fabric, tek başına Windows Server paketi olarak adlandırılan Service Fabric kümeleri oluşturmak için bir kurulum paketi sağlar. Azure 'daki geleneksel Service Fabric kümeleri yönetilen bir hizmet olarak kullanılabilir, tek başına Service Fabric kümeler self servis.

Bu makale, Service Fabric tek başına kümesi oluşturma adımlarında size yol gösterir.

> [!NOTE]
> Bu tek başına Windows Server paketi, ticari olarak ücretsiz olarak kullanılabilir ve üretim dağıtımlarında kullanılabilir. Bu paket, "Önizleme" içinde yeni Service Fabric özellikler içerebilir. Aşağı kaydırarak "[Bu pakette bulunan Önizleme özellikleri](#previewfeatures_anchor)" ne gidin. Önizleme özelliklerinin listesi. [EULA 'nın bir kopyasını şimdi indirebilirsiniz](https://go.microsoft.com/fwlink/?LinkID=733084) .
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Windows Server paketi Service Fabric için destek alın
* [Azure Service Fabric forumundaki](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?)Windows Server için Service Fabric tek başına paketi hakkında Topluluğa sorun.
* [Service Fabric Için profesyonel destek](https://support.microsoft.com/oas/default.aspx?prid=16146)bileti açın.  [Microsoft 'Tan](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)profesyonel destek hakkında daha fazla bilgi edinin.
* [Microsoft Premier destek](https://support.microsoft.com/en-us/premier)bir parçası olarak bu paket için de destek alabilirsiniz.
* Daha ayrıntılı bilgi için lütfen bkz. [Azure Service Fabric destek seçenekleri](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
* Destek amaçlarıyla günlükleri toplamak için [Service Fabric tek başına günlük toplayıcıyı](service-fabric-cluster-standalone-package-contents.md)çalıştırın.

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Windows Server paketi için Service Fabric indirme
Kümeyi oluşturmak için, burada bulunan Windows Server paketi için Service Fabric (Windows Server 2012 R2 ve daha yeni bir sürümü) kullanın: <br>
[Bağlantı Service Fabric tek başına paketini indir-Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

Paketin içeriğiyle ilgili ayrıntıları [buradan](service-fabric-cluster-standalone-package-contents.md)bulabilirsiniz.

Service Fabric çalışma zamanı paketi, küme oluşturma sırasında otomatik olarak indirilir. İnternet 'e bağlı olmayan bir makineden dağıtım yapıyorsanız, lütfen çalışma zamanı paketini buradan bant dışı indirin: <br>
[Bağlantı Service Fabric çalışma zamanını indirin-Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Tek başına küme yapılandırma örneklerini şurada bulabilirsiniz: <br>
[Tek başına küme yapılandırma örnekleri](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Kümeyi oluşturma
Kurulum paketiyle birlikte birkaç örnek küme yapılandırma dosyası yüklenir. Tek bir bilgisayarda çalışan korumasız ve üç düğümlü bir küme olan *ClusterConfig.Unsecure.DevCluster.json*, en basit küme yapılandırmasıdır.  Diğer yapılandırma dosyalarında X.509 sertifikalarıyla ya da Windows güvenliğiyle korunan tek veya çok makineli kümeler açıklanır.  Bu öğreticide varsayılan yapılandırma ayarlarından herhangi birini değiştirmeniz gerekmez, ancak yapılandırma dosyasını inceleyip ayarları tanıyın.  **Düğümler** bölümünde, kümedeki üç düğüm açıklanmaktadır: ad, IP adresi, [düğüm türü, hata etki alanı ve yükseltme etki alanı](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  **Özellikler** bölümü kümenin [güvenlik, güvenilirlik düzeyi, tanılama koleksiyonu ve düğüm türlerini](service-fabric-cluster-manifest.md#cluster-properties) tanımlar.

Bu makalede oluşturulan küme güvenli değildir.  Herkes anonim olarak bağlanıp yönetim işlemleri gerçekleştirebileceğinden, üretim kümeleri her zaman X.509 sertifikaları veya Windows güvenliği kullanılarak güvenli hale getirilmelidir.  Güvenlik yalnızca küme oluşturma sırasında yapılandırılır ve küme oluşturulduktan sonra güvenliği etkinleştirmek mümkün değildir. Yapılandırma dosyasını güncelleştirme [sertifika güvenliğini](service-fabric-windows-cluster-x509-security.md) etkinleştirme veya [Windows güvenliği](service-fabric-windows-cluster-windows-security.md). Service Fabric küme güvenliği hakkında daha fazla bilgi edinmek için [Küme güvenliğini sağlama](service-fabric-cluster-security.md) makalesini okuyun.

### <a name="step-1-create-the-cluster"></a>1\. Adım: kümeyi oluşturma

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Senaryo A: güvenli olmayan bir yerel geliştirme kümesi oluşturma
Service Fabric, [örneklere](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)dahil olan *clusterConfig. güvensiz. devcluster. JSON* dosyası kullanılarak tek makineli bir geliştirme kümesine dağıtılabilir.

Tek başına paketin makinenize paketini açın, örnek yapılandırma dosyasını yerel makineye kopyalayın, sonra tek başına paket klasöründen bir yönetici PowerShell oturumu aracılığıyla *Createservicefabriccluster. ps1* betiğini çalıştırın.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Sorun giderme ayrıntıları için [plan ve küme dağıtımınızı hazırlama](service-fabric-cluster-standalone-deployment-preparation.md) konusundaki ortam kurulumu bölümüne bakın.

Geliştirme senaryolarını çalıştırmayı tamamladıysanız, "[kümeyi kaldırma](#removecluster_anchor)" bölümündeki adımlara başvurarak Service Fabric kümesini makineden kaldırabilirsiniz. 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Senaryo B: çok makineli küme oluşturma
Planlama ve hazırlık adımlarında daha sonra, [küme Dağıtımınızı planlama ve hazırlama](service-fabric-cluster-standalone-deployment-preparation.md)adımlarını tamamladıktan sonra, küme yapılandırma dosyanızı kullanarak üretim kümenizi oluşturmaya hazırlanın.

Kümeyi dağıtan ve yapılandıran küme yöneticisinin bilgisayarda yönetici ayrıcalıklarına sahip olması gerekir. Service Fabric’i bir etki alanı denetleyicisine yükleyemezsiniz.

1. Tek başına paketteki *TestConfiguration.ps1* betiği, bir kümenin belirli bir ortamda dağıtılıp dağıtılamayacağını doğrulamak için bir en iyi yöntem çözümleyicisi olarak kullanılır. [Dağıtım hazırlığı](service-fabric-cluster-standalone-deployment-preparation.md) belgesinde, ön koşullar ve ortam gereksinimleri listelenir. Geliştirme kümesini oluşturabileceğinizi doğrulamak için betiği çalıştırın:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Aşağıdakine benzer bir çıktı görmeniz gerekir. "Geçildi" alt alanı "true" olarak döndürülürse, sağlamlık denetimleri geçildi ve küme, giriş yapılandırmasına göre dağıtılabilir olarak görünür.

    ```powershell
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Küme oluşturma: Service Fabric kümesini yapılandırmadaki her makine arasında dağıtmak için *Createservicefabriccluster. ps1* betiğini çalıştırın. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Dağıtım izlemeleri, üzerinde CreateServiceFabricCluster.ps1 PowerShell betiğini çalıştırabileceğiniz VM/makineye yazılır. Bunlar, betiğin çalıştırıldığı dizine göre DeploymentTraces alt klasöründe bulunabilir. Service Fabric’in bir makineye doğru şekilde dağıtılıp dağıtılmadığını görmek için, FabricSettings bölümündeki küme yapılandırma dosyasında (varsayılan olarak c:\ProgramData\SF) ayrıntılı olarak açıklandığı gibi FabricDataRoot dizinindeki yüklü dosyaları bulun. Ayrıca, FabricHost.exe ve Fabric.exe işlemlerinin Görev Yöneticisi’nde çalıştığı görülebilir.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Senaryo C: çevrimdışı (internet bağlantısı kesilen) küme oluşturma
Service Fabric çalışma zamanı paketi, küme oluşturma sırasında otomatik olarak indirilir. Bir kümeyi Internet 'e bağlı olmayan makinelere dağıttığınızda, Service Fabric çalışma zamanı paketini ayrı olarak indirmeniz ve küme oluşturma sırasında bu yolu sağlamanız gerekir.
Çalışma zamanı paketi, [Indirme bağlantı Service Fabric çalışma zamanı-Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)' da, internet 'e bağlı başka bir makineden ayrı olarak indirilebilir. Çalışma zamanı paketini çevrimdışı kümeyi dağıttığınız yere kopyalayın ve aşağıdaki örnekte gösterildiği gibi `-FabricRuntimePackagePath` parametresi dahil `CreateServiceFabricCluster.ps1` çalıştırarak kümeyi oluşturun: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

. *\Clusterconfig.exe* ve *.\MicrosoftAzureServiceFabric.cab* , sırasıyla küme yapılandırmasının ve Runtime. cab dosyasının yollarıdır.

### <a name="step-2-connect-to-the-cluster"></a>2\. Adım: kümeye bağlanma
Kümenin çalıştığını ve kullanılabilir olduğunu doğrulamak için kümeye bağlanın. ServiceFabric PowerShell modülü çalışma zamanıyla birlikte yüklenir.  Kümeye küme düğümlerinden birinden veya Service Fabric çalışma zamanına sahip uzak bir bilgisayardan bağlanabilirsiniz.  [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet’i, kümeyle bir bağlantı kurar.

Güvenli olmayan bir kümeye bağlanmak için aşağıdaki PowerShell komutunu çalıştırın:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Örnek:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```

Bir kümeye bağlanmayla ilgili diğer örnekler için bkz. [Güvenli bir kümeye bağlanma](service-fabric-connect-to-secure-cluster.md). Kümeye bağlandıktan sonra, kümedeki düğümlerin listesini ve her bir düğümün durum bilgilerini görüntülemek için [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) cmdlet’ini kullanın. Her düğüm için **HealthState** değerinin *OK* olması gerekir.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>3\. Adım: Service Fabric gezginini kullanarak kümeyi görselleştirme
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), kümenizi görselleştirmek ve uygulamaları yönetmek için iyi bir araçtır.  Service Fabric Explorer, ' a [http://localhost:19080/Explorer](http://localhost:19080/Explorer)giderek bir tarayıcı kullanarak erişebileceğiniz, kümede çalışan bir hizmettir.

Küme panosu, kümenize uygulama ve düğüm durumunun özetini de içeren bir genel bakış sağlar. Düğüm görünümü, kümenin fiziksel düzenini gösterir. Belirli bir düğümde, hangi uygulamalara kod dağıtıldığını denetleyebilirsiniz.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Düğüm ekleme ve kaldırma
İş gereksinimleriniz değiştikçe tek başına Service Fabric kümenize düğüm ekleyebilir veya kaldırabilirsiniz. Ayrıntılı adımlar için bkz. [Service Fabric tek başına kümesine düğüm ekleme veya kaldırma](service-fabric-cluster-windows-server-add-remove-nodes.md).

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Bir kümeyi kaldırma
Bir kümeyi kaldırmak için paket klasöründen *RemoveServiceFabricCluster.ps1* PowerShell betiğini çalıştırın ve yolu JSON yapılandırma dosyasına geçirin. İsteğe bağlı olarak silme işleminin günlüğü için bir konum belirtebilirsiniz.

Bu betik, küme yapılandırma dosyasında düğüm olarak listelenen tüm makinelere yönetici erişimi olan herhangi bir makinede çalıştırılabilir. Bu betiğin çalıştırıldığı makinenin kümenin bir parçası olması gerekmez.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Toplanan telemetri verileri ve devre dışı bırakma
Ürün, varsayılan olarak ürünü geliştirmek için Service Fabric kullanımı üzerinde telemetri toplar. Kurulumun bir parçası olarak çalışan En Iyi Yöntem Çözümleyicisi [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)bağlantıyı denetler. Erişilebilir değilse, Telemetri devre dışı bırakılmadığınız takdirde kurulum başarısız olur.

1. Telemetri ardışık düzeni, her gün [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) için aşağıdaki verileri karşıya yüklemeye çalışır. Bu en iyi çaba yükleme, küme işlevselliğini etkilemez. Telemetri yalnızca yük devretme Yöneticisi birincisini çalıştıran düğümden gönderilir. Başka hiçbir düğüm telemetri göndermedi.
2. Telemetri aşağıdakilerden oluşur:

* Hizmet sayısı
* ServiceType sayısı
* Uygulama sayısı
* Applicationyükseltmelerin sayısı
* FailoverUnits sayısı
* Inbuildfailoverunits sayısı
* Healthunits sayısı
* Çoğaltma sayısı
* Inbuildçoğaltmalar sayısı
* Bir Standya yineleme sayısı
* OfflineReplicas sayısı
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Düğüm sayısı
* Iscontexttamamlanmıştır: true/false
* Clusterıd: Bu, her küme için rastgele oluşturulmuş bir GUID 'dir
* ServiceFabricVersion
* Telemetriyi karşıya yüklediğiniz sanal makinenin veya makinenin IP adresi

Telemetriyi devre dışı bırakmak için, küme yapılandırması ' na aşağıdaki *özellikleri* ekleyin: *enabletelemetri: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Bu pakette bulunan Önizleme özellikleri
Hiçbiri.


> [!NOTE]
> [Windows Server (sürüm 5.3.204. x) için tek başına kümenin yeni GA sürümünden](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)başlayarak, kümenizi el ile veya otomatik olarak gelecek sürümlere yükseltebilirsiniz. Ayrıntılar için [tek başına Service Fabric küme sürümü belgelerini yükseltme](service-fabric-cluster-upgrade-windows-server.md) bölümüne bakın.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
* [PowerShell kullanarak uygulama dağıtma ve kaldırma](service-fabric-deploy-remove-applications.md)
* [Tek başına Windows kümesi için yapılandırma ayarları](service-fabric-cluster-manifest.md)
* [Tek başına Service Fabric kümesine düğüm ekleme veya kaldırma](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Tek başına Service Fabric küme sürümünü yükseltme](service-fabric-cluster-upgrade-windows-server.md)
* [Windows çalıştıran Azure VM 'leriyle tek başına Service Fabric kümesi oluşturma](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Windows güvenliğini kullanarak Windows 'da tek başına kümeyi güvenli hale getirme](service-fabric-windows-cluster-windows-security.md)
* [X509 sertifikalarını kullanarak Windows 'da tek başına kümeyi güvenli hale getirme](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
