---
title: 'Hızlı başlangıç: ortak yük dengeleyici oluşturma-Azure şablonu'
titleSuffix: Azure Load Balancer
description: Bu hızlı başlangıçta, bir Azure Resource Manager şablonu kullanarak yük dengeleyicinin nasıl oluşturulacağı gösterilmektedir.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 7067d7d76815103541fa4654da2bf977ba03a4ef
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056258"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak VM 'Lerin yükünü dengelemek için ortak yük dengeleyici oluşturma

Yük dengeleme, gelen istekleri birden fazla sanal makineye (VM) yayarak daha yüksek bir kullanılabilirlik ve ölçek düzeyi sağlar. 

Bu hızlı başlangıçta, sanal makinelerin yük dengelenmesi için standart yük dengeleyicinin nasıl dağıtılacağı gösterilir.

ARM şablonunun kullanılması, diğer dağıtım yöntemleriyle karşılaştırıldığında daha az adım sürer.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-load-balancer-standard-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-load-balancer-standard-create/) alınmıştır.

Yük dengeleyici ve genel IP SKU 'Ları eşleşmelidir. Standart yük dengeleyici oluşturduğunuzda, standart yük dengeleyici için ön uç olarak yapılandırılmış yeni bir standart genel IP adresi de oluşturmanız gerekir. Temel yük dengeleyici oluşturmak istiyorsanız [Bu şablonu](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/)kullanın. Microsoft, üretim iş yükleri için standart SKU kullanmayı önerir.

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json":::

Şablonda birden çok Azure kaynağı tanımlanmış:

- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft. Network/Publicıpaddresses**](/azure/templates/microsoft.network/publicipaddresses): yük dengeleyici, savunma Konağı ve üç sanal makinenin her biri için.
- [**Microsoft. Network/bastionHosts**](/azure/templates/microsoft.network/bastionhosts)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. COMPUTE/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3).
- [**Microsoft. Network/NetworkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3).
- [**Microsoft. COMPUTE/virtualMachine/Extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3): Internet Information Server (IIS) ve Web sayfalarını yapılandırmak için kullanın.

Azure Load Balancer ilgili daha fazla şablon bulmak için bkz. [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Azure Cloud Shell açmak için aşağıdaki kod bloğundan **deneyin** ' i seçin ve ardından Azure 'da oturum açmak için yönergeleri izleyin.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   İstemi konsolundan görene kadar bekleyin.

1. PowerShell betiğini kopyalamak için önceki kod bloğundan **Kopyala** ' yı seçin.

1. Kabuk konsol bölmesine sağ tıklayın ve ardından **Yapıştır**' ı seçin.

1. Değerleri girin.

   Şablon dağıtımı üç kullanılabilirlik bölgesi oluşturur. Kullanılabilirlik alanları yalnızca [belirli bölgelerde](../availability-zones/az-overview.md)desteklenir. Desteklenen bölgelerden birini kullanın. Emin değilseniz, **merkezileştirme** girin.

   Kaynak grubu adı, **RG** eklenmiş proje adıdır. Sonraki bölümde kaynak grubu adına ihtiyacınız vardır.

Şablonun dağıtılması yaklaşık 10 dakika sürer. Tamamlandığında, çıkış şuna benzerdir:

![Azure Standart Load Balancer Kaynak Yöneticisi şablonu PowerShell dağıtım çıkışı](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell, şablonu dağıtmak için kullanılır. Azure portal, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-portal.md).

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmeden **kaynak grupları** ' nı seçin.

1. Önceki bölümde oluşturduğunuz kaynak grubunu seçin. Varsayılan kaynak grubu adı, **RG** eklenmiş proje adıdır.

1. Yük dengeleyiciyi seçin. Varsayılan adı **-lb** eklenen proje adıdır.

1. Genel IP adresinin yalnızca IP adresi kısmını kopyalayın ve ardından tarayıcınızın adres çubuğuna yapıştırın.

   ![Azure Standart yük dengeleyici Kaynak Yöneticisi şablonu genel IP 'si](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    Tarayıcı Internet Information Services (IIS) Web sunucusunun varsayılan sayfasını görüntüler.

   ![IIS web sunucusu](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Yük dengeleyicinin trafiği üç VM 'ye dağıtmasını görmek için, istemci makinesinden Web tarayıcınızı yenilemeye zorlayabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyacınız kalmadığında şunu silin: 

* Kaynak grubu
* Yük dengeleyici
* İlgili kaynaklar

Azure portal gidin, yük dengeleyiciyi içeren kaynak grubunu seçin ve **kaynak grubunu sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta:

* Yük dengeleyici ve sanal makineler için bir sanal ağ oluşturuldu.
* Yönetim için bir Azure savunma ana bilgisayarı oluşturuldu.
* Standart yük dengeleyici ve buna bağlı VM 'Ler oluşturuldu.
* Yük dengeleyici trafik kuralını ve durum araştırmasını yapılandırdınız.
* Yük dengeleyici test edildi.

Daha fazla bilgi edinmek için Azure Load Balancer öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Azure Load Balancer öğreticileri](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
