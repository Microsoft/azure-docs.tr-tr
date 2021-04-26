---
title: Azure CLı kullanarak bir savunma Konağı oluşturma | Azure savunma
description: Bu makalede, bir savunma konağını oluşturma ve silme hakkında bilgi edinin
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
ms.openlocfilehash: 8654b557de8242910c665596ca8bfd29fe86d1ff
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106578834"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Azure CLI’yi kullanarak bir Azure Bastion konağı oluşturma

Bu makalede, Azure CLı kullanarak Azure savunma ana bilgisayarı oluşturma işlemi gösterilmektedir. Sanal ağınızda Azure savunma hizmetini sağladığınızda, sorunsuz RDP/SSH deneyimi aynı sanal ağdaki tüm VM 'Ler tarafından kullanılabilir. Azure savunma dağıtımı, abonelik/hesap veya sanal makine başına değil, sanal ağ başına değildir.

İsteğe bağlı olarak, [Azure Portal](./tutorial-create-host-portal.md)kullanarak veya [Azure PowerShell](bastion-create-host-powershell.md)kullanarak bir Azure savunma ana bilgisayarı oluşturabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz olduğunu doğrulayın. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial) için kaydolabilirsiniz.

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

 >[!NOTE]
 >Azure Özel DNS bölgeleriyle Azure savunma kullanımı Şu anda desteklenmiyor. Başlamadan önce lütfen savunma kaynağınızı dağıtmayı planladığınız sanal ağın özel bir DNS bölgesine bağlı olmadığından emin olun.
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Savunma Konağı oluşturma

Bu bölüm, Azure CLı kullanarak yeni bir Azure savunma kaynağı oluşturmanıza yardımcı olur.

> [!NOTE]
> Örneklerde gösterildiği gibi, `--location` `--resource-group` kaynakların birlikte dağıtıldığından emin olmak için her komutla birlikte parametresini kullanın.

1. Bir sanal ağ ve bir Azure savunma alt ağı oluşturun. **AzureBastionSubnet** ad değerini kullanarak Azure savunma alt ağını oluşturmanız gerekir. Bu değer, Azure 'un savunma kaynaklarını hangi alt ağa dağıtacağınızı bilmesini sağlar. Bu, bir ağ geçidi alt ağından farklıdır. En az/27 veya daha büyük alt ağın (/27,/26, vb.) bir alt ağını kullanmanız gerekir. Rota tabloları veya temsilcileri olmadan **AzureBastionSubnet** oluşturun. **AzureBastionSubnet** üzerinde ağ güvenlik grupları kullanıyorsanız [Nsgs ile çalışma](bastion-nsg.md) makalesine başvurun.

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Azure Bastion için genel bir IP adresi oluşturun. Genel IP, RDP/SSH 'ye erişilecek savunma kaynağına genel IP adresidir (443 numaralı bağlantı noktası üzerinden). Genel IP adresi, oluşturmakta olduğunuz savunma kaynağıyla aynı bölgede olmalıdır.

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. Sanal ağınızın AzureBastionSubnet yeni bir Azure savunma kaynağı oluşturun. Savunma kaynağının oluşturulması ve dağıtılması yaklaşık 5 dakika sürer.

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```

## <a name="next-steps"></a>Sonraki adımlar

* Ek bilgi için savunma [hakkında SSS](bastion-faq.md) makalesini okuyun.
* Azure savunma alt ağıyla ağ güvenlik gruplarını kullanmak için bkz. [NSG Ile çalışma](bastion-nsg.md).
