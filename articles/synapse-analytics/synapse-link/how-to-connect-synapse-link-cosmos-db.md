---
title: Azure Cosmos DB için Azure Synapse Link’e bağlanma
description: Azure SYNAPSE bağlantısıyla bir Azure Cosmos DB veritabanını Azure SYNAPSE çalışma alanına bağlamayı öğrenin.
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 9a73ea697a48a5b2514f4701b8a24896e9a04c1f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627702"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Azure Cosmos DB için Azure Synapse Link’e bağlanma

Bu makalede Azure SYNAPSE bağlantısıyla Azure SYNAPSE Analytics Studio 'dan bir Azure Cosmos DB veritabanına nasıl erişebileceğiniz açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Azure Cosmos DB bir veritabanını çalışma alanınıza bağlanmadan önce şunları yapmanız gerekir:

* Mevcut Azure Cosmos DB veritabanı veya [hızlı başlangıç: bir Azure Cosmos DB hesabını yönetme](../../cosmos-db/how-to-manage-database-account.md)bölümündeki adımları izleyerek yeni bir hesap oluşturun.
* Mevcut Azure SYNAPSE çalışma alanı veya [hızlı başlangıç: SYNAPSE çalışma alanı oluşturma](../quickstart-create-workspace.md)bölümündeki adımları izleyerek yeni bir çalışma alanı oluşturun.

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>Azure Cosmos DB veritabanı hesabında SYNAPSE bağlantısını etkinleştir

Büyük ölçekli analizlerinizi işletimsel performansınızı etkilemeden Azure Cosmos DB olarak çalıştırmak için, Azure Cosmos DB için SYNAPSE bağlantısının etkinleştirilmesini öneririz. SYNAPSE link, Azure SYNAPSE 'de bir kapsayıcıya ve yerleşik desteğe HTAP özelliği sunar.

## <a name="go-to-synapse-studio"></a>SYNAPSE Studio 'ya git

Azure SYNAPSE çalışma alanınızdan **SYNAPSE Studio 'Yu Başlat**' ı seçin. SYNAPSE Studio giriş sayfasında, size veri Nesne Gezgini alan **veri**' yi seçin.

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>Azure Cosmos DB veritabanını bir Azure SYNAPSE çalışma alanına bağlama

Azure Cosmos DB bir veritabanını bağlama, bağlantılı hizmet olarak yapılır. Azure Cosmos DB bağlı bir hizmetle, Azure SYNAPSE Analytics veya SQL için Apache Spark Azure Cosmos DB 'e göz atabilir ve bu verileri okuyabilir ve yazabilirsiniz.

Veri Nesne Gezgini, aşağıdaki adımları izleyerek bir Azure Cosmos DB veritabanına doğrudan bağlanabilirsiniz:

1. **+** **Verilerin** yakınında bulunan simgeyi seçin.
1. **Dış verilere bağlan**' ı seçin.
1. Bağlanmak istediğiniz API 'yi (örneğin, **SQL API** veya **MongoDB için API**) seçin.
1. **Devam**’ı seçin.
1. Bağlı hizmeti adlandırmak için kolay bir ad kullanın. Ad, veri Nesne Gezgini görünür ve Azure SYNAPSE çalışma zamanları tarafından veritabanına ve kapsayıcılara bağlanmak için kullanılır.
1. **Azure Cosmos DB hesap adını** ve **veritabanı adını** seçin.
1. Seçim Hiçbir bölge belirtilmemişse, Azure SYNAPSE çalışma zamanı işlemleri analitik deponun etkinleştirildiği en yakın bölgeye yönlendirilir. Ayrıca kullanıcılarınızın Azure Cosmos DB analitik depoya erişmek için kullanmasını istediğiniz bölgeyi el ile ayarlayabilirsiniz. **Ek bağlantı özellikleri**' ni seçin ve ardından **Yeni**' yi seçin. **Özellik adı** altında, **preferredregion** yazın. **Değeri** istediğiniz bölgeye ayarlayın, örneğin, **WestUS2**. (Sözcükler ve sayı arasında boşluk yoktur.)
1. **Oluştur**’u seçin.

Azure Cosmos DB veritabanları, **Azure Cosmos DB** bölümünün altındaki **bağlı** sekmede görüntülenir. Azure Cosmos DB ile, bir HTAP etkin kapsayıcıyı aşağıdaki simgelerle yalnızca OLTP kapsayıcılarından ayırt edebilirsiniz:

**Yalnızca OLTP kapsayıcısı**:

![OLTP kapsayıcı simgesini gösteren görselleştirme.](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Htap etkin kapsayıcı**:

![HTAP kapsayıcı simgesini gösteren görselleştirme.](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Kod tarafından oluşturulan eylemlerle hızlıca etkileşim kurun

Bir kapsayıcıya sağ tıklayarak Spark veya SQL çalışma zamanını tetikleyecek bir hareket listeniz olur. Bir kapsayıcıya yazmak Azure Cosmos DB Işlem deposu aracılığıyla gerçekleşir ve Istek birimlerini kullanır.  

## <a name="next-steps"></a>Sonraki adımlar

* [Azure SYNAPSE ve Azure Cosmos DB arasında nelerin desteklendiğini öğrenin](./concept-synapse-link-cosmos-db-support.md)
* [Azure Cosmos DB analitik depo için özel uç noktaları yapılandırma](../../cosmos-db/analytical-store-private-endpoints.md)
* [Spark ile analitik depoyu sorgulama hakkında bilgi edinin](./how-to-query-analytical-store-spark.md)