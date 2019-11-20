---
title: Azure için MongoDB, tepki verme ve Node. js öğreticisi
description: Video temelli bu öğretici serisiyle Azure Cosmos DB üzerinde React ve Node ile MongoDB için kullandığınız aynı API'leri kullanarak bir MongoDB uygulaması oluşturmayı öğrenirsiniz.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: jopapa
ms.reviewer: sngun
ms.openlocfilehash: 4efaa90281ddcebf1b800e1016578e38c24be651
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614106"
---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>React ve Azure Cosmos DB ile bir MongoDB uygulaması oluşturma  

Bu çok parçalı videolu öğretici, React ön ucuyla hero izleme uygulaması oluşturmayı gösterir. Sunucu için düğüm ve Express kullanılan uygulama, [MongoDB için Azure Cosmos DB API 'si](mongodb-introduction.md)Ile yapılandırılmış Cosmos veritabanına bağlanır ve sonra tepki ön ucuna uygulamanın sunucu bölümüne bağlanır. Öğreticide Ayrıca, herkesin en sevdiğiniz Heroes 'yi izleyebilmesi için Azure portal Cosmos DB ölçeklendirme ve uygulamanın internet 'e nasıl dağıtılacağı gösterilmektedir. 

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) MongoDB ile kablo protokol uyumluluğunu destekler ve Istemcilerin mongodb yerine Azure Cosmos DB kullanmasına olanak tanır.  

Bu çok bölümlü öğretici aşağıdaki görevleri içerir:

> [!div class="checklist"]
> * Giriş
> * Projeyi ayarlama
> * React ile kullanıcı arabirimini oluşturma
> * Azure Portal’ı kullanarak Azure Cosmos DB hesabı oluşturma
> * Azure Cosmos DB’ye bağlanmak için Mongoose kullanma
> * Uygulamaya Yanıt Verme, Güncelleştirme ve Silme işlemleri ekleme

Aynı uygulamayı Angular ile oluşturmak ister misiniz? Bkz. [Angular öğretici video serisi](tutorial-develop-mongodb-nodejs.md).

## <a name="prerequisites"></a>Önkoşullar
* [Node.js](https://www.nodejs.org)

### <a name="finished-project"></a>Tamamlanmış Proje
Tamamlanmış uygulamayı [github'dan](https://github.com/Azure-Samples/react-cosmosdb) alabilirsiniz.

## <a name="introduction"></a>Giriş 

Bu videoda, Burke Holland Azure Cosmos DB’yi tanıtır ve bu video serisindeki uygulamayı oluşturma konusunda size yol gösterir. 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>Proje ayarları

Bu videoda aynı projede Express ve React’in nasıl ayarlanacağı gösterilmiştir. Burke, daha sonra da projedeki kodları açıklar.

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>Kullanıcı Arabirimini oluşturma

Bu videoda React ile uygulamanın kullanıcı arabirimini (UI) nasıl oluşturacağınız gösterilmiştir. 

> [!NOTE]
> Bu videoda başvurulan CSS [react-cosmosdb GitHub deposunda](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css) bulunabilir.

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Azure Cosmos DB’ye bağlanma

Bu videoda, Azure portalında bir Azure Cosmos DB hesabı oluşturma, MongoDB ve Mongoose paketlerini yükleme ve ardından uygulamayı Azure Cosmos DB bağlantı dizesini kullanarak yeni oluşturulan hesapla bağlama gösterilmiştir. 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>Uygulamada hero'ları okuma ve oluşturma

Bu videoda, Heroes 'nin nasıl okunacağı ve Cosmos veritabanında Heroes 'nin nasıl oluşturulduğu ve bu yöntemlerin Postman ve tepki verme Kullanıcı arabirimi kullanılarak nasıl test yapılacağı gösterilmektedir. 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>Uygulamada hero'ları silme ve güncelleştirme

Bu video, uygulamada hero'ları silmeyi ve güncelleştirmeyi, ardından güncelleştirmeleri kullanıcı arabiriminde görüntülemeyi gösterir. 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>Uygulamayı tamamlayın

Bu video, uygulamayı tamamlamayı ve kullanıcı arabirimini arka uç API'siyle bağlamayı gösterir. 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekseniz aşağıdaki adımları kullanarak Azure portalında bu öğretici tarafından oluşturulan tüm kaynakları silin: 

1. Azure portalında sol taraftaki menüden, **Kaynak grupları**'na ve ardından oluşturduğunuz kaynağın adına tıklayın. 
2. Kaynak grubu sayfanızda, **Sil**'e tıklayın, metin kutusuna silinecek kaynağın adını yazın ve ardından **Sil**'e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * React, Node, Express ve Azure Cosmos DB ile uygulama oluşturma 
> * Azure Cosmos DB hesabı oluşturma
> * Uygulamayı Azure Cosmos DB hesabına bağlama
> * Uygulamayı Postman kullanarak test etme
> * Uygulamayı çalıştırma ve veritabanına hero'ları ekleme

Bir sonraki öğreticiye geçip MongoDB verilerini Azure Cosmos DB’de içeri aktarmayı öğrenin.  

> [!div class="nextstepaction"]
> [Azure Cosmos DB’ye MongoDB verileri aktarma](mongodb-migrate.md)
 
