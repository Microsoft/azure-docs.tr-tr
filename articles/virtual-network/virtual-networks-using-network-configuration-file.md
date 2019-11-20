---
title: Azure sanal ağ (klasik)-ağ yapılandırma dosyası yapılandırma | Microsoft Docs
description: Bir ağ yapılandırma dosyasını dışarı aktararak, değiştirerek ve içeri aktararak sanal ağları (klasik) oluşturma ve değiştirme hakkında bilgi edinin.
services: virtual-network
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: ab4faa0f727469e27eb30af54f24036292ec3118
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058696"
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Ağ yapılandırma dosyası kullanarak bir sanal ağ (klasik) yapılandırma
> [!IMPORTANT]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Bu makale klasik dağıtım modelini incelemektedir. Microsoft, en yeni dağıtımların Kaynak Yöneticisi dağıtım modelini kullanmasını önerir.

Azure klasik komut satırı arabirimi (CLı) veya Azure PowerShell kullanarak bir ağ yapılandırma dosyası ile bir sanal ağ (klasik) oluşturup yapılandırabilirsiniz. Bir ağ yapılandırma dosyası kullanarak Azure Resource Manager dağıtım modeli aracılığıyla bir sanal ağ oluşturamaz veya değiştiremezsiniz. Ağ yapılandırma dosyası kullanarak bir sanal ağ (klasik) oluşturmak veya değiştirmek için Azure portal kullanamazsınız, ancak bir ağ yapılandırma dosyası kullanmadan bir sanal ağ (klasik) oluşturmak için Azure portal kullanabilirsiniz.

Bir ağ yapılandırma dosyası ile bir sanal ağ (klasik) oluşturmak ve yapılandırmak, dosyanın verilmesini, değiştirilmesini ve içeri aktarılmasını gerektirir.

## <a name="export"></a>Bir ağ yapılandırma dosyasını dışarı aktarma

Bir ağ yapılandırma dosyasını dışarı aktarmak için PowerShell veya Azure klasik CLı kullanabilirsiniz. PowerShell bir XML dosyası dışarı aktarır, ancak Azure klasik CLı bir JSON dosyası dışarı aktarır.

### <a name="powershell"></a>PowerShell
 
