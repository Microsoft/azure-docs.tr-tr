---
title: Azure portal bir Azure Cosmos hesabı, kapsayıcısı ve öğeleri oluşturun.
description: Azure portal bir Azure Cosmos hesabı, kapsayıcısı ve öğeleri oluşturun.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/01/2019
ms.openlocfilehash: e0a9f4fa6ca5ff7447d2ffaef3eab2f3c54fdeae
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241252"
---
# <a name="quickstart-create-an-azure-cosmos-account-container-and-items-with-the-azure-portal"></a>Hızlı Başlangıç: Azure portal ile bir Azure Cosmos hesabı, kapsayıcısı ve öğeleri oluşturun

> [!div class="op_single_selector"]
> * [Azure portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB, Microsoft'un genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Azure Cosmos DB, bir bütün olarak genel dağıtım ve yatay Azure Cosmos DB ölçek özelliğinden yararlanan anahtar/değer veritabanlarını, belge veritabanlarını ve grafik veritabanlarını hızlıca oluşturmak ve sorgulamak için kullanabilirsiniz. 

Bu hızlı başlangıçta Azure portal kullanarak Azure Cosmos DB bir [SQL API](sql-api-introduction.md) hesabı oluşturma, bir belge veritabanı ve kapsayıcı oluşturma ve kapsayıcıya veri ekleme işlemlerinin nasıl yapılacağı gösterilmiştir. 

## <a name="prerequisites"></a>Önkoşullar

Bir Azure aboneliği veya ücretsiz Azure Cosmos DB deneme hesabı
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB hesabı oluşturma

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Veritabanı ve kapsayıcı ekleme 

Veritabanı ve kapsayıcı oluşturmak için Azure portal Veri Gezgini kullanabilirsiniz. 

1.  Azure Cosmos DB hesabı sayfanızda sol gezinti **Veri Gezgini** seçin ve ardından **yeni kapsayıcı**' yı seçin. 
    
    **Kapsayıcı Ekle** penceresini görmek için sağa kaydırmanız gerekebilir.
    
    ![Azure portal Veri Gezgini, kapsayıcı bölmesi Ekle](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  **Kapsayıcı Ekle** bölmesinde, yeni kapsayıcının ayarlarını girin.
    
    |Ayar|Önerilen değer|Açıklama
    |---|---|---|
    |**Veritabanı Kimliği**|ToDoList|Yeni veritabanının adı olarak *ToDoList* girin. Veritabanı adı 1 ila 255 karakter içermeli ve içeremezler `/, \\, #, ?`, veya bir boşluk. Veritabanı **Işleme sağlama** seçeneğini kontrol edin, veritabanı içindeki tüm kapsayıcılar üzerinde veritabanı için sağlanan aktarım hızını paylaşmanıza olanak sağlar. Bu seçenek maliyet tasarruflarıyla de yardımcı olur. |
    |**Aktarım hızı**|400|Aktarım hızını saniyede 400 istek birimi (RU/s) olarak bırakın. Daha sonra gecikme süresini azaltmak isterseniz aktarım hızının ölçeğini artırabilirsiniz.| 
    |**Kapsayıcı KIMLIĞI**|Öğeler|*Öğeleri* yeni kapsayıcının adı olarak girin. Kapsayıcı kimlikleri, veritabanı adlarıyla aynı karakter gereksinimlerine sahiptir.|
    |**Bölüm anahtarı**| /kategori| Bu makalede açıklanan örnek, bölüm anahtarı olarak */category* kullanır.|

    
    Bu örnek için **benzersiz anahtarlar** eklemeyin. Benzersiz anahtarlar, bölüm anahtarı başına bir veya daha fazla değerin benzersizliğini sağlayarak veritabanına veri bütünlüğü katmanı eklemenizi sağlar. Daha fazla bilgi için bkz. [Azure Cosmos DB Içindeki benzersiz anahtarlar](unique-keys.md).
    
1.  **Tamam**’ı seçin. Veri Gezgini yeni veritabanını ve oluşturduğunuz kapsayıcıyı görüntüler.

## <a name="add-data-to-your-database"></a>Veritabanınıza veri ekleme

Veri Gezgini kullanarak yeni veritabanınıza veri ekleyin.

1. **Veri Gezgini**' de, **ToDoList** veritabanını genişletin ve **öğeler** kapsayıcısını genişletin. Sonra, **öğeler**' i ve sonra **Yeni öğe**' yi seçin. 
   
   ![Azure portalındaki Veri Gezgini'nde yeni belge oluşturma](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. **Belgeler** bölmesinin sağ tarafındaki belgeye aşağıdaki yapıyı ekleyin:

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. **Kaydet**’i seçin.
   
   ![JSON verilerini kopyalayın ve Azure portal Veri Gezgini Kaydet ' i seçin](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. **Yeni belge** ' yi yeniden seçin ve benzersiz `id`ve istediğiniz diğer özellikleri ve değerleri içeren başka bir belge oluşturun ve kaydedin. Azure Cosmos DB verileriniz üzerinde herhangi bir şema uygulamayan, belgeleriniz herhangi bir yapıya sahip olabilir.

## <a name="query-your-data"></a>Verilerinizi sorgulayın

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Veri Gezgini kullanarak bir Azure Cosmos DB hesabı oluşturmayı, veritabanı ve kapsayıcı oluşturmayı öğrendiniz. Şimdi Azure Cosmos DB hesabınıza ek veriler aktarabilirsiniz. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB hesabınıza veri aktarma](import-data.md)