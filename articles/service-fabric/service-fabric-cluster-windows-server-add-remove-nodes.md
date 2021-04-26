---
title: Tek başına Service Fabric kümesine düğüm ekleme veya kaldırma
description: Şirket içinde veya herhangi bir bulutta olabilecek Windows Server çalıştıran fiziksel veya sanal bir makinede Azure Service Fabric kümesine düğüm ekleme veya kaldırma hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 26945b4785a0591d997139f2427b0ae6b59fa742
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790605"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Windows Server’da çalışan tek başına Service Fabric kümesine düğüm ekleme veya kaldırma
[Tek başına Service Fabric kümenizi Windows Server makinelerinde oluşturduktan](service-fabric-cluster-creation-for-windows-server.md)sonra, (iş) gereksinimleriniz değişebilir ve bu makalede açıklandığı gibi kümenize düğüm eklemeniz veya kaldırmanız gerekir.

> [!NOTE]
> Düğüm ekleme ve kaldırma işlevselliği yerel geliştirme kümelerinde desteklenmez.

## <a name="add-nodes-to-your-cluster"></a>Kümenize düğüm ekleme

1. [Service Fabric kümesi dağıtımınızı planlayın ve hazırlayın](service-fabric-cluster-standalone-deployment-preparation.md)bölümünde özetlenen adımları izleyerek kümenize eklemek istediğiniz VM/makineyi hazırlayın.

2. Bu VM/makinenin ekleneceği hata etki alanı ve yükseltme etki alanını belirler.

   Kümeyi güvenli hale getirmek için sertifikalar kullanırsanız, düğümün kümeye katılması için gereken yerel sertifika depolarına sertifikaların yüklenmesi beklenir. Örneksel, diğer güvenlik biçimleri kullanılırken geçerlidir.

3. Kümeye eklemek istediğiniz VM/makineye Uzak Masaüstü (RDP).