1. [Azure PowerShell yükleyip Azure 'da oturum açın](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Dizini değiştirin (ve var olduğundan emin olun) ve aşağıdaki komutta dosya adını istenen şekilde değiştirin ve ardından ağ yapılandırma dosyasını dışarı aktarmak için komutunu çalıştırın:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Azure klasik CLI

1. [Azure klasık CLI 'Yı yükler](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Geriye kalan adımları klasik CLı komut isteminden doldurun.
2. `azure login` Komutunu girerek Azure 'da oturum açın.
3. `azure config mode asm` Komutu girerek ASM modunda olduğunuzdan emin olun.
4. Dizini değiştirin (ve var olduğundan emin olun) ve aşağıdaki komutta dosya adını istenen şekilde değiştirin ve ardından ağ yapılandırma dosyasını dışarı aktarmak için komutunu çalıştırın:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Ağ yapılandırma dosyası oluşturma veya değiştirme

Bir ağ yapılandırma dosyası, bir XML dosyasıdır (PowerShell kullanılırken) veya bir JSON dosyası (klasik CLı kullanılırken). Dosyayı herhangi bir metin veya XML/JSON düzenleyicisinde düzenleyebilirsiniz. [Ağ yapılandırma dosya şeması ayarları](https://msdn.microsoft.com/library/azure/jj157100.aspx) makalesi tüm ayarların ayrıntılarını içerir. Ayarlar hakkında daha fazla bilgi için bkz. [sanal ağları ve ayarları görüntüleme](manage-virtual-network.md#view-virtual-networks-and-settings). Dosyada yaptığınız değişiklikler:

- Şemayla uyumlu olmalı veya ağ yapılandırma dosyasını içeri aktarma başarısız olur.
- Aboneliğiniz için var olan tüm ağ ayarlarının üzerine yazın, bu nedenle değişiklik yaparken çok dikkatli olun. Örneğin, aşağıdaki örnek ağ yapılandırma dosyalarına başvurun. Özgün dosyada iki **Virtualnetworksite** örneği bulunduğundan ve örneklerde gösterildiği gibi onu değiştirmiş olursunuz. Dosyayı içeri aktardığınızda Azure, dosyasında kaldırdığınız **Virtualnetworksite** örneği için sanal ağı siler. Bu basitleştirilmiş senaryo, sanal ağ üzerinde kaynak olmadığını varsayar, ancak sanal ağ silinemez ve içeri aktarma başarısız olur.

> [!IMPORTANT]
> Azure **, kullanıldığı şekilde**kendisine dağıtılan bir ağa sahip olan bir alt ağı kabul eder. Bir alt ağ kullanımda olduğunda, değiştirilemez. Bir ağ yapılandırma dosyasındaki alt ağ bilgilerini değiştirmeden önce, alt ağa dağıttığınız herhangi bir şeyi değiştirilmeyen farklı bir alt ağa taşıyın. Ayrıntılar için bkz. [VM veya rol örneğini farklı bir alt ağa taşıma](virtual-networks-move-vm-role-to-subnet.md) .

### <a name="example-xml-for-use-with-powershell"></a>PowerShell ile kullanılacak örnek XML

Aşağıdaki örnek ağ yapılandırma dosyası, *Doğu ABD* Azure bölgesinde *10.0.0.0/16* adres alanı ile *myVirtualNetwork* adlı bir sanal ağ oluşturur. Sanal ağ, *10.0.0.0/24*adres ön ekine sahip *mysubnet* adlı bir alt ağ içerir.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

Verdiğiniz ağ yapılandırma dosyası içerik içermiyorsa, önceki örnekteki XML 'i kopyalayabilir ve yeni bir dosyaya yapıştırabilirsiniz.

### <a name="example-json-for-use-with-the-classic-cli"></a>Klasik CLı ile kullanılacak örnek JSON

Aşağıdaki örnek ağ yapılandırma dosyası, *Doğu ABD* Azure bölgesinde *10.0.0.0/16* adres alanı ile *myVirtualNetwork* adlı bir sanal ağ oluşturur. Sanal ağ, *10.0.0.0/24*adres ön ekine sahip *mysubnet* adlı bir alt ağ içerir.

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

Verdiğiniz ağ yapılandırma dosyası herhangi bir içerik içermiyorsa, önceki örnekteki JSON 'u kopyalayabilir ve yeni bir dosyaya yapıştırabilirsiniz.

## <a name="import"></a>Ağ yapılandırma dosyasını içeri aktarma

Bir ağ yapılandırma dosyasını içeri aktarmak için PowerShell veya klasik CLı kullanabilirsiniz. PowerShell bir XML dosyasını içeri aktarır, ancak klasik CLı bir JSON dosyası içeri aktarır. İçeri aktarma başarısız olursa, dosyanın [ağ yapılandırması şemasıyla](https://msdn.microsoft.com/library/azure/jj157100.aspx)uyumlu olduğunu doğrulayın. 

### <a name="powershell"></a>PowerShell
 
1. [Azure PowerShell yükleyip Azure 'da oturum açın](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Aşağıdaki komutta dizin ve dosya adını gereken şekilde değiştirin ve sonra ağ yapılandırma dosyasını içeri aktarmak için komutunu çalıştırın:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Azure klasik CLI

1. [Azure klasık CLI 'Yı yükler](/cli/azure/install-classic-cli). Geriye kalan adımları klasik CLı komut isteminden doldurun.
2. `azure login` Komutunu girerek Azure 'da oturum açın.
3. `azure config mode asm` Komutu girerek ASM modunda olduğunuzdan emin olun.
4. Aşağıdaki komutta dizin ve dosya adını gereken şekilde değiştirin ve sonra ağ yapılandırma dosyasını içeri aktarmak için komutunu çalıştırın:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
