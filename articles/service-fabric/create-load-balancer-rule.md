---
title: Küme için Azure Load Balancer kuralı oluşturma
description: Azure Service Fabric kümeniz için bağlantı noktaları açmak üzere bir Azure Load Balancer yapılandırın.
services: service-fabric
documentationcenter: na
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: atsenthi
ms.openlocfilehash: 2e730ae8ecf6f1fab12aff23cab0ac3aa246233a
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173354"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Service Fabric kümesi için bağlantı noktalarını açma

Azure Service Fabric kümeniz ile dağıtılan yük dengeleyici, trafiği bir düğümde çalışan uygulamanıza yönlendirir. Uygulamanızı farklı bir bağlantı noktası kullanacak şekilde değiştirirseniz, bu bağlantı noktasını (veya farklı bir bağlantı noktasını yönlendirmelisiniz) Azure Load Balancer.

Service Fabric kümenizi Azure 'a dağıttığınızda, sizin için otomatik olarak bir yük dengeleyici oluşturulur. Yük dengeleyiciniz yoksa, bkz. [Internet 'e yönelik yük dengeleyici yapılandırma](../load-balancer/load-balancer-get-started-internet-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Service Fabric 'i yapılandırma

Service Fabric uygulamanızın **Servicemanifest. xml** yapılandırma dosyası, uygulamanızın kullanmasını beklediği uç noktaları tanımlar. Yapılandırma dosyası bir uç nokta tanımlamak üzere güncelleştirildikten sonra, bu (veya farklı) bir bağlantı noktasını göstermek için yük dengeleyici 'nin güncellenmesi gerekir. Service Fabric uç noktası oluşturma hakkında daha fazla bilgi için bkz. [bir uç nokta kurma](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Bir Load Balancer kuralı, internet 'e yönelik bir bağlantı noktası açar ve trafiği, uygulamanız tarafından kullanılan iç düğümün bağlantı noktasına iletir. Yük dengeleyiciniz yoksa, bkz. [Internet 'e yönelik yük dengeleyici yapılandırma](../load-balancer/load-balancer-get-started-internet-portal.md).

Load Balancer kuralı oluşturmak için aşağıdaki bilgileri toplamanız gerekir:

- Yük dengeleyici adı.
- Yük dengeleyici ve Service Fabric kümesinin kaynak grubu.
- Dış bağlantı noktası.
- İç bağlantı noktası.

## <a name="azure-cli"></a>Azure CLI
Yalnızca **Azure CLI**ile yük dengeleyici kuralı oluşturmak için tek bir komut alır. Yeni bir kural oluşturmak için yalnızca yük dengeleyici ve kaynak grubunun adını bilmeniz gerekir.

>[!NOTE]
>Yük dengeleyicinin adını belirlemeniz gerekiyorsa, tüm yük dengeleyiciler ve ilişkili kaynak gruplarının listesini hızlıca almak için bu komutu kullanın.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Azure CLı komutu, aşağıdaki tabloda açıklanan birkaç parametreye sahiptir:

| Parametre | Açıklama |
| --------- | ----------- |
| `--backend-port`  | Service Fabric uygulamanın dinlediği bağlantı noktası. |
| `--frontend-port` | Yük dengeleyicinin dış bağlantılar için sunduğu bağlantı noktası. |
| `-lb-name` | Değiştirilecek yük dengeleyicinin adı. |
| `-g`       | Hem yük dengeleyici hem de Service Fabric kümesine sahip olan kaynak grubu. |
| `-n`       | Kuralın istenen adı. |


>[!NOTE]
>Azure CLı ile yük dengeleyici oluşturma hakkında daha fazla bilgi için bkz. [Azure CLI ile yük dengeleyici oluşturma](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell, Azure CLı 'den biraz daha karmaşıktır. Bir kural oluşturmak için bu kavramsal adımları izleyin:

1. Azure 'dan yük dengeleyiciyi alın.
2. Bir kural oluşturun.
3. Kuralı yük dengeleyiciye ekleyin.
4. Yük dengeleyiciyi güncelleştirin.

>[!NOTE]
>Yük dengeleyicinin adını belirlemeniz gerekiyorsa, tüm yük dengeleyiciler ve ilişkili kaynak gruplarının listesini hızlıca almak için bu komutu kullanın.
>
>`Get-AzLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzLoadBalancer
```

Komutuyla ilgili olarak, yük dengeleyicinin `-BackendPort` dış bağlantılar için sunduğu bağlantı noktasını temsilederveServiceFabricuygulamasınındinlediğibağlantınoktasınıtemsileder.`-FrontendPort` `New-AzLoadBalancerRuleConfig`

>[!NOTE]
>PowerShell ile yük dengeleyici oluşturma hakkında daha fazla bilgi için bkz. [PowerShell ile yük dengeleyici oluşturma](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Sonraki adımlar

[Service Fabric 'de ağ oluşturma](service-fabric-patterns-networking.md)hakkında daha fazla bilgi edinin.
