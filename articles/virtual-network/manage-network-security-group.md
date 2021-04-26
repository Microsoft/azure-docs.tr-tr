---
title: Azure ağ güvenlik grubu oluşturma, değiştirme veya silme
titlesuffix: Azure Virtual Network
description: Güvenlik kuralları ve bir ağ güvenlik grubu oluşturma, değiştirme veya silme hakkındaki bilgileri nerede bulacağınızı öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 82f23c1fea29e2a88dd2a67ec9c89c7bf05bfff7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783512"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Ağ güvenlik grubu oluşturma, değiştirme veya silme

Ağ güvenlik gruplarındaki güvenlik kuralları, sanal ağ alt ağları ve ağ arabirimlerini akacak ve dışı bir ağ trafiği türünü filtrelemenizi sağlar. Ağ güvenlik grupları hakkında daha fazla bilgi edinmek için bkz. [ağ güvenlik grubuna genel bakış](./network-security-groups-overview.md). Daha sonra ağ güvenlik gruplarıyla ilgili bir deneyim kazanmak için [ağ trafiğini filtrele](tutorial-filter-network-traffic.md) öğreticisini doldurun.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hesabınız yoksa, etkin abonelikle bir Azure hesabı ayarlayın. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Bu makalenin geri kalanını başlatmadan önce bu görevlerden birini doldurun:

