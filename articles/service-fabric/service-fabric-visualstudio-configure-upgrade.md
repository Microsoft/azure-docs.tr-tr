---
title: Service Fabric uygulamasının yükseltmesini yapılandırma | Microsoft Docs
description: Microsoft Visual Studio kullanarak Service Fabric uygulamasını yükseltme ayarlarını yapılandırmayı öğrenin.
services: service-fabric
documentationcenter: na
author: mikkelhegn
manager: mfussell
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/29/2017
ms.author: mikhegn
ms.openlocfilehash: 5979541146b7cd7b854f35c5bf204e71208f066b
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876750"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Visual Studio 'da Service Fabric uygulamasının yükseltmesini yapılandırma
Azure Service Fabric için Visual Studio Araçları, yerel veya uzak kümelerdeki yayımlamaya yönelik yükseltme desteği sağlar. Test ve hata ayıklama sırasında uygulamanızı değiştirmek yerine uygulamanızı daha yeni bir sürüme yükseltmek istediğiniz üç senaryo vardır:

* Yükseltme sırasında uygulama verileri kaybolmaz.
* Kullanılabilirlik yüksek kalır, böylece yükseltme etki alanları arasında yayılan hizmet örnekleri varsa yükseltme sırasında herhangi bir hizmet kesintisi olmayacaktır.
* Testler, yükseltilirken bir uygulamaya karşı çalıştırılabilir.

## <a name="parameters-needed-to-upgrade"></a>Yükseltmek için gereken parametreler
İki tür dağıtım arasından seçim yapabilirsiniz: normal veya yükseltme. Düzenli dağıtım, kümedeki önceki dağıtım bilgilerini ve verileri siler, bu da bir yükseltme dağıtımı tarafından korunur. Visual Studio 'da bir Service Fabric uygulamasını yükselttiğinizde, uygulama yükseltme parametreleri ve sistem durumu denetim ilkeleri sağlamanız gerekir. Uygulama yükseltme parametreleri yükseltmeyi denetlemeye yardımcı olur, ancak sistem durumu denetim ilkeleri yükseltmenin başarılı olup olmadığını belirtir. Daha fazla ayrıntı için bkz. [Service Fabric uygulama yükseltme: yükseltme parametreleri](service-fabric-application-upgrade-parameters.md) .

Üç yükseltme modu vardır: *İzleniyor*, izlenemez *toredauto*ve Iziztoredmanual.

* Izlenen bir yükseltme, yükseltme ve uygulama sistem durumu denetimini otomatikleştirir.
* Tasmesiz yükseltme, yükseltmeyi otomatikleştirir, ancak uygulama sistem durumu denetimini atlar.
* Geriye doğru yükseltme yaptığınızda, her yükseltme etki alanını el ile yükseltmeniz gerekir.

Her yükseltme modu farklı parametre kümeleri gerektirir. Kullanılabilir yükseltme seçenekleri hakkında daha fazla bilgi edinmek için bkz. [uygulama yükseltme parametreleri](service-fabric-application-upgrade-parameters.md) .

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Visual Studio 'da Service Fabric uygulamasını yükseltme
Bir Service Fabric uygulamasını yükseltmek için Visual Studio Service Fabric araçları kullanıyorsanız, **uygulamayı Yükselt** onay kutusunu işaretleyerek, düzenli bir dağıtım yerine yükseltme yapmak için bir yayımlama işlemi belirtebilirsiniz.

### <a name="to-configure-the-upgrade-parameters"></a>Yükseltme parametrelerini yapılandırmak için
1. Onay kutusunun yanındaki **Ayarlar** düğmesine tıklayın. **Yükseltme parametrelerini düzenle** iletişim kutusu görüntülenir. **Yükseltme parametrelerini düzenle** Iletişim kutusu Izlenen, Izlenmeyen Toredauto ve izlenemez yükseltme modlarını destekler.
2. Kullanmak istediğiniz yükseltme modunu seçin ve ardından parametre kılavuzunu doldurun.

    Her parametrenin varsayılan değerleri vardır. *Defaultservicetypehealthpolicy* isteğe bağlı parametresi bir karma tablo girişi alır. *Defaultservicetypehealthpolicy*için karma tablo giriş biçimine bir örnek aşağıda verilmiştir:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *Servicetypehealthpolicymap* , aşağıdaki biçimde bir karma tablo girişi alan başka bir isteğe bağlı parametredir:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    İşte gerçek hayatta bir örnek:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Izleme izlemeyi geri al ' ı seçerseniz, devam etmek ve yükseltme işlemini tamamlaması için bir PowerShell konsolu 'nu el ile başlatmanız gerekir. El ile yükseltmenin nasıl çalıştığını öğrenmek için [Service Fabric uygulama yükseltme: gelişmiş konular](service-fabric-application-upgrade-advanced.md) bölümüne bakın.

## <a name="upgrade-an-application-by-using-powershell"></a>PowerShell kullanarak bir uygulamayı yükseltme
Service Fabric uygulamasını yükseltmek için PowerShell cmdlet 'lerini kullanabilirsiniz. Ayrıntılı bilgi için bkz. [uygulama yükseltme öğreticisi](service-fabric-application-upgrade-tutorial.md) ve [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) Service Fabric.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Uygulama bildirim dosyasında bir sistem durumu denetim ilkesi belirtin
Bir Service Fabric uygulamasındaki her hizmet, varsayılan değerleri geçersiz kılan kendi sistem durumu ilkesi parametrelerine sahip olabilir. Bu parametre değerlerini uygulama bildirim dosyasında sağlayabilirsiniz.

Aşağıdaki örnek, uygulama bildiriminde her bir hizmet için benzersiz bir sistem durumu denetim ilkesinin nasıl uygulanacağını gösterir.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Sonraki adımlar
Bir uygulamayı yükseltme hakkında daha fazla bilgi için bkz. [Visual Studio kullanarak bir uygulamayı yükseltme](service-fabric-application-upgrade-tutorial.md).
