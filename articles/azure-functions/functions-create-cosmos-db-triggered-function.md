---
title: Azure Cosmos DB tarafından tetiklenen bir işlev oluşturma
description: Azure İşlevleri kullanarak Azure Cosmos DB’de bir veritabanına veri eklendiğinde çağrılan sunucusuz bir işlev oluşturun.
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: how-to
ms.date: 04/28/2020
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 621773a84db99dbacfaa163f77189974ba102163
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98034824"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Azure Cosmos DB tarafından tetiklenen bir işlev oluşturma

Azure Cosmos DB’de veri eklendiğinde veya değiştirildiğinde tetiklenen bir işlev oluşturmayı öğrenin. Azure Cosmos DB hakkında daha fazla bilgi edinmek için bkz. [Azure Cosmos DB: Azure İşlevleri ile sunucusuz veritabanı işlemleri](../cosmos-db/serverless-computing-database.md).

:::image type="content" source="./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png" alt-text="Azure Cosmos DB kodu":::

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

+ Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure hesabınızla [Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB hesabı oluşturma

Tetikleyiciyi oluşturabilmek için SQL API'sini kullanan bir Azure Cosmos DB hesabınızın olması gerekir.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-a-function-app-in-azure"></a>Azure’da bir işlev uygulaması oluşturma

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Ardından, yeni işlev uygulamasında bir işlev oluşturun.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Azure Cosmos DB tetikleyicisi oluşturma

1. İşlev uygulamanızda, sol menüden **işlevler** ' i seçin ve ardından üst menüden **Ekle** ' yi seçin. 

1. **Yeni işlev** sayfasında, `cosmos` Arama alanına girin ve sonra **Azure Cosmos DB tetikleyici** şablonunu seçin.

   :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-choose-cosmos.png" alt-text="Azure portal işlevler sayfası":::


1. Yeni tetikleyiciyi aşağıdaki tabloda belirtilen ayarlarla yapılandırın:

    | Ayar      | Önerilen değer  | Açıklama                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Yeni Işlev** | Varsayılan adı kabul edin | İşlevin adı. |
    | **Cosmos DB hesabı bağlantısı** | Varsayılan yeni adı kabul et | **Yeni**, daha önce oluşturduğunuz **veritabanı hesabı** ' nı seçin ve ardından **Tamam**' a tıklayın. Bu eylem, hesap bağlantınız için bir uygulama ayarı oluşturur. Bu ayar bağlama tarafından veritabanı bağlantısı için kullanılır. |
    | **Veritabanı adı** | Görevler | İzlenecek koleksiyonu içeren veritabanının adı. |
    | **Koleksiyon adı** | Öğeler | İzlenecek koleksiyonun adı. |
    | **Kiralamalar için koleksiyon adı** | leases | Kiralamaları depolayacak koleksiyonun adı. |
    | **Yoksa kira koleksiyonu oluştur** | Yes | Kira koleksiyonunun varlığını denetler ve otomatik olarak oluşturur. |

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png" alt-text="Azure Cosmos DB tarafından tetiklenen işlevi oluşturma":::

1. **İşlev Oluştur**'u seçin. 

    Azure, Cosmos DB tetikleyici işlevini oluşturur.

1. Şablon tabanlı işlev kodunu göstermek için **Code + test**' i seçin.

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png" alt-text="C# dilinde Cosmos DB işlev şablonu":::

    Bu işlev şablonu, günlüklere belge sayısını ve ilk belgenin kimliğini yazar.

Sonra, Azure Cosmos DB hesabınıza bağlanıp `Items` kapsayıcıyı `Tasks` veritabanında oluşturursunuz.

## <a name="create-the-items-container"></a>Öğe kapsayıcısını oluşturma

1. Tarayıcıdaki yeni bir sekmede [Azure portalının](https://portal.azure.com) ikinci bir örneğini açın.

1. Portalın sol tarafındaki simge çubuğunu genişletin, arama alanına `cosmos` yazıp **Azure Cosmos DB**’yi seçin.

    ![Azure Cosmos DB hizmetini arama](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Azure Cosmos DB hesabınızı seçin ve ardından **Veri Gezgini**’ni seçin. 

1. **SQL API 'si** altında **Görevler** veritabanı ' nı seçin ve **yeni kapsayıcı**' yı seçin.

    ![Kapsayıcı oluşturma](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container.png)

1. **Kapsayıcı Ekle**' de, görüntünün altındaki tabloda gösterilen ayarları kullanın. 

    ![Görevler kapsayıcısını tanımlama](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container2.png)

    | Ayar|Önerilen değer|Açıklama |
    | ---|---|--- |
    | **Veritabanı Kimliği** | Görevler |Yeni veritabanınızın adı. Bu, işlev bağlamanızda tanımlanan adla eşleşmelidir. |
    | **Kapsayıcı Kimliği** | Öğeler | Yeni kapsayıcının adı. Bu, işlev bağlamanızda tanımlanan adla eşleşmelidir.  |
    | **[Bölüm anahtarı](../cosmos-db/partitioning-overview.md)** | /kategori|Verileri her bölüme eşit şekilde dağıtan bir bölüm anahtarı. Doğru bölüm anahtarının seçilmesi, bir performanslı kapsayıcı oluşturmak için önemlidir. | 
    | **İşleme hızı** |400 RU| Varsayılan değeri kullanın. Daha sonra gecikme süresini azaltmak isterseniz işlem hızının ölçeğini artırabilirsiniz. |    

1. Öğeler kapsayıcısını oluşturmak için **Tamam** ' ı tıklatın. Kapsayıcının oluşturulması kısa bir zaman alabilir.

İşlev bağlamasında belirtilen kapsayıcı varsa, bu yeni kapsayıcıya öğe ekleyerek işlevi test edebilirsiniz.

## <a name="test-the-function"></a>İşlevi test etme

1. Veri Gezgini yeni **öğeler** kapsayıcısını genişletin, **öğeler**' i seçin ve sonra **Yeni öğe**' yi seçin.

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png" alt-text="Öğeler kapsayıcısında bir öğe oluşturma":::

1. Yeni öğenin içeriğini aşağıdaki içerikle değiştirin ve ardından **Kaydet**' i seçin.

    ```yaml
    {
        "id": "task1",
        "category": "general",
        "description": "some task"
    }
    ```

1. Portalda işlevinizi içeren ilk tarayıcı sekmesine geçin. İşlev günlüklerini genişletin ve yeni belgenin işlevi tetiklediğini doğrulayın. `task1` belge kimliğinin günlüklere yazılıp yazılmadığına bakın. 

    ![Günlüklerde iletiyi görüntüleyin.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (İsteğe bağlı) Belgenize dönerek bir değişiklik yapın ve **Güncelleştir**’e tıklayın. Sonra işlev günlüklerine dönerek güncelleştirmenin işlevi de tetiklediğini doğrulayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB’nizde bir belge eklendiğinde ya da değiştirildiğinde çalışan bir işlev oluşturdunuz. Azure Cosmos DB tetikleyicileri hakkında daha fazla bilgi için bkz. [Azure İşlevleri için Azure Cosmos DB bağlamaları](functions-bindings-cosmosdb.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
