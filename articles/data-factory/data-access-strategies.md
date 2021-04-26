---
title: Veri erişim stratejileri
description: Azure Data Factory artık statik IP adresi aralıklarını desteklemektedir.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: edc773ec2db078b6c50b55c81ad6570758a3f5f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389255"
---
# <a name="data-access-strategies"></a>Veri erişim stratejileri

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Kuruluşun önemli bir güvenlik amacı, veri depolarını Internet üzerinden rastgele erişimden korumak, şirket içi veya bulut/SaaS veri deposu olabilir. 

Genellikle bir bulut veri deposu aşağıdaki mekanizmalardan yararlanarak erişimi denetler:
* Bir sanal ağdan özel uç nokta etkin veri kaynaklarına özel bağlantı
* Bağlantıyı IP adresine göre sınırlayan güvenlik duvarı kuralları
* Kullanıcıların kimliklerini kanıtlamasını gerektiren kimlik doğrulama mekanizmaları
* Kullanıcıları belirli eylemlerle ve verilerle kısıtlayan Yetkilendirme mekanizmaları

> [!TIP]
> [STATIK IP adres aralığının kullanıma sunulmasıyla](./azure-integration-runtime-ip-addresses.md)birlikte, bulut veri depolarındaki tüm Azure IP adreslerine izin vermek zorunda olmadığınızdan emin olmak Için belirli Azure tümleştirme çalışma zamanı BÖLGESI için IP aralıkları listesine izin verebilirsiniz. Bu şekilde, veri depolarına erişmelerine izin verilen IP adreslerini kısıtlayabilirsiniz.

> [!NOTE] 
> IP adresi aralıkları Azure Integration Runtime için engellenir ve şu anda yalnızca veri taşıma, işlem hattı ve dış etkinlikler için kullanılır. Yönetilen sanal ağı etkinleştiren veri akışları ve Azure Integration Runtime artık bu IP aralıklarını kullanmaz. 

Bu pek çok senaryoda çalışmalıdır ve tümleştirme çalışma zamanı başına benzersiz bir statik IP adresi istenmekte olduğunu anladık, ancak bu, sunucusuz olan Azure Integration Runtime Şu anda mümkün olmayabilir. Gerekirse, her zaman kendi kendine barındırılan bir Integration Runtime ayarlayabilir ve statik IP 'nizi bununla birlikte kullanabilirsiniz. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Azure Data Factory aracılığıyla veri erişimi stratejileri

