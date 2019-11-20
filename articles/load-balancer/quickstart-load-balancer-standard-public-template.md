---
title: 'Hızlı başlangıç: Standart yük dengeleyici oluşturma-Azure şablonu'
titleSuffix: Azure Load Balancer
description: Bu hızlı başlangıçta, Azure Resource Manager şablonu kullanılarak standart yük dengeleyicinin nasıl oluşturulacağı gösterilmektedir.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 884ba1106e7b68fefaf91ee4e25cacd05bf96fc0
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048741"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Hızlı başlangıç: Azure Resource Manager şablonu kullanarak VM 'Lerin yükünü dengelemek için standart yük dengeleyici oluşturma

Yük dengeleme, gelen istekleri birden fazla sanal makineye (VM) yayarak daha yüksek bir kullanılabilirlik ve ölçek düzeyi sağlar. Bu hızlı başlangıçta, VM 'Lerin yük dengelemesi için standart yük dengeleyici oluşturan bir Azure Resource Manager şablonunun nasıl dağıtılacağı gösterilir. Kaynak Yöneticisi şablonu kullanmak diğer dağıtım yöntemleriyle karşılaştırıldığında daha az adım sürer.

[Kaynak Yöneticisi Şablon](../azure-resource-manager/template-deployment-overview.md) , projeniz için altyapıyı ve yapılandırmayı tanımlayan bir JAVASCRIPT nesne GÖSTERIMI (JSON) dosyasıdır. Şablon bildirim temelli sözdizimini kullanır, bu, oluşturmak için programlama komutlarının dizisini yazmak zorunda kalmadan ne dağıtmayı istediğinizi belirtmenize olanak tanır. Kaynak Yöneticisi şablonları geliştirme hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Kaynak Yöneticisi belgeleri](/azure/azure-resource-manager/) ve [şablon başvurusu](/azure/templates/microsoft.network/loadbalancers).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-a-standard-load-balancer"></a>Standart yük dengeleyici oluşturma

Standart Load Balancer yalnızca standart bir genel IP adresini destekler. Standart yük dengeleyici oluşturduğunuzda, standart yük dengeleyici için ön uç olarak yapılandırılmış yeni bir standart genel IP adresi de oluşturmanız gerekir.

Bu hızlı başlangıçta kullanılan şablon bir [hızlı başlangıç şablonudur](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

[!code-json[<Azure Resource Manager template create standard load balancer>](~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json)]

Şablonda birden çok Azure kaynağı tanımlanmış:

- **Microsoft. Network/loadBalancers**
- Yük Dengeleyici için **Microsoft. Network/Publicıpaddresses**:.
- **Microsoft. Network/networkSecurityGroups**
- **Microsoft. Network/virtualNetworks**
- **Microsoft. COMPUTE/virutalMachines** (3)
- **Microsoft. Network/Publicıpaddresses** (3): üç sanal makinenin her biri için.
- **Microsoft. Network/NetworkInterfaces** (3)
- **Microsoft. COMPUTE/virtualMachine/Extensions** (3 BT): IIS 'i ve Web sayfalarını yapılandırmak için kullanın

Azure Load Balancer ilgili daha fazla şablon bulmak için bkz. [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

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

   Şablon dağıtımı üç kullanılabilirlik bölgesi oluşturur. Kullanılabilirlik alanları yalnızca [belirli bölgelerde](../availability-zones/az-overview.md)desteklenir. Desteklenen bölgelerden birini kullanın. Emin değilseniz, **merkezileştirme**girin.

   Kaynak grubu adı, **RG** eklenmiş proje adıdır. Sonraki bölümde kaynak grubu adına ihtiyacınız vardır.

Şablonun dağıtılması yaklaşık 10 dakika sürer. Tamamlandığında, çıkış şuna benzerdir:

![Azure Standart Load Balancer Kaynak Yöneticisi şablonu PowerShell dağıtım çıkışı](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell, şablonu dağıtmak için kullanılır. Azure PowerShell ek olarak, Azure portal, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/resource-group-template-deploy-portal.md).

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

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

Artık ihtiyacınız kalmadığında, kaynak grubunu, yük dengeleyiciyi ve tüm ilgili kaynakları silin. Bunu yapmak için Azure portal gidin, yük dengeleyiciyi içeren kaynak grubunu seçin ve **kaynak grubunu sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir standart yük dengeleyici oluşturdunuz, bu sanal makineye bağlı VM 'Ler, yük dengeleyici trafik kuralını yapılandırdınız, bir sistem durumu araştırması ve sonra yük dengeleyiciyi test ediyor.

Daha fazla bilgi edinmek için Load Balancer öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Azure Load Balancer öğreticileri](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
