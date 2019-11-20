---
title: Azure Container Instances kaynak kullanılabilirliği
description: Farklı Azure bölgelerindeki Azure Container Instances hizmeti için işlem ve bellek kaynaklarının kullanılabilirliği.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 05/14/2019
ms.author: danlep
ms.openlocfilehash: 24edce511c2d07050db1e77edeae4e587fcd79b0
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172387"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure bölgelerindeki Azure Container Instances kaynak kullanılabilirliği

Bu makalede, Azure bölgelerindeki Azure Container Instances işlem ve bellek kaynaklarının kullanılabilirliği ayrıntılı olarak açıklanır. 

Sunulan değerler, bir [kapsayıcı grubunun](container-instances-container-groups.md)dağıtımı başına kullanılabilir en fazla kaynak sayısıdır. Değerler yayın sırasında geçerli. 

> [!NOTE]
> Bu kaynak sınırları içinde oluşturulan kapsayıcı grupları dağıtım bölgesi içinde kullanılabilirliğine tabidir. Bir bölge ağı yük altında olduğunda, örnek dağıtırken hatayla karşılaşabilirsiniz. Bu tür bir dağıtım hatasını azaltmak için, daha düşük kaynak ayarlarıyla örnekleri dağıtmaya çalışın veya dağıtımınızı daha sonra deneyin.

Dağıtımlarınızdaki kotalar ve diğer sınırlar hakkında daha fazla bilgi için bkz. [Azure Container Instances Için kotalar ve sınırlar](container-instances-quotas.md).

## <a name="availability---general"></a>Kullanılabilirlik-genel

Aşağıdaki bölgeler ve kaynaklar, Linux ve [desteklenen](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server 2016 tabanlı kapsayıcılar ile kapsayıcı grupları için kullanılabilir.

| Location | OS | CPU | Bellek (GB) |
| -------- | -- | :---: | :-----------: |
| Kanada Orta, Orta Hindistan, Orta ABD, Doğu Asya, Doğu ABD, Doğu ABD 2, Kuzey Avrupa, Orta Güney ABD, Güneydoğu Asya, UK Güney, Batı ABD | Linux | 4 | 16 |
| Batı Avrupa, Batı ABD 2 | Linux | 4 | 14 |
| Avustralya Doğu, Japonya Doğu | Linux | 2 | 8 |
| Orta Kuzey ABD, Güney Hindistan | Linux | 2 | 3,5 |
| Batı Avrupa | Windows | 4 | 16 |
| Doğu ABD, Batı ABD | Windows | 4 | 14 |
| Avustralya Doğu, Kanada Orta, Orta Hindistan, Orta ABD, Doğu Asya, Doğu ABD 2, Japonya Doğu, Orta Kuzey ABD, Kuzey Avrupa, Orta Güney ABD, Güneydoğu Asya, Güney Hindistan, UK Güney, Batı ABD 2 | Windows | 2 | 3,5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Kullanılabilirlik-Windows Server 2019 LTSC, 1809 dağıtımları (Önizleme)

Windows Server 2019 tabanlı kapsayıcılar (Önizleme) ile kapsayıcı grupları için aşağıdaki bölgeler ve kaynaklar mevcuttur.

| Location | OS | CPU | Bellek (GB) |
| -------- | -- | :---: | :-----------: |
| Güneydoğu Asya, Kuzey Avrupa, Batı Avrupa, Orta ABD, Doğu ABD, Batı ABD, Batı ABD 2 | Windows | 4 | 16 |
| Doğu ABD 2 | Windows | 2 | 3,5 |


## <a name="availability---virtual-network-deployment-preview"></a>Kullanılabilirlik-sanal ağ dağıtımı (Önizleme)

Aşağıdaki bölgeler ve kaynaklar bir [Azure sanal ağında](container-instances-vnet.md) (Önizleme) dağıtılan bir kapsayıcı grubu için kullanılabilir.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Kullanılabilirlik-GPU kaynakları (Önizleme)

Aşağıdaki bölgeler ve kaynaklar, [GPU kaynaklarıyla](container-instances-gpu.md) dağıtılan bir kapsayıcı grubu için kullanılabilir (Önizleme).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Sonraki adımlar

[Aka.MS/aci/feedback](https://aka.ms/aci/feedback)adresinde ek bölgeler mi yoksa daha fazla kaynak kullanılabilirliği mi görmek istediğinizi takımın bilmesini sağlayın.

Kapsayıcı örneği dağıtımı sorunlarını giderme hakkında bilgi için bkz. [Azure Container Instances dağıtım sorunlarını giderme](container-instances-troubleshooting.md).
