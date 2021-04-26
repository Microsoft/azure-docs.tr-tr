---
title: Tek başına kümenin sürümünü yükseltme
description: Tek başına Service Fabric kümesi çalıştıran Azure Service Fabric kodunu yükseltin.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 5831fcffe718e4966c3444a29d35c5bbd55e5f57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842946"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Kümenizde çalışan Service Fabric sürümünü yükseltme 

Modern bir sistem için yükseltme özelliği, ürününüzün uzun süreli başarısına yönelik bir anahtardır. Azure Service Fabric kümesi, sahip olduğunuz bir kaynaktır. Bu makalede, tek başına kümenizde çalışan Service Fabric sürümünün nasıl yükseltileceği açıklanır.

> [!NOTE]
> Kümenizin her zaman desteklenen bir Service Fabric sürümü çalıştırmasını sağlayın. Microsoft yeni bir Service Fabric sürümü duyurusu yaparken, önceki sürüm, duyuru tarihinden itibaren en az 60 gün sonra destek sonuna kadar işaretlenir. Yeni yayınlar [Service Fabric ekip blogundan](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)duyurulur. Yeni sürüm, bu noktada seçim yapmak için kullanılabilir.
>
>

Kümenizi yalnızca, her Service Fabric düğümünün ayrı fiziksel veya sanal bir makinede ayrıldığı bir üretim stili düğüm yapılandırması kullanıyorsanız, yeni sürüme yükseltebilirsiniz. Birden fazla Service Fabric düğümünün tek bir fiziksel veya sanal makinede bulunduğu bir geliştirme kümeniz varsa, kümeyi yeni sürümü ile yeniden oluşturmanız gerekir.

