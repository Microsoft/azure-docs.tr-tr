---
title: VNet veri kaynakları için Azure Analysis Services yapılandırma | Microsoft Docs
description: Azure sanal ağ (VNet) üzerinde veri kaynakları için bir ağ geçidi kullanmak üzere bir Azure Analysis Services sunucusunu nasıl yapılandıracağınızı öğrenin.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572273"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Azure sanal ağı (VNet) üzerinde veri kaynakları için ağ geçidi kullanma

Bu makalede, veri kaynakları bir [Azure sanal ağı 'nda (VNet)](../virtual-network/virtual-networks-overview.md)olduğunda kullanılmak üzere Azure Analysis Services **Alwaysusegateway** Server özelliği açıklanmaktadır.

## <a name="server-access-to-vnet-data-sources"></a>VNet veri kaynaklarına sunucu erişimi

Veri kaynaklarınıza bir sanal ağ üzerinden erişiliyorsa, Azure Analysis Services sunucunuzun kendi ortamınızda, şirket içinde olduğu gibi bu veri kaynaklarına bağlanması gerekir. Bir [Şirket içi ağ geçidi](analysis-services-gateway.md)aracılığıyla tüm veri kaynaklarına erişmek için sunucuyu belirtmek üzere **Alwaysusegateway** sunucusu özelliğini yapılandırabilirsiniz. 

Azure SQL veritabanı yönetilen örnek veri kaynakları, Azure sanal ağı 'nda özel bir IP adresiyle çalışır. Örnekte ortak uç nokta etkinse, bir ağ geçidi gerekli değildir. Ortak uç nokta etkinleştirilmemişse, bir şirket içi veri ağ geçidi gerekir ve AlwaysUseGateway özelliği true olarak ayarlanmalıdır.

> [!NOTE]
> Bu özellik yalnızca şirket [Içi veri ağ geçidi](analysis-services-gateway.md) yüklenip yapılandırıldığında geçerlidir. Ağ Geçidi VNet üzerinde olabilir.

## <a name="configure-alwaysusegateway-property"></a>AlwaysUseGateway özelliğini yapılandırma

1. SSMS > Server > **özellikleri** **genel** > , **Gelişmiş (tümü) özelliklerini göster**' i seçin.
2. **Aspaas\alwaysusegateway**içinde **true**seçeneğini belirleyin.

    ![Her zaman ağ geçidi özelliğini kullan](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Ayrıca bkz.
Şirket [içi veri kaynaklarına bağlanma](analysis-services-gateway.md)   
[Şirket içi veri ağ geçidi  yükleyip yapılandırma](analysis-services-gateway-install.md)  
[Azure sanal ağı (VNET)](../virtual-network/virtual-networks-overview.md)   

