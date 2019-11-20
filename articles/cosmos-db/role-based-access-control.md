---
title: Azure Active Directory tümleştirmeyle Azure Cosmos DB rol tabanlı erişim denetimi
description: Azure Cosmos DB Active Directory Tümleştirmesi (RBAC) ile nasıl veritabanı koruması sağladığını öğrenin.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: c7605bbc1824e2e859cc94296834daf4e4e0907b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072357"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB rol tabanlı erişim denetimi

Azure Cosmos DB, Azure Cosmos DB ortak yönetim senaryoları için yerleşik rol tabanlı erişim denetimi (RBAC) sağlar. Azure Active Directory bir profili olan bir kişi, Azure Cosmos DB kaynaklardaki kaynaklara ve işlemlere erişim vermek veya erişimi reddetmek için bu RBAC rollerini kullanıcılara, gruplara, hizmet sorumlularına veya yönetilen kimliklere atayabilir. Rol atamaları yalnızca, Azure Cosmos hesaplarına, veritabanlarına, kapsayıcılarına ve tekliflere (verimlilik) erişimi de içeren denetim düzlemi erişimini kapsar.

## <a name="built-in-roles"></a>Yerleşik roller

Azure Cosmos DB tarafından desteklenen yerleşik roller aşağıda verilmiştir:

|**Yerleşik rol**  |**Açıklama**  |
|---------|---------|
|[DocumentDB hesabı Katılımcısı](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|, Azure Cosmos DB hesaplarını yönetebilir.|
|[Cosmos DB hesabı okuyucusu](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Azure Cosmos DB hesabı verilerini okuyabilir.|
|[Cosmos yedekleme Işletmeni](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|, Bir Azure Cosmos veritabanı veya kapsayıcısı için geri yükleme isteği gönderebilir.|
|[Cosmos DB Işleci](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Azure Cosmos hesaplarını, veritabanlarını ve kapsayıcıları sağlayabilir, ancak verilere erişmek için gereken anahtarlara erişemez.|

> [!IMPORTANT]
> Azure Cosmos DB ' de RBAC desteği yalnızca denetim düzlemi işlemleri için geçerlidir. Veri düzlemi işlemleri, ana anahtarlar veya kaynak belirteçleri kullanılarak güvenli hale getirilir. Daha fazla bilgi için bkz. [Azure Cosmos DB verilere güvenli erişim](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Kimlik ve erişim yönetimi (IAM)

Azure portal **erişim denetimi (IAM)** bölmesi Azure Cosmos kaynaklarında rol tabanlı erişim denetimini yapılandırmak için kullanılır. Roller, kullanıcılar, gruplar, hizmet sorumluları ve Active Directory içindeki yönetilen kimliklere uygulanır. Bireyler ve gruplar için yerleşik roller veya özel roller kullanabilirsiniz. Aşağıdaki ekran görüntüsünde, Azure portal erişim denetimi (ıAM) kullanan Active Directory tümleştirme (RBAC) gösterilmektedir:

![Azure portal gösteren veritabanı güvenliğine erişim denetimi (ıAM)](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Özel roller

Kullanıcılar, yerleşik rollere ek olarak Azure 'da [özel roller](../role-based-access-control/custom-roles.md) de oluşturabilir ve bu rolleri, Active Directory kiracısındaki tüm aboneliklerde hizmet sorumlularına uygulayabilir. Özel roller, kullanıcılara özel bir kaynak sağlayıcısı işlemleri kümesiyle RBAC rol tanımları oluşturmak için bir yol sağlar. Azure Cosmos DB için özel roller oluşturmaya yönelik hangi işlemlerin kullanılabildiğini öğrenmek için bkz. [Azure Cosmos DB kaynak sağlayıcısı işlemleri](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>Cosmos SDK 'dan değişiklikler engelleniyor

Cosmos kaynak sağlayıcısı, hesap anahtarları aracılığıyla bağlanan herhangi bir istemciden Cosmos hesabı, veritabanları, kapsayıcılar ve aktarım hızı (Cosmos SDK aracılığıyla bağlanan uygulamalar) dahil olmak üzere kaynaklarda değişiklik yapılmasını engellemek için kilitlenebilir. Ayarlandığında, herhangi bir kaynakta yapılan değişiklikler uygun RBAC rolüne ve kimlik bilgilerine sahip bir kullanıcıdan olmalıdır. Bu yetenek, Cosmos kaynak sağlayıcısında `disableKeyBasedMetadataWriteAccess` Özellik değeri ile ayarlanır. Bu özellik ayarıyla Azure Resource Manager şablona bir örnek aşağıda verilmiştir.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynakları için rol tabanlı erişim denetimi (RBAC) nedir?](../role-based-access-control/overview.md)
- [Azure kaynakları için özel roller](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB kaynak sağlayıcısı işlemleri](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
