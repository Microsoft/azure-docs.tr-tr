---
title: Mongoose kullanarak angular uygulamasını MongoDB için Azure Cosmos DB API 'sine bağlama
description: Bu öğreticide, Cosmos DB depolanan verileri yönetmek için angular ve Express kullanarak bir Node.js uygulamasının nasıl oluşturulacağı açıklanmaktadır. Bu bölümde, Azure Cosmos DB bağlanmak için Mongoose kullanırsınız.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: a3097fa539f460ef5e8ffe73598fa5d55516717e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097813"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>MongoDB için Azure Cosmos DB API 'SI ile angular uygulaması oluşturma-Cosmos DB bağlanmak için Mongoose kullanın
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Bu çok parçalı öğreticide, Express ve angular ile bir Node.js uygulamasının nasıl oluşturulacağı ve bunu [MongoDB için Cosmos DB API 'siyle yapılandırılmış Cosmos hesabınıza](mongodb-introduction.md)nasıl bağlayacağı gösterilmektedir. Bu makalede öğreticinin 5. bölümü ve [4. Bölüm](tutorial-develop-mongodb-nodejs-part4.md)üzerinde derlemeler açıklanmaktadır.

Öğreticinin bu bölümünde şunları yapmanız gerekir:

> [!div class="checklist"]
> * Cosmos DB bağlanmak için Mongoose kullanın.
> * Cosmos DB Bağlantı dizenizi alın.
> * Hero modelini oluşturun.
> * Hero hizmetini, Hero verilerini almak için oluşturun.
> * Uygulamayı yerel olarak çalıştırın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

* Bu öğreticiye başlamadan önce [Bölüm 4](tutorial-develop-mongodb-nodejs-part4.md)' teki adımları izleyin.

* Bu öğretici, Azure CLı 'yı yerel olarak çalıştırmanızı gerektirir. Azure CLI 2.0 veya sonraki bir sürümünü yüklemiş olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Azure CLı 'yı yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure clı 2,0 'Yi yüklemek](/cli/azure/install-azure-cli).

* Bu öğretici, uygulama adımını adım adım oluşturmaya yönelik adımlarda size yol gösterir. Tamamlanmış projeyi indirmek isterseniz, tamamlanmış uygulamayı github'daki [angular-cosmosdb deposundan](https://github.com/Azure-Samples/angular-cosmosdb) alabilirsiniz.

## <a name="use-mongoose-to-connect"></a>Bağlanmak için Mongoose kullanma

Mongoose, MongoDB için bir nesne veri modelleme (ODM) kitaplığı ve Node.js. Mongoose kullanarak Azure Cosmos DB hesabınıza bağlanabilirsiniz. Mongoose yüklemek ve Azure Cosmos DB bağlanmak için aşağıdaki adımları kullanın:

1. Mongoose NPM modülünü, MongoDB ile konuşmak için kullanılan bir API 'yi yükler.

    ```bash
    npm i mongoose --save
    ```

1. **Sunucu** klasöründe **mongo.js** adlı bir dosya oluşturun. Azure Cosmos DB hesabınızın bağlantı ayrıntılarını bu dosyaya ekleyeceksiniz.

1. Aşağıdaki kodu **mongo.js** dosyasına kopyalayın. Kod aşağıdaki işlevleri sağlar:

   * Mongoose gerektirir.
   * ES6/ES2015 ve sonraki sürümlerde yerleşik olan temel taahhüdünü kullanmak için Mongo Promise 'i geçersiz kılar.
   * Hazırlama, üretim veya geliştirme aşamasında olup olmadığına göre belirli şeyleri ayarlamanıza olanak tanıyan bir env dosyası üzerindeki çağrılar. Sonraki bölümde bu dosyayı oluşturacaksınız.
   * Env dosyasında ayarlanan MongoDB bağlantı dizesini içerir.
   * Mongoose’u çağıran bir bağlanma işlevi oluşturur.

     ```javascript
     const mongoose = require('mongoose');
     /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
     */
     mongoose.Promise = global.Promise;

     const env = require('./env/environment');

     // eslint-disable-next-line max-len
     const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

     function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
     }

     module.exports = {
     connect,
     mongoose
     };
     ```
    
1. Gezgin bölmesinde, **sunucu** altında **ortam** adlı bir klasör oluşturun. **Ortam** klasöründe **environment.js** adlı bir dosya oluşturun.

1. mongo.js dosyasından,, ve parametrelerinin değerlerini içermesi gerekir `dbName` `key` `cosmosPort` . Aşağıdaki kodu **environment.js** dosyasına kopyalayın:

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>Bağlantı dizesini alma

Uygulamanızı Azure Cosmos DB bağlamak için, uygulamanın yapılandırma ayarlarını güncelleştirmeniz gerekir. Ayarları güncelleştirmek için aşağıdaki adımları kullanın: 

1. Azure portal, Azure Cosmos DB hesabınız için bağlantı noktası numarasını, Azure Cosmos DB hesap adını ve birincil anahtar değerlerini alın.

1. **environment.js** dosyasında, değerini `port` 10255 olarak değiştirin. 

    ```javascript
    const port = 10255;
    ```

1. **environment.js** dosyasında, değerini `accountName` öğreticinin [4. bölümünde](tutorial-develop-mongodb-nodejs-part4.md) oluşturduğunuz Azure Cosmos DB hesabının adına değiştirin. 

1. Terminal penceresinde aşağıdaki CLI komutunu kullanarak Azure Cosmos DB hesabı için birincil anahtarı alın: 

    ```azurecli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name> , öğreticinin [4. bölümünde](tutorial-develop-mongodb-nodejs-part4.md) oluşturduğunuz Azure Cosmos DB hesabının adıdır.

1. Birincil anahtarı değer olarak **environment.js** dosyasına kopyalayın `key` .

Artık uygulamanızda Azure Cosmos DB bağlanmak için gerekli tüm bilgiler vardır. 

## <a name="create-a-hero-model"></a>Hero modeli oluşturma

Daha sonra, bir model dosyası tanımlayarak Azure Cosmos DB içinde depolanacak verilerin şemasını tanımlamanız gerekir. Verilerin şemasını tanımlayan bir _Hero modeli_ oluşturmak için aşağıdaki adımları kullanın:

1. Gezgin bölmesinde, **sunucu** klasörü altında **hero.model.js** adlı bir dosya oluşturun.

1. Aşağıdaki kodu **hero.model.js** dosyasına kopyalayın. Kod aşağıdaki işlevleri sağlar:

   * Mongoose gerektirir.
   * Bir kimliği, adı ve deyişi olan yeni bir şema oluşturur.
   * Şemayı kullanarak bir model oluşturur.
   * Modeli dışarı aktarır. 
   * Koleksiyon **Heroes** 'Yi (Mongoose çoğul adlandırma kurallarına göre koleksiyonun varsayılan adı olan **Heros** yerine) adlandırır.

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Hero hizmeti oluşturma

Hero modelini oluşturduktan sonra, verileri okumak için bir hizmet tanımlamanız ve liste, oluşturma, silme ve güncelleştirme işlemleri gerçekleştirmeniz gerekir. Azure Cosmos DB verileri sorgulayan bir _Hero hizmeti_ oluşturmak için aşağıdaki adımları kullanın:

1. Gezgin bölmesinde, **sunucu** klasörü altında **hero.service.js** adlı bir dosya oluşturun.

1. Aşağıdaki kodu **hero.service.js** dosyasına kopyalayın. Kod aşağıdaki işlevleri sağlar:

   * Oluşturduğunuz modeli alır.
   * Veritabanına bağlanır.
   * `docquery` `hero.find` Tüm Heroes döndüren bir sorgu tanımlamak için yöntemini kullanan bir değişken oluşturur.
   * `docquery.exec`Yanıt durumunun 200 olduğu Heroes 'nin bir listesini almak için bir taahhütle birlikte bir sorgu çalıştırır. 
   * Durum 500 ise hata iletisini geri gönderir.
   * Modüller kullandığından Heroes 'yi alır. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="configure-routes"></a>Rotaları yapılandırma

Sonra get, Create, Read ve DELETE isteklerinin URL 'Lerini işlemek için yollar ayarlamanız gerekir. Yönlendirme yöntemleri, geri çağırma işlevlerini belirtir (Ayrıca, _işleyici işlevleri_ olarak da adlandırılır). Bu işlevler, uygulama belirtilen uç noktaya ve HTTP yöntemine bir istek aldığında çağrılır. Hero hizmetini eklemek ve rotalarınızı tanımlamak için aşağıdaki adımları kullanın:

1. Visual Studio Code **routes.js** dosyasında, `res.send` örnek Hero verilerini gönderen işlevi not edin. Bunun yerine işlevi çağırmak için bir satır ekleyin `heroService.getHeroes` .

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. **routes.js** dosyasında `require` Hero hizmeti:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. **hero.service.js** dosyasında, `getHeroes` işlevi `req` ve `res` parametreleri aşağıdaki gibi olacak şekilde güncelleştirin:

    ```javascript
    function getHeroes(req, res) {
    ```

Önceki kodu gözden geçirmek ve izlenecek bir dakikanızı atalım. İlk olarak, düğüm sunucusunu ayarlayan index.js dosyasına geldik. Bu bildirimin, rotalarınızı ayarladığına ve tanımladığına dikkat edin. Sonra, routes.js dosyanız Hero hizmetine geçer ve bu hizmete, **Getheroes** gibi işlevlerinizi almasını ve isteği ve yanıtı ileteceğini söyler. hero.service.js dosyası modeli alır ve Mongo 'ya bağlanır. Bu durumda, çağrı yapıldığında **Getheroes** yürütülür ve 200 yanıtını geri döndürür. 

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Ardından, aşağıdaki adımları kullanarak uygulamayı çalıştırın:

1. Visual Studio Code, tüm değişikliklerinizi kaydedin. Sol tarafta **Hata Ayıkla** düğmesini seçin :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png"::: ve ardından **hata ayıklamayı Başlat** düğmesini seçin :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png"::: .

1. Şimdi tarayıcıya geçiş yapın. **Geliştirici araçlarını** ve **Ağ sekmesini** açın. ' A gidin `http://localhost:3000` ve uygulamamızı görürsünüz.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png" alt-text="Azure Portal’daki yeni Azure Cosmos DB hesabı":::

Uygulamada henüz depolanan Heroes yok. Bu öğreticinin sonraki bölümünde put, push ve delete işlevleri ekleyeceğiz. Daha sonra Azure Cosmos veritabanınıza Mongoose bağlantılarını kullanarak kullanıcı arabiriminden Hero 'Ları ekleyebilir, güncelleştirebilir ve silebilirsiniz. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynaklara artık ihtiyacınız kalmadığında, kaynak grubunu, Azure Cosmos DB hesabı ve tüm ilgili kaynakları silebilirsiniz. Kaynak grubunu silmek için aşağıdaki adımları kullanın:

 1. Azure Cosmos DB hesabı oluşturduğunuz kaynak grubuna gidin.
 1. **Kaynak grubunu sil**'i seçin.
 1. Silinecek kaynak grubunun adını onaylayın ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Uygulamaya Post, put ve delete işlevleri eklemek için öğreticinin 6. bölümüne ilerleyin:

> [!div class="nextstepaction"]
> [6. Bölüm: uygulamaya gönderi, put ve delete işlevleri ekleme](tutorial-develop-mongodb-nodejs-part6.md)