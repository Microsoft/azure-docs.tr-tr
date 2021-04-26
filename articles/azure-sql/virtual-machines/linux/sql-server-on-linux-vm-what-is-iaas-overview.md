---
title: Linux için Azure sanal makinelerinde SQL Server genel bakış | Microsoft Docs
description: Linux için Azure sanal makinelerinde tam SQL Server sürümlerinin nasıl çalıştırılacağı hakkında bilgi edinin. Tüm Linux SQL Server VM görüntülerinin ve ilgili içeriklerin doğrudan bağlantılarını alın.
services: virtual-machines-sql
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: overview
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3613b8417c78d0a01582defb7396cc65521e2f4c
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449491"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Azure Sanal Makinelerinde SQL Server'a Genel Bakış (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

Azure sanal makinelerinde SQL Server, şirket içi donanımları yönetmek zorunda kalmadan bulutta SQL Server tam sürümlerini kullanmanıza olanak sağlar. SQL Server VM’leri, kullandıkça ödeme yaptığınızda lisanslama maliyetlerini de basitleştirir.

Azure sanal makineleri dünyanın birçok farklı [coğrafi bölgesinde](https://azure.microsoft.com/regions/) çalışır. Bu makineler, ayrıca çeşitli [makine boyutları](../../../virtual-machines/sizes.md) sunar. Sanal makine resim galerisi, doğru sürüm ve işletim sistemiyle bir SQL Server VM’i oluşturmanızı sağlar. Bu, sanal makineleri birçok farklı SQL Server iş yükleri için iyi bir seçenek yapar. 

Azure SQL 'e yeni başladıysanız, derinlemesine Azure [SQL video serimizin](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)Azure *VM 'sine genel bakış videosunu SQL Server* inceleyin:
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a> SQL Server VM 'Leri kullanmaya başlama

Kullanmaya başlamak için, gerekli olan sürüm ve işletim sisteminizle birlikte bir SQL Server sanal makine resmi seçin. Aşağıdaki bölümlerde SQL Server sanal makine galeri görüntüleri için Azure portalının doğrudan bağlantıları verilmektedir.

> [!TIP]
> SQL Server görüntülerinin fiyatlandırmasının nasıl anlaşılmasıyla ilgili daha fazla bilgi için, [SQL Server çalıştıran Linux VM 'lerinin fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)bakın.

| Sürüm | İşletim sistemi | Sürüm |
| --- | --- | --- |
| **SQL Server 2019** | Ubuntu 18.04 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804web-ARM), [Geliştirici](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804sqldev-ARM) | 
| **SQL Server 2019** | Red Hat Enterprise Linux (RHEL) 8 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8web-ARM), [Geliştirici](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8sqldev-ARM)|
| **SQL Server 2019** | SUSE Linux Enterprise Server (SLES) V12 SP5 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5web-ARM), [Geliştirici](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5sqldev-ARM)|
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Windows için kullanılabilir SQL Server sanal makine görüntülerini görmek için bkz. [Azure sanal makinelerinde SQL Server genel bakış (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Yüklü paketler

Linux üzerinde SQL Server yapılandırdığınızda, veritabanı motoru paketini ve sonra gereksinimlerinize bağlı olarak birkaç isteğe bağlı paketi yüklersiniz. SQL Server için Linux sanal makine görüntüleri birçok paketi otomatik olarak sizin için yükler. Aşağıdaki tabloda her dağıtımda yüklenen paketler gösterilmektedir.

| Dağıtım | [Veritabanı altyapısı](/sql/linux/sql-server-linux-setup) | [Araçlar](/sql/linux/sql-server-linux-setup-tools) | [SQL Server Aracısı](/sql/linux/sql-server-linux-setup-sql-agent) | [Tam metin araması](/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](/sql/linux/sql-server-linux-setup-ssis) | [HA eklentisi](/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![RHEL ve veritabanı altyapısı](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL ve araçlar](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL ve SQL Server Aracısı](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL ve tam metin araması](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL ve SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL ve HA eklentisi](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |
| SLES | ![SLES ve veritabanı altyapısı](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES ve araçlar](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES ve SQL Server Aracısı](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES ve tam metin araması](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES ve SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![SLES ve HA eklentisi](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png)|
| Ubuntu | ![Ubuntu ve veritabanı altyapısı](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu ve araçları](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu ve SQL Server Aracısı](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu ve tam metin araması](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu ve SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu ve HA eklentisi](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>İlgili ürün ve hizmetler

### <a name="linux-virtual-machines"></a>Linux sanal makineleri

* [Azure sanal makinelerine genel bakış](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Depolama

* [Microsoft Azure Depolama'ya Giriş](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Ağ

* [Sanal ağa genel bakış](../../../virtual-network/virtual-networks-overview.md)
* [Azure'da IP adresleri](../../../virtual-network/public-ip-addresses.md)
* [Azure portalda Tam Etki Alanı Adı oluşturma](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL

* [Linux üzerinde SQL Server belgeleri](/sql/linux)
* [Azure SQL Veritabanı karşılaştırması](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Sonraki adımlar

Linux üzerinde SQL Server sanal makinelerle çalışmaya başlama:

* [Azure portalında SQL Server VM’i oluşturma](sql-vm-create-portal-quickstart.md)

Linux 'ta SQL Server VM 'Ler hakkında sık sorulan soruların yanıtlarını alın:

* [Azure Sanal Makinelerde SQL Server SSS](frequently-asked-questions-faq.md)