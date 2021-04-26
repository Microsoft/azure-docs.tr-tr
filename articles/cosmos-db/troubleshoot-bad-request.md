---
title: Hatalı istek özel durumları Azure Cosmos DB sorun giderme
description: Giriş içeriği veya bölüm anahtarı gibi hatalı istek özel durumlarını tanılamaya ve gidermeye, Bölüm anahtarının Azure Cosmos DB ile eşleşmemesi hakkında bilgi edinin.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 04/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e0ce00331eb524d064fd6e7c5205be8b66d4dbc2
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556190"
---
# <a name="diagnose-and-troubleshoot-bad-request-exceptions-in-azure-cosmos-db"></a>Azure Cosmos DB hatalı istek özel durumlarını Tanıla ve sorun giderme
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP durum kodu 400, isteği geçersiz veriler içeriyor veya gerekli parametreler eksik.

## <a name="missing-the-id-property"></a><a name="missing-id-property"></a>ID özelliği eksik
Bu senaryoda, hatayı görmek yaygındır:

*Gerekli özellikler olduğundan (' kimlik; ') giriş içeriği geçersiz '-eksik*

Bu hatayla bir yanıt, hizmete gönderilen JSON belgesinde gerekli KIMLIK özelliği olmadığı anlamına gelir.

### <a name="solution"></a>Çözüm
`id`Belgenizin bir parçası olarak [rest belirtimine](/rest/api/cosmos-db/documents) göre dize değeri olan bir özellik belirtin. SDK 'lar, bu özellik için değerleri otomatik olarak içermez.

## <a name="invalid-partition-key-type"></a><a name="invalid-partition-key-type"></a>Geçersiz bölüm anahtarı türü
Bu senaryoda, aşağıdaki gibi hataları görmek yaygındır:

*Bölüm anahtarı... geçersiz.*

Bu hatayla bir yanıt, bölüm anahtar değerinin geçersiz bir tür olduğu anlamına gelir.

### <a name="solution"></a>Çözüm
Bölüm anahtarının değeri bir dize veya sayı olmalıdır, değerin beklenen türlerde olduğundan emin olun.

## <a name="wrong-partition-key-value"></a><a name="wrong-partition-key-value"></a>Yanlış bölüm anahtarı değeri
Bu senaryoda, hatayı görmek yaygındır:

*Belgeden ayıklanan PartitionKey, üst bilgide belirtilen bir ile eşleşmiyor*

Bu hatayla bir yanıt, bir işlemi yürüttüğünüz ve beklenen özellik için belgenin gövde değeriyle eşleşmeyen bir bölüm anahtarı değeri geçirmekte olduğunuz anlamına gelir. Koleksiyonun bölüm anahtarı yolu ise `/myPartitionKey` , belgede `myPartitionKey` SDK yöntemi çağrılırken bölüm anahtarı değeri olarak sağlanmayan değerle eşleşmeyen bir değer ile çağrılan bir özellik vardır.

### <a name="solution"></a>Çözüm
Belge özelliği değeri ile eşleşen bölüm anahtarı değer parametresini gönderin.

## <a name="next-steps"></a>Sonraki adımlar
* Azure Cosmos DB .NET SDK kullandığınızda sorunları [tanılayın ve sorun giderin](troubleshoot-dot-net-sdk.md) .
* [.Net v3](performance-tips-dotnet-sdk-v3-sql.md) ve [.net v2](performance-tips.md)için performans yönergeleri hakkında bilgi edinin.
* Java v4 SDK Azure Cosmos DB kullandığınızda sorunları [tanılayın ve sorun giderin](troubleshoot-java-sdk-v4-sql.md) .
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md)için performans yönergeleri hakkında bilgi edinin.
