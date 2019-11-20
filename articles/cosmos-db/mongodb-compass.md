---
title: Compass kullanarak Azure Cosmos DB'ye bağlanma
description: Azure Cosmos DB'de veri depolamak ve yönetmek için MongoDB Compass kullanmayı öğrenin.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 06/24/2019
author: roaror
ms.author: roaror
ms.openlocfilehash: 102d3fdc2e36f812e9a86286383a06f9930a1947
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67665925"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB'nin API'sine bağlanmak için MongoDB Compass kullanın 

Bu öğreticide nasıl kullanılacağını gösterir [MongoDB Compass](https://www.mongodb.com/products/compass) depolamak ve/veya verileri Cosmos DB'de yönetme olduğunda. Azure Cosmos DB API MongoDB için bu kılavuz için kullanırız. Bu size tanıdık GUI MongoDB için Compass olur. Yaygın olarak kullanıldığı verilerinizi görselleştirmek için verilerinizi yönetme yanı sıra geçici sorgular çalıştırın. 

Cosmos DB, Microsoft'un Global olarak dağıtılmış çok modelli veritabanı hizmetidir. Hızla oluşturun ve belge, anahtar/değer ve her biri genel dağıtım ve yatay ölçeklendirme özelliklerinden faydalanabilirsiniz Cosmos DB'nin grafik veritabanlarını sorgulama.


## <a name="pre-requisites"></a>Ön koşullar 
Robo 3T kullanma, Cosmos DB hesabınıza bağlanmak için şunları yapmalısınız:

* İndirme ve yükleme [Compass](https://www.mongodb.com/download-center/compass?jmp=hero)
* Cosmos DB sahip [bağlantı dizesi](connect-mongodb-account.md) bilgileri

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>MongoDB için Cosmos DB'nin API'sine bağlanma 
Çalışmak için pusula Cosmos DB hesabınıza bağlanmak için izleyebileceğiniz adımlar aşağıda:

1. Yönergeleri kullanarak Azure Cosmos DB'nin API'sini MongoDB ile yapılandırılmış Cosmos hesabınız için bağlantı bilgilerini almak [burada](connect-mongodb-account.md).

    ![Bağlantı dizesi dikey penceresinin ekran görüntüsü](./media/mongodb-compass/mongodb-compass-connection.png)

2. Bildiren düğmesine tıklayın **Panoya Kopyala** yanındaki, **birincil/ikincil bağlantı dizesi** Cosmos DB'de. Bu düğmeye tıklandığında, tüm bağlantı dizesini panonuza kopyalayın. 

    ![Pano düğmesini Kopyala görüntüsü](./media/mongodb-compass/mongodb-connection-copy.png)

3. Compass Masaüstü/makinenizde açın ve tıklayarak **Connect** ve ardından **bağlanın...** . 

4. Pusula, bağlantı otomatik olarak saptar dize panoya ve sormak, bağlanmak için kullanılacak isteyip istemediğinizi sorar. Tıklayarak **Evet** aşağıdaki ekran görüntüsünde gösterildiği gibi.

    ![Bağlanmak için Compass komut isteminin ekran görüntüsü](./media/mongodb-compass/mongodb-compass-detect.png)

5. Üzerine tıklayarak **Evet** ve Yukarıdaki adımda ayrıntılarınızı bağlantı dizesinden otomatik olarak doldurulur. İçinde otomatik olarak doldurulur değeri kaldırın **çoğaltma kümesi adı** diğer bir deyişle emin olmak için alanı boş kaldı. 

    ![Bağlanmak için Compass komut isteminin ekran görüntüsü](./media/mongodb-compass/mongodb-compass-replica.png)

6. Tıklayarak **Connect** sayfanın alt kısmındaki. Cosmos DB hesabınız ve veritabanları artık MongoDB Compass içinde görünür olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

- Bilgi edinmek için nasıl [Studio 3T kullanma](mongodb-mongochef.md) Azure Cosmos DB'nin MongoDB API'si ile.
- MongoDB keşfedin [örnekleri](mongodb-samples.md) Azure Cosmos DB'nin MongoDB API'si ile.