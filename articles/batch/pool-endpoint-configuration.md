---
title: Azure Batch havuzundaki düğüm uç noktalarını yapılandırma | Microsoft Docs
description: Azure Batch havuzundaki işlem düğümlerinde SSH veya RDP bağlantı noktalarına erişimi yapılandırma veya devre dışı bırakma.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: lahugh
ms.openlocfilehash: e6c7f2762a6742a1aff7a2c3aff977b5e3657349
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322466"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Azure Batch havuzundaki işlem düğümlerine uzaktan erişimi yapılandırma veya devre dışı bırakma

Batch, varsayılan olarak, ağ bağlantısı olan [düğüm kullanıcısının](/rest/api/batchservice/computenode/adduser) bir toplu iş havuzundaki bir işlem düğümüne dışarıdan bağlanmasına izin verir. Örneğin, bir Kullanıcı Windows havuzundaki bir işlem düğümüne 3389 numaralı bağlantı noktası üzerinden uzak masaüstü (RDP) ile bağlanabilir. Benzer şekilde, varsayılan olarak bir Kullanıcı, bağlantı noktası 22 ' de Secure Shell (SSH) ile Linux havuzundaki bir işlem düğümüne bağlanabilir. 

Ortamınızda, bu varsayılan dış erişim ayarlarını kısıtlamanız veya devre dışı bırakmanız gerekebilir. [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) özelliğini ayarlamak Için Batch API 'lerini kullanarak bu ayarları değiştirebilirsiniz. 

## <a name="about-the-pool-endpoint-configuration"></a>Havuz uç noktası yapılandırması hakkında
Uç nokta yapılandırması, ön uç bağlantı noktalarının bir veya daha fazla [ağ adresi çevirisi (NAT) havuzlarından](/rest/api/batchservice/pool/add#inboundnatpool) oluşur. (Bir NAT havuzunu işlem düğümleri Batch havuzu ile karıştırmayın.) Her NAT havuzunu, havuzun işlem düğümlerinde varsayılan bağlantı ayarlarını geçersiz kılmak için ayarlarsınız. 

Her NAT havuzu yapılandırması bir veya daha fazla [ağ güvenlik grubu (NSG) kuralı](/rest/api/batchservice/pool/add#networksecuritygrouprule)içerir. Her NSG kuralı, uç nokta için belirli ağ trafiğine izin verir veya reddeder. Tüm trafiğe izin vermeyi veya reddetmeyi, bir [hizmet etiketiyle](../virtual-network/security-overview.md#service-tags) ("Internet" gibi) veya belirli IP adreslerinden veya alt ağlardan gelen trafiğe izin vermeyi veya vermemeyi seçebilirsiniz.

### <a name="considerations"></a>Dikkat edilmesi gerekenler
* Havuz uç noktası yapılandırması, havuzun [ağ yapılandırmasının](/rest/api/batchservice/pool/add#networkconfiguration)bir parçasıdır. Ağ yapılandırması isteğe bağlı olarak, havuzun bir [Azure sanal ağına](batch-virtual-network.md)katılması için ayarları dahil edebilir. Havuzu bir sanal ağda ayarlarsanız, sanal ağdaki adres ayarlarını kullanan NSG kuralları oluşturabilirsiniz.
* Bir NAT havuzunu yapılandırırken birden çok NSG kuralı yapılandırabilirsiniz. Kurallar öncelik sırasına göre denetlenir. Bir kural uygulandığı zaman eşleştirme için başka hiçbir kural test edilmez.


## <a name="example-deny-all-rdp-traffic"></a>Örnek: Tüm RDP trafiğini Reddet

Aşağıdaki C# kod parçacığında, bir Windows havuzundaki Işlem düğümlerinde RDP uç noktasının tüm ağ trafiğini reddedecek şekilde nasıl yapılandırılacağı gösterilmektedir. Uç noktası *60000-60099*aralığında bağlantı noktalarının ön uç havuzunu kullanır. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Örnek: İnternet 'ten gelen tüm SSH trafiğini reddetme

Aşağıdaki Python kod parçacığında, tüm internet trafiğini reddetmek için bir Linux havuzundaki işlem düğümlerinde SSH uç noktasının nasıl yapılandırılacağı gösterilmektedir. Uç noktası *4000-4100*aralığında bağlantı noktalarının ön uç havuzunu kullanır. 

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                    source_address_prefix='Internet'
                )
            ]
        )
        ]
    )
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Örnek: Belirli bir IP adresinden gelen RDP trafiğine izin ver

Aşağıdaki C# kod parçacığında, bir Windows havuzundaki Işlem düğümlerinde RDP uç noktasının yalnızca *198.51.100.7*IP adresinden RDP erişimine izin vermek üzere nasıl yapılandırılacağı gösterilmektedir. İkinci NSG kuralı, IP adresiyle eşleşmeyen trafiği reddeder.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Örnek: Belirli bir alt ağdan gelen SSH trafiğine izin ver

Aşağıdaki Python kod parçacığı, bir Linux havuzundaki işlem düğümlerinde SSH uç noktasının yalnızca *192.168.1.0/24*alt ağından erişime izin verecek şekilde nasıl yapılandırılacağını gösterir. İkinci NSG kuralı, alt ağla eşleşmeyen trafiği reddeder.

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access='allow',
                    source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                    priority=175,
                    access='deny',
                    source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Sonraki adımlar

- Azure 'daki NSG kuralları hakkında daha fazla bilgi için bkz. ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/security-overview.md).

- Toplu Işe yönelik ayrıntılı genel bakış için bkz. [Batch ile büyük ölçekli paralel işlem çözümleri geliştirme](batch-api-basics.md).

