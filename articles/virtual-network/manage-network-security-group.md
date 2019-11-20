---
title: Azure ağ güvenlik grubu oluşturma, değiştirme veya silme
titlesuffix: Azure Virtual Network
description: Ağ güvenlik grubu oluşturmayı, değiştirmeyi veya silmeyi öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: kumud
ms.openlocfilehash: 1c00f23570c3f8d80e39f3fe3901f866e40dc2ea
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305675"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Ağ güvenlik grubu oluşturma, değiştirme veya silme

Ağ güvenlik gruplarındaki güvenlik kuralları, sanal ağ alt ağları ve ağ arabirimlerini akacak ve dışı bir ağ trafiği türünü filtrelemenizi sağlar. Ağ güvenlik grupları hakkında bilginiz yoksa, bu konuda daha fazla bilgi edinmek için [ağ güvenlik grubuna genel bakış](security-overview.md) ' a bakın ve ağ güvenlik gruplarıyla ilgili bir deneyim kazanmak için ağ [trafiğini filtrele](tutorial-filter-network-traffic.md) öğreticisini doldurun.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu makalenin herhangi bir bölümündeki adımları tamamlamadan önce aşağıdaki görevleri doldurun:

- Henüz bir Azure hesabınız yoksa [ücretsiz deneme hesabı](https://azure.microsoft.com/free)için kaydolun.
- Portalı kullanıyorsanız, açın https://portal.azure.com ve Azure hesabınızla oturum açın.
- Bu makaledeki görevleri tamamlamaya yönelik PowerShell komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/powershell)komutları çalıştırın veya PowerShell 'i bilgisayarınızdan çalıştırarak çalıştırın. Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.
- Bu makaledeki görevleri gerçekleştirmek için Azure komut satırı arabirimi (CLı) komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da bilgisayarınızdan CLI 'yı çalıştırarak. Bu öğretici, Azure CLı sürüm 2.0.28 veya üstünü gerektirir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](/cli/azure/install-azure-cli). Azure CLI 'yi yerel olarak çalıştırıyorsanız, Azure ile bir bağlantı oluşturmak için çalıştırmanız `az login` da gerekir.

Oturum açmak veya Azure 'a bağlanmak için kullandığınız hesap, [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya [izinlerde](#permissions)listelenen uygun eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır.

## <a name="work-with-network-security-groups"></a>Ağ güvenlik grupları ile çalışma

