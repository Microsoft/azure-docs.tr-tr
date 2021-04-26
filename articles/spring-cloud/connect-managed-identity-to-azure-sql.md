---
title: Azure SQL 'i Azure Spring Cloud App 'e bağlamak için yönetilen kimlik kullanma
description: Azure SQL 'i Azure yay bulut uygulamasına bağlamak için yönetilen kimliği ayarlayın.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: ed729dde51316b9a67f396e3f7de3d7d9f6d4568
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378797"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Azure SQL veritabanı 'nı Azure yay bulutu uygulamasına bağlamak için yönetilen bir kimlik kullanma

**Bu makale şu şekilde geçerlidir:** ✔️ Java

Bu makalede, Azure yay bulutu uygulaması için yönetilen kimlik oluşturma ve Azure SQL veritabanı 'na erişim için kullanma hakkında yönergeler verilmektedir.

[Azure SQL veritabanı](https://azure.microsoft.com/services/sql-database/) , bulut için tasarlanan akıllı, ölçeklenebilir, ilişkisel bir veritabanı hizmetidir. BT, performansı ve dayanıklılığı en iyileştiren AI destekli ve otomatikleştirilmiş özelliklerle her zaman güncel değildir. Sunucusuz işlem ve hiper ölçek depolama seçenekleri, kaynakları isteğe bağlı olarak otomatik olarak ölçeklendirerek, depolama boyutu veya kaynak yönetimi hakkında endişelenmeden yeni uygulamalar oluşturmaya odaklanmanız gerekir.

## <a name="prerequisites"></a>Önkoşullar
Bu örnek aşağıdaki kaynakları kullanır.
* Azure SQL veritabanı sağlamak ve bir Java uygulamasıyla yerel olarak çalışmasını sağlamak için [Spring Data JPA öğreticisini](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server) izleyin
* Mı etkin bir Azure yay bulutu uygulaması sağlamak için [Azure Spring Cloud sistem tarafından atanan yönetilen kimlik öğreticisini](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity) izleyin

## <a name="grant-permission-to-the-managed-identity"></a>Yönetilen kimliğe izin ver
SQL Server 'a bağlanın ve aşağıdaki SQL sorgusunu çalıştırın:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

Bu <MIName> kural şöyledir: `<service instance name>/apps/<app name>` , ör. myspringcloud/Apps/sqldemo. Ayrıca, Azure CLı ile MIName 'ı sorgulayabilirsiniz:

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>Java Uygulamanızı yönetilen kimlik kullanacak şekilde yapılandırma
Dosyasını açın `src/main/resources/application.properties` ve `Authentication=ActiveDirectoryMSI;` aşağıdaki satırın sonuna ekleyin. $AZ _DATABASE_NAME değişkeni için doğru değeri kullandığınızdan emin olun.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>Uygulamayı derleyin ve Azure Spring Cloud 'a dağıtın
Uygulamayı yeniden oluşturun ve Önkoşullar altındaki ikinci madde işareti noktasında sağlanan Azure Spring Cloud uygulamasına dağıtın. Artık Azure Spring Cloud 'daki bir Azure SQL veritabanından veri depolamak ve almak için JPA kullanan yönetilen bir kimlik tarafından kimliği doğrulanmış bir Spring Boot uygulamasına sahipsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Spring Cloud 'da yönetilen kimlikle Depolama Blobu 'ne erişme](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Azure yay bulut uygulaması için sistem tarafından atanan yönetilen kimliği etkinleştirme](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Azure kaynakları için Yönetilen kimlikler hakkında daha fazla bilgi edinin](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [GitHub eylemlerinde Key Vault Azure Spring Cloud kimlik doğrulaması yapma](./spring-cloud-github-actions-key-vault.md)