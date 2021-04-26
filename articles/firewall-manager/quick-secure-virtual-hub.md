---
title: "Hızlı başlangıç: Azure Güvenlik Duvarı Yöneticisi 'Ni Kaynak Yöneticisi şablonu kullanarak güvenli sanal hub"
description: Bu hızlı başlangıçta, Azure Güvenlik Duvarı Yöneticisi 'Ni kullanarak sanal hub 'ınızı güvenli hale getirme hakkında bilgi edineceksiniz.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 08/28/2020
ms.topic: quickstart
ms.service: firewall-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 88b961f269c89a1e19c0fd4419ffdc33706423de
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529657"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---arm-template"></a>Hızlı başlangıç: Azure Güvenlik Duvarı Yöneticisi-ARM şablonunu kullanarak sanal hub 'ınızı güvenli hale getirme

Bu hızlı başlangıçta, Azure Güvenlik Duvarı Yöneticisi 'Ni kullanarak sanal hub 'ınızı güvenli hale getirmek için bir Azure Resource Manager şablonu (ARM şablonu) kullanacaksınız. Dağıtılan güvenlik duvarında bağlantılara izin veren bir uygulama kuralı vardır `www.microsoft.com` . Güvenlik duvarını test etmek için iki Windows Server 2019 sanal makine dağıtılır. Bir geçiş sunucusu, iş yükü sunucusuna bağlanmak için kullanılır. İş yükü sunucusundan yalnızca ' a bağlanabilirsiniz `www.microsoft.com` .

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Azure Güvenlik Duvarı Yöneticisi hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Yöneticisi nedir?](overview.md).

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu şablon, senaryoyu desteklemek için gerekli kaynaklarla birlikte Azure Güvenlik Duvarı Yöneticisi 'ni kullanarak güvenli bir sanal hub oluşturur.

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/fwm-docs-qs/) alınmıştır.

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json":::

Şablonda birden çok Azure kaynağı tanımlanmış:

- [**Microsoft. Network/Virtualwan 'Lar**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft. Network/Virtualhub 'Ları**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft. Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/NetworkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/Publicıpaddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>Şablonu dağıtma

ARM şablonunu Azure 'a dağıtma:

1. Azure 'da oturum açmak için **Azure 'A dağıt** ' ı seçin ve şablonu açın. Şablon bir Azure Güvenlik Duvarı, sanal bir WAN ve sanal hub, ağ altyapısı ve iki sanal makine oluşturur.

   [![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. Portalda, **güvenli sanal hub 'lar** sayfasında, aşağıdaki değerleri yazın veya seçin:
   - Abonelik: mevcut abonelikler arasından seçim yapın 
   - Kaynak grubu: mevcut kaynak gruplarından seçim yapın veya **Yeni oluştur**' u seçin ve **Tamam**' ı seçin.
   - Konum: bir konum seçin
   - Yönetici Kullanıcı adı: yönetici kullanıcı hesabı için Kullanıcı adı yazın 
   - Yönetici parolası: yönetici parolasını veya anahtarını yazın

3. **Gözden Geçir + oluştur**’u ve sonra da **Oluştur**’u seçin. Dağıtımın tamamlanması 10 dakika veya daha uzun sürebilir.

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Şimdi, beklendiği gibi çalıştığını doğrulamak için güvenlik duvarı kurallarını test edin.

1. Azure portal, **Iş yükü-SRV** sanal makinesi için ağ ayarlarını gözden geçirin ve özel IP adresini aklınızda edin.
2. Uzak bir masaüstünü, **Atsrv** sanal makinesine bağlayın ve oturum açın. Buradan, **Iş yükü-SRV** özel IP adresine bir Uzak Masaüstü bağlantısı açın.

3. Internet Explorer'ı açın ve `www.microsoft.com` adresine gidin.
4.   >  Internet Explorer güvenlik uyarıları 'nda Tamam **Kapat** ' ı seçin.

   Microsoft giriş sayfasını görmeniz gerekir.

5. `www.google.com` adresine gidin.

   Güvenlik duvarının engellemesi gerekir.

Artık Güvenlik Duvarı kurallarının çalıştığını doğruladınız:

- İzin verilen bir FQDN'ye göz atabilir ancak diğerlerine göz atamazsınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Güvenlik duvarıyla oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, kaynak grubunu silin. Bu, güvenlik duvarını ve tüm ilgili kaynakları kaldırır.

Kaynak grubunu silmek için `Remove-AzResourceGroup` cmdlet 'ini çağırın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Güvenlik iş ortağı sağlayıcıları hakkında bilgi edinin](trusted-security-partners.md)
