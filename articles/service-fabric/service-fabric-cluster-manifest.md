---
title: Azure Service Fabric tek başına kümenizi yapılandırma
description: Tek başına veya şirket içi Azure Service Fabric kümenizi nasıl yapılandıracağınızı öğrenin.
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: fd93263b38340ce080cca1aecb98f3a599ff1861
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91843167"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Tek başına Windows kümesi için yapılandırma ayarları
Bu makalede, *ClusterConfig.json* dosyasında ayarlanyüklenebilen tek başına Azure Service Fabric kümesinin yapılandırma ayarları açıklanmaktadır. Bu dosyayı, küme düğümleri, güvenlik yapılandırmalarının yanı sıra hata ve yükseltme etki alanları açısından ağ topolojisi ile ilgili bilgileri belirtmek için kullanacaksınız.  Yapılandırma ayarlarını değiştirdikten veya ekledikten sonra [tek başına küme oluşturabilir](service-fabric-cluster-creation-for-windows-server.md) veya [tek başına bir kümenin yapılandırmasını yükseltebilirsiniz](service-fabric-cluster-config-upgrade-windows-server.md).

[Tek başına Service Fabric paketini indirdiğinizde](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), örnekleri de ClusterConfig.jsde dahil edilmiştir. Adlarında "DevCluster" olan örnekler, mantıksal düğümler kullanılarak aynı makinede yer alan her üç düğüme sahip bir küme oluşturur. Bu düğümlerin dışında, en az birinin birincil düğüm olarak işaretlenmesi gerekir. Bu tür bir küme, geliştirme veya test ortamları için yararlıdır. Üretim kümesi olarak desteklenmez. Adlarında "MultiMachine" olan örnekler, her düğüm ayrı bir makinede bulunan üretim sınıfı kümeleri oluşturmaya yardımcı olur. Bu kümelerin birincil düğümlerin sayısı, kümenin [güvenilirlik düzeyini](#reliability)temel alır. Sürüm 5,7 ' de, API sürüm 05-2017 ' de güvenilirlik düzeyi özelliğini kaldırdık. Bunun yerine, kodumuz kümeniz için en iyileştirilmiş güvenilirlik düzeyini hesaplar. 5,7 ve sonraki sürümlerde bu özellik için bir değer ayarlamayı denemeyin.

* ClusterConfig.Unsecure.DevCluster.jsve ClusterConfig.Unsecure.MultiMachine.js, sırasıyla güvenli olmayan bir test veya üretim kümesi oluşturmayı gösterir.

* Üzerinde ClusterConfig.Windows.DevCluster.jsve ClusterConfig.Windows.MultiMachine.js[Windows güvenliği](service-fabric-windows-cluster-windows-security.md)kullanılarak güvenliği sağlanan test veya üretim kümelerinin nasıl oluşturulacağını gösterir.

* ClusterConfig.X509.DevCluster.jsve ClusterConfig.X509.MultiMachine.js, [x509 sertifika tabanlı güvenlik](service-fabric-windows-cluster-x509-security.md)kullanılarak güvenliği sağlanan test veya üretim kümelerinin nasıl oluşturulacağını gösterir.

Şimdi de ClusterConfig.jsdosyadaki çeşitli bölümleri incelim.

## <a name="general-cluster-configurations"></a>Genel küme yapılandırması
Genel küme yapılandırmalarında, aşağıdaki JSON kod parçacığında gösterildiği gibi, kümeye özgü geniş kapsamlı konfigürasyonlar ele alınmaktadır:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Ad değişkenine atayarak Service Fabric kümenize kolay bir ad verebilirsiniz. ClusterConfigurationVersion, kümenizin sürüm numarasıdır. Service Fabric kümenizi her yükselttiğinizde arttırın. ApiVersion değerini varsayılan değere ayarlayın.

## <a name="nodes-on-the-cluster"></a>Kümedeki düğümler
Aşağıdaki kod parçacığında gösterildiği gibi düğümler bölümünü kullanarak Service Fabric kümenizdeki düğümleri yapılandırabilirsiniz:
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Service Fabric kümenin en az üç düğüm içermesi gerekir. Bu bölüme, kuruluma göre daha fazla düğüm ekleyebilirsiniz. Aşağıdaki tabloda her düğüm için yapılandırma ayarları açıklanmaktadır:

| **Düğüm yapılandırması** | **Açıklama** |
| --- | --- |
| nodeName |Düğüme kolay bir ad verebilirsiniz. |
| Belirlenemiyor |Bir komut penceresi açıp yazarak düğümünüz IP adresini bulun `ipconfig` . IPV4 adresine göz atar ve bunu IPAddress değişkenine atayın. |
| nodeTypeRef |Her düğüme farklı bir düğüm türü atanabilir. [Düğüm türleri](#node-types) aşağıdaki bölümde tanımlanmıştır. |
| faultDomain |Hata etki alanları, paylaşılan fiziksel bağımlılıklar nedeniyle, küme yöneticilerinin aynı anda başarısız olabilecek fiziksel düğümleri tanımlamasını sağlar. |
| upgradeDomain |Yükseltme etki alanları, Service Fabric yükseltmeleri için aynı anda kapatılmış düğüm kümelerini tanımlar. Herhangi bir fiziksel gereksinimlerle sınırlı olmadıkları için, hangi düğümlerin yükseltme etki alanlarına atanacağını seçebilirsiniz. |

## <a name="cluster-properties"></a>Küme özellikleri
ClusterConfig.jsüzerindeki Özellikler bölümü, kümeyi gösterildiği gibi yapılandırmak için kullanılır:

### <a name="reliability"></a>Güvenilirlik
Rahatlımadüzeyi kavramı, kümenin birincil düğümlerinde çalışabilen Service Fabric sistem hizmetlerinin çoğaltma sayısını veya örneklerini tanımlar. Bu hizmetlerin güvenilirliğini ve dolayısıyla kümeyi belirler. Değer, küme oluşturma ve yükseltme zamanında sistem tarafından hesaplanır.

### <a name="diagnostics"></a>Tanılama
Diagnosticstransaction bölümünde, aşağıdaki kod parçacığında gösterildiği gibi, tanılamayı etkinleştirmek ve düğüm veya küme hatalarıyla ilgili sorunları gidermek için parametreleri yapılandırabilirsiniz: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Meta veriler, küme tanılamalarınızın bir açıklamasıdır ve kuruluma göre ayarlanabilir. Bu değişkenler, ETW izleme günlüklerini ve kilitlenme dökümlerinin yanı sıra performans sayaçlarını toplamaya yardımcı olur. ETW izleme günlükleri hakkında daha fazla bilgi için bkz. [tracelog](/windows-hardware/drivers/devtest/tracelog) ve [ETW izleme](/dotnet/framework/wcf/samples/etw-tracing). [Kilitlenme dökümleri](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) ve [performans sayaçları](/windows/win32/perfctrs/performance-counters-portal)dahil tüm Günlükler, makinenizde ConnectionString klasörüne yönlendirilebilir. Ayrıca, tanılamayı depolamak için AzureStorage de kullanabilirsiniz. Aşağıdaki örnek kod parçacığına bakın:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Güvenlik
Güvenlik Bölümü, güvenli bir tek başına Service Fabric kümesi için gereklidir. Aşağıdaki kod parçacığı, bu bölümün bir parçasını gösterir:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

Meta veriler, güvenli kümenizin bir açıklamasıdır ve kuruluma göre ayarlanabilir. ClusterCredentialType ve ServerCredentialType, kümenin ve düğümlerin uygulama güvenlik türünü belirlenir. Sertifika tabanlı güvenlik ya da Active Directory tabanlı güvenlik için *Windows* için *x509* olarak ayarlanabilir. Güvenlik bölümünün geri kalanı güvenlik türünü temel alır. Güvenlik bölümünün geri kalanını nasıl dolduracağı hakkında daha fazla bilgi için, tek başına kümede veya [Windows Güvenlik 'de tek başına bir kümede](service-fabric-windows-cluster-windows-security.md) [sertifika tabanlı güvenlik](service-fabric-windows-cluster-x509-security.md) bölümüne bakın.

### <a name="node-types"></a>Düğüm türleri
NodeTypes bölümü, kümenizin sahip olduğu düğümlerin türünü açıklar. Aşağıdaki kod parçacığında gösterildiği gibi, bir küme için en az bir düğüm türünün belirtilmesi gerekir: 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

Ad, bu belirli düğüm türü için kolay addır. Bu düğüm türünde bir düğüm oluşturmak için, [daha önce belirtildiği](#nodes-on-the-cluster)gibi, kolay adını bu düğüm Için nodetyperef değişkenine atayın. Her düğüm türü için, kullanılan bağlantı uç noktalarını tanımlayın. Bu bağlantı uç noktaları için herhangi bir bağlantı noktası numarasını, bu kümedeki diğer uç noktalarla çakışmadığı sürece seçebilirsiniz. Bir çok düğümlü kümede, bir veya daha fazla birincil düğüm vardır (yani, ısprımary, *doğru* olarak ayarlanır [) ve güvenlere bağlıdır.](#reliability) Birincil ve birincil olmayan düğüm türleri hakkında daha fazla bilgi edinmek için bkz. nodeTypes ve Relilelevel hakkında bilgi için [küme kapasitesi planlama konuları Service Fabric](service-fabric-cluster-capacity.md) . 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Düğüm türlerini yapılandırmak için kullanılan uç noktalar
* clientConnectionEndpointPort, istemci API 'Leri kullanıldığında, istemci tarafından kümeye bağlanmak için kullanılan bağlantı noktasıdır. 
* clusterConnectionEndpointPort, düğümlerin birbirleriyle iletişim kurduğu bağlantı noktasıdır.
* leaseDriverEndpointPort, düğümlerin hala etkin olup olmadığını bulmak için küme Kiralama sürücüsü tarafından kullanılan bağlantı noktasıdır. 
* serviceConnectionEndpointPort, belirli bir düğümdeki Service Fabric istemcisiyle iletişim kurmak üzere bir düğüme dağıtılan uygulamalar ve hizmetler tarafından kullanılan bağlantı noktasıdır.
* httpGatewayEndpointPort, kümeye bağlanmak için Service Fabric Explorer tarafından kullanılan bağlantı noktasıdır.
* ephemeralPorts [, işletim sistemi tarafından kullanılan dinamik bağlantı noktalarını](https://support.microsoft.com/kb/929851)geçersiz kılar. Service Fabric, bu bağlantı noktalarının bir parçasını uygulama bağlantı noktaları olarak kullanır ve kalan işletim sistemi için kullanılabilir. Ayrıca, bu aralığı işletim sisteminde mevcut olan aralığa eşler, bu nedenle tüm amaçlarla, örnek JSON dosyalarında verilen aralıkları kullanabilirsiniz. Başlangıç ve bitiş bağlantı noktaları arasındaki farkın en az 255 olduğundan emin olun. Bu fark çok düşükse çakışmalar ile karşılaşabilirsiniz. bu Aralık işletim sistemi ile paylaşılmıştır. Yapılandırılmış dinamik bağlantı noktası aralığını görmek için, öğesini çalıştırın `netsh int ipv4 show dynamicport tcp` .
* applicationPorts, Service Fabric uygulamalar tarafından kullanılan portlardır. Uygulama bağlantı noktası aralığı uygulamalarınızın uç nokta gereksinimini kapsayacak kadar büyük olmalıdır. Bu Aralık, makinedeki dinamik bağlantı noktası aralığından, yani ephemeralPorts, yapılandırmada ayarlandığı şekilde özel olmalıdır. Service Fabric, her yeni bağlantı noktası gerektiğinde bu bağlantı noktalarını kullanır ve bu bağlantı noktaları için güvenlik duvarını açma işlemini gerçekleştirir. 
* Smarproxyendpointport isteğe bağlı bir ters proxy uç noktasıdır. Daha fazla bilgi için bkz. [ters proxy Service Fabric](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Günlük ayarları
FabricSettings bölümünde Service Fabric verileri ve günlükleri için kök dizinleri ayarlayabilirsiniz. Bu dizinleri yalnızca ilk küme oluşturma sırasında özelleştirebilirsiniz. Bu bölümün aşağıdaki örnek kod parçacığına bakın:

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

FabricDataRoot ve FabricLogRoot olarak işletim sistemi olmayan bir sürücü kullanmanızı öneririz. İşletim sistemi yanıt vermeyi durdurduğunda durumlardan kaçınılarak daha fazla güvenilirlik sağlar. Yalnızca veri kökünü özelleştirirseniz, günlük kökü veri kökünün altına bir düzey yerleştirilir.

### <a name="stateful-reliable-services-settings"></a>Durum bilgisi olan Reliable Services ayarları
Ktlgünlükçü bölümünde Reliable Services için genel yapılandırma ayarlarını belirleyebilirsiniz. Bu ayarlar hakkında daha fazla bilgi için bkz. [durum bilgisi Reliable Services yapılandırma](service-fabric-reliable-services-configuration.md). Aşağıdaki örnekte, durum bilgisi olan hizmetler için güvenilir koleksiyonları geri yüklemek üzere oluşturulan paylaşılan işlem günlüğünün nasıl değiştirileceği gösterilmektedir:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Eklenti özellikleri
Eklenti özelliklerini yapılandırmak için apiVersion 'ı 04-2017 veya üzeri olarak yapılandırın ve addonFeatures 'ı burada gösterildiği gibi yapılandırın:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Tüm kullanılabilir eklenti özellikleri [Service Fabric REST API başvurusunda](/rest/api/servicefabric/sfrp-model-addonfeatures)görülebilir.

### <a name="container-support"></a>Kapsayıcı desteği
Tek başına kümeler için hem Windows Server kapsayıcıları hem de Hyper-V kapsayıcıları için kapsayıcı desteğini etkinleştirmek üzere DnsService eklentisi özelliğinin etkinleştirilmesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar
Tek başına küme kuruluma göre yapılandırılmış dosya *üzerinde birClusterConfig.js* tamamladıktan sonra, kümenizi dağıtabilirsiniz. [Tek başına Service Fabric kümesi oluşturma](service-fabric-cluster-creation-for-windows-server.md)bölümündeki adımları izleyin. 

Dağıtılan tek başına bir kümeniz varsa, [tek başına bir kümenin yapılandırmasını da yükseltebilirsiniz](service-fabric-cluster-config-upgrade-windows-server.md). 

[Service Fabric Explorer ile kümenizi görselleştirmeyi](service-fabric-visualizing-your-cluster.md)öğrenin.
