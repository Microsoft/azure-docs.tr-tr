---
title: Azure 'da bir SQL Server VM için lisans modelini değiştirme
description: Azure 'daki SQL Server bir sanal makine için lisanslamayı, Azure Hibrit Avantajı kullanarak kendi lisansınızı adım adım nasıl yapacağınızı öğrenin.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aac20034fb4a528e48d5b383f39205a952878539
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900685"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Azure 'da SQL Server bir sanal makine için lisans modelini değiştirme
Bu makalede, Azure 'daki bir SQL Server sanal makinesi (VM) için lisans modelinin, **Microsoft. SqlVirtualMachine**ADLı yenı SQL VM kaynak sağlayıcısı kullanılarak nasıl değiştirileceği açıklanır.

SQL Server barındıran bir VM için iki lisans modeli vardır: Kullandıkça öde ve Azure Hibrit Avantajı. SQL Server VM lisans modelini, Azure portal, Azure CLı veya PowerShell kullanarak değiştirebilirsiniz. 

Kullandıkça Öde modeli, Azure VM 'yi çalıştırmanın ikinci başına maliyetinin SQL Server lisansın maliyetini içerir.
[Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) , SQL Server ÇALıŞTıRAN bir VM ile kendi SQL Server lisansınızı kullanmanıza olanak sağlar. 

Azure Hibrit Avantajı, Azure sanal makinelerinde yazılım güvencesi ("nitelikli lisans") ile SQL Server lisansların kullanılmasına izin verir. Azure Hibrit Avantajı, müşteriler bir sanal makinede SQL Server Lisansı kullanımı için ücretlendirilmez. Ancak yine de temel alınan bulut işlem maliyeti (yani, taban fiyat), depolama ve yedeklemeler için ücret ödemelidir. Ayrıca, Hizmetleri kullanımıyla ilişkili g/ç için de ödeme yapılmalıdır (geçerli olduğu gibi).

Microsoft Ürün koşullarına göre: "Müşterilerin Azure 'da Azure SQL veritabanı (yönetilen örnek, Elastik Havuz ve Tek Veritabanı), Azure Data Factory, SQL Server Integration Services veya SQL Server sanal makineler kullandığını belirtmesi gerekir Azure 'da iş yüklerini yapılandırırken SQL Server hibrit avantajı. "

Bir Azure sanal makinesinde SQL Server için Azure Hibrit Avantajı kullanımını göstermek ve uyumlu olmak için üç seçeneğiniz vardır:

- Azure Marketi 'nden kendi lisansını getir SQL Server görüntüsünü kullanarak bir sanal makine sağlayın. Bu seçenek yalnızca Kurumsal Anlaşma olan müşteriler için kullanılabilir.
- Azure Marketi 'nden Kullandıkça Öde SQL Server görüntüsünü kullanarak bir sanal makine sağlayın ve Azure Hibrit Avantajı etkinleştirin.
- Azure VM 'de kendi kendine SQL Server, SQL Server VM el ile [kaydedin](virtual-machines-windows-sql-register-with-resource-provider.md)ve Azure hibrit avantajı etkinleştirin.

SQL Server lisans türü, VM sağlandığında ayarlanır. Daha sonra istediğiniz zaman değiştirebilirsiniz. Lisans modelleri arasında geçiş kapalı kalma süresi yoktur, VM 'yi yeniden başlatmaz, ek maliyet eklemez ve hemen geçerli olur. Aslında Azure Hibrit Avantajı etkinleştirilmesi maliyeti *azaltır* .

## <a name="prerequisites"></a>Önkoşullar

