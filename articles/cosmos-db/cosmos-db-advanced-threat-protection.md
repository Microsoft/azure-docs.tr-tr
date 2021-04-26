---
title: Azure Cosmos DB için Gelişmiş tehdit koruması
description: Azure Cosmos DB, bekleyen verilerin şifrelenmesi ve nasıl uygulandığı hakkında bilgi edinin.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: b73e7f8c13f621bc359a2ae79a725829420a3ecc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455623"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Azure Cosmos DB için Gelişmiş tehdit koruması (Önizleme)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB için Gelişmiş tehdit koruması, Azure Cosmos DB hesaplara yönelik olağan dışı ve potansiyel olarak zararlı girişimleri algılayan ek bir güvenlik zekası katmanı sağlar. Bu koruma katmanı, güvenlik uzmanı olmadan bile tehditleri ele almanıza ve bunları Merkezi güvenlik izleme sistemleriyle tümleştirmenize olanak tanır.

Etkinlikte anomali oluştuğunda güvenlik uyarıları tetiklenir. Bu güvenlik uyarıları  [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)ile tümleşiktir ve ayrıca, şüpheli etkinliğin ayrıntıları ve tehditleri İnceleme ve düzeltme önerileri ile abonelik yöneticilerine e-posta yoluyla da gönderilir.

> [!NOTE]
>
> * Azure Cosmos DB için Gelişmiş tehdit koruması Şu anda yalnızca SQL API 'SI için kullanılabilir.
> * Azure Cosmos DB için Gelişmiş tehdit koruması Şu anda Azure Kamu ve bağımsız bulut bölgelerinde kullanılamaz.

Güvenlik uyarılarının tam araştırma deneyimi için, tüm belgeler, kapsayıcılar ve veritabanlarına yönelik CRUD işlemleri de dahil olmak üzere, veritabanı üzerindeki işlemleri günlüğe kaydeden [Azure Cosmos DB tanılama günlüğünü](./monitor-cosmos-db.md)etkinleştirmeyi öneririz.

## <a name="threat-types"></a>Tehdit türleri

Azure Cosmos DB için Gelişmiş tehdit koruması, veritabanları için olağandışı ve potansiyel olarak zararlı girişimleri gösteren anormal etkinlikleri algılar. Şu anda şu uyarıları tetikleyebilirler:

- **Olağan dışı konumlardan erişim**: Bu uyarı, bir Azure Cosmos hesabına yönelik erişim modelinde bir değişiklik olduğunda tetiklenir ve bu, birisinin olağan dışı bir coğrafi konumdan Azure Cosmos DB uç noktasına bağlandığı yerdir. Bazı durumlarda, uyarı yeni bir uygulama veya geliştiricinin bakım işlemini ifade eden yasal bir eylemi algılar. Diğer durumlarda, uyarı eski bir çalışan, dış saldırgan vb. için kötü amaçlı bir eylem algılar.

- **Olağan dışı veri ayıklama**: Bu uyarı, bir istemci bir Azure Cosmos DB hesabından olağan dışı miktarda veri ayıkladığında tetiklenir. Bu, hesapta depolanan tüm verilerin bir dış veri deposuna aktarılması için gerçekleştirilen bazı veri dışlanan belirtisi olabilir.



## <a name="configure-advanced-threat-protection"></a>Gelişmiş Tehdit Koruması'nı yapılandırma

