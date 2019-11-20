---
title: Azure ayrılmış ana bilgisayar üzerinde SQL Server VM
description: Azure adanmış bir konakta SQL Server VM çalıştırmaya ilişkin ayrıntılar hakkında bilgi edinin.
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
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8b29bbce1511b022def522d46c74b99967a76ea3
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204529"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>Azure ayrılmış ana bilgisayar üzerinde SQL Server VM 

Bu makalede, [Azure adanmış ana bilgisayar](/azure/virtual-machines/windows/dedicated-hosts)ile SQL Server VM kullanmanın özellikleri ayrıntılı olarak açıklanır. Azure adanmış ana bilgisayar hakkında ek bilgilere, [Azure adanmış ana bilgisayarı tanıtma](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/)blog gönderisine ulaşabilirsiniz. 

## <a name="overview"></a>Genel Bakış
[Azure adanmış ana bilgisayar](/azure/virtual-machines/windows/dedicated-hosts) , bir veya daha fazla sanal makineyi bir Azure aboneliğine ayrılmış olarak barındırabilecek fiziksel sunucular sağlayan bir hizmettir. Adanmış konaklar, Microsoft 'un bir kaynak olarak sağlanmış olan veri merkezlerinde kullanılan fiziksel sunuculardır. Bir bölge, kullanılabilirlik alanı ve hata etki alanı içinde adanmış konaklar sağlayabilirsiniz. Daha sonra, gereksinimlerinizi en iyi şekilde karşılayan her yapılandırma için VM 'Leri doğrudan sağlanan konaklarınıza yerleştirebilirsiniz.


[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]


## <a name="licensing"></a>Lisanslama

SQL Server VM Azure ayrılmış bir konağa eklerken iki farklı lisans seçeneği arasından seçim yapabilirsiniz. 

  - **SQL VM lisanslama**: Bu, her bir SQL Server VM lisansı için ayrı olarak ödediğiniz mevcut lisanslama seçeneğidir. 
  - **Adanmış ana bilgisayar lisanslama**: SQL Server lisanslarının paketlenmiş ve ana bilgisayar düzeyinde ödendiği Azure adanmış ana bilgisayar için kullanılabilen yeni lisanslama modeli. 


Mevcut SQL Server lisanslarını kullanmaya yönelik konak düzeyi seçenekleri: 
  - SQL Server Enterprise Edition Azure Hibrit Avantajı
    - SA veya aboneliği olan müşteriler tarafından kullanılabilir.
    - Tüm kullanılabilir fiziksel çekirdekleri lisanslayın ve sınırsız sanallaştırmadan yararlanın (konak tarafından desteklenen maks. sanal CPU 'lara kadar).
        - Azure adanmış ana bilgisayara Azure Hibrit Avantajı uygulama hakkında daha fazla bilgi için [Azure HIBRIT AVANTAJı SSS](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)bölümüne bakın. 
  - SQL Server Lisans 1 Ekim 'Den önce alındı
      - SQL Server Enterprise sürümünde hem ana bilgisayar düzeyi hem de VM 'ler lisans seçenekleri vardır. 
      - SQL Server Standard Edition 'ın yalnızca sanal makine lisansı seçeneği kullanılabilir. 
          - Ayrıntılar için bkz. [Microsoft 'un ürün koşulları](https://www.microsoft.com/licensing/product-licensing/products). 
  - SQL Server adanmış bir konak düzeyi seçeneği seçili değilse, tıpkı çok kiracılı VM 'Lerde olduğu gibi, tek tek sanal makineler düzeyinde SQL Server AHB seçilebilir.



## <a name="provisioning"></a>Sağlama  
Adanmış konağa SQL Server VM sağlama diğer Azure sanal makinenden farklı değildir. [Azure PowerShell](../dedicated-hosts-powershell.md), [Azure Portal](../dedicated-hosts-portal.md)ve [Azure CLI](../../linux/dedicated-hosts-cli.md)kullanarak bunu yapabilirsiniz.

Ayrılmış konağa var olan bir SQL Server VM ekleme işlemi kapalı kalma süresi gerektirir, ancak verileri etkilemez ve veri kaybına neden olmayacaktır. Nonetheless, sistem veritabanları da dahil olmak üzere tüm veritabanlarının taşımadan önce yedeklenmesi gerekir.

## <a name="virtualization"></a>Sanallaştırma 

Adanmış bir konağın avantajlarından biri sınırsız sanallaştırmadır. Örneğin, 64 sanal çekirdek lisanslarına sahip olabilirsiniz, ancak ana bilgisayarı 128 sanal çekirdeğe sahip olacak şekilde yapılandırabilir, bu sayede sanal çekirdekleri yalnızca SQL Server lisanslarının ücretini ödeirsiniz. 

Ana bilgisayarınız olduğundan bu yana bir 1:2 oranıyla sanallaştırmayı ayarlamaya uygun olursunuz. 

## <a name="faq"></a>SSS

**S: Azure Hibrit Avantajı Azure ayrılmış ana bilgisayarında Windows Server/SQL Server lisansları için nasıl çalışır?**

Y: Müşteriler, Azure Hibrit Avantajı kullanarak Azure adanmış ana bilgisayar üzerinde daha düşük bir ücret ödemek için mevcut Windows Server ve Yazılım Güvencesi kapsamındaki SQL Server lisanslarını veya uygun abonelik lisanslarını kullanabilir. Windows Server Datacenter ve SQL Server Enterprise Edition müşterileri, tüm Konağı lisanslarsa sınırsız sanallaştırma (ana bilgisayarın fiziksel kapasitesine bağlı olarak çok sayıda Windows Server sanal makinesi olarak dağıtın) alır ve Azure Hibrit Avantajı kullanın.  Azure ayrılmış ana bilgisayarındaki tüm Windows Server ve SQL Server iş yükleri, ek ücret ödemeden Windows Server ve SQL Server 2008/R2 için genişletilmiş güvenlik güncelleştirmelerine de uygundur. 

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makalelere bakın: 

* [Windows VM 'de SQL Server genel bakış](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 'de SQL Server hakkında SSS](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM üzerinde SQL Server için fiyatlandırma Kılavuzu](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 'de SQL Server için sürüm notları](virtual-machines-windows-sql-server-iaas-release-notes.md)


