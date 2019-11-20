---
title: Azure Cosmos DB tarafından tetiklenen bir işlev oluşturma | Microsoft Docs
description: Azure İşlevleri kullanarak Azure Cosmos DB’de bir veritabanına veri eklendiğinde çağrılan sunucusuz bir işlev oluşturun.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.service: azure-functions
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 9cd09349ae37d11e4d01bc308162512ebfa03dfc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086348"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Azure Cosmos DB tarafından tetiklenen bir işlev oluşturma

Azure Cosmos DB’de veri eklendiğinde veya değiştirildiğinde tetiklenen bir işlev oluşturmayı öğrenin. Azure Cosmos DB hakkında daha fazla bilgi için bkz [. Azure Cosmos DB: Azure Işlevleri](../cosmos-db/serverless-computing-database.md)'ni kullanarak sunucusuz veritabanı hesaplama.

![Günlüklerde iletiyi görüntüleyin.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

+ Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB hesabı oluşturma

Tetikleyiciyi oluşturabilmek için SQL API'sini kullanan bir Azure Cosmos DB hesabınızın olması gerekir.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-an-azure-function-app"></a>Azure İşlev uygulaması oluşturma

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Ardından, yeni işlev uygulamasında bir işlev oluşturun.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Azure Cosmos DB tetikleyicisi oluşturma

1. İşlev uygulamanızı genişletin ve **İşlevler**'in yanındaki **+** düğmesine tıklayın. Bu, işlev uygulamanızdaki ilk işlevse **Portalda**'yı ve ardından **Devam**'ı seçin. Aksi takdirde üçüncü adıma geçin.

   ![Azure portalındaki İşlevler hızlı başlangıç sayfası](./media/functions-create-cosmos-db-triggered-function/function-app-quickstart-choose-portal.png)

1. **Diğer şablonlar**'ı ve ardından **Sonlandır ve şablonları görüntüle**'yi seçin.

    ![İşlevler hızlı başlangıcı diğer şablonlar](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

1. Arama alanına `cosmos` yazıp **Azure Cosmos DB tetikleyicisi** şablonunu seçin.

1. İstenirse, işlev uygulamasına Azure Cosmos DB uzantısını yüklemek için, **yüklensin** ' i seçin. Yükleme başarılı olduktan sonra **Devam**'ı seçin.

    ![Bağlama uzantılarını yükleme](./media/functions-create-cosmos-db-triggered-function/functions-create-cosmos-db-trigger-portal.png)

1. Yeni tetikleyiciyi resmin altındaki tabloda belirtilen ayarlarla yapılandırın.

    ![Azure Cosmos DB tarafından tetiklenen işlevi oluşturma](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)

    | Ayar      | Önerilen değer  | Açıklama                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Name** | Varsayılan | Şablonun önerdiği varsayılan işlev adını kullanın.|
    | **Azure Cosmos DB hesabı bağlantısı** | Yeni ayar | **Yeni**'yi ve ardından **Aboneliğinizi**, önceden oluşturduğunuz **Veritabanı hesabını** ve **Seç**'i belirtin. Bunu yaptığınızda hesap bağlantınız için bir uygulama ayarı oluşturulur. Bu ayar bağlama tarafından veritabanı bağlantısı için kullanılır. |
    | **Koleksiyon adı** | Öğeler | İzlenecek koleksiyonun adı. |
    | **Yoksa kira koleksiyonu oluşturun** | İşaretli | Koleksiyon henüz mevcut değil, bu yüzden oluşturun. |
    | **Veritabanı adı** | Görevler | İzlenecek koleksiyonu içeren veritabanının adı. |

1. **Oluştur**’a tıklayarak Azure Cosmos DB tarafından tetiklenen işlevinizi oluşturun. İşlev oluşturulduktan sonra şablon temelli işlev kodu görüntülenir.  

    ![C# dilinde Cosmos DB işlev şablonu](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Bu işlev şablonu, günlüklere belge sayısını ve ilk belgenin kimliğini yazar.

Daha sonra, Azure Cosmos DB hesabınız bağlar ve `Tasks` veritabanında `Items` koleksiyonunu oluşturursunuz.

## <a name="create-the-items-collection"></a>Öğeler koleksiyonunu oluşturma

1. Tarayıcıdaki yeni bir sekmede [Azure portalının](https://portal.azure.com) ikinci bir örneğini açın.

1. Portalın sol tarafındaki simge çubuğunu genişletin, arama alanına `cosmos` yazıp **Azure Cosmos DB**’yi seçin.

    ![Azure Cosmos DB hizmetini arama](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Azure Cosmos DB hesabınızı seçin ve ardından **Veri Gezgini**’ni seçin. 

1. **Koleksiyonlar** bölümünde **taskDatabase**’i seçip **Yeni Koleksiyon**’u seçin.

    ![Koleksiyon oluşturma](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection.png)

1. **Koleksiyon Ekle** bölümünde, resmin altındaki tabloda gösterilen ayarları kullanın. 

    ![taskCollection’ı tanımlama](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection2.png)

    | Ayar|Önerilen değer|Açıklama |
    | ---|---|--- |
    | **Veritabanı Kimliği** | Görevler |Yeni veritabanınızın adı. Bu, işlev bağlamanızda tanımlanan adla eşleşmelidir. |
    | **Koleksiyon Kimliği** | Öğeler | Yeni koleksiyonun adı. Bu, işlev bağlamanızda tanımlanan adla eşleşmelidir.  |
    | **Depolama kapasitesi** | Sabit (10 GB)|Varsayılan değeri kullanın. Bu değer, veritabanının depolama kapasitesidir. |
    | **Aktarım hızı** |400 RU| Varsayılan değeri kullanın. Daha sonra gecikme süresini azaltmak isterseniz aktarım hızının ölçeğini artırabilirsiniz. |
    | **[Bölüm anahtarı](../cosmos-db/partition-data.md)** | /kategori|Verileri her bölüme eşit şekilde dağıtan bir bölüm anahtarı. Koleksiyon performansının yüksek olması için doğru bölüm anahtarının seçilmesi önemlidir. | 

1. Items koleksiyonunu oluşturmak için **Tamam**’a tıklayın. Koleksiyonun oluşturulması biraz zaman alabilir.

İşlev bağlamasında belirtilen koleksiyon oluşturulduktan sonra bu yeni koleksiyona belge ekleyerek işlevi test edebilirsiniz.

## <a name="test-the-function"></a>İşlevi test etme

1. Veri Gezgini’nde yeni **taskCollection** koleksiyonunu genişletin, **Belgeler**’i ve sonra **Yeni Belge**’yi seçin.

    ![taskCollection’da belge oluşturma](./media/functions-create-cosmos-db-triggered-function/create-document-in-collection.png)

1. Yeni belgenin içeriğini aşağıdaki içerikle değiştirip **Kaydet**’i seçin.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. Portalda işlevinizi içeren ilk tarayıcı sekmesine geçin. İşlev günlüklerini genişletin ve yeni belgenin işlevi tetiklediğini doğrulayın. `task1` belge kimliğinin günlüklere yazılıp yazılmadığına bakın. 

    ![Günlüklerde iletiyi görüntüleyin.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (İsteğe bağlı) Belgenize dönerek bir değişiklik yapın ve **Güncelleştir**’e tıklayın. Sonra işlev günlüklerine dönerek güncelleştirmenin işlevi de tetiklediğini doğrulayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB’nizde bir belge eklendiğinde ya da değiştirildiğinde çalışan bir işlev oluşturdunuz. Azure Cosmos DB tetikleyicileri hakkında daha fazla bilgi için bkz. [Azure İşlevleri için Azure Cosmos DB bağlamaları](functions-bindings-cosmosdb.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
