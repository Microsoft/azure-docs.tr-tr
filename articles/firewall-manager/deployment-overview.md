---
title: Azure Güvenlik Duvarı Yöneticisi önizleme dağıtımına genel bakış
description: Azure Güvenlik Duvarı Yöneticisi önizlemesi için gereken üst düzey dağıtım adımlarını öğrenin
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: df87e652d2969d4ae12e97a2b455648cf39382c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502033"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Azure Güvenlik Duvarı Yöneticisi önizleme dağıtımına genel bakış

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Güvenlik Duvarı Yöneticisi önizlemesi dağıtmanın birden çok yolu vardır, ancak aşağıdaki genel süreç önerilir.

## <a name="prerequisites"></a>Önkoşullar

> [!IMPORTANT]
> Azure Güvenlik Duvarı Yöneticisi önizlemenin `Register-AzProviderFeature` PowerShell komutu kullanılarak açıkça etkinleştirilmesi gerekir.
>PowerShell komut isteminden aşağıdaki komutları çalıştırın:
>
>```azure-powershell
>connect-azaccount
>Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
>```
>Özellik kaydının tamamlanabilmesi 30 dakika kadar sürer. > Kayıt durumunuzu denetlemek için aşağıdaki komutu çalıştırın:
>
>`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`



## <a name="general-deployment-process"></a>Genel dağıtım işlemi

1. Hub ve bağlı bileşen mimarinizi oluşturma

   - Azure Güvenlik Duvarı Yöneticisi 'Ni kullanarak güvenli bir sanal hub oluşturun ve sanal ağ bağlantıları ekleyin.<br>*veya*<br>
   - Sanal bir WAN hub 'ı oluşturun ve sanal ağ bağlantıları ekleyin.
2. Güvenlik sağlayıcıları seçin

   - Güvenli bir sanal hub oluşturulurken bitti.<br>*veya*<br>
   - Mevcut bir sanal WAN hub 'ını güvenli sanal hub 'a dönüştürün.
3. Bir güvenlik duvarı ilkesi oluşturup hub 'ınız ile ilişkilendirin

   - Yalnızca Azure Güvenlik Duvarı kullanılıyorsa geçerlidir.
   - Hizmet olarak üçüncü taraf güvenlik (SECaaS) ilkeleri, iş ortakları yönetim deneyimi aracılığıyla yapılandırılır.
4. Trafiği güvenli hub 'ınıza yönlendirmek için yol ayarlarını yapılandırma

   - Güvenli sanal hub yolu ayarı sayfasını kullanarak, bağlı olan sanal ağlarda Kullanıcı tanımlı yollar (UDR) olmadan filtreleme ve günlüğe kaydetme için trafiği güvenli bir şekilde kolayca yönlendirin.

> [!NOTE]
> - Her bölge için sanal WAN başına birden fazla hub 'ınız olamaz. Ancak bunu elde etmek için bölgeye birden çok sanal WAN ekleyebilirsiniz.
> - VWAN 'da hub 'lar için çakışan IP alanları olamaz.
> - Hub VNet bağlantılarınızın hub ile aynı bölgede olması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Azure portal kullanarak bulut ağınızı Azure Güvenlik Duvarı Yöneticisi önizleme ile koruyun](secure-cloud-network.md)