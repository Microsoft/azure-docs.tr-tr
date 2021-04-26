---
title: MongoDB için Azure Cosmos DB API 'SI ile bir angular uygulamasına CRUD işlevleri ekleyin
description: Azure Cosmos DB üzerinde Angular ve Node ile MongoDB için kullandığınız aynı API'leri kullanarak bir MongoDB uygulaması oluşturma öğreticisi dizisinin 6. bölümü
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: c8e2c707566b08219b495e76be7f6f6130d876ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93081323"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---add-crud-functions-to-the-app"></a>MongoDB için Azure Cosmos DB API 'SI ile angular uygulaması oluşturma-uygulamaya CRUD işlevleri ekleme
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Bu çok parçalı öğreticide, Express ve angular ile Node.js yazılmış yeni bir uygulama oluşturma ve ardından bunu [MongoDB için Cosmos DB API 'siyle yapılandırılmış Cosmos hesabınıza](mongodb-introduction.md)bağlama işlemlerinin nasıl yapılacağı gösterilmektedir. Öğreticinin 6. bölümünde [5. bölümdeki](tutorial-develop-mongodb-nodejs-part5.md) konular genişletilir ve aşağıdaki görevler yer alır:

> [!div class="checklist"]
> * Hero hizmeti için Post, Put ve Delete işlevleri oluşturma
> * Uygulamayı çalıştırma

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>Önkoşullar

Öğreticinin bu bölümüne başlamadan önce öğreticinin [5. bölümündeki](tutorial-develop-mongodb-nodejs-part5.md) adımları tamamladığınızdan emin olun.

> [!TIP]
> Bu öğretici, uygulamanızı adım adım oluşturmanızı sağlayacak adımlarla size yol gösterir. Tamamlanmış projeyi indirmek isterseniz, tamamlanmış uygulamayı github'daki [angular-cosmosdb deposundan](https://github.com/Azure-Samples/angular-cosmosdb) alabilirsiniz.

## <a name="add-a-post-function-to-the-hero-service"></a>Hero hizmetine bir Post işlevi ekleme

1. Visual Studio Code, **bölünmüş düzenleyici** düğmesine basarak **routes.js** açın ve yan yana **hero.service.js** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/split-editor-button.png"::: .

    Routes.js’de 7. satırda, **hero.service.js**’nin 5. satırındaki `getHeroes` işlevinin çağrıldığına dikkat edin.  Post, put ve delete işlevleri için de aynı eşleşmeyi oluşturmamız gerekiyor. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/routes-heroservicejs.png" alt-text="Visual Studio Code’da routes.js ve hero.service.js":::
    
    Hero hizmetini kodlayarak başlayalım. 

2. **hero.service.js**’de `getHeroes` işlevinden sonra, `module.exports` bölümünden önce aşağıdaki kodu kopyalayın. Bu kod:  
   * Yeni bir hero göndermek için hero modelini kullanır.
   * Bir hata olup olmadığını görmek için yanıtları denetler ve 500 durum değeri döndürür.

   ```javascript
   function postHero(req, res) {
     const originalHero = { uid: req.body.uid, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. **hero.service.js**’de, `module.exports` değerini yeni `postHero` işlevini içerecek şekilde güncelleştirin. 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. **routes.js**’de `get` yönlendiricisinden sonra `post` işlevi için bir yönlendirici ekleyin. Bu yönlendirici aynı anda bir hero gönderir. Yönlendirici dosyasını böyle yapılandırdığınızda tüm kullanılabilir API uç noktalarını rahatça görebilirsiniz ve asıl iş **hero.service.js** dosyası tarafından yapılır.

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. Uygulamayı çalıştırarak her şeyin çalıştığından emin olun. Visual Studio Code, tüm değişikliklerinizi kaydedin, sol taraftaki **Hata Ayıkla** düğmesini seçin :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/debug-button.png"::: ve ardından **hata ayıklamayı Başlat** düğmesini seçin :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/start-debugging-button.png"::: .

6. Şimdi İnternet tarayıcınıza dönün ve çoğu makinede F12 tuşuna basarak açılan Geliştirici Araçları Ağı sekmesini açın. Ağ üzerinden yapılan çağrıları izlemek için `http://localhost:3000` adresine gidin.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/add-new-hero.png" alt-text="Chrome’da ağ etkinliğini gösteren ağ sekmesi":::

7. **Yeni Hero Ekle** düğmesini seçerek yeni bir hero ekleyin. "999" kimliğini, "Fred" adını ve "Hello" ifadesini girin, ardından **Kaydet**’i seçin. Ağ sekmesinde yeni bir hero için POST isteği gönderdiğinizi görmeniz gerekir. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/post-new-hero.png" alt-text="Get ve Post işlevleri için ağ etkinliğini gösteren Chrome ağ sekmesi":::

    Şimdi geri dönüp uygulamaya Put ve Delete işlevlerini ekleyelim.

## <a name="add-the-put-and-delete-functions"></a>Put ve Delete işlevlerini ekleme

1. **routes.js**’de gönderme yönlendiricisinden sonra `put` ve `delete` yönlendiricilerini ekleyin.

    ```javascript
    router.put('/hero/:uid', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:uid', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. **hero.service.js**’de `checkServerError` işlevinden sonra aşağıdaki kodu kopyalayın. Bu kod:
   * `put` ve `delete` işlevlerini oluşturur
   * Heronun bulunup bulunmadığını denetler
   * Hata işlemeyi gerçekleştirir 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       uid: parseInt(req.params.uid, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ uid: originalHero.uid }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const uid = parseInt(req.params.uid, 10);
     Hero.findOneAndRemove({ uid: uid })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. **hero.service.js**’de yeni modülleri dışarı aktarın:

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. Kodu güncelleştirdik, şimdi Visual Studio Code ' deki **Yeniden Başlat** düğmesini seçin :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/restart-debugger-button.png"::: .

5. İnternet tarayıcınızda sayfayı yenileyin ve **Yeni Hero Ekle** düğmesini seçin. "9" kimliğine, "Starlord" adına ve "Hi" metnine sahip bir hero ekleyin. Yeni heroyu kaydetmek için **Kaydet** düğmesini seçin.

6. Şimdi **Starlord** adlı heroyu seçin ve "Hi" ifadesini "Bye" ifadesiyle değiştirip **Kaydet** düğmesini seçin. 

    Artık yükü görmek için ağ sekmesinde kimliği seçebilirsiniz. Yükte ifadenin "Bye" olarak ayarlandığını görebilirsiniz.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/put-hero-function.png" alt-text="Heroes uygulaması ve yükü gösteren Ağ sekmesi"::: 

    Kullanıcı arabiriminde herolardan birini silin ve silme işlemini tamamlamak için gereken süreye bakın. Bunu "Fred" adlı hero için "Sil" düğmesini seçerek deneyebilirsiniz.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/times.png" alt-text="Heroes uygulaması ve işlevleri tamamlamak için gereken zamanı gösteren Ağ sekmesi"::: 

    Sayfayı yenilerseniz Ağ sekmesi heroları almak için gereken zamanı gösterir. Bu süreler kısa olsa da, büyük ölçüde verilerinizin dünyanın neresinde olduğuna ve kullanıcılarınıza yakın bir konuma coğrafi çoğaltma olanağınıza bağlıdır. Coğrafi çoğaltma hakkında daha fazla bilgiyi, yakında yayınlanacak bir sonraki öğreticide bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Öğreticinin bu bölümünde aşağıdakileri yaptınız:

> [!div class="checklist"]
> * Uygulamaya Post, Put ve Delete işlevleri ekleme 

Bu öğretici serisine eklenecek videolar için burayı daha sonra tekrar ziyaret edin.

