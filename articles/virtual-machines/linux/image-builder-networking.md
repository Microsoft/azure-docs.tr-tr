---
title: Azure görüntü Oluşturucu hizmeti ağ seçenekleri
description: Azure VM Image Builder hizmetini dağıtma sırasında ağ seçeneklerini anlama
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: cd5027ca6e0ce3dc02da14b7dd6afd6e00e3f92d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669435"
---
# <a name="azure-image-builder-service-networking-options"></a>Azure görüntü Oluşturucu hizmeti ağ seçenekleri

Azure Image Builder 'ı mevcut bir VNET ile veya olmadan dağıtmayı seçmeniz gerekir.

## <a name="deploy-without-specifying-an-existing-vnet"></a>Mevcut bir VNET belirtmeden dağıtma

Mevcut bir VNET belirtmezseniz, Azure Image Builder hazırlama kaynak grubunda bir sanal ağ ve alt ağ oluşturur. Genel bir IP kaynağı, Azure görüntü Oluşturucu hizmeti ile gelen trafiği kısıtlamak için bir ağ güvenlik grubuyla birlikte kullanılır. Genel IP, görüntü derlemesi sırasında Azure görüntü Oluşturucu komutlarına yönelik kanalı kolaylaştırır. Oluşturma işlemi tamamlandıktan sonra VM, genel IP, diskler ve VNET silinir. Bu seçeneği kullanmak için, hiçbir VNET özelliği belirtmeyin.

## <a name="deploy-using-an-existing-vnet"></a>Mevcut bir VNET 'i kullanarak dağıtın

VNET ve alt ağ belirtirseniz Azure Image Builder, derleme VM 'sini seçtiğiniz VNET 'iniz için dağıtır. VNET 'iniz üzerinde erişilebilir olan kaynaklara erişebilirsiniz. Ayrıca, başka bir sanal ağa bağlı olmayan bir yığılıyor VNET de oluşturabilirsiniz. VNET belirtirseniz Azure görüntü Oluşturucu genel IP adresi kullanmaz. Azure görüntü Oluşturucu hizmetinden derleme VM 'sine yönelik iletişim Azure özel bağlantı teknolojisini kullanır.

Daha fazla bilgi için aşağıdaki örneklerden birine bakın:

* [Mevcut bir Azure sanal ağına erişime izin veren Windows VM 'Leri için Azure Image Builder 'ı kullanma](../windows/image-builder-vnet.md)
* [Mevcut bir Azure sanal ağına erişime izin veren Linux VM 'Leri için Azure Image Builder 'ı kullanma](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>Azure Özel Bağlantı nedir?

Azure özel bağlantısı, bir sanal ağdan bir hizmet olarak Azure platformu (PaaS), müşteriye ait veya Microsoft iş ortağı hizmetleri arasında özel bağlantı sağlar. Ağ mimarisini basitleştirir ve genel İnternet 'teki verileri açığa kaldırarak Azure 'daki uç noktalar arasındaki bağlantıyı korur. Daha fazla bilgi için bkz. [özel bağlantı belgeleri](../../private-link/index.yml).

### <a name="required-permissions-for-an-existing-vnet"></a>Mevcut bir VNET için gerekli izinler

Azure Image Builder, var olan bir sanal ağı kullanmak için belirli izinler gerektirir. Daha fazla bilgi için bkz. Azure [CLI kullanarak Azure Image Builder hizmet Izinlerini yapılandırma](image-builder-permissions-cli.md) veya [PowerShell kullanarak Azure Image Builder hizmeti izinleri yapılandırma](image-builder-permissions-powershell.md).

### <a name="what-is-deployed-during-an-image-build"></a>Görüntü oluşturma sırasında dağıtılan özellikler nelerdir?

Mevcut VNET 'in kullanılması, Azure Image Builder 'ın özel bağlantıya bağlı ek bir VM (proxy VM) ve bir Azure Load Balancer (ALB) dağıttığı anlamına gelir. AıB hizmetinden gelen trafik, ALB 'ye özel bağlantı üzerinden gider. ALB, Linux OS veya Windows işletim sistemi için 60000 numaralı bağlantı 60001 noktasını kullanarak proxy VM 'ye iletişim kurar. Proxy, Linux OS veya Windows işletim sistemi için 5986 numaralı bağlantı noktasını kullanarak komutları derleme VM 'sine iletir.

> [!NOTE]
> VNET, Azure görüntü Oluşturucu hizmet bölgesiyle aynı bölgede olmalıdır.
> 

### <a name="why-deploy-a-proxy-vm"></a>Proxy VM neden dağıtılır?

Ortak IP olmayan bir VM, dahili bir Load Balancer arkasında olduğunda, Internet erişimi yoktur. VNET için kullanılan Azure Load Balancer iç. Proxy VM, derlemeler sırasında derleme VM 'si için Internet erişimine izin verir. İlişkili ağ güvenlik grupları, derleme VM erişimini kısıtlamak için kullanılabilir.

Dağıtılan proxy VM boyutu, yapı VM 'ye ek olarak standart A1_v2. Proxy VM, Azure görüntü Oluşturucu hizmeti ve yapı VM arasında komut göndermek için kullanılır. Proxy VM özellikleri boyut veya işletim sistemi dahil değiştirilemez. Hem Windows hem de Linux görüntü yapıları için proxy VM özelliklerini değiştiremezsiniz.

### <a name="image-template-parameters-to-support-vnet"></a>VNET 'i desteklemek için görüntü şablonu parametreleri
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| Ayar | Açıklama |
|---------|---------|
| name | Seçim Önceden var olan bir sanal ağın adı. |
| subnetName | Belirtilen sanal ağ içindeki alt ağın adı. Yalnızca *ad* belirtilmişse belirtilmesi gerekir. |
| resourceGroupName | Belirtilen sanal ağı içeren kaynak grubunun adı. Yalnızca *ad* belirtilmişse belirtilmesi gerekir. |

Özel bağlantı hizmeti, belirtilen VNET ve alt ağdan bir IP gerektirir. Şu anda Azure bu IP 'lerde ağ Ilkelerini desteklemez. Bu nedenle, ağ ilkelerinin alt ağda devre dışı bırakılması gerekir. Daha fazla bilgi için bkz. [özel bağlantı belgeleri](../../private-link/index.yml).

### <a name="checklist-for-using-your-vnet"></a>VNET 'iniz kullanmaya yönelik denetim listesi

1. Azure Load Balancer (ALB) ile bir NSG 'deki proxy VM ile iletişim kurmasına izin ver
    * [AZ CLı örneği](image-builder-vnet.md#add-network-security-group-rule)
    * [PowerShell örneği](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. Alt ağda özel hizmet Ilkesini devre dışı bırak
    * [AZ CLı örneği](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [PowerShell örneği](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. Azure Image Builder 'ın ALB oluşturma ve sanal ağa VM ekleme izni verme
    * [AZ CLı örneği](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [PowerShell örneği](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. Azure Image Builder 'ın kaynak görüntüleri okumasına/yazmasına ve görüntü oluşturmasına izin ver
    * [AZ CLı örneği](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [PowerShell örneği](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. VNET 'i Azure görüntü Oluşturucu hizmeti bölgesiyle aynı bölgede kullandığınızdan emin olun.


## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Azure Image Builder 'a genel bakış](../image-builder-overview.md).