Bir ağ güvenlik grubu oluşturabilir, bunları [görüntüleyebilir](#view-all-network-security-groups), [ayrıntılarını görüntüleyebilir](#view-details-of-a-network-security-group), [değiştirebilir](#change-a-network-security-group)ve [silebilirsiniz](#delete-a-network-security-group) . Ayrıca ağ güvenlik grubunu bir ağ arabiriminden veya alt ağıyla [ilişkilendirebilir veya](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) ilişkisini kaldırabilirsiniz.

### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

Azure konumu ve aboneliği başına kaç ağ güvenlik grubunun oluşturabileceğiniz bir sınır vardır. Ayrıntılar için [Azure limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) makalesini inceleyin.

1. Portalın sol üst köşesinde **+ kaynak oluştur**' u seçin.
2. Ağ **' ı**ve ardından **ağ güvenlik grubu**' nu seçin.
3. Ağ güvenlik grubu için bir **ad** girin, **aboneliğinizi**seçin, yeni bir **kaynak grubu**oluşturun veya var olan bir kaynak grubunu seçin, bir **konum**seçin ve ardından **Oluştur**' u seçin.

**Komut**

- Azure CLı: [az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Tüm ağ güvenlik gruplarını görüntüle

Portalın üst kısmındaki arama kutusuna *ağ güvenlik grupları*' nı girin. Arama sonuçlarında **ağ güvenlik grupları** görüntülendiğinde, bunu seçin. Aboneliğinizde mevcut olan ağ güvenlik grupları listelenir.

**Komut**

- Azure CLı: [az Network NSG List](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Bir ağ güvenlik grubunun ayrıntılarını görüntüleme

1. Portalın üst kısmındaki arama kutusuna *ağ güvenlik grupları*' nı girin. Arama sonuçlarında **ağ güvenlik grupları** görüntülendiğinde, bunu seçin.
2. Ayrıntılarını görüntülemek istediğiniz listede ağ güvenlik grubunu seçin. **Ayarlar** altında, ağ güvenlik grubunun ilişkilendirildiği **ağ arabirimlerini** ve **alt ağları** **gelen güvenlik kurallarını** ve **giden güvenlik kurallarını**görüntüleyebilirsiniz. Ayrıca **tanılama günlüklerini** etkinleştirebilir veya devre dışı bırakabilir ve **geçerli güvenlik kurallarını**görüntüleyebilirsiniz. Daha fazla bilgi edinmek için bkz. [tanılama günlükleri](virtual-network-nsg-manage-log.md) ve [etkin güvenlik kurallarını görüntüleme](diagnose-network-traffic-filter-problem.md).
3. Listelenen ortak Azure ayarları hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
    *   [Etkinlik Günlüğü](../azure-monitor/platform/activity-logs-overview.md)
    *   [Erişim denetimi (ıAM)](../role-based-access-control/overview.md)
    *   [Etiketler](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Kaynaktaki](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Otomasyon betiği](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Komut**

- Azure CLı: [az Network NSG Show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Ağ güvenlik grubunu değiştirme

1. Portalın üst kısmındaki arama kutusuna *ağ güvenlik grupları* ' nı arama kutusuna girin. Arama sonuçlarında **ağ güvenlik grupları** görüntülendiğinde, bunu seçin.
2. Değiştirmek istediğiniz ağ güvenlik grubunu seçin. En yaygın değişiklikler güvenlik kuralları [ekliyor](#create-a-security-rule) veya [kaldırıyor](#delete-a-security-rule) ve [bir ağ güvenlik grubunun bir alt ağ veya ağ arabiriminden ya da bir ağ arabirimine ilişkilendirilmesi ya](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)da ilişkisini kaldırır.

**Komut**

- Azure CLı: [az Network NSG Update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Bir ağ güvenlik grubunu bir alt ağ veya ağ arabiriminden ilişkilendir veya ilişkisini kaldır

Bir ağ güvenlik grubunu bir ağ arabiriminden ilişkilendirmek veya bir ağ güvenlik grubunun ilişkilendirmesini kaldırmak için, bkz. ağ güvenlik grubunu bir ağ [arabiriminden ilişkilendir veya](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)bir ağ güvenlik grubunun ilişkisini kaldır. Bir ağ güvenlik grubunu bir alt ağdan ilişkilendirmek veya bir ağ güvenlik grubunun ilişkilendirmesini kaldırmak için bkz. [alt ağ ayarlarını değiştirme](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Ağ güvenlik grubunu silme

Bir ağ güvenlik grubu, herhangi bir alt ağ veya ağ arabirimi ile ilişkilendirilmişse silinemez. Bir ağ güvenlik grubunun silmeyi denemeden önce tüm alt ağlardan ve ağ arabirimlerinden ilişkilendirmesini kaldırın.

1. Portalın üst kısmındaki arama kutusuna *ağ güvenlik grupları* ' nı arama kutusuna girin. Arama sonuçlarında **ağ güvenlik grupları** görüntülendiğinde, bunu seçin.
2. Listeden silmek istediğiniz ağ güvenlik grubunu seçin.
3. **Sil**' i seçin ve ardından **Evet**' i seçin.

**Komut**

- Azure CLı: [az Network NSG Delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>Güvenlik kuralları ile çalışma

Bir ağ güvenlik grubu sıfır veya daha fazla güvenlik kuralı içerir. Bir güvenlik kuralının oluşturabilir, [hepsini görüntüleyebilir](#view-all-security-rules), [ayrıntılarını görüntüleyebilir](#view-details-of-a-security-rule), [değiştirebilir](#change-a-security-rule)ve [silebilirsiniz](#delete-a-security-rule) .

### <a name="create-a-security-rule"></a>Güvenlik kuralı oluşturma

Her Azure konumu ve aboneliği için ağ güvenlik grubu başına kural oluşturma sınırının bir sınırı vardır. Ayrıntılar için [Azure limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) makalesini inceleyin.

1. Portalın üst kısmındaki arama kutusuna *ağ güvenlik grupları* ' nı arama kutusuna girin. Arama sonuçlarında **ağ güvenlik grupları** görüntülendiğinde, bunu seçin.
2. Güvenlik kuralı eklemek istediğiniz listeden ağ güvenlik grubunu seçin.
3. **Ayarlar**altında **gelen güvenlik kuralları** ' nı seçin. Var olan çeşitli kurallar listelenmiştir. Eklenmeyebilirsiniz bazı kurallar. Bir ağ güvenlik grubu oluşturulduğunda, içinde birkaç varsayılan güvenlik kuralı oluşturulur. Daha fazla bilgi için bkz. [varsayılan güvenlik kuralları](security-overview.md#default-security-rules).  Varsayılan güvenlik kurallarını silemezsiniz, ancak daha yüksek önceliğe sahip kurallarla bunları geçersiz kılabilirsiniz.
4. <a name = "security-rule-settings"></a> **+ Ekle**' yi seçin.  Aşağıdaki ayarlara ilişkin değerleri seçin veya ekleyin ve ardından **Tamam**' ı seçin:
    
    |Ayar  |Value  |Ayrıntılar  |
    |---------|---------|---------|
    |Source     | Gelen güvenlik kuralları için **herhangi bir** **uygulama güvenlik grubu**, **IP adresi**veya **hizmet etiketi** seçin. Giden güvenlik kuralı oluşturuyorsanız, Seçenekler **hedef**için listelenen seçeneklerle aynıdır.       | **Uygulama güvenlik grubu**' nu seçerseniz, ağ arabirimiyle aynı bölgede bulunan bir veya daha fazla var olan uygulama güvenlik grubunu seçin. [Uygulama güvenlik grubu oluşturmayı](#create-an-application-security-group)öğrenin. Hem **kaynak** hem de **hedef**için **uygulama güvenlik grubu** ' nu seçerseniz, her iki uygulama güvenlik grubu içindeki ağ arabirimlerinin aynı sanal ağda olması gerekir. **IP adresleri**' ni SEÇIP **kaynak IP adresleri/CIDR aralıklarını**belirtin. Birden çok değerin tek bir değer veya virgülle ayrılmış bir listesini belirtebilirsiniz. Birden çok değere örnek 10.0.0.0/16, 192.188.1.1. Belirtebileceğiniz değer sayısı için sınırlar vardır. Ayrıntılar için bkz. [Azure Limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) . **Service Tag**' i seçerseniz, bir hizmet etiketi seçin. Bir hizmet etiketi, bir IP adresleri kategorisi için önceden tanımlanmış bir tanıtıcıdır. Kullanılabilir hizmet etiketleri ve etiketlerin ne gösterdiği hakkında daha fazla bilgi edinmek için bkz. [hizmet etiketleri](security-overview.md#service-tags). Belirttiğiniz IP adresi bir Azure sanal makinesine atanmışsa, sanal makineye atanan genel IP adresini değil, özel IP 'yi belirttiğinizden emin olun. Güvenlik kuralları, Azure genel IP adresini gelen güvenlik kuralları için özel bir IP adresine çevirdikten sonra ve Azure bir özel IP adresini giden kuralları için genel bir IP adresine dönüştürdüğünde işlenir. Azure 'da ortak ve özel IP adresleri hakkında daha fazla bilgi edinmek için bkz. [IP adresi türleri](virtual-network-ip-addresses-overview-arm.md).        |
    |Source port ranges     | 80 gibi tek bir bağlantı noktası, 1024-65535 gibi bir bağlantı noktası aralığı veya tek bağlantı noktaları ve/veya bağlantı noktası aralıklarının virgülle ayrılmış bir listesini (örneğin, 80, 1024-65535) belirtin. Herhangi bir bağlantı noktasında trafiğe izin vermek için bir yıldız işareti girin. | Bağlantı noktaları ve aralıklar, kural tarafından hangi bağlantı noktası trafiğinin izin verildiğini veya reddedildiğini belirtir. Belirtebileceğiniz bağlantı noktası sayısı için sınırlar vardır. Ayrıntılar için bkz. [Azure Limitleri](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .  |
    |Hedef     | Giden güvenlik kuralları için **herhangi bir** **uygulama güvenlik grubu**, **IP adresi**veya **sanal ağ** seçin. Bir gelen güvenlik kuralı oluşturuyorsanız, Seçenekler **kaynak**için listelenen seçeneklerle aynıdır.        | **Uygulama güvenlik grubu** ' nu seçerseniz, ağ arabirimiyle aynı bölgede bulunan bir veya daha fazla var olan uygulama güvenlik grubunu seçmeniz gerekir. [Uygulama güvenlik grubu oluşturmayı](#create-an-application-security-group)öğrenin. **Uygulama güvenlik grubu**' nu seçerseniz, ağ arabirimiyle aynı bölgede bulunan mevcut bir uygulama güvenlik grubunu seçin. **IP adresleri**' ni SEÇIP **hedef IP adresleri/CIDR aralıklarını**belirtin. **Kaynak** ve **kaynak IP adresleri/CIDR aralıklarına**benzer şekilde, tek veya birden çok adres veya Aralık belirtebilirsiniz ve belirtebileceğiniz sayı için sınırlar vardır. Bir hizmet etiketi olan **sanal ağ**' ı seçtiğinizde, trafiğe sanal ağın adres ALANıNDAKI tüm IP adreslerinin izin verdiği anlamına gelir. Belirttiğiniz IP adresi bir Azure sanal makinesine atanmışsa, sanal makineye atanan genel IP adresini değil, özel IP 'yi belirttiğinizden emin olun. Güvenlik kuralları, Azure genel IP adresini gelen güvenlik kuralları için özel bir IP adresine çevirdikten sonra ve Azure bir özel IP adresini giden kuralları için genel bir IP adresine dönüştürdüğünde işlenir. Azure 'da ortak ve özel IP adresleri hakkında daha fazla bilgi edinmek için bkz. [IP adresi türleri](virtual-network-ip-addresses-overview-arm.md).        |
    |Hedef bağlantı noktası aralıkları     | Tek bir değer veya virgülle ayrılmış bir değerler listesi belirtin. | **Kaynak bağlantı noktası aralıklarına**benzer şekilde, tek veya birden çok bağlantı noktası ve Aralık belirtebilirsiniz ve belirtebileceğiniz sayı için sınırlar vardır. |
    |Protocol     | **Herhangi bir** **TCP**, **UDP** veya **ICMP**seçin.        |         |
    |Action     | **Izin ver** veya **Reddet**' i seçin.        |         |
    |Priority     | Ağ güvenlik grubu içindeki tüm güvenlik kuralları için benzersiz olan 100-4096 arasında bir değer girin. |Kurallar öncelik sırasına göre işlenir. Sayı ne kadar düşükse öncelik o kadar yüksektir. Kuralları oluştururken 100, 200, 300 gibi öncelik sayıları arasında bir boşluk bırakmanız önerilir. Boşlukları bırakmak, daha sonra mevcut kurallardan daha yüksek veya daha düşük bir hale getirmeniz gerekebilecek kurallar eklemenizi kolaylaştırır.         |
    |Ad     | Ağ güvenlik grubu içindeki kural için benzersiz bir ad.        |  Ad en fazla 80 karakter olabilir. Bir harf veya sayı ile başlamalı, harf, sayı veya alt çizgi ile bitmelidir ve yalnızca harf, sayı, alt çizgi, nokta veya kısa çizgi içerebilir.       |
    |Açıklama     | İsteğe bağlı bir açıklama.        |         |

**Komut**

- Azure CLı: [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Tüm güvenlik kurallarını görüntüle

Bir ağ güvenlik grubu sıfır veya birden çok kural içerir. Kuralları görüntülerken listelenen bilgiler hakkında daha fazla bilgi edinmek için bkz. [ağ güvenlik grubuna genel bakış](security-overview.md).

1. Portalın üst kısmındaki arama kutusuna *ağ güvenlik grupları*' nı girin. Arama sonuçlarında **ağ güvenlik grupları** görüntülendiğinde, bunu seçin.
2. Kurallarını görüntülemek istediğiniz listeden ağ güvenlik grubunu seçin.
3. **Ayarlar**altında **gelen güvenlik kuralları** veya **giden güvenlik kuralları** ' nı seçin.

Liste, oluşturduğunuz kuralları ve ağ güvenlik grubu [varsayılan güvenlik kurallarını](security-overview.md#default-security-rules)içerir.

**Komut**

- Azure CLı: [az Network NSG Rule List](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Bir güvenlik kuralının ayrıntılarını görüntüleme

1. Portalın üst kısmındaki arama kutusuna *ağ güvenlik grupları*' nı girin. Arama sonuçlarında **ağ güvenlik grupları** görüntülendiğinde, bunu seçin.
2. İçin bir güvenlik kuralının ayrıntılarını görüntülemek istediğiniz ağ güvenlik grubunu seçin.
3. **Ayarlar**altında **gelen güvenlik kuralları** veya **giden güvenlik kuralları** ' nı seçin.
4. Ayrıntılarını görüntülemek istediğiniz kuralı seçin. Tüm ayarların ayrıntılı bir açıklaması için bkz. [güvenlik kuralı ayarları](#security-rule-settings).

**Komut**

- Azure CLı: [az Network NSG Rule Show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Bir güvenlik kuralını değiştirme

1. [Bir güvenlik kuralının ayrıntılarını görüntüleme](#view-details-of-a-security-rule)bölümündeki adımları uygulayın.
2. Ayarları istediğiniz şekilde değiştirin ve ardından **Kaydet**' i seçin. Tüm ayarların ayrıntılı bir açıklaması için bkz. [güvenlik kuralı ayarları](#security-rule-settings).

**Komut**

- Azure CLı: [az Network NSG Rule Update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Bir güvenlik kuralını Sil

1. [Bir güvenlik kuralının ayrıntılarını görüntüleme](#view-details-of-a-security-rule)bölümündeki adımları uygulayın.
2. **Sil**' i seçin ve ardından **Evet**' i seçin.

**Komut**

- Azure CLı: [az Network NSG Rule Delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Uygulama güvenlik gruplarıyla çalışma

Bir uygulama güvenlik grubu sıfır veya daha fazla ağ arabirimi içerir. Daha fazla bilgi için bkz. [uygulama güvenlik grupları](security-overview.md#application-security-groups). Bir uygulama güvenlik grubundaki tüm ağ arabirimlerinin aynı sanal ağda mevcut olması gerekir. Bir uygulama güvenlik grubuna ağ arabirimi ekleme hakkında bilgi edinmek için bkz. bir [uygulama güvenlik grubuna ağ arabirimi ekleme](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Uygulama güvenlik grubu oluşturma

1. Azure portalının sol üst köşesinde bulunan **+ Kaynak oluştur** seçeneğini belirleyin.
2. **Market içinde ara** kutusuna *Uygulama güvenlik grubu* girin. Arama sonuçlarında **Uygulama güvenlik grubu** gösterildiğinde bunu seçin, **Her şey**'in altında yeniden **Uygulama güvenlik grubu**'nu seçin ve sonra da **Oluştur**'u seçin.
3. Aşağıdaki bilgileri girin veya seçin ve sonra **Oluştur**’u seçin:

    | Ayar        | Value                                                   |
    | ---            | ---                                                     |
    | Ad           | Ad, kaynak grubu içinde benzersiz olmalıdır.        |
    | Subscription   | Aboneliğinizi seçin.                               |
    | Resource group | Var olan bir kaynak grubunu seçin veya yeni bir tane oluşturun. |
    | Location       | Bir konum seçin                                       |

**Komut**

- Azure CLı: [az Network ASG Create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Tüm uygulama güvenlik gruplarını görüntüle

1. Azure portal sol üst köşedeki **tüm hizmetler** ' i seçin.
2. **Tüm hizmetler filtre** kutusuna *uygulama güvenlik grupları* ' nı girin ve arama sonuçlarında göründüğünde **uygulama güvenlik grupları** ' nı seçin.

**Komut**

- Azure CLı: [az Network ASG List](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Belirli bir uygulama güvenlik grubunun ayrıntılarını görüntüleme

1. Azure portal sol üst köşedeki **tüm hizmetler** ' i seçin.
2. **Tüm hizmetler filtre** kutusuna *uygulama güvenlik grupları* ' nı girin ve arama sonuçlarında göründüğünde **uygulama güvenlik grupları** ' nı seçin.
3. Ayrıntılarını görüntülemek istediğiniz uygulama güvenlik grubunu seçin.

**Komut**

- Azure CLı: [az Network ASG Show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Uygulama güvenlik grubunu değiştirme

1. Azure portal sol üst köşedeki **tüm hizmetler** ' i seçin.
2. **Tüm hizmetler filtre** kutusuna *uygulama güvenlik grupları* ' nı girin ve arama sonuçlarında göründüğünde **uygulama güvenlik grupları** ' nı seçin.
3. Ayarlarını değiştirmek istediğiniz uygulama güvenlik grubunu seçin. Etiketler ekleyebilir veya kaldırabilir ya da uygulama güvenlik grubuna izinler atayabilir veya kaldırabilirsiniz.

- Azure CLı: [az Network ASG Update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: PowerShell cmdlet 'i yok.

### <a name="delete-an-application-security-group"></a>Uygulama güvenlik grubunu silme

Bir uygulama güvenlik grubunu, içinde herhangi bir ağ arabirimi varsa silemezsiniz. Ağ arabirimi ayarlarını değiştirerek ya da ağ arabirimlerini silerek tüm ağ arabirimlerini uygulama güvenlik grubundan kaldırın. Ayrıntılar için bkz. [uygulama güvenlik gruplarından bir ağ arabirimi ekleme veya kaldırma](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) veya [ağ arabirimini silme](virtual-network-network-interface.md#delete-a-network-interface).

1. Azure portal sol üst köşedeki **tüm hizmetler** ' i seçin.
2. **Tüm hizmetler filtre** kutusuna *uygulama güvenlik grupları* ' nı girin ve arama sonuçlarında göründüğünde **uygulama güvenlik grupları** ' nı seçin.
3. Silmek istediğiniz uygulama güvenlik grubunu seçin.
4. **Sil**' i seçin ve ardından uygulama güvenlik grubunu silmek için **Evet** ' i seçin.

**Komut**

- Azure CLı: [az Network ASG Delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>İzinler

Ağ güvenlik gruplarında, güvenlik kurallarında ve uygulama güvenlik gruplarında görevler gerçekleştirmek için, hesabınız [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya aşağıda listelenen uygun izinlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır takvimleri

### <a name="network-security-group"></a>Ağ güvenlik grubu

| Action                                                        |   Ad                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Ağ güvenlik grubunu al                                          |
| Microsoft. Network/networkSecurityGroups/Write                 |   Ağ güvenlik grubu oluştur veya güncelleştir                             |
| Microsoft. Network/networkSecurityGroups/Delete                |   Ağ güvenlik grubunu sil                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Ağ güvenlik grubunu bir alt ağ veya ağ arabirimiyle ilişkilendir 


### <a name="network-security-group-rule"></a>Ağ güvenlik grubu kuralı

| Action                                                        |   Ad                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft. Network/networkSecurityGroups/Rules/Read            |   Kural al                                                            |
| Microsoft. Network/networkSecurityGroups/Rules/Write           |   Kural oluştur veya güncelleştir                                               |
| Microsoft. Network/networkSecurityGroups/Rules/Delete          |   Kuralı Sil                                                         |

### <a name="application-security-group"></a>Uygulama güvenlik grubu

| Action                                                                     | Ad                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft. Network/applicationSecurityGroups/Joinıp/Action     | Bir IP yapılandırmasını bir uygulama güvenlik grubuna katma|
| Microsoft. Network/applicationSecurityGroups/joinNetworkSecurityRule/Action | Bir güvenlik kuralına uygulama güvenlik grubuna ekleme    |
| Microsoft. Network/applicationSecurityGroups/Read                           | Uygulama güvenlik grubu al                        |
| Microsoft. Network/applicationSecurityGroups/Write                          | Uygulama güvenlik grubu oluşturma veya güncelleştirme           |
| Microsoft. Network/applicationSecurityGroups/Delete                         | Uygulama güvenlik grubunu silme                     |

## <a name="next-steps"></a>Sonraki adımlar

- [PowerShell](powershell-samples.md) veya [Azure CLI](cli-samples.md) örnek betikleri kullanarak veya Azure [Kaynak Yöneticisi şablonlarını](template-samples.md) kullanarak bir ağ veya uygulama güvenlik grubu oluşturun
- Sanal ağlar için [Azure ilkesi](policy-samples.md) oluşturma ve uygulama