SQL VM kaynak sağlayıcısı 'nın kullanımı, SQL Server IaaS uzantısını gerektirir. Bu nedenle, şunlar gerekir:
- Bir [Azure aboneliği](https://azure.microsoft.com/free/).
- [Yazılım güvencesi](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- [SQL VM kaynak sağlayıcısına](virtual-machines-windows-sql-register-with-resource-provider.md)kayıtlı bir [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) .


## <a name="change-the-license-for-vms-already-registered-with-the-resource-provider"></a>Kaynak sağlayıcısıyla zaten kayıtlı olan VM 'Lerin lisansını değiştirme 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Lisans modelini doğrudan portaldan değiştirebilirsiniz: 

1. [Azure Portal](https://portal.azure.com) açın ve SQL Server VM [SQL sanal makineler kaynağını](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) açın. 
1. **Ayarlar**altında **Yapılandır** ' ı seçin. 
1. **Azure hibrit avantajı** seçeneğini belirleyin ve yazılım güvencesi ile SQL Server lisansınızın olduğunu onaylamak için onay kutusunu işaretleyin. 
1. **Yapılandır** sayfasının alt kısmındaki **Uygula** ' yı seçin. 

![Portalda Azure Hibrit Avantajı](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı 'yi, lisans modelinizi değiştirmek için kullanabilirsiniz.  

Aşağıdaki kod parçacığı, kendi lisansını getir (veya Azure Hibrit Avantajı kullanarak) Kullandıkça Öde lisans modelinize geçiş yapar:

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Aşağıdaki kod parçacığı, kendi lisans modelinizi Kullandıkça Öde için değiştirir: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell 'i, lisans modelinizi değiştirmek için kullanabilirsiniz.

Aşağıdaki kod parçacığı, kendi lisansını getir (veya Azure Hibrit Avantajı kullanarak) Kullandıkça Öde lisans modelinize geçiş yapar:

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

Aşağıdaki kod parçacığı, kendi lisans modelinizi Kullandıkça Öde için değiştirir:

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```
---

## <a name="change-the-license-for-vms-not-registered-with-the-resource-provider"></a>Kaynak sağlayıcısına kayıtlı olmayan VM 'Lerin lisansını değiştirme

Kullandıkça Öde Azure Marketi görüntülerinden bir SQL Server VM sağladıysanız, SQL Server lisans türü Kullandıkça öde olur. Azure Marketi 'nden kendi lisansını getir görüntüsünü kullanarak bir SQL Server VM sağladıysanız, lisans türü AHUB olur. Varsayılan (Kullandıkça öde) veya kendi lisansını getir Azure Marketi görüntülerinin sağlandığı tüm SQL Server VM 'Ler otomatik olarak SQL VM kaynak sağlayıcısına kaydedilir ve bu sayede [lisans türünü](#change-the-license-for-vms-already-registered-with-the-resource-provider)değiştirebilirler.

Azure VM üzerinde SQL Server Azure Hibrit Avantajı aracılığıyla yalnızca kendi kendine yüklemeye uygunsunuz. Bu VM 'Leri, Microsoft Ürün koşullarına göre Azure Hibrit Avantajı kullanımını göstermek için SQL Server lisansını Azure Hibrit Avantajı olarak ayarlayarak [SQL VM kaynak sağlayıcısı ile kaydetmeniz](virtual-machines-windows-sql-register-with-resource-provider.md) gerekir.

SQL Server VM lisans türünü, yalnızca SQL Server VM SQL VM kaynak sağlayıcısı 'na kayıtlıysa, Kullandıkça Öde veya Azure Hibrit Avantajı olarak değiştirebilirsiniz.

## <a name="remarks"></a>Açıklamalar

- Azure bulut çözümü sağlayıcısı (CSP) müşterileri, önce bir Kullandıkça Öde sanal makinesi dağıtarak ve ardından etkin yazılım güvencesi varsa, kendi lisansını getir 'e dönüştürerek Azure Hibrit Avantajı kullanabilir.
- SQL Server VM kaynağınızı düşürülebiliyorsanız, görüntünün sabit kodlu lisans ayarına geri dönebilirsiniz. 
- Lisans modelini değiştirme özelliği, SQL VM kaynak sağlayıcısı 'nın bir özelliğidir. Azure Marketi görüntüsünü Azure portal aracılığıyla dağıtmak, SQL Server VM otomatik olarak kaynak sağlayıcısıyla kaydeder. Ancak kendi kendini yükleyen SQL Server müşterilerin SQL Server VM el ile [kaydetmesi](virtual-machines-windows-sql-register-with-resource-provider.md)gerekir. 
- Bir kullanılabilirlik kümesine SQL Server VM eklendiğinde VM 'nin yeniden oluşturulması gerekir. Bu nedenle, bir kullanılabilirlik kümesine eklenen tüm VM 'Ler varsayılan Kullandıkça Öde lisans türüne geri döner. Azure Hibrit Avantajı yeniden etkinleştirilmesi gerekir. 


## <a name="limitations"></a>Sınırlamalar

- Lisans modelinin değiştirilmesi yalnızca yazılım güvencesi olan müşteriler tarafından kullanılabilir.
- Lisans modelinin değiştirilmesi yalnızca SQL Server Standard ve Enterprise sürümleri için desteklenir. Express, Web ve geliştirici için lisans değişiklikleri desteklenmez. 
- Lisans modelinin değiştirilmesi yalnızca Azure Resource Manager modeli aracılığıyla dağıtılan sanal makineler için desteklenir. Klasik model aracılığıyla dağıtılan VM 'Ler desteklenmez. SANAL cihazınızı klasik bilgisayardan Kaynak Yöneticisi modeline geçirebilir ve SQL VM kaynak sağlayıcısına kaydedebilirsiniz. VM, SQL VM kaynak sağlayıcısına kaydedildikten sonra, VM 'de lisans modeli değişiklikleri kullanılabilir olacaktır.
- Lisans modelinin değiştirilmesi yalnızca genel bulut yüklemeleri için etkinleştirilmiştir.
- Lisans modelinin değiştirilmesi yalnızca tek bir NIC (ağ arabirimi) olan sanal makinelerde desteklenir. Birden fazla NIC içeren sanal makinelerde, yordamı denemeden önce önce NIC 'lerden birini kaldırmanız gerekir (Azure portal kullanarak). Aksi takdirde aşağıdakine benzer bir hata alırsınız: 
   
  `The virtual machine '\<vmname\>' has more than one NIC associated.` 
   
  Lisans modelini değiştirdikten sonra NIC 'i sanal makineye geri ekleyebiliyor olsanız da, Azure portal otomatik düzeltme eki uygulama ve yedekleme gibi SQL Server yapılandırma sayfasından gerçekleştirilen işlemler artık desteklenmeyecektir.

## <a name="known-errors"></a>Bilinen hatalar

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>'\<Resource-Group > ' kaynak grubundaki ' Microsoft. SqlVirtualMachine/SqlVirtualMachines/\<Resource-Group > ' kaynağı bulunamadı.
Bu hata, SQL VM kaynak sağlayıcısına kayıtlı olmayan bir SQL Server VM lisans modelini değiştirmeye çalıştığınızda oluşur:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Aboneliğinizi kaynak sağlayıcısına kaydetmeniz ve ardından [SQL Server VM kaynak sağlayıcısına kaydetmeniz](virtual-machines-windows-sql-register-with-resource-provider.md)gerekir. 

### <a name="cannot-validate-argument-on-parameter-sku"></a>' SKU ' parametresindeki bağımsız değişken doğrulanamıyor
4,0 ' den sonraki Azure PowerShell sürümlerini kullanarak SQL Server VM lisans modelinizi değiştirmeye çalışırken bu hatayla karşılaşabilirsiniz:

`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Bu hatayı çözmek için, lisans modelinize geçiş yaparken, daha önce bahsedilen PowerShell kod parçacığındaki bu satırların açıklamasını kaldırın:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
Azure PowerShell sürümünüzü doğrulamak için aşağıdaki kodu kullanın:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makalelere bakın: 

* [Windows VM 'de SQL Server genel bakış](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 'de SQL Server hakkında SSS](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM üzerinde SQL Server için fiyatlandırma Kılavuzu](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 'de SQL Server için sürüm notları](virtual-machines-windows-sql-server-iaas-release-notes.md)


