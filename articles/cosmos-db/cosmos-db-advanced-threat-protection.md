---
title: Azure Cosmos DB için Gelişmiş tehdit koruması
description: Azure Cosmos DB, bekleyen verilerin şifrelenmesi ve nasıl uygulandığı hakkında bilgi edinin.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: 57303c41d90f6205c284c7de67bb8643aa3cd368
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888062"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db"></a>Azure Cosmos DB için Gelişmiş tehdit koruması

Azure Cosmos DB için Gelişmiş tehdit koruması, Azure Cosmos DB hesaplara yönelik olağan dışı ve potansiyel olarak zararlı girişimleri algılayan ek bir güvenlik zekası katmanı sağlar. Bu koruma katmanı, güvenlik uzmanı olmadan bile tehditleri ele almanıza ve bunları Merkezi güvenlik izleme sistemleriyle tümleştirmenize olanak tanır.

Güvenlik uyarıları, etkinlik durumunda olan bozukluklar gerçekleştiğinde tetiklenir. Bu güvenlik uyarıları [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)ile tümleşiktir ve ayrıca, şüpheli etkinliğin ayrıntıları ve tehditleri İnceleme ve düzeltme önerileri ile abonelik yöneticilerine e-posta yoluyla da gönderilir.

> [!NOTE]
>
> * Azure Cosmos DB için Gelişmiş tehdit koruması Şu anda yalnızca SQL API 'SI için kullanılabilir.
> * Azure Cosmos DB için Gelişmiş tehdit koruması Şu anda Azure Kamu ve bağımsız bulut bölgelerinde kullanılamaz.

Güvenlik uyarılarının tam araştırma deneyimi için, tüm belgeler, kapsayıcılar ve veritabanlarına yönelik CRUD işlemleri de dahil olmak üzere, veritabanı üzerindeki işlemleri günlüğe kaydeden [Azure Cosmos DB tanılama günlüğünü](https://docs.microsoft.com/azure/cosmos-db/logging)etkinleştirmeyi öneririz.

## <a name="set-up-advanced-threat-protection"></a>Gelişmiş tehdit koruması ayarlama

### <a name="set-up-atp-using-the-portal"></a>Portalı kullanarak ATP ayarlama

1. [https://portal.azure.com](https://portal.azure.com/)Azure Portal başlatın.

2. Azure Cosmos DB hesabından, **Ayarlar** menüsünden **Gelişmiş güvenlik**' i seçin.

    ![ATP ayarla](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. **Gelişmiş güvenlik** yapılandırması dikey penceresinde:

    * **Gelişmiş tehdit koruması** seçeneğine tıklayarak **Açık**olarak ayarlayın.
    * Yeni veya güncelleştirilmiş Gelişmiş tehdit koruması ilkesini kaydetmek için **Kaydet** ' e tıklayın.   

### <a name="set-up-atp-using-rest-api"></a>REST API kullanarak ATP ayarlama

Belirli bir Azure Cosmos DB hesabına yönelik gelişmiş tehdit koruması ayarını oluşturmak, güncelleştirmek veya almak için REST API komutlarını kullanın.

* [Gelişmiş tehdit koruması-oluştur](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Gelişmiş tehdit koruması-Get](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Azure PowerShell kullanarak ATP ayarlama

Aşağıdaki PowerShell cmdlet 'lerini kullanın:

* [Gelişmiş tehdit korumasını etkinleştir](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Gelişmiş tehdit koruması al](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Gelişmiş tehdit korumasını devre dışı bırak](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager şablonları kullanma

Gelişmiş tehdit koruması etkinleştirilmiş Cosmos DB ayarlamak için bir Azure Resource Manager şablonu kullanın.
Daha fazla bilgi için bkz. [Gelişmiş tehdit koruması Ile CosmosDB hesabı oluşturma](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="using-azure-policy"></a>Azure Ilkesini kullanma

Cosmos DB için Gelişmiş tehdit koruması 'nı etkinleştirmek üzere bir Azure Ilkesi kullanın.

1. Azure **Ilke tanımları** sayfasını başlatın ve **Cosmos DB Ilkesi Için Gelişmiş tehdit koruması dağıt** ' ı arayın.

    ![Ilke ara](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. **CosmosDB Için Gelişmiş tehdit koruması dağıtma** İlkesi ' ne tıklayın ve ardından **ata**' ya tıklayın.

    ![Abonelik veya grup seçin](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. **Kapsam** alanından üç noktaya tıklayın, bir Azure aboneliği veya kaynak grubu seçin ve ardından **Seç**' e tıklayın.

    ![İlke tanımları sayfası](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Diğer parametreleri girip **ata**' ya tıklayın.

## <a name="manage-atp-security-alerts"></a>ATP güvenlik uyarılarını yönetme

Azure Cosmos DB etkinlik bozuklukları gerçekleştiğinde, şüpheli güvenlik olayı hakkında bilgi içeren bir güvenlik uyarısı tetiklenir. 

 Azure Güvenlik Merkezi 'nde geçerli [güvenlik uyarılarınızı](../security-center/security-center-alerts-overview.md)gözden geçirebilir ve yönetebilirsiniz.  Olası nedenleri ve olası tehdidi araştırmak ve azaltmak için önerilen eylemleri görüntülemek için [Güvenlik Merkezi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) 'nde belirli bir uyarıya tıklayın. Aşağıdaki görüntüde, güvenlik merkezi 'nde sunulan bir uyarı ayrıntıları örneği gösterilmektedir.

 ![Tehdit ayrıntıları](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Uyarı ayrıntıları ve önerilen eylemlerle birlikte bir e-posta bildirimi de gönderilir. Aşağıdaki görüntüde bir uyarı e-postası örneği gösterilmektedir.

 ![Uyarı ayrıntıları](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>ATP uyarılarını Cosmos DB

 Azure Cosmos DB hesaplarını izlerken oluşturulan uyarıların listesini görmek için, güvenlik merkezi belgelerindeki [Cosmos DB uyarılar](../security-center/security-center-alerts-data-services.md#cosmos-db) bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Tanılama günlüğü](https://docs.microsoft.com/azure/cosmos-db/logging#turn-on-logging-in-the-azure-portal) hakkında daha fazla bilgi edinin Azure Cosmos DB
* [Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/security-center/security-center-intro) hakkında daha fazla bilgi edinin
