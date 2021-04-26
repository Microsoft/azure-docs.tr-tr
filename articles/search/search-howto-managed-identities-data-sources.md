---
title: Yönetilen kimlik kullanarak bir veri kaynağına bağlantı ayarlama
titleSuffix: Azure Cognitive Search
description: Yönetilen kimlik kullanarak bir veri kaynağına Dizin Oluşturucu bağlantısı ayarlamayı öğrenin
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 95f1c4bf9b599da8285ac69e299549e5aa73c2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519597"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity"></a>Yönetilen kimlik kullanarak veri kaynağına bir Dizin Oluşturucu bağlantısı kurma

> [!IMPORTANT] 
> Yönetilen kimlik kullanarak bir veri kaynağına bağlantı kurulması, ücretsiz Azure Bilişsel Arama katmanında desteklenmez.

Azure Bilişsel Arama 'de [Dizin Oluşturucu](search-indexer-overview.md) , veri kaynağınızdan Azure bilişsel arama veri çekmek için bir yol sağlayan bir gezgin. Dizin Oluşturucu, oluşturduğunuz veri kaynağı nesnesinden bir veri kaynağı bağlantısı edinir. Veri kaynağı nesnesi genellikle hedef veri kaynağı için kimlik bilgilerini içerir. Örneğin, bir BLOB depolama kapsayıcısından veri indekslemek istiyorsanız veri kaynağı nesnesi bir Azure depolama hesabı anahtarı içerebilir.

Çoğu durumda doğrudan veri kaynağı nesnesinde kimlik bilgileri sağlamaya bir sorun yoktur, ancak bu durum ortaya çıkabilir.
* Nasıl yaparım? veri kaynağı nesnesini oluşturan kodumdaki kimlik bilgilerini güvende tutun mi?
* Hesap anahtarım veya parolam tehlikeye atılırsa ve bunu değiştirmem gerekiyorsa, Dizin oluşturucuumun veri kaynağına yeniden bağlanabilmesi için artık veri kaynağı nesnelerimi yeni hesap anahtarı veya parolasıyla güncelleştirmem gerekiyor.

Bu sorunlar, yönetilen bir kimlik kullanılarak bağlantınız ayarlanarak çözülebilir.

## <a name="using-managed-identities"></a>Yönetilen kimlikleri kullanma

[Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) , azure hizmetlerini Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen kimlik sağlayan bir özelliktir. Bu özelliği, kimlik bilgileri içermeyen bir bağlantı dizesiyle bir veri kaynağı nesnesi oluşturmak için Azure Bilişsel Arama ' de kullanabilirsiniz. Bunun yerine, arama hizmetinize Azure rol tabanlı erişim denetimi (Azure RBAC) aracılığıyla veri kaynağına erişim izni verilir.

Yönetilen kimlik kullanarak bir veri kaynağı ayarlarken, veri kaynağı kimlik bilgilerinizi değiştirebilirsiniz ve Dizin oluşturucularınız yine de veri kaynağına bağlanabilir. Ayrıca, bir hesap anahtarı dahil etmek veya bir hesap anahtarı almak için Key Vault kullanabilmeniz gerekmeden kodunuzda veri kaynağı nesneleri de oluşturabilirsiniz.

## <a name="limitations"></a>Sınırlamalar

Aşağıdaki veri kaynakları yönetilen kimlikleri kullanarak bir Dizin Oluşturucu bağlantısı ayarlamayı destekler. 

* [Azure Blob depolama, Azure Data Lake Storage 2. (Önizleme), Azure Tablo depolama](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Veritabanı](search-howto-managed-identities-sql.md)

Aşağıdaki özellikler Şu anda bağlantıyı kurmak için yönetilen kimlikleri kullanmayı desteklememektedir:
* Bilgi Deposu
* Özel beceriler
 
## <a name="next-steps"></a>Sonraki adımlar

Yönetilen kimlikleri kullanarak Dizin Oluşturucu bağlantısı ayarlama hakkında daha fazla bilgi edinin:

* [Azure Blob depolama, Azure Data Lake Storage 2. (Önizleme), Azure Tablo depolama](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Veritabanı](search-howto-managed-identities-sql.md)