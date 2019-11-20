---
title: Tablo API'si için Azure Cosmos DB küresel dağıtım öğreticisi
description: Tablo API’sini kullanarak Azure Cosmos DB genel dağıtımını ayarlamayı öğrenin.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/13/2018
ms.reviewer: sngun
ms.openlocfilehash: 8562d37d81ce02e150e6ad1cc2a440cf7bb1e5e3
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693350"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Tablo API’sini kullanarak Azure Cosmos DB genel dağıtımını ayarlama

Bu makale aşağıdaki görevleri kapsar: 

> [!div class="checklist"]
> * Azure portalını kullanarak genel dağıtımı yapılandırma
> * [Tablo API’sini](table-introduction.md) kullanarak genel dağıtımı yapılandırma

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Tablo API’sini kullanarak tercih edilen bir bölgeye bağlanma

[Genel dağıtımdan](distribute-data-globally.md) yararlanmak için istemci uygulamaları, belge işlemlerini gerçekleştirmek için kullanılacak bölgelerin tercihe göre sıralanmış listesini belirtebilir. [TableConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet) özelliği ayarlanarak bu yapılabilir. Azure Cosmos DB Tablo API’si SDK’sı, hesap yapılandırmasına, geçerli bölgesel kullanılabilirliğe ve sağlanan tercih listesine göre iletişim kurmak için en iyi uç noktayı seçer.

PreferredLocations, okuma için tercih edilen (çok girişli) konumların virgülle ayrılmış listesini içermelidir. Her istemci düşük gecikme okumaları için tercih edilen sırayla bu bölgelerin bir alt kümesini belirtebilir. Bölgeler [görünen adları](https://msdn.microsoft.com/library/azure/gg441293.aspx) kullanılarak adlandırılmalıdır, örneğin `West US`.

Tüm okuma işlemleri, PreferredLocations listesindeki ilk kullanılabilir bölgeye gönderilir. İstek başarısız olursa, istemci listedeki sonraki bölgeyi dener ve bu şekilde devam eder.

SDK, PreferredLocations listesinde belirtilen bölgelerden okuma işlemi yapmaya çalışır. Bu nedenle, örneğin, Veritabanı Hesabı dört bölgede kullanılabiliyorsa ancak istemci, PreferredLocations için yalnızca iki yazma bölgesi belirtiyorsa, yük devretme durumunda bile yazma bölgesinden okuma işlemi yapılmaz.

SDK, tüm yazma işlemlerini otomatik olarak geçerli yazma bölgesine gönderir.

PreferredLocations özelliği ayarlanmazsa, tüm istekler geçerli yazma bölgesinden işlenir.

Hepsi bu kadar. Böylece bu öğretici tamamlanmış olur. [Azure Cosmos DB’deki tutarlılık düzeyleri](consistency-levels.md) bölümünü okuyarak genel olarak çoğaltılan hesabınızın tutarlılığının nasıl yönetileceğini öğrenebilirsiniz. Ayrıca genel veritabanı çoğaltmasının Azure Cosmos DB’de nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure Cosmos DB ile verileri genel olarak dağıtma](distribute-data-globally.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdakileri yaptınız:

> [!div class="checklist"]
> * Azure portalını kullanarak genel dağıtımı yapılandırma
> * Azure Cosmos DB Tablo API’lerini kullanarak genel dağıtımı yapılandırma

