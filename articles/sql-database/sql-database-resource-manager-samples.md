---
title: Azure Resource Manager şablonları
description: Azure SQL veritabanı oluşturmak ve yapılandırmak için Azure Resource Manager şablonlarını kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 16091286aecc6dfdbcfa833a76920ff4bc9c64de
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821120"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Azure SQL veritabanı için Azure Resource Manager şablonları

Azure Resource Manager şablonlar altyapınızı kod olarak tanımlamanızı ve çözümlerinizi Azure bulutuna dağıtmanızı sağlar.

## <a name="single-database--elastic-pooltabsingle-database"></a>[Tek veritabanı & elastik havuz](#tab/single-database)

Aşağıdaki tabloda, Azure SQL veritabanı için Azure Resource Manager şablonlarının bağlantıları yer almaktadır.

| |  |
|---|---|
| [Tek veritabanı](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Bu Azure Resource Manager şablonu, mantıksal sunucu ile tek bir Azure SQL veritabanı oluşturur ve güvenlik duvarı kurallarını yapılandırır. |
| [Mantıksal sunucu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Bu Azure Resource Manager şablonu, Azure SQL veritabanı için bir mantıksal sunucu oluşturur. |
| [Elastik havuz](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Bu şablon, yeni ilişkili SQL Server ve buna atamak için yeni SQL veritabanlarıyla yeni bir elastik havuz dağıtmanıza olanak tanır. |
| [Yük devretme grupları](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Bu şablon iki Azure SQL mantıksal sunucusu, bir SQL veritabanı ve bir yük devretme grubu oluşturur.|
| [Tehdit algılama](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Bu şablon, her veritabanı için uyarılar için bir e-posta adresi ile bir Azure SQL mantıksal sunucusunu ve tehdit algılama özelliği etkinleştirilmiş bir dizi Azure SQL veritabanını dağıtmanıza olanak tanır. Tehdit algılama, SQL Gelişmiş tehdit koruması (ATP) sunumunun bir parçasıdır ve SQL Server ve veritabanları üzerinden olası tehditlere yanıt veren bir güvenlik katmanı sağlar.|
| [Azure Blob depolamaya yönelik denetim](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Bu şablon, Denetim günlüklerini blob depolamaya yazmak için denetim etkinleştirilmiş bir Azure SQL mantıksal sunucusu dağıtmanıza olanak tanır. Azure SQL veritabanı için denetim, veritabanı olaylarını izler ve bunları Azure depolama hesabınıza, OMS çalışma alanınıza veya Event Hubs yerleştirilebilecek bir denetim günlüğüne yazar.|
| [Azure Olay Hub 'ına denetim](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Bu şablon, çıkış bir olay hub 'ına denetim günlükleri yazmak için etkin denetim ile bir Azure SQL Server dağıtmanıza olanak tanır. Olay Hub 'ına denetim olayları göndermek için denetim ayarlarını `Enabled` `State` olarak ayarlayın ve `IsAzureMonitorTargetEnabled` `true`olarak ayarlayın. Ayrıca, tanılama ayarlarını `master` veritabanında `SQLSecurityAuditEvents` tanılama günlükleri kategorisi ile yapılandırın (örneğin, hizmeti düzeyinde denetim için). Azure SQL veritabanı ve SQL veri ambarı için denetim, veritabanı olaylarını izler ve bunları Azure depolama hesabınıza, OMS çalışma alanınıza veya Event Hubs yerleştirilebilecek bir denetim günlüğüne yazar.|
| [SQL veritabanı ile Azure Web uygulaması](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Bu örnek, "temel" hizmet düzeyinde ücretsiz bir Azure Web uygulaması ve SQL veritabanı oluşturur.|
| [Azure Web uygulaması ve SQL veritabanı ile Redis Cache](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Bu şablon, aynı kaynak grubunda bir Web uygulaması, Redis Cache ve SQL veritabanı oluşturur ve SQL veritabanı ve Redis Cache için Web uygulamasında iki bağlantı dizesi oluşturur.|
| [ADF v2 kullanarak blob depolamadan veri aktarma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Bu Azure Resource Manager şablonu, verileri Azure Blob depolama alanından SQL veritabanına kopyalayan Azure Data Factory v2 oluşturur.|
| [SQL veritabanı içeren HDInsight kümesi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Bu şablon, bir HDInsight kümesi, bir SQL veritabanı sunucusu, bir SQL veritabanı ve iki tablo oluşturmanıza olanak sağlar. Bu şablon, [HDInsight 'Ta Hadoop Ile Sqoop kullanma makalesinde](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) kullanılır |
| [Bir SQL saklı yordamını zamanlamaya göre çalıştıran Azure Logic App](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Bu şablon, zamanlamaya göre bir SQL saklı yordamı çalıştıracak bir mantıksal uygulama oluşturmanıza olanak tanır. Yordamın tüm bağımsız değişkenleri, şablonun gövde bölümüne yerleştirilebilir.|

## <a name="managed-instancetabmanaged-instance"></a>[Yönetilen örnek](#tab/managed-instance)

Aşağıdaki tabloda, Azure SQL veritabanı yönetilen örneği için Azure Resource Manager şablonlarının bağlantıları yer almaktadır.

| |  |
|---|---|
| [Yeni bir VNet 'te yönetilen örnek](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Bu Azure Resource Manager şablonu, sanal ağda yeni bir yapılandırılmış Azure VNet ve yönetilen örnek oluşturur. |
| [Yönetilen örnek için ağ ortamı](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Bu dağıtım, yönetilen örneklerinize ayrılan iki alt ağa sahip yapılandırılmış bir Azure sanal ağı ve diğer kaynakları (örneğin, VM 'Ler, App Service ortamları, vb.) yerleştirebileceğiniz başka bir sanal ağ oluşturur. Bu şablon, yönetilen örnekleri dağıtabileceğiniz, düzgün şekilde yapılandırılmış bir ağ ortamı oluşturacak. |
| [P2S bağlantısıyla yönetilen örnek](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Bu dağıtım, iki alt ağ `ManagedInstance` ve `GatewaySubnet`bir Azure sanal ağı oluşturacak. Yönetilen örnek, ManagedInstance alt ağında dağıtılacak. Sanal ağ geçidi, `GatewaySubnet` alt ağda oluşturulur ve Noktadan siteye VPN bağlantısı için yapılandırılır. |
| [Sanal makine ile yönetilen örnek](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Bu dağıtım, iki alt ağ `ManagedInstance` ve `Management`bir Azure sanal ağı oluşturacak. Yönetilen örnek, `ManagedInstance` alt ağına dağıtılacak. SQL Server Management Studio (SSMS) en son sürümüne sahip sanal makine `Management` alt ağda dağıtılır. |

---
