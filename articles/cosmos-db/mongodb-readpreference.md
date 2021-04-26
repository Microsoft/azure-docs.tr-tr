---
title: MongoDB için Azure Cosmos DB API 'SI ile okuma tercihi kullanın
description: MongoDB için Azure Cosmos DB API 'siyle MongoDB okuma tercihini nasıl kullanacağınızı öğrenin
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 02/26/2019
ms.custom: devx-track-js
ms.openlocfilehash: 3c78ad6605e927015d35df12cadf0347dd0337cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349053"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB MongoDB için API 'sini kullanarak genel olarak okuma dağıtımı
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Bu makalede, MongoDB için Azure Cosmos DB API 'sini kullanarak [MongoDB okuma tercihi](https://docs.mongodb.com/manual/core/read-preference/) ayarları ile okuma işlemlerinin genel olarak nasıl dağıtılacağı gösterilmektedir.

## <a name="prerequisites"></a>Ön koşullar 
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Genel dağıtım ile Cosmos hesabı ayarlamak için Azure portal kullanma hakkında yönergeler için bu [hızlı başlangıç](tutorial-global-distribution-mongodb.md) makalesine başvurun ve sonra buna bağlanın.

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Git bash gibi bir git terminal penceresi açın ve `cd` ile çalışma dizinine gidin.  

Örnek depoyu kopyalamak için aşağıdaki komutları çalıştırın. İlgilendiğiniz platforma bağlı olarak, aşağıdaki örnek depolardan birini kullanın:

1. [.NET örnek uygulaması](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [NodeJS örnek uygulaması]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongoose örnek uygulaması](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java örnek uygulaması](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot örnek uygulaması](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Kullanılan platforma bağlı olarak, gerekli paketleri yükleyip uygulamayı başlatın. Bağımlılıkları yüklemek için örnek uygulama deposuna dahil edilen Benioku dosyasını izleyin. Örneğin, NodeJS örnek uygulamasında, gerekli paketleri yüklemek ve uygulamayı başlatmak için aşağıdaki komutları kullanın.

```bash
cd mean
npm install
node index.js
```
Uygulama bir MongoDB kaynağına bağlanmaya çalışır ve bağlantı dizesi geçersiz olduğundan başarısız olur. Bağlantı dizesini güncelleştirmek için BENIOKU dosyasındaki adımları izleyin `url` . Ayrıca, öğesini `readFromRegion` Cosmos hesabınızdaki bir okuma bölgesine güncelleştirin. Aşağıdaki yönergeler NodeJS örneğinden alınır:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Bu adımları tamamladıktan sonra örnek uygulama çalışır ve aşağıdaki çıktıyı üretir:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Okuma tercihi modunu kullanarak okuma

MongoDB protokolü, istemcilerin kullanması için aşağıdaki okuma tercihi modlarını sağlar:

1. BIRINC
2. PRIMARY_PREFERRED
3. IK
4. SECONDARY_PREFERRED
5. ÝN

Bu okuma tercihi modlarının her birinin davranışıyla ilgili ayrıntılar için ayrıntılı [MongoDB okuma tercihi davranış](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) belgelerine bakın. Cosmos DB, birincil olarak yazma bölgesi ve ikincil haritalar ile, bölge okumak için eşlenir.

Yaygın senaryolara bağlı olarak, aşağıdaki ayarları kullanmanızı öneririz:

1. **Düşük gecikmeli okumalar** gerekliyse, **en yakın** okuma tercihi modunu kullanın. Bu ayar, okuma işlemlerini kullanılabilir en yakın bölgeye yönlendirir. En yakın bölge, yazma bölgedeyse, bu işlemlerin bu bölgeye yönlendirildiğini unutmayın.
2. **Yüksek kullanılabilirlik ve çok sayıda okumaların coğrafi dağıtımı** gerekliyse (gecikme bir kısıtlama değildir), ardından **birincil tercih EDILEN** veya **İkincil tercih edilen** okuma tercihi modunu kullanın. Bu ayar, okuma işlemlerini kullanılabilir bir yazma veya okuma bölgesine yönlendirir. Bölge kullanılamıyorsa, istekler okuma tercihi davranışına göre bir sonraki kullanılabilir bölgeye yönlendirilir.

Örnek uygulamadaki aşağıdaki kod parçacığında, NodeJS ' de en yakın okuma tercihini yapılandırma gösterilmektedir:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Benzer şekilde, aşağıdaki kod parçacığında NodeJS içinde SECONDARY_PREFERRED okuma tercihi 'nin nasıl yapılandırılacağı gösterilmektedir:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Okuma tercihi, `readPreference` bağlantı dizesi URI 'si seçeneklerinde parametre olarak geçirerek de ayarlanabilir:

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

[.Net](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) ve [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)gibi diğer platformlar için karşılık gelen örnek uygulama deposuna bakın.

## <a name="read-using-tags"></a>Etiketleri kullanarak okuma

MongoDB protokolü, okuma tercih moduna ek olarak, okuma işlemlerini yönlendirmek için etiketlerin kullanılmasına izin verir. MongoDB için Cosmos DB API 'sinde, `region` etiket varsayılan olarak yanıtın bir parçası olarak dahil edilir `isMaster` :

```json
"tags": {
         "region": "West US"
      }
```

Bu nedenle, MongoClient, `region` okuma işlemlerini belirli bölgelere yönlendirmek için bölge adıyla birlikte etiketini kullanabilir. Cosmos hesapları için, bölge adları, sol taraftaki ayarlar ' ın altında Azure portal, **genel olarak çoğaltma verileri >**' nde bulunabilir. Bu ayar, istemci uygulamanın okuma işlemlerini yalnızca belirli bir bölgeye yönlendirmek istediği **okuma yalıtımına** ulaşmak için yararlıdır. Bu ayar, arka planda çalıştırılan ve üretim açısından kritik hizmetler olmayan, üretim dışı/analiz türü senaryolar için idealdir.

Örnek uygulamadaki aşağıdaki kod parçacığında, NodeJS içindeki etiketlerle oku tercihinin nasıl yapılandırılacağı gösterilmektedir:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

[.Net](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) ve [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)gibi diğer platformlar için karşılık gelen örnek uygulama deposuna bakın.

Bu makalede, MongoDB için Azure Cosmos DB API 'SI ile okuma tercihini kullanarak okuma işlemlerini küresel olarak nasıl dağıtabileceğiniz hakkında bilgi edindiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekiz aşağıdaki adımlarla Azure portal Bu makalede oluşturulan tüm kaynakları silin:

1. Azure portalında sol taraftaki menüden, **Kaynak grupları**'na ve ardından oluşturduğunuz kaynağın adına tıklayın. 
2. Kaynak grubu sayfanızda, **Sil**'e tıklayın, metin kutusuna silinecek kaynağın adını yazın ve ardından **Sil**'e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB’ye MongoDB verileri aktarma](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
* [MongoDB için Azure Cosmos DB API 'SI ile küresel olarak dağıtılmış bir veritabanı kurma](tutorial-global-distribution-mongodb.md)
* [Azure Cosmos DB öykünücüsü ile yerel olarak geliştirme](local-emulator.md)