- **Portal kullanıcıları**: [Azure Portal](https://portal.azure.com) Azure hesabınızla oturum açın.

- **PowerShell kullanıcıları**: [Azure Cloud Shell](https://shell.azure.com/powershell)komutları çalıştırın ya da bilgisayarınızdan PowerShell 'i çalıştırın. Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Azure Cloud Shell tarayıcısı sekmesinde **ortam Seç** açılan listesini bulun, ardından henüz seçili değilse **PowerShell** ' i seçin.

    PowerShell 'i yerel olarak çalıştırıyorsanız, Azure PowerShell Module sürüm 1.0.0 veya üstünü kullanın. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az.Network` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). Azure ile bağlantı oluşturmak için `Connect-AzAccount` komutunu çalıştırın.

- **Azure komut satırı arabirimi (CLI) kullanıcıları**: [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da CLI 'yı bilgisayarınızdan çalıştırın. Azure CLı 'yi yerel olarak çalıştırıyorsanız, Azure CLı sürüm 2.0.28 veya üstünü kullanın. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli). Azure ile bağlantı oluşturmak için `az login` komutunu çalıştırın.

Oturum açmak veya Azure 'a bağlanmak için kullandığınız hesap, [ağ katılımcısı rolüne](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) veya [izinlerde](#permissions)listelenen uygun eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır.

## <a name="work-with-network-security-groups"></a>Ağ güvenlik grupları ile çalışma

Bir ağ güvenlik grubu oluşturabilir, bunları [görüntüleyebilir](#view-all-network-security-groups), [ayrıntılarını görüntüleyebilir](#view-details-of-a-network-security-group), [değiştirebilir](#change-a-network-security-group)ve [silebilirsiniz](#delete-a-network-security-group) . Ayrıca ağ güvenlik grubunu bir ağ arabiriminden veya alt ağıyla [ilişkilendirebilir veya](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) ilişkisini kaldırabilirsiniz.

### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

Her bir Azure konumu ve aboneliği için kaç ağ güvenlik grubunun oluşturabileceğiniz bir sınır vardır. Daha fazla bilgi edinmek için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. [Azure portalı](https://portal.azure.com) menüsünde veya **Giriş** sayfasında **Kaynak oluştur**’u seçin.

2. Ağ **' ı** ve ardından **ağ güvenlik grubu**' nu seçin.

3. **Ağ güvenlik grubu oluştur** sayfasında, **temel bilgiler** sekmesinde, aşağıdaki ayarlar için değerleri ayarlayın:

    | Ayar | Eylem |
    | --- | --- |
    | **Abonelik** | Aboneliğinizi seçin. |
    | **Kaynak grubu** | Var olan bir kaynak grubunu seçin veya yeni bir kaynak grubu oluşturmak için **Yeni oluştur** ' u seçin. |
    | **Ad** | Kaynak grubu içinde benzersiz bir metin dizesi girin. |
    | **Bölge** | İstediğiniz konumu seçin. |

4. **Gözden geçir ve oluştur**’u seçin.

5. **Doğrulama başarılı** iletisini gördüğünüzde, **Oluştur**' u seçin.

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Tüm ağ güvenlik gruplarını görüntüle

Ağ güvenlik gruplarınızı görüntülemek için [Azure Portal](https://portal.azure.com) gidin. **Ağ güvenlik gruplarını** arayın ve seçin. Aboneliğiniz için ağ güvenlik gruplarının listesi görüntülenir.

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network NSG List](/cli/azure/network/nsg#az_network_nsg_list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Bir ağ güvenlik grubunun ayrıntılarını görüntüleme

1. Ağ güvenlik gruplarınızı görüntülemek için [Azure Portal](https://portal.azure.com) gidin. **Ağ güvenlik gruplarını** arayın ve seçin.

2. Ağ güvenlik grubunuzun adını seçin.

Ağ güvenlik grubunun menü çubuğunda, **Ayarlar**' ın altında, ağ güvenlik grubunun ilişkilendirildiği **gelen güvenlik kurallarını**, **giden güvenlik kurallarını**, **ağ arabirimlerini** ve **alt ağları** görüntüleyebilirsiniz.

**İzleme** altında **tanılama ayarlarını** etkinleştirebilir veya devre dışı bırakabilirsiniz. **Destek + sorun giderme** altında, **geçerli güvenlik kurallarını** görüntüleyebilirsiniz. Daha fazla bilgi edinmek için bkz. [bir ağ güvenlik grubu Için tanılama günlüğü](virtual-network-nsg-manage-log.md) ve [bir VM ağ trafiği Filtreleme sorununu](diagnose-network-traffic-filter-problem.md)tanılama.

Listelenen ortak Azure ayarları hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Etkinlik günlüğü](../azure-monitor/essentials/platform-logs-overview.md)
- [Erişim denetimi (IAM)](../role-based-access-control/overview.md)
- [Etiketler](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Kilitler](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Otomasyon betiği](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network NSG Show](/cli/azure/network/nsg#az_network_nsg_show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Ağ güvenlik grubunu değiştirme

1. Ağ güvenlik gruplarınızı görüntülemek için [Azure Portal](https://portal.azure.com) gidin. **Ağ güvenlik gruplarını** arayın ve seçin.

2. Değiştirmek istediğiniz ağ güvenlik grubunun adını seçin.

En yaygın değişiklikler, [bir güvenlik kuralı eklemek](#create-a-security-rule), [bir kuralı kaldırmak](#delete-a-security-rule)ve [ağ güvenlik grubunu bir alt ağ ya da ağ arabiriminden ya da bir ağ arabiriminden ilişkilendirmek ya](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)da geri eklemektir.

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network NSG Update](/cli/azure/network/nsg#az_network_nsg_update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Bir ağ güvenlik grubunu bir alt ağ veya ağ arabiriminden ilişkilendir veya ilişkisini kaldır

Bir ağ güvenlik grubunu bir ağ arabiriminden ilişkilendirmek veya bir ağ güvenlik grubunun ilişkilendirmesini kaldırmak için, bkz. ağ güvenlik grubunu bir ağ [arabiriminden ilişkilendir veya](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)bir ağ güvenlik grubunun ilişkisini kaldır. Bir ağ güvenlik grubunu bir alt ağdan ilişkilendirmek veya bir ağ güvenlik grubunun ilişkilendirmesini kaldırmak için bkz. [alt ağ ayarlarını değiştirme](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Ağ güvenlik grubunu silme

Bir ağ güvenlik grubu, herhangi bir alt ağ veya ağ arabirimi ile ilişkilendirilmişse silinemez. Bir ağ güvenlik grubunun silmeyi denemeden önce tüm alt ağlardan ve ağ arabirimlerinden ilişkilendirmesini kaldırın.

1. Ağ güvenlik gruplarınızı görüntülemek için [Azure Portal](https://portal.azure.com) gidin. **Ağ güvenlik gruplarını** arayın ve seçin.

2. Silmek istediğiniz ağ güvenlik grubunun adını seçin.

3. Ağ güvenlik grubunun araç çubuğunda **Sil**' i seçin. Onay iletişim kutusunda **Evet** ' i seçin.

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network NSG Delete](/cli/azure/network/nsg#az_network_nsg_delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Güvenlik kuralları ile çalışma

Bir ağ güvenlik grubu sıfır veya daha fazla güvenlik kuralı içerir. Bir güvenlik kuralının oluşturabilir, [hepsini görüntüleyebilir](#view-all-security-rules), [ayrıntılarını görüntüleyebilir](#view-details-of-a-security-rule), [değiştirebilir](#change-a-security-rule)ve [silebilirsiniz](#delete-a-security-rule) .

### <a name="create-a-security-rule"></a>Güvenlik kuralı oluşturma

Her bir Azure konumu ve aboneliği için ağ güvenlik grubu başına kaç kural oluşturabileceğiniz bir sınır vardır. Daha fazla bilgi edinmek için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Ağ güvenlik gruplarınızı görüntülemek için [Azure Portal](https://portal.azure.com) gidin. **Ağ güvenlik gruplarını** arayın ve seçin.

2. Güvenlik kuralı eklemek istediğiniz ağ güvenlik grubunun adını seçin.

3. Ağ güvenlik grubunun menü çubuğunda **gelen güvenlik kuralları** ' nı veya **giden güvenlik kuralları**' nı seçin.

    Bazı mevcut kurallar listelenmeyebilir, bazıları dahil değildir. Bir ağ güvenlik grubu oluşturduğunuzda, çeşitli varsayılan güvenlik kuralları oluşturulur. Daha fazla bilgi için bkz. [varsayılan güvenlik kuralları](./network-security-groups-overview.md#default-security-rules).  Varsayılan güvenlik kurallarını silemezsiniz, ancak daha yüksek önceliğe sahip kurallarla bunları geçersiz kılabilirsiniz.

4. <a name="security-rule-settings"></a>**Ekle**' yi seçin. Aşağıdaki ayarlara ilişkin değerleri seçin veya ekleyin ve ardından **Tamam**' ı seçin:

    | Ayar | Değer | Ayrıntılar |
    | ------- | ----- | ------- |
    | **Kaynak** | Aşağıdakilerden biri:<ul><li>**Tümü**</li><li>**IP adresleri**</li><li>**Hizmet etiketi** (gelen güvenlik kuralı) veya **VirtualNetwork** (giden güvenlik kuralı)</li><li>**Uygulama &nbsp; Güvenlik &nbsp; grubu**</li></ul> | <p>**IP adresleri**' ni seçerseniz, **kaynak IP adresleri/CIDR aralıklarını** de belirtmeniz gerekir.</p><p>**Hizmet etiketi**' ni seçerseniz bir **kaynak hizmeti etiketi** de seçebilirsiniz.</p><p>**Uygulama güvenlik grubu**' nu seçerseniz, var olan bir uygulama güvenlik grubunu da seçmeniz gerekir. Hem **kaynak** hem de **hedef** için **uygulama güvenlik grubu** ' nu seçerseniz, her iki uygulama güvenlik grubu içindeki ağ arabirimlerinin aynı sanal ağda olması gerekir.</p> |
    | **Kaynak IP adresleri/CıDR aralıkları** | IP adresleri ve sınıfsız etki alanları arası yönlendirme (CıDR) aralıklarının virgülle ayrılmış listesi | <p>**Kaynağı** **IP adresleri** olarak değiştirirseniz bu ayar görüntülenir. Birden çok değerin tek bir değer veya virgülle ayrılmış bir listesini belirtmeniz gerekir. Birden çok değere bir örnek vardır `10.0.0.0/16, 192.188.1.1` . Belirtebileceğiniz değer sayısı için sınırlar vardır. Daha fazla ayrıntı için bkz. [Azure Limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Belirttiğiniz IP adresi bir Azure VM 'sine atanmışsa, genel IP adresini değil, özel IP adresini belirtin. Azure, genel IP adresini gelen güvenlik kuralları için özel bir IP adresine dönüştürdükten sonra, ancak giden kuralları için bir özel IP adresini genel IP adresine çevirmadan önce güvenlik kurallarını işler. Azure 'da ortak ve özel IP adresleri hakkında daha fazla bilgi edinmek için bkz. [IP adresi türleri](./public-ip-addresses.md).</p> |
    | **Kaynak hizmeti etiketi** | Açılan listeden bir hizmet etiketi | Bu isteğe bağlı ayar, bir gelen güvenlik kuralı için **kaynak** olarak **Service Tag** ' i ayarlarsanız görüntülenir. Bir hizmet etiketi, bir IP adresleri kategorisi için önceden tanımlanmış bir tanıtıcıdır. Kullanılabilir hizmet etiketleri ve etiketlerin ne gösterdiği hakkında daha fazla bilgi edinmek için bkz. [hizmet etiketleri](./network-security-groups-overview.md#service-tags). |
    | **Kaynak uygulama güvenlik grubu** | Var olan bir uygulama güvenlik grubu | Bu ayar, **kaynağı** **uygulama güvenlik grubu** olarak ayarlarsanız görüntülenir. Ağ arabirimiyle aynı bölgede bulunan bir uygulama güvenlik grubu seçin. [Uygulama güvenlik grubu oluşturmayı](#create-an-application-security-group)öğrenin. |
    | **Kaynak bağlantı noktası aralıkları** | Aşağıdakilerden biri:<ul><li>Gibi tek bir bağlantı noktası `80`</li><li>Gibi bir bağlantı noktası aralığı `1024-65535`</li><li>Tek bağlantı noktaları ve/veya bağlantı noktası aralıklarının (örneğin,) virgülle ayrılmış listesi `80, 1024-65535`</li><li>`*`Herhangi bir bağlantı noktasında trafiğe izin vermek için yıldız işareti ()</li></ul> | Bu ayar, kuralın trafiğe izin verdiği veya erişimini engellediği bağlantı noktalarını belirtir. Belirtebileceğiniz bağlantı noktası sayısı için sınırlar vardır. Daha fazla ayrıntı için bkz. [Azure Limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Hedef** | Aşağıdakilerden biri:<ul><li>**Tümü**</li><li>**IP adresleri**</li><li>**Hizmet etiketi** (giden güvenlik kuralı) veya **VirtualNetwork** (gelen güvenlik kuralı)</li><li>**Uygulama &nbsp; Güvenlik &nbsp; grubu**</li></ul> | <p>**IP adresleri**' ni seçerseniz, **hedef IP adresleri/CIDR aralıklarını** de belirtin.</p><p>**VirtualNetwork** seçeneğini belirlerseniz, sanal ağın adres ALANıNDAKI tüm IP adreslerinin trafiğe izin verilir. **VirtualNetwork** bir hizmet etikettir.</p><p>**Uygulama güvenlik grubu**' nu seçerseniz, var olan bir uygulama güvenlik grubunu seçmeniz gerekir. [Uygulama güvenlik grubu oluşturmayı](#create-an-application-security-group)öğrenin.</p> |
    | **Hedef IP adresleri/CıDR aralıkları** | IP adreslerinin ve CıDR aralıklarının virgülle ayrılmış listesi | <p>**Hedefi** **IP adresleri** olarak değiştirirseniz bu ayar görüntülenir. **Kaynak** ve **kaynak IP adresleri/CIDR aralıklarına** benzer şekilde, tek veya birden çok adres veya Aralık belirtebilirsiniz. Belirtebileceğiniz sayı için sınırlar vardır. Daha fazla ayrıntı için bkz. [Azure Limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Belirttiğiniz IP adresi bir Azure VM 'sine atanmışsa, genel IP adresini değil, özel IP 'sini belirttiğinizden emin olun. Azure, genel IP adresini gelen güvenlik kuralları için özel bir IP adresine çevirdikten sonra güvenlik kurallarını işler, ancak Azure, giden kuralları için bir özel IP adresini genel IP adresine çevirmadan önce. Azure 'da ortak ve özel IP adresleri hakkında daha fazla bilgi edinmek için bkz. [IP adresi türleri](./public-ip-addresses.md).</p> |
    | **Hedef hizmet etiketi** | Açılan listeden bir hizmet etiketi | Giden bir güvenlik kuralı için **hedefi** **hizmet etiketi** olarak değiştirirseniz, bu isteğe bağlı ayar görüntülenir. Bir hizmet etiketi, bir IP adresleri kategorisi için önceden tanımlanmış bir tanıtıcıdır. Kullanılabilir hizmet etiketleri ve etiketlerin ne gösterdiği hakkında daha fazla bilgi edinmek için bkz. [hizmet etiketleri](./network-security-groups-overview.md#service-tags). |
    | **Hedef uygulama güvenlik grubu** | Var olan bir uygulama güvenlik grubu | **Hedefi** **uygulama güvenlik grubu** olarak ayarlarsanız bu ayar görüntülenir. Ağ arabirimiyle aynı bölgede bulunan bir uygulama güvenlik grubu seçin. [Uygulama güvenlik grubu oluşturmayı](#create-an-application-security-group)öğrenin. |
    | **Hedef bağlantı noktası aralıkları** | Aşağıdakilerden biri:<ul><li>Gibi tek bir bağlantı noktası `80`</li><li>Gibi bir bağlantı noktası aralığı `1024-65535`</li><li>Tek bağlantı noktaları ve/veya bağlantı noktası aralıklarının (örneğin,) virgülle ayrılmış listesi `80, 1024-65535`</li><li>`*`Herhangi bir bağlantı noktasında trafiğe izin vermek için yıldız işareti ()</li></ul> | **Kaynak bağlantı noktası aralıklarında** olduğu gibi, tek veya birden çok bağlantı noktası ve aralığı belirtebilirsiniz. Belirtebileceğiniz sayı için sınırlar vardır. Daha fazla ayrıntı için bkz. [Azure Limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Protokol** | **Any**, **TCP**, **UDP** veya **ICMP** | Kuralı Iletim Denetim Protokolü (TCP), Kullanıcı Datagram Protokolü (UDP) veya Internet Denetim Iletisi Protokolü (ıCMP) ile kısıtlayabilirsiniz. Kuralın tüm protokollere uygulanması için varsayılan değer varsayılandır. |
    | **Eylem** | **Izin ver** veya **Reddet** | Bu ayar, bu kuralın sağlanan kaynak ve hedef yapılandırma için erişim izni verip vermemeyeceğini belirtir. |
    | **Priority** | Ağ güvenlik grubundaki tüm güvenlik kuralları için benzersiz olan 100 ve 4096 arasında bir değer | Azure Güvenlik kurallarını öncelik sırasına göre işler. Sayı ne kadar düşükse öncelik o kadar yüksektir. 100, 200 ve 300 gibi kurallar oluştururken, öncelik numaraları arasında bir boşluk bırakmanız önerilir. Boşlukları bırakmak, daha sonra kuralların daha kolay veya daha düşük öncelikli olmasını sağlamak için daha kolay kurallar eklemenizi sağlar. |
    | **Ad** | Ağ güvenlik grubu içindeki kural için benzersiz bir ad | Ad en fazla 80 karakter olabilir. Bir harf veya sayı ile başlamalı ve bir harf, sayı veya alt çizgi ile bitmelidir. Ad yalnızca harf, sayı, alt çizgi, nokta veya kısa çizgi içerebilir. |
    | **Açıklama** | Metin açıklaması | İsteğe bağlı olarak güvenlik kuralı için bir metin açıklaması belirtebilirsiniz. Açıklama 140 karakterden daha uzun olamaz. |

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Tüm güvenlik kurallarını görüntüle

Bir ağ güvenlik grubu sıfır veya daha fazla kural içerir. Kuralları görüntülerken listelenen bilgiler hakkında daha fazla bilgi edinmek için bkz. [ağ güvenlik grubuna genel bakış](./network-security-groups-overview.md).

1. Bir ağ güvenlik grubunun kurallarını görüntülemek için [Azure Portal](https://portal.azure.com) gidin. **Ağ güvenlik gruplarını** arayın ve seçin.

2. Kurallarını görüntülemek istediğiniz ağ güvenlik grubunun adını seçin.

3. Ağ güvenlik grubunun menü çubuğunda **gelen güvenlik kuralları** ' nı veya **giden güvenlik kuralları**' nı seçin.

Liste, oluşturduğunuz kuralları ve ağ güvenlik grubunun [varsayılan güvenlik kurallarını](./network-security-groups-overview.md#default-security-rules)içerir.

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az_network_nsg_rule_list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Bir güvenlik kuralının ayrıntılarını görüntüleme

1. Bir ağ güvenlik grubunun kurallarını görüntülemek için [Azure Portal](https://portal.azure.com) gidin. **Ağ güvenlik gruplarını** arayın ve seçin.

2. Bir kuralın ayrıntılarını görüntülemek istediğiniz ağ güvenlik grubunun adını seçin.

3. Ağ güvenlik grubunun menü çubuğunda **gelen güvenlik kuralları** ' nı veya **giden güvenlik kuralları**' nı seçin.

4. Ayrıntılarını görüntülemek istediğiniz kuralı seçin. Tüm ayarların açıklaması için bkz. [güvenlik kuralı ayarları](#security-rule-settings).

    > [!NOTE]
    > Bu yordam yalnızca özel bir güvenlik kuralı için geçerlidir. Varsayılan bir güvenlik kuralı seçerseniz bu çalışmaz.

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network NSG Rule Show](/cli/azure/network/nsg/rule#az_network_nsg_rule_show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Bir güvenlik kuralını değiştirme

1. [Bir güvenlik kuralının ayrıntılarını görüntüleme](#view-details-of-a-security-rule)bölümündeki adımları uygulayın.

2. Ayarları gerektiği gibi değiştirin ve ardından **Kaydet**' i seçin. Tüm ayarların açıklaması için bkz. [güvenlik kuralı ayarları](#security-rule-settings).

    > [!NOTE]
    > Bu yordam yalnızca özel bir güvenlik kuralı için geçerlidir. Varsayılan bir güvenlik kuralını değiştirmenize izin verilmiyor.

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Bir güvenlik kuralını Sil

1. [Bir güvenlik kuralının ayrıntılarını görüntüleme](#view-details-of-a-security-rule)bölümündeki adımları uygulayın.

2. **Sil**'i ve sonra da **Evet**'i seçin.

    > [!NOTE]
    > Bu yordam yalnızca özel bir güvenlik kuralı için geçerlidir. Varsayılan bir güvenlik kuralını silme izniniz yok.

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network NSG Rule Delete](/cli/azure/network/nsg/rule#az_network_nsg_rule_delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Uygulama güvenlik gruplarıyla çalışma

Bir uygulama güvenlik grubu sıfır veya daha fazla ağ arabirimi içerir. Daha fazla bilgi için bkz. [uygulama güvenlik grupları](./network-security-groups-overview.md#application-security-groups). Bir uygulama güvenlik grubundaki tüm ağ arabirimlerinin aynı sanal ağda mevcut olması gerekir. Bir uygulama güvenlik grubuna ağ arabirimi ekleme hakkında bilgi edinmek için bkz. bir [uygulama güvenlik grubuna ağ arabirimi ekleme](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Uygulama güvenlik grubu oluşturma

1. [Azure portalı](https://portal.azure.com) menüsünde veya **Giriş** sayfasında **Kaynak oluştur**’u seçin.

2. Arama kutusuna *uygulama güvenlik grubu*' nu girin.

3. **Uygulama güvenlik grubu** sayfasında **Oluştur**' u seçin.

4. **Uygulama güvenlik grubu oluştur** sayfasında, **temel bilgiler** sekmesinde, aşağıdaki ayarlar için değerleri ayarlayın:

    | Ayar | Eylem |
    | --- | --- |
    | **Abonelik** | Aboneliğinizi seçin. |
    | **Kaynak grubu** | Var olan bir kaynak grubunu seçin veya yeni bir kaynak grubu oluşturmak için **Yeni oluştur** ' u seçin. |
    | **Ad** | Kaynak grubu içinde benzersiz bir metin dizesi girin. |
    | **Bölge** | İstediğiniz konumu seçin. |

5. **Gözden geçir ve oluştur**’u seçin.

6. **Gözden geçir + oluştur** sekmesinin altında, **doğrulama başarılı** Iletisini görbaşladıktan sonra **Oluştur**' u seçin.

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network ASG Create](/cli/azure/network/asg#az_network_asg_create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Tüm uygulama güvenlik gruplarını görüntüle

Uygulama güvenlik gruplarınızı görüntülemek için [Azure Portal](https://portal.azure.com) gidin. **Uygulama güvenlik gruplarını** arayın ve seçin. Azure portal, uygulama güvenlik gruplarınızın bir listesini görüntüler.

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network ASG List](/cli/azure/network/asg#az_network_asg_list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Belirli bir uygulama güvenlik grubunun ayrıntılarını görüntüleme

1. Bir uygulama güvenlik grubunu görüntülemek için [Azure Portal](https://portal.azure.com) gidin. **Uygulama güvenlik gruplarını** arayın ve seçin.

2. Ayrıntılarını görüntülemek istediğiniz uygulama güvenlik grubunun adını seçin.

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network ASG Show](/cli/azure/network/asg#az_network_asg_show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Uygulama güvenlik grubunu değiştirme

1. Bir uygulama güvenlik grubunu görüntülemek için [Azure Portal](https://portal.azure.com) gidin. **Uygulama güvenlik gruplarını** arayın ve seçin.

2. Değiştirmek istediğiniz uygulama güvenlik grubunun adını seçin.

3. Değiştirmek istediğiniz ayarın yanındaki **Değiştir** ' i seçin. Örneğin, **etiket** ekleyebilir veya kaldırabilir ya da **kaynak grubunu** veya **aboneliğini** değiştirebilirsiniz.

    > [!NOTE]
    > Konumu değiştiremezsiniz.

    Menü çubuğunda, **erişim denetimi (IAM)** seçeneğini de belirleyebilirsiniz. **Erişim denetimi (IAM)** sayfasında, uygulama güvenlik grubuna izinler atayabilir veya kaldırabilirsiniz.

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network ASG Update](/cli/azure/network/asg#az_network_asg_update) |
| PowerShell | PowerShell cmdlet 'i yok |

### <a name="delete-an-application-security-group"></a>Uygulama güvenlik grubunu silme

Herhangi bir ağ arabirimi içeriyorsa, bir uygulama güvenlik grubunu silemezsiniz. Tüm ağ arabirimlerini uygulama güvenlik grubundan kaldırmak için, ağ arabirimi ayarlarını değiştirin ya da ağ arabirimlerini silin. Daha fazla bilgi edinmek için bkz. [uygulama güvenlik gruplarına ekleme veya kaldırma](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) veya [ağ arabirimini silme](virtual-network-network-interface.md#delete-a-network-interface).

1. Uygulama güvenlik gruplarınızı yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Uygulama güvenlik gruplarını** arayın ve seçin.

2. Silmek istediğiniz uygulama güvenlik grubunun adını seçin.

3. **Sil**' i seçin ve ardından uygulama güvenlik grubunu silmek için **Evet** ' i seçin.

#### <a name="commands"></a>Komutlar

| Araç | Komut |
| ---- | ------- |
| Azure CLI | [az Network ASG Delete](/cli/azure/network/asg#az_network_asg_delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>İzinler

Ağ güvenlik grupları, güvenlik kuralları ve uygulama güvenlik grupları üzerinde görevler yapmak için, hesabınız [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya aşağıdaki tablolarda listelenen uygun Izinlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır:

### <a name="network-security-group"></a>Ağ güvenlik grubu

| Eylem                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft. Network/networkSecurityGroups/Read                  |   Ağ güvenlik grubunu al                                          |
| Microsoft. Network/networkSecurityGroups/Write                 |   Ağ güvenlik grubu oluştur veya güncelleştir                             |
| Microsoft. Network/networkSecurityGroups/Delete                |   Ağ güvenlik grubunu sil                                       |
| Microsoft. Network/networkSecurityGroups/JOIN/Action           |   Ağ güvenlik grubunu bir alt ağ veya ağ arabirimiyle ilişkilendir 


>[!NOTE]
> `write`Bir ağ güvenlik grubu üzerinde işlem gerçekleştirmek için abonelik hesabının, `read` izin ile birlikte kaynak grubu için en az izinlere sahip olması gerekir `Microsoft.Network/networkSecurityGroups/write` .


### <a name="network-security-group-rule"></a>Ağ güvenlik grubu kuralı

| Eylem                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft. Network/networkSecurityGroups/securityRules/Read            |   Kural al                                                            |
| Microsoft. Network/networkSecurityGroups/securityRules/Write           |   Kural oluştur veya güncelleştir                                               |
| Microsoft. Network/networkSecurityGroups/securityRules/Delete          |   Kuralı silme                                                         |

### <a name="application-security-group"></a>Uygulama güvenlik grubu

| Eylem                                                                     | Name                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft. Network/applicationSecurityGroups/Joinıp/Action     | Bir IP yapılandırmasını bir uygulama güvenlik grubuna katma|
| Microsoft. Network/applicationSecurityGroups/joinNetworkSecurityRule/Action | Bir güvenlik kuralına uygulama güvenlik grubuna ekleme    |
| Microsoft. Network/applicationSecurityGroups/Read                           | Uygulama güvenlik grubu al                        |
| Microsoft. Network/applicationSecurityGroups/Write                          | Uygulama güvenlik grubu oluşturma veya güncelleştirme           |
| Microsoft. Network/applicationSecurityGroups/Delete                         | Uygulama güvenlik grubunu silme                     |

## <a name="next-steps"></a>Sonraki adımlar

- [PowerShell](powershell-samples.md) veya [Azure CLI](cli-samples.md) örnek betikleri veya Azure [Kaynak Yöneticisi şablonları](template-samples.md) kullanarak ağ veya uygulama güvenlik grubu oluşturma
- Sanal ağlar için [Azure ilke tanımları](./policy-reference.md) oluşturma ve atama
