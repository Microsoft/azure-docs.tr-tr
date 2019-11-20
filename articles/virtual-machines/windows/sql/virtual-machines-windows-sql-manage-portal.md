---
title: Azure portal kullanarak Azure 'da SQL Server VM 'Leri yönetme | Microsoft Docs
description: Azure 'da barındırılan bir SQL Server VM için Azure portal SQL sanal makine kaynağına erişmeyi öğrenin.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 244ad7d079fd7baf25f8079557576c42d25ca785
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102058"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Azure 'da SQL Server VM 'Leri Azure portal kullanarak yönetin

[Azure Portal](https://portal.azure.com), **SQL sanal makineler** kaynağı bağımsız bir yönetim hizmetidir. Bunu, tüm SQL Server sanal makinelerinizi aynı anda görüntülemek ve SQL Server adanmış ayarları değiştirmek için kullanabilirsiniz: 

![SQL sanal makineler kaynağı](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Açıklamalar

- Azure 'da SQL Server VM 'lerinizi görüntülemek ve yönetmek için **SQL sanal makineler** kaynağını kullanmanızı öneririz. Ancak şu anda **SQL sanal makineler** kaynağı, [destek sonu](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server VM 'lerinin yönetimini desteklemez. Destek sonu SQL Server sanal makinelerinizin ayarlarını yönetmek için, bunun yerine kullanım dışı [SQL Server Yapılandırma sekmesini](#access-the-sql-server-configuration-tab) kullanın. 
- **SQL sanal makineler** kaynağı yalnızca [SQL VM kaynak sağlayıcısına kayıtlı](virtual-machines-windows-sql-register-with-resource-provider.md)SQL Server VM 'ler için kullanılabilir. 


## <a name="access-the-sql-virtual-machines-resource"></a>SQL sanal makineler kaynağına erişin
**SQL sanal makineler** kaynağına erişmek için aşağıdakileri yapın:

1. [Azure portalı](https://portal.azure.com) açın. 
1. **Tüm hizmetler**' i seçin. 
1. Arama kutusuna **SQL sanal makinelerini** girin.
1. (İsteğe bağlı): Bu seçeneği **Sık Kullanılanlar** menünüzde eklemek için **SQL sanal makineler** ' in yanındaki yıldızı seçin. 
1. **SQL sanal makinelerini**seçin. 

   ![Tüm hizmetlerde SQL Server sanal makineler bulun](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Portal, abonelik içinde kullanılabilir olan tüm SQL Server VM 'Leri listeler. **SQL sanal makineler** kaynağını açmak için yönetmek istediğiniz birini seçin. SQL Server VM görünmediğinde arama kutusunu kullanın. 

   ![Tüm kullanılabilir SQL Server VM 'Leri](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   SQL Server VM seçtiğinizde **SQL sanal makineler** kaynağı açılır: 


   ![SQL sanal makineler kaynağı](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> **SQL sanal makineler** kaynağı adanmış SQL Server ayarları içindir. **Sanal makine** kutusunda VM 'nin adını seçerek VM 'ye özgü ayarları açın, ancak SQL Server dışlamalı. 

## <a name="access-the-sql-server-configuration-tab"></a>SQL Server yapılandırma sekmesine erişin
**SQL Server yapılandırma** sekmesi kullanım dışı bırakıldı. Şu anda, [destek sonu](virtual-machines-windows-sql-server-2008-eos-extend-support.md) SQL Server vm 'leri SQL Server ve [SQL VM kaynak sağlayıcısına kayıtlı](virtual-machines-windows-sql-register-with-resource-provider.md)olmayan VM 'leri yönetmeye yönelik tek yöntem budur.

Kullanım dışı **SQL Server yapılandırma** sekmesine erişmek için **sanal makineler** kaynağına gidin. Aşağıdaki adımları kullanın:

1. [Azure portalı](https://portal.azure.com) açın. 
1. **Tüm hizmetler**' i seçin. 
1. Arama kutusuna **sanal makineler** girin.
1. (İsteğe bağlı): Bu seçeneği **Sık Kullanılanlar** menünüzde eklemek için **sanal makineler** ' in yanındaki yıldızı seçin. 
1. Seçin **sanal makineler**. 

   ![Sanal makineleri ara](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Portal, abonelikteki tüm sanal makineleri listeler. **Sanal makineler** kaynağını açmak için yönetmek istediğiniz birini seçin. SQL Server VM görünmediğinde arama kutusunu kullanın. 
1. SQL Server VM yönetmek için **Ayarlar** bölmesinde **SQL Server yapılandırma** ' yı seçin. 

   ![SQL Server yapılandırması](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makalelere bakın: 

* [Windows VM 'de SQL Server genel bakış](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 'de SQL Server hakkında SSS](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM üzerinde SQL Server için fiyatlandırma Kılavuzu](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 'de SQL Server için sürüm notları](virtual-machines-windows-sql-server-iaas-release-notes.md)