4. [Windows Server için Service Fabric için tek başına PAKETI](https://go.microsoft.com/fwlink/?LinkId=730690) VM/makineye kopyalayın veya indirin ve paketi açın.

5. PowerShell 'i yükseltilmiş ayrıcalıklarla çalıştırın ve daraltılmış paketin konumuna gidin.

6. *AddNode.ps1* betiği, eklenecek yeni düğümü açıklayan parametrelerle çalıştırın. Aşağıdaki örnek, UD1 ve FD:/DC1/R0 içine NodeType0 ve IP adresi 182.17.34.52 ile VM5 adlı yeni bir düğüm ekler. `ExistingClusterConnectionEndPoint` , var olan kümede zaten bulunan bir düğüm için, kümedeki *herhangi* BIR düğümün IP adresi olabilecek bir bağlantı uç noktasıdır. 

   Güvenli olmayan (prototipleme):

   ```
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
   ```

   Güvenli (sertifika tabanlı):

   ```  
   $CertThumbprint= "***********************"
    
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -X509Credential -ServerCertThumbprint $CertThumbprint  -AcceptEULA

   ```

   Betik çalışmayı bitirdiğinde, [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) cmdlet 'ini çalıştırarak yeni düğümün eklenip eklenmeyeceğini kontrol edebilirsiniz.

7. Kümedeki farklı düğümlerde tutarlılık sağlamak için bir yapılandırma yükseltmesi başlatmanız gerekir. En son yapılandırma dosyasını almak için [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) komutunu çalıştırın ve yeni eklenen düğümü "Nodes" bölümüne ekleyin. Aynı yapılandırmaya sahip bir kümeyi yeniden dağıtmanız gerektiğinde, her zaman en son küme yapılandırmasına sahip olmak da önerilir.

   ```
    {
        "nodeName": "vm5",
        "iPAddress": "182.17.34.52",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD1"
    }
   ```

8. Yükseltmeyi başlatmak için [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) komutunu çalıştırın.

   ```
   Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
   ```

   Service Fabric Explorer yükseltmenin ilerlemesini izleyebilirsiniz. Alternatif olarak, [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade)komutunu da çalıştırabilirsiniz.

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>GMSA kullanarak Windows güvenliği ile yapılandırılmış kümelere düğüm ekleme
Grup yönetilen hizmet hesabı (gMSA) ile yapılandırılan kümeler için (bir https://technet.microsoft.com/library/hh831782.aspx) yapılandırma yükseltmesi kullanılarak yeni bir düğüm eklenebilir):
1. En son yapılandırma dosyasını almak ve "düğümler" bölümünde eklemek istediğiniz yeni düğüm hakkındaki ayrıntıları eklemek için mevcut düğümlerin herhangi birinde [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) komutunu çalıştırın. Yeni düğümün aynı grup tarafından yönetilen hesabın parçası olduğundan emin olun. Bu hesabın tüm makinelerde yönetici olması gerekir.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Yükseltmeyi başlatmak için [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) komutunu çalıştırın.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Service Fabric Explorer yükseltmenin ilerlemesini izleyebilirsiniz. Alternatif olarak, [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) komutunu da çalıştırabilirsiniz

### <a name="add-node-types-to-your-cluster"></a>Kümenize düğüm türleri ekleme
Yeni bir düğüm türü eklemek için, yapılandırmanızı "Özellikler" in altındaki "NodeTypes" bölümüne eklemek ve [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)kullanarak bir yapılandırma yükseltmesine başlamak için değiştirin. Yükseltme tamamlandıktan sonra bu düğüm türü ile kümenize yeni düğümler ekleyebilirsiniz.

## <a name="remove-nodes-from-your-cluster"></a>Kümeinizden düğümleri kaldırma
Bir düğüm, yapılandırma yükseltmesi kullanılarak bir kümeden aşağıdaki şekilde kaldırılabilir:

1. En son yapılandırma dosyasını almak ve düğümü "düğümler" bölümünden *kaldırmak* için [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) komutunu çalıştırın.
"FabricSettings" bölümünün içindeki "Kurulum" bölümüne "Nodestoberetaşınmış" parametresini ekleyin. "Değer", kaldırılması gereken düğümlerin düğüm adlarının virgülle ayrılmış bir listesi olmalıdır.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. Yükseltmeyi başlatmak için [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) komutunu çalıştırın.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Service Fabric Explorer yükseltmenin ilerlemesini izleyebilirsiniz. Alternatif olarak, [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade)komutunu da çalıştırabilirsiniz.

> [!NOTE]
> Düğümlerin kaldırılması, birden çok yükseltme başlatabilir. Bazı düğümler `IsSeedNode=”true”` etiketiyle işaretlenir ve kullanılarak küme bildirimi sorgulanarak belirlenebilir `Get-ServiceFabricClusterManifest` . Çekirdek düğümlerin bu tür senaryolarda taşınması gerektiğinden, bu tür düğümlerin kaldırılması diğerlerinden daha uzun sürebilir. Küme en az 3 birincil düğüm türü düğümü korumalıdır.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Düğüm türlerini kümenizdeki kaldırma
Düğüm türünü kaldırmadan önce düğüm türüne başvuran düğüm olup olmadığını kontrol edin. Karşılık gelen düğüm türünü kaldırmadan önce bu düğümleri kaldırın. Karşılık gelen tüm düğümler kaldırıldıktan sonra, küme yapılandırmasından NodeType 'yi kaldırabilir ve [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)kullanarak bir yapılandırma yükseltmesi başlatabilirsiniz.


### <a name="replace-primary-nodes-of-your-cluster"></a>Kümenizin birincil düğümlerini değiştirme
Birincil düğümlerin yerine geçen bir düğüm, ve sonra toplu işlemlere eklemek yerine bir düğüm daha yapılmalıdır.


## <a name="next-steps"></a>Sonraki adımlar
* [Tek başına Windows kümesi için yapılandırma ayarları](service-fabric-cluster-manifest.md)
* [X509 sertifikalarını kullanarak Windows 'da tek başına kümeyi güvenli hale getirme](service-fabric-windows-cluster-x509-security.md)
* [Windows çalıştıran Azure VM 'leriyle tek başına Service Fabric kümesi oluşturma](./service-fabric-cluster-creation-via-arm.md)