* **[Özel bağlantı](../private-link/private-link-overview.md)** -Azure Data Factory yönetilen sanal ağ içinde bir Azure Integration Runtime oluşturabilirsiniz ve desteklenen veri depolarına güvenli bir şekilde bağlanmak için özel uç noktalardan yararlanabilir. Yönetilen sanal ağ ve veri kaynakları arasındaki trafik, Microsoft omurga ağını dolaşır ve genel ağa maruz değildir.
* **[Güvenilen hizmet](../storage/common/storage-network-security.md#exceptions)** -Azure depolama (Blob, ADLS 2.), güvenilen Azure platform hizmetlerinin depolama hesabına güvenli bir şekilde erişmesini sağlayan güvenlik duvarı yapılandırmasını destekler. Güvenilen hizmetler yönetilen kimlik kimlik doğrulamasını zorlar ve bu, yönetilen kimliği kullanarak böyle bir veri fabrikasının bu depolamaya bağlanamamasını sağlar. **[Bu blogda](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)** daha fazla ayrıntı bulabilirsiniz. Bu nedenle, bu son derece güvenlidir ve önerilir. 
* **Benzersiz STATIK IP** -Data Factory bağlayıcıları IÇIN statik IP almak üzere şirket içinde barındırılan bir tümleştirme çalışma zamanı ayarlamanız gerekir. Bu mekanizma, diğer tüm IP adreslerinden erişimi engellemenize de engel olur. 
* **[STATIK IP aralığı](./azure-integration-runtime-ip-addresses.md)** -Azure INTEGRATION RUNTIME kendi IP adreslerini, depolama ortamınızda listelemek için kullanabilirsiniz (S3, Salesforce, vb.). Veri depolarına bağlanabilecek, ancak kimlik doğrulama/yetkilendirme kurallarına de dayanan IP adreslerini kesinlikle kısıtlar.
* **[Hizmet etiketi](../virtual-network/service-tags-overview.md)** -hizmet etiketi, belirli bir Azure hizmetinden (Azure Data Factory gibi) bir IP adresi ön eki grubunu temsil eder. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir ve ağ güvenlik kuralları için sık sık güncelleştirmelerin karmaşıklığını en aza indirir. Sanal ağdaki IaaS barındırılan veri depolarında veri erişimi filtrelendiğinde faydalıdır.
* **Azure hizmetlerine Izin ver** -bazı hizmetler, bu seçeneği tercih etmeniz durumunda tüm Azure hizmetlerinin bağlanmasına izin vermenizi sağlar. 

Azure Integration Runtime ve şirket içinde barındırılan Integration Runtime veri depolarında desteklenen ağ güvenlik mekanizmaları hakkında daha fazla bilgi için, iki tablonun altına bakın.  
* **Azure Integration Runtime**

    | Veri Depoları                  | Veri depolarında desteklenen ağ güvenlik mekanizması | Özel Bağlantı     | Güvenilen hizmet     | Statik IP aralığı | Hizmet Etiketleri | Azure hizmetlerine izin ver |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|-----------------|
    | Azure PaaS veri depoları       | Azure Cosmos DB                                     | Evet              | -                   | Yes             | -            | Yes                  |
    |                              | Azure Veri Gezgini                                 | -                | -                   | Evet*            | Evet*         | -                    |
    |                              | Azure Data Lake Gen1                                | -                | -                   | Yes             | -            | Yes                  |
    |                              | MariaDB, MySQL, PostgreSQL için Azure veritabanı       | -                | -                   | Yes             | -            | Yes                  |
    |                              | Azure Dosya Depolama                                  | Yes              | -                   | Yes             | -            | .                    |
    |                              | Azure depolama (blob, ADLS 2.)                     | Yes              | Evet (yalnızca MSI kimlik doğrulaması) | Yes             | -            | .                    |
    |                              | Azure SQL DB, Azure SYNAPSE Analytics), SQL ml  | Evet (yalnızca Azure SQL DB/DW)        | -                   | Yes             | -            | Yes                  |
    |                              | Azure Key Vault (gizli dizileri/bağlantı dizesi getiriliyor) | evet      | Yes                 | Yes             | -            | -                    |
    | Diğer PaaS/SaaS veri depoları | AWS S3, SalesForce, Google bulut depolama, vb.    | -                | -                   | Yes             | -            | -                    |
    | Azure laaS                   | SQL Server, Oracle, vb.                          | -                | -                   | Yes             | Yes          | -                    |
    | Şirket içi laaS              | SQL Server, Oracle, vb.                          | -                | -                   | Yes             | -            | -                    |
    
    **Yalnızca Azure Veri Gezgini, sanal ağ eklendiğinde ve IP aralığı NSG/güvenlik duvarında uygulanabildiğinde geçerlidir.* 

* **Şirket içinde barındırılan Integration Runtime (VNet/şirket içi)**
    
    | Veri depoları                  | Veri depolarında desteklenen ağ güvenlik mekanizması         | Statik IP | Güvenilen hizmetler  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Azure PaaS veri depoları       | Azure Cosmos DB                                               | Evet       | -                   |
    |                                | Azure Veri Gezgini                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | Yes       | -                   |
    |                                | MariaDB, MySQL, PostgreSQL için Azure veritabanı               | Yes       | -                   |
    |                                | Azure Dosya Depolama                                            | Yes       | -                   |
    |                                | Azure depolama (blog, ADLS 2.)                             | Yes       | Evet (yalnızca MSI kimlik doğrulaması) |
    |                                | Azure SQL DB, Azure SYNAPSE Analytics), SQL ml          | Yes       | -                   |
    |                                | Azure Key Vault (gizli dizileri/bağlantı dizesi getiriliyor) | Yes       | Yes                 |
    | Diğer PaaS/SaaS veri depoları | AWS S3, SalesForce, Google bulut depolama, vb.              | Yes       | -                   |
    | Azure laaS                     | SQL Server, Oracle, vb.                                  | Yes       | -                   |
    | Şirket içi laaS              | SQL Server, Oracle, vb.                                  | Evet       | -                   |    

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki ilgili makalelere bakın:
* [Desteklenen veri depoları](./copy-activity-overview.md#supported-data-stores-and-formats)
* [' Güvenilir hizmetler ' Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)
* [Azure Storage ' güvenilir Microsoft Hizmetleri '](../storage/common/storage-network-security.md#trusted-microsoft-services)
* [Data Factory için yönetilen kimlik](./data-factory-service-identity.md)