Gelişmiş tehdit korumasını aşağıdaki bölümlerde açıklanan çeşitli yollarla yapılandırabilirsiniz.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Üzerinde Azure portal başlatın  [https://portal.azure.com](https://portal.azure.com/) .

2. Azure Cosmos DB hesabından, **Ayarlar** menüsünden **Gelişmiş güvenlik**' i seçin.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png" alt-text="ATP ayarla":::

3. **Gelişmiş güvenlik** yapılandırması dikey penceresinde:

    * **Gelişmiş tehdit koruması** seçeneğine tıklayarak **Açık** olarak ayarlayın.
    * Yeni veya güncelleştirilmiş Gelişmiş Tehdit Koruması ilkesini kaydetmek için **Kaydet**’e tıklayın.   

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Belirli bir Azure Cosmos DB hesabına yönelik gelişmiş tehdit koruması ayarını oluşturmak, güncelleştirmek veya almak için REST API komutlarını kullanın.

* [Gelişmiş tehdit koruması-oluştur](/rest/api/securitycenter/advancedthreatprotection/create)
* [Gelişmiş tehdit koruması-Get](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aşağıdaki PowerShell cmdlet 'lerini kullanın:

* [Gelişmiş Tehdit Koruması'nı etkinleştirme](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Gelişmiş tehdit koruması al](/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Gelişmiş tehdit korumasını devre dışı bırak](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

### <a name="arm-template"></a>[ARM şablonu](#tab/arm-template)

Gelişmiş tehdit koruması etkinleştirilmiş Cosmos DB ayarlamak için bir Azure Resource Manager (ARM) şablonu kullanın.
Daha fazla bilgi için bkz. [Gelişmiş tehdit koruması Ile CosmosDB hesabı oluşturma](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="azure-policy"></a>[Azure İlkesi](#tab/azure-policy)

Cosmos DB için Gelişmiş tehdit koruması 'nı etkinleştirmek üzere bir Azure Ilkesi kullanın.

1. Azure **Ilke tanımları** sayfasını başlatın ve **Cosmos DB Ilkesi Için Gelişmiş tehdit koruması dağıt** ' ı arayın.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db.png" alt-text="Ilke ara"::: 

1. **CosmosDB Için Gelişmiş tehdit koruması dağıtma** İlkesi ' ne tıklayın ve ardından **ata**' ya tıklayın.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png" alt-text="Abonelik veya grup seçin":::


1. **Kapsam** alanından üç noktaya tıklayın, bir Azure aboneliği veya kaynak grubu seçin ve ardından **Seç**' e tıklayın.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png" alt-text="İlke tanımları sayfası":::


1. Diğer parametreleri girip **ata**' ya tıklayın.




## <a name="manage-atp-security-alerts"></a>ATP güvenlik uyarılarını yönetme

Azure Cosmos DB etkinlik bozuklukları gerçekleştiğinde, şüpheli güvenlik olayı hakkında bilgi içeren bir güvenlik uyarısı tetiklenir. 

 Azure Güvenlik Merkezi 'nde geçerli [güvenlik uyarılarınızı](../security-center/security-center-alerts-overview.md)gözden geçirebilir ve yönetebilirsiniz.  Olası nedenleri ve olası tehdidi araştırmak ve azaltmak için önerilen eylemleri görüntülemek için [Güvenlik Merkezi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) 'nde belirli bir uyarıya tıklayın. Aşağıdaki görüntüde, güvenlik merkezi 'nde sunulan bir uyarı ayrıntıları örneği gösterilmektedir.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png" alt-text="Tehdit ayrıntıları":::

Uyarı ayrıntıları ve önerilen eylemlerle birlikte bir e-posta bildirimi de gönderilir. Aşağıdaki görüntüde bir uyarı e-postası örneği gösterilmektedir.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png" alt-text="Uyarı ayrıntıları":::

## <a name="cosmos-db-atp-alerts"></a>ATP uyarılarını Cosmos DB

 Azure Cosmos DB hesaplarını izlerken oluşturulan uyarıların listesini görmek için, Azure Güvenlik Merkezi belgelerindeki [Cosmos DB uyarılar](../security-center/alerts-reference.md#alerts-azurecosmos) bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Tanılama günlüğü](cosmosdb-monitor-resource-logs.md) hakkında daha fazla bilgi edinin Azure Cosmos DB
* [Azure Güvenlik Merkezi](../security-center/security-center-introduction.md) hakkında daha fazla bilgi edinin