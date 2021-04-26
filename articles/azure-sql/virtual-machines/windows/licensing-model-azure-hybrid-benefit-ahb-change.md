---
title: Azure 'da bir SQL VM için lisans modelini değiştirme
description: Azure 'daki bir SQL Server VM için lisanslamayı, Azure Hibrit Avantajı kullanarak kendi lisansınıza Kullandıkça öde ' ye nasıl yükleyeceğinizi öğrenin.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: management
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5813331d5eafd953d776dd19d9cc885ff71b8be0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361562"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Azure'da SQL sanal makinesi için lisans modelini değiştirme
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Bu makalede, [SQL IaaS Aracısı uzantısı](./sql-server-iaas-agent-extension-automate-management.md)kullanılarak Azure 'da bir SQL Server sanal MAKINESI (VM) için lisans modelinin nasıl değiştirileceği açıklanır.

## <a name="overview"></a>Genel Bakış

SQL Server barındıran bir Azure VM için üç lisans modeli vardır: Kullandıkça öde, Azure Hibrit Avantajı (AHB) ve yüksek kullanılabilirlik/olağanüstü durum kurtarma (HA/DR). SQL Server VM lisans modelini, Azure portal, Azure CLı veya PowerShell kullanarak değiştirebilirsiniz. 

- **Kullandıkça Öde** modeli, Azure VM 'yi çalıştırmanın ikinci başına maliyetinin SQL Server lisansın maliyetini içerir.
- [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) , SQL Server ÇALıŞTıRAN bir VM ile kendi SQL Server lisansınızı kullanmanıza olanak sağlar. 
- **Ha/Dr** lisans türü, Azure 'DAKI [ÜCRETSIZ ha/Dr çoğaltması](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) için kullanılır. 

Azure Hibrit Avantajı, Azure sanal makinelerinde yazılım güvencesi ("nitelikli lisans") ile SQL Server lisansların kullanılmasına izin verir. Azure Hibrit Avantajı, müşteriler bir sanal makinede SQL Server Lisansı kullanımı için ücretlendirilmez. Ancak yine de temel alınan bulut işlem maliyeti (yani, taban fiyat), depolama ve yedeklemeler için ücret ödemelidir. Ayrıca, Hizmetleri kullanımıyla ilişkili g/ç için de ödeme yapılmalıdır (geçerli olduğu gibi).

Microsoft [Ürün koşullarına](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS)göre: "müşteriler Azure 'da iş yüklerini yapılandırırken SQL Server Azure hibrit avantajı altındakı Azure SQL veritabanı (yönetilen örnek, Elastik Havuz ve tek veritabanı), Azure Data Factory, SQL Server Integration Services veya SQL Server sanal makineler kullandığını göstermelidir."

Azure VM 'de SQL Server Azure Hibrit Avantajı kullanımını ve uyumlu olduğunu göstermek için üç seçeneğiniz vardır:

- Azure Marketi 'nden kendi lisansını getir SQL Server görüntüsünü kullanarak bir sanal makine sağlayın. Bu seçenek yalnızca Kurumsal Anlaşma olan müşteriler için kullanılabilir.
- Azure Marketi 'nden Kullandıkça Öde SQL Server görüntüsünü kullanarak bir sanal makine sağlayın ve Azure Hibrit Avantajı etkinleştirin.
- Azure VM 'de SQL Server kendi kendine yükleyip [SQL IaaS Aracısı Uzantısı ile](sql-agent-extension-manually-register-single-vm.md)el ile kaydedin ve Azure hibrit avantajı etkinleştirin.

SQL Server lisans türü, VM sağlandığında veya daha sonra dilediğiniz zaman yapılandırılabilir. Lisans modelleri arasında geçiş kesinti olmaz, VM 'yi veya SQL Server hizmetini yeniden başlatmaz, hiçbir ek maliyet eklemez ve hemen geçerli olur. Aslında Azure Hibrit Avantajı etkinleştirilmesi maliyeti *azaltır* .

## <a name="prerequisites"></a>Önkoşullar

SQL Server VM lisanslama modelinin değiştirilmesi aşağıdaki gereksinimlere sahiptir: 

