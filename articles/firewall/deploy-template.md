---
title: Şablon kullanarak Azure Güvenlik Duvarı dağıtma
description: Bir şablon kullanarak Azure Güvenlik Duvarı dağıtın. Oluşturulan ağın üç alt ağı olan bir VNet 'i vardır. İki çekirdekli iki çekirdekli Windows Server sanal makineleri dağıtılır.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169192"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Şablon kullanarak Azure Güvenlik Duvarı dağıtma

[Create AzureFirewall Sandbox kurulum şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) , güvenlik duvarıyla bir test ağı ortamı oluşturur. Ağda üç alt ağa sahip bir sanal ağ (VNet) vardır: *AzureFirewallSubnet*, *Serverssubnet*ve *jumpboxsubnet*. *Serverssubnet* ve *jumpboxsubnet* alt ağı her birinin tek, Iki çekirdekli bir Windows Server sanal makinesi vardır.

Güvenlik Duvarı *AzureFirewallSubnet* alt ağıdır ve `www.microsoft.com`erişimine izin veren tek bir kuralla bir uygulama kuralı koleksiyonuna sahiptir.

Kullanıcı tanımlı bir yol, Güvenlik Duvarı kurallarının uygulandığı güvenlik duvarı aracılığıyla *Serverssubnet* alt ağından gelen ağ trafiğini gösterir.

Azure Güvenlik Duvarı hakkında daha fazla bilgi için bkz. [Azure Güvenlik duvarını Azure Portal kullanarak dağıtma ve yapılandırma](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Azure Güvenlik Duvarı 'Nı dağıtmak için şablonu kullanma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

**Şablonu kullanarak Azure Güvenlik Duvarı 'Nı yüklemek ve dağıtmak için:**

1. [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)konumundaki şablona erişin.
   
1. Girişi okuyun **ve dağıtıma hazırlanma ' yi seçin.**
   
1. Gerekirse Azure portal oturum açın. 

1. Portalda, **AzureFirewall için Sandbox kurulumu oluştur** sayfasında, aşağıdaki değerleri yazın veya seçin:
   
   - **Kaynak grubu**: **Yeni oluştur**' u seçin, kaynak grubu için bir ad yazın ve **Tamam**' ı seçin. 
   - **Sanal ağ adı**: yeni VNET için bir ad yazın. 
   - **Yönetici Kullanıcı adı**: yönetici kullanıcı hesabı için bir Kullanıcı adı yazın.
   - **Yönetici parolası**: bir yönetici parolası yazın. 
   
1. Hüküm ve koşulları okuyun ve ardından **yukarıda belirtilen hüküm ve koşulları kabul ediyorum**' u seçin.
   
1. **Satın al**'ı seçin.
   
   Kaynakların oluşturulması birkaç dakika sürer. 
   
1. Güvenlik duvarıyla oluşturulan kaynakları keşfedebilir. 

Bir şablondaki güvenlik duvarının JSON sözdizimi ve özellikleri hakkında bilgi edinmek için bkz. [Microsoft. Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyacınız kalmadığında, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) PowerShell komutunu çalıştırarak kaynak grubunu, güvenlik duvarını ve ilgili tüm kaynakları kaldırabilirsiniz. *Myresourcegroup*adlı bir kaynak grubunu kaldırmak için şunu çalıştırın: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Güvenlik Duvarı izleme öğreticisine devam etmeyi planlıyorsanız, henüz kaynak grubunu ve güvenlik duvarını kaldırmayın. 

## <a name="next-steps"></a>Sonraki adımlar

Ardından, Azure Güvenlik Duvarı günlüklerini izleyebilirsiniz:

> [!div class="nextstepaction"]
> [Öğretici: Azure Güvenlik Duvarı günlüklerini izleme](./tutorial-diagnostics.md)