İki ayrı iş akışı, kümenizi en son sürüme veya desteklenen bir Service Fabric sürümüne yükseltebilirler. Tek bir iş akışı, en son sürümü otomatik olarak indirmek üzere bağlantısı olan kümelere yöneliktir. Diğer iş akışı, en son Service Fabric sürümünü indirmek üzere bağlantısı olmayan kümeler içindir.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Kümenizin Service Fabric sürümünün otomatik yükseltmesini etkinleştirin
Microsoft 'un yeni bir sürüm yayınlarsa Service Fabric güncelleştirmelerini indirmek üzere kümenizi ayarlamak için, `fabricClusterAutoupgradeEnabled` küme yapılandırmasını *doğru* olarak ayarlayın. Kümenizin açık olmasını istediğiniz Service Fabric desteklenen bir sürümünü el ile seçmek için, `fabricClusterAutoupgradeEnabled` küme yapılandırmasını *yanlış* olarak ayarlayın.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>En son kodu ve yapılandırmayı indirmek üzere bağlantısı olan kümeleri yükseltme
Küme düğümlerinizin [Microsoft Indirme merkezi](https://download.microsoft.com)'ne internet bağlantısı varsa, kümenizi desteklenen bir sürüme yükseltmek için bu adımları kullanın.

Microsoft [Indirme merkezi](https://download.microsoft.com)bağlantısı olan kümeler için Microsoft, yeni Service Fabric sürümlerinin kullanılabilirliğini düzenli olarak denetler.

Yeni bir Service Fabric sürümü kullanılabilir olduğunda, paket yerel olarak kümeye indirilir ve yükseltme için sağlanır. Ayrıca, bu yeni sürümün müşterisine bildirmek için, sistem şuna benzer bir açık küme durumu uyarısı gösterir:

"Geçerli küme sürümü [sürüm #] destek bitişi [Date]."

Küme en son sürümü çalıştırdıktan sonra, uyarı kaybolur.

Küme durumu uyarısını gördüğünüzde, kümeyi yükseltin:

1. Kümede düğüm olarak listelenen tüm makinelere yönetici erişimi olan herhangi bir makineden kümeye bağlanın. Bu betiğin çalıştırıldığı makinenin kümenin bir parçası olması gerekmez.

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Yükselteceğiniz Service Fabric sürümlerinin listesini alın.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Aşağıdakine benzer bir çıktı almalısınız:

    ![Service Fabric sürümlerini al][getfabversions]
3. [Başlat-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows PowerShell komutunu kullanarak kullanılabilir bir sürüme küme yükseltmesi başlatın.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Yükseltmenin ilerlemesini izlemek için Service Fabric Explorer kullanabilir veya aşağıdaki PowerShell komutunu çalıştırabilirsiniz:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Küme sistem durumu ilkelerine uyulmazsa, yükseltme geri alınır. Start-ServiceFabricClusterUpgrade komutuna yönelik özel sistem durumu ilkeleri belirtmek için [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade)belgelerine bakın.

    Geri alma ile sonuçlanan sorunları düzelttikten sonra, daha önce açıklanan adımları izleyerek yükseltmeyi yeniden başlatın.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>En son kodu ve yapılandırmayı indirmek için *bağlantısı* olmayan kümeleri yükseltin
Küme düğümlerinizin [Microsoft Indirme merkezi](https://download.microsoft.com)'ne internet bağlantısı yoksa, kümenizi desteklenen bir sürüme yükseltmek için bu adımları kullanın.

> [!NOTE]
> İnternet 'e bağlı olmayan bir küme çalıştırıyorsanız, yeni sürümler hakkında bilgi edinmek için [Service Fabric ekip blogunu](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) izlemeniz gerekir. Sistem, yeni sürümlerden sizi uyarmak için bir küme sistem durumu uyarısı göstermez.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Otomatik sağlama vs. el ile sağlama
En son kod sürümü için otomatik indirmeyi ve kaydolmayı etkinleştirmek üzere Service Fabric güncelleştirme hizmetini ayarlayın. Yönergeler için bkz. [tek başına paketteki](service-fabric-cluster-standalone-package-contents.md) *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* .

El ile işlem için bu yönergeleri izleyin.

Bir yapılandırma yükseltmesi başlamadan önce aşağıdaki özelliği *false* olarak ayarlamak için küme yapılandırmanızı değiştirin:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Kullanım ayrıntıları için, [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) PowerShell komutuna bakın. Yapılandırma yükseltmesini başlamadan önce JSON 'inizdeki ' clusterConfigurationVersion ' öğesini güncelleştirdiğinizden emin olun.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Küme yükseltme iş akışı

1. Kümedeki düğümlerden birinden [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) komutunu çalıştırın ve *targetcodeversion*' a göz önünde yararlanın.

2. Güncel sürüme sahip tüm yükseltme uyumlu sürümleri listelemek ve ilişkili indirme bağlantılarından karşılık gelen paketi indirmek için Internet 'e bağlı bir makineden aşağıdakileri çalıştırın:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Kümede düğüm olarak listelenen tüm makinelere yönetici erişimi olan herhangi bir makineden kümeye bağlanın. Bu betiğin çalıştırıldığı makinenin kümenin bir parçası olması gerekmez.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. İndirilen paketi küme görüntü deposuna kopyalayın.

5. Kopyalanmış paketi kaydedin.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Kullanılabilir bir sürüme küme yükseltmesi başlatın.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    Service Fabric Explorer yükseltmenin ilerlemesini izleyebilir veya şu PowerShell komutunu çalıştırabilirsiniz:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Küme sistem durumu ilkelerine uyulmazsa, yükseltme geri alınır. Start-ServiceFabricClusterUpgrade komutuna yönelik özel sistem durumu ilkeleri belirtmek için [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade)belgelerine bakın.

    Geri alma ile sonuçlanan sorunları düzelttikten sonra, daha önce açıklanan adımları izleyerek yükseltmeyi yeniden başlatın.

## <a name="next-steps"></a>Sonraki adımlar
* [Tek başına kümenin yapılandırmasını yükseltme](service-fabric-cluster-config-upgrade-windows-server.md)
* Bazı [Service Fabric kümesi ayarlarını](service-fabric-cluster-fabric-settings.md)özelleştirin.
* [Kümenizi ve ölçeğini ölçeklendirin](service-fabric-cluster-scale-in-out.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