- Bir [Azure aboneliği](https://azure.microsoft.com/free/).
- [SQL IaaS Aracısı uzantısına](./sql-server-iaas-agent-extension-automate-management.md)kayıtlı bir [SQL Server VM](./create-sql-vm-portal.md) .
- [Yazılım güvencesi](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/)kullanmak için bir gereksinimdir. 


## <a name="change-license-model"></a>Lisans modelini değiştirme

# <a name="azure-portal"></a>[Azure portalı](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Lisans modelini doğrudan portaldan değiştirebilirsiniz: 

1. [Azure Portal](https://portal.azure.com) açın ve SQL Server VM [SQL sanal makineler kaynağını](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) açın. 
1. **Ayarlar** altında **Yapılandır** ' ı seçin. 
1. **Azure hibrit avantajı** seçeneğini belirleyin ve yazılım güvencesi ile SQL Server lisansınızın olduğunu onaylamak için onay kutusunu işaretleyin. 
1. **Yapılandır** sayfasının alt kısmındaki **Uygula** ' yı seçin. 

![Portalda Azure Hibrit Avantajı](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı 'yi, lisans modelinizi değiştirmek için kullanabilirsiniz.  

**Lisans türü** için aşağıdaki değerleri belirtin:
- `AHUB` Azure Hibrit Avantajı için
- `PAYG` Kullandıkça Öde için
- `DR` Ücretsiz HA/DR çoğaltmasını etkinleştirmek için


```azurecli-interactive
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type <license-type>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 'i, lisans modelinizi değiştirmek için kullanabilirsiniz.

**Lisans türü** için aşağıdaki değerleri belirtin:
- `AHUB` Azure Hibrit Avantajı için
- `PAYG` Kullandıkça Öde için
- `DR` Ücretsiz HA/DR çoğaltmasını etkinleştirmek için


```powershell-interactive
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType <license-type>
```

---

## <a name="remarks"></a>Açıklamalar

- Azure bulut çözümü sağlayıcısı (CSP) müşterileri, önce bir Kullandıkça Öde sanal makinesi dağıtarak ve ardından etkin yazılım güvencesi varsa, kendi lisansını getir 'e dönüştürerek Azure Hibrit Avantajı kullanabilir.
- SQL sanal makine kaynağınızı düşürülebiliyorsanız, görüntünün sabit kodlu lisans ayarına geri dönebilirsiniz. 
- Lisans modelini değiştirme özelliği, SQL IaaS Aracısı uzantısının bir özelliğidir. Azure Marketi görüntüsünü Azure portal aracılığıyla dağıtmak, SQL Server VM otomatik olarak uzantıya kaydeder. Ancak kendi kendini yükleyen SQL Server müşterilerin SQL Server VM el ile [kaydetmesi](sql-agent-extension-manually-register-single-vm.md)gerekir. 
- Bir kullanılabilirlik kümesine SQL Server VM eklendiğinde VM 'nin yeniden oluşturulması gerekir. Bu nedenle, bir kullanılabilirlik kümesine eklenen tüm VM 'Ler varsayılan Kullandıkça Öde lisans türüne geri döner. Azure Hibrit Avantajı yeniden etkinleştirilmesi gerekir. 


## <a name="limitations"></a>Sınırlamalar

Lisans modelinin değiştirilmesi:
   - Yalnızca [yazılım güvencesi](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)olan müşteriler tarafından kullanılabilir.
   - Yalnızca SQL Server Standard ve Enterprise sürümleri için desteklenir. Express, Web ve Developer için lisans değişiklikleri desteklenmez. 
   - Yalnızca Azure Resource Manager modeli aracılığıyla dağıtılan sanal makineler için desteklenir. Klasik model aracılığıyla dağıtılan sanal makineler desteklenmez. 
   - Yalnızca genel veya Azure Kamu bulutları için kullanılabilir. 

> [!Note]
> Yalnızca yazılım güvencesi veya abonelik lisanslarına sahip çekirdek tabanlı lisanslama SQL Server Azure Hibrit Avantajı uygundur. SQL Server için sunucu + CAL Lisanslama kullanıyorsanız ve yazılım güvencesi varsa, bu sunucular için lisans taşınabilirliği ' ni kullanarak Azure SQL Server sanal makine görüntüsüne kendi lisansını getir ' i kullanabilirsiniz, ancak Azure Hibrit Avantajı diğer özelliklerinden yararlanabilirsiniz. 

## <a name="known-errors"></a>Bilinen hatalar

Yaygın olarak bilinen hataları ve bunların çözümlerini gözden geçirin. 

**' ' Kaynak grubunun altındaki ' Microsoft. SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> ' kaynağı \<resource-group> bulunamadı.**

Bu hata, SQL Server IaaS Aracısı uzantısına kayıtlı olmayan bir SQL Server VM lisans modelini değiştirmeye çalıştığınızda oluşur:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Aboneliğinizi kaynak sağlayıcısına kaydetmeniz ve sonra [SQL Server VM SQL IaaS Aracısı Uzantısı ile kaydetmeniz](sql-agent-extension-manually-register-single-vm.md)gerekir. 



## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri inceleyin: 

* [Windows VM 'de SQL Server genel bakış](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 'de SQL Server hakkında SSS](frequently-asked-questions-faq.md)
* [Windows VM üzerinde SQL Server için fiyatlandırma Kılavuzu](pricing-guidance.md)
* [Windows VM 'de SQL Server için sürüm notları](../../database/doc-changes-updates-release-notes.md)
* [SQL IaaS Aracısı uzantısına genel bakış](./sql-server-iaas-agent-extension-automate-management.md)
