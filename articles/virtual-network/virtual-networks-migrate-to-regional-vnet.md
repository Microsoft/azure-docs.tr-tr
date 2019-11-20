---
title: Bir Azure sanal ağını (klasik) bir benzeşim grubundan bölgeye geçirme | Microsoft Docs
description: Bir benzeşim grubundan bir sanal ağı (klasik) bir bölgeye geçirmeyi öğrenin.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: d33d9ec4eadeaa3a082103f1ad699e2fc3010e3b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058389"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Bir benzeşim grubundan bir sanal ağı (klasik) bir bölgeye geçirme

> [!IMPORTANT]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Bu makale klasik dağıtım modelini incelemektedir. Microsoft, en yeni dağıtımların Kaynak Yöneticisi dağıtım modelini kullanmasını önerir.

Benzeşim grupları aynı benzeşim grubunda oluşturulan kaynakların, birlikte yakın sunucular tarafından fiziksel olarak barındırıldığından ve bu kaynakların daha hızlı iletişim kurmasını sağlar. Geçmişte, benzeşim grupları sanal ağlar (klasik) oluşturmak için bir gereksinimdi. Bu sırada, yönetilen sanal ağların (klasik) Ağ Yöneticisi hizmeti yalnızca bir fiziksel sunucu kümesi veya ölçek birimi içinde çalışabilir. Mimari iyileştirmeler ağ yönetiminin kapsamını bir bölgeye artmıştır.

Bu mimari geliştirmelerin sonucu olarak, benzeşim grupları artık önerilmez veya sanal ağlar (klasik) için gerekli değildir. Sanal ağlar (klasik) için benzeşim gruplarının kullanımı, bölgelere göre değiştirilmiştir. Bölgelerle ilişkili sanal ağlar (klasik) bölgesel sanal ağlar olarak adlandırılır.

Genel olarak benzeşim grupları kullanmanızı öneririz. Sanal ağ gereksiniminden farklı olarak, benzeşim grupları ayrıca, bilgi işlem (klasik) ve depolama (klasik) gibi kaynakların birbirini yakın şekilde yerleştirildiğinden emin olmak için de önemlidir. Ancak, geçerli Azure ağ mimarisi ile bu yerleştirme gereksinimleri artık gerekli değildir.

> [!IMPORTANT]
> Bir benzeşim grubuyla ilişkili bir sanal ağ oluşturmak yine teknik olarak mümkün olsa da, bunu yapmak için etkileyici bir neden yoktur. Ağ güvenlik grupları gibi birçok sanal ağ özelliği yalnızca bölgesel bir sanal ağ kullanılırken kullanılabilir ve benzeşim gruplarıyla ilişkili sanal ağlarda kullanılamaz.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Ağ yapılandırma dosyasını düzenleme

1. Ağ yapılandırma dosyasını dışarı aktarın. PowerShell veya Azure komut satırı arabirimi (CLı) 1,0 kullanarak bir ağ yapılandırma dosyasını dışarı aktarmayı öğrenmek için bkz. bir [ağ yapılandırma dosyası kullanarak sanal ağ](virtual-networks-using-network-configuration-file.md#export)yapılandırma.
2. **Affinitygroup** 'u **konum**ile değiştirerek ağ yapılandırma dosyasını düzenleyin. **Konum**Için bir Azure [bölgesi](https://azure.microsoft.com/regions) belirtirsiniz.
   
   > [!NOTE]
   > **Konum** , sanal ağınızla (klasik) ilişkili benzeşim grubu için belirttiğiniz bölgedir. Örneğin, sanal ağınız (klasik) Batı ABD ' de bulunan bir benzeşim grubuyla ilişkiliyse, **konumunuz** Batı ABD işaret etmelidir. 
   > 
   > 
   
    Ağ yapılandırma dosyanızda, değerleri kendi değerlerinizle değiştirerek aşağıdaki satırları düzenleyin: 
   
    **Eski değer:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **Yeni değer:** \<VirtualNetworkSitename = "Vnetusbatı" location = "Batı ABD"\>
3. Değişikliklerinizi kaydedin ve ağ yapılandırmasını Azure 'a [aktarın](virtual-networks-using-network-configuration-file.md#import) .

> [!NOTE]
> Bu geçiş, hizmetlerinize kesinti yapılmasına neden olmaz.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Benzeşim grubunda VM (klasik) varsa Yapılacaklar
Şu anda benzeşim grubundaki VM 'Lerin (klasik) benzeşim grubundan kaldırılması gerekmez. VM dağıtıldıktan sonra tek bir ölçek birimine dağıtılır. Benzeşim grupları, yeni bir VM dağıtımı için kullanılabilir VM boyutları kümesini kısıtlayabilir, ancak dağıtılan mevcut VM 'ler, VM 'nin dağıtıldığı ölçek biriminde bulunan VM boyutları kümesiyle zaten kısıtlıdır. VM zaten bir ölçek birimine dağıtıldığından, bir VM 'nin bir benzeşim grubundan kaldırılması VM üzerinde hiçbir etkiye sahip değildir.
