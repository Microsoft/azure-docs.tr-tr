---
title: 'Hızlı başlangıç: Azure Cosmos DB SQL API hesabı kullanarak bir Python uygulaması oluşturma'
description: Azure Cosmos DB SQL API'sine bağlanmak ve sorgu göndermek için kullanabileceğiniz bir Python kodu örneği sunar
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 11/03/2019
ms.author: sngun
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
ms.openlocfilehash: 82426c0093550864b421d7acc35780c4173895a8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824723"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Hızlı başlangıç: Azure Cosmos DB SQL API hesabı kullanarak bir Python uygulaması oluşturma

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Bu hızlı başlangıç belgesinde Azure portalını kullanarak bir Azure Cosmos DB [SQL API](sql-api-introduction.md) hesabını, belge veritabanını ve kapsayıcısını nasıl oluşturacağınız anlatılmıştır. Daha sonra [SQL API](sql-api-sdk-python.md)'si için Python SDK'sı ile bir konsol uygulamasını derleyip çalıştıracaksınız.

Azure Cosmos DB, Microsoft'un genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Belgeleri, anahtar/değer, geniş sütun ve grafik veritabanlarını hızlıca oluşturup sorgulayabilirsiniz. Bu işlemlerin tümü Azure Cosmos DB dağıtım ve ölçeğinden faydalanır.

Bu hızlı başlangıçta [Python SDK 'sının](https://pypi.org/project/azure-cosmos/#history)4 sürümü kullanılmaktadır.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>Ön koşullar

* `PATH``python` çalıştırılabilir dosyası ile [Python 3.6 +](https://www.python.org/downloads/).
* [Visual Studio Code](https://code.visualstudio.com/)
* [Visual Studio Code için Python uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Veritabanı hesabı oluşturma

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Kapsayıcı ekleme

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Örnek verileri ekleme

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Verilerinizi sorgulayın

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Şimdi GitHub'dan bir SQL API uygulaması kopyalayalım, bağlantı dizesini ayarlayalım ve uygulamayı çalıştıralım.

1. Bir komut istemini açın, git-samples adlı yeni bir klasör oluşturun ve komut istemini kapatın.

    ```cmd
    md "git-samples"
    ```
   Bash istemi kullanıyorsanız, bunun yerine aşağıdaki komutu kullanmanız gerekir:

   ```bash
   mkdir "git-samples"
   ```

2. Git Bash gibi bir Git terminal penceresi açın ve örnek uygulamayı yüklemek üzere yeni bir klasör olarak değiştirmek için `cd` komutunu kullanın.

    ```bash
    cd "git-samples"
    ```

3. Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. Bu komut bilgisayarınızda örnek uygulamanın bir kopyasını oluşturur. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Bağlantı dizenizi güncelleştirme

Bu adımda Azure portalına dönerek bağlantı dizesi bilgilerinizi kopyalayıp uygulamaya ekleyin.

1. [Azure Portal](https://portal.azure.com/), Azure Cosmos hesabınızda sol gezinti ' ın altında bulunan **anahtarlar**' ı seçin. Ekranın sağ tarafındaki kopyalama düğmelerini kullanarak **URI** ve **Primary Key** değerlerini kopyalayıp sonraki adımda bunları `cosmos_get_started.py` dosyasına yapıştırın.

    ![Azure portal anahtarlar ayarlarından bir erişim anahtarı ve URI alın](./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png)

2. Visual Studio Code içinde `cosmos_get_started.py` dosyasını \git-samples\azure-Cosmos-DB-Python-Getting-Started içinde açın.

3. Portaldan **URI** değerini kopyalayın (kopyalama düğmesini kullanarak) ve ``cosmos_get_started.py``**uç nokta** değişkeninin değeri yapın. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Ardından, portaldan **BIRINCIL anahtar** değerini kopyalayın ve ``cosmos_get_started.py``**anahtarın** değeri yapın. Bu adımlarla uygulamanıza Azure Cosmos DB ile iletişim kurması için gereken tüm bilgileri eklemiş oldunuz. 

    `key = 'FILLME'`

5. ``cosmos_get_started.py`` dosyasını kaydedin.

## <a name="review-the-code"></a>Kodu gözden geçirin

Bu adım isteğe bağlıdır. Kodda oluşturulan veritabanı kaynakları hakkında bilgi edinin veya [Bağlantı dizenizi güncelleştirmek](#update-your-connection-string)için bir adım atlayın.

Aşağıdaki kod parçacıklarının tümü `cosmos_get_started.py` dosyasından alınmıştır.

* CosmosClient başlatılır. [Bağlantı dizenizi güncelleştirme](#update-your-connection-string) bölümünde açıklandığı gibi "Endpoint" ve "Key" değerlerini güncelleştirdiğinizden emin olun. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* Yeni bir veritabanı oluşturulur.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* [Sağlanan aktarım hızı](request-units.md)400 ru/sn ile yeni bir kapsayıcı oluşturulur. Bu özelliğe filtre uygulayan etkili sorgular yapabilmemiz için [bölüm anahtarı](partitioning-overview.md#choose-partitionkey)olarak `lastName` seçiyoruz. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Kapsayıcıya bazı öğeler eklenir. Kapsayıcılar, değişen şemaya sahip olabilecek öğelerin (JSON belgeleri) bir koleksiyonudur. Yardımcı yöntemler ```get_[name]_family_item``` JSON belgeleri olarak Azure Cosmos DB depolanan bir aileyi döndürür.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Nokta okuma (anahtar değeri aramaları) `read_item` yöntemi kullanılarak gerçekleştirilir. Her bir işlemin [ru ücreti](request-units.md) yazdırılsın.
    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* SQL sorgu söz dizimi kullanılarak bir sorgu gerçekleştirilir. WHERE yan tümcesinde ```lastName``` bölüm anahtarı değerlerini kullandığımızda, Azure Cosmos DB bu sorguyu ilgili bölümlere etkin bir şekilde yönlendirerek performansı geliştirir.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Uygulamayı çalıştırma

1. Visual Studio Code’da, **Görünüm** > **Komut Paleti**’ni seçin. 

2. Komut isteminde, **Python: Yorumlayıcı Seçin** girip kullanılacak Python sürümün seçin.

    Visual Studio Code’daki alt bilgi, seçilen yorumlayıcıyı belirtmek için güncelleştirilir. 

3. Visual Studio Code tümleşik terminalini açmak için **Görünüm** > **Tümleşik Terminal**'i seçin.

4. Tümleşik terminal penceresinde, azure-cosmos-db-python-getting-started klasöründe olduğunuzdan emin olun. Değilseniz, örnek klasörü değiştirmek için aşağıdaki komutu çalıştırın. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. azure-cosmos package'i yüklemek için aşağıdaki komutu çalıştırın. 

    ```python
    pip install --pre azure-cosmos
    ```

    azure-cosmos'u yüklemeye çalışırken erişim engellendi hatası alırsanız, [VS Code’u yönetici olarak çalıştırmanız gerekir](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Örneği çalıştırmak ve Azure Cosmos DB yeni belgeler oluşturmak ve depolamak için aşağıdaki komutu çalıştırın.

    ```python
    python cosmos_get_started.py
    ```

7. Oluşturulan ve kaydedilen yeni öğeleri onaylamak için, Azure portal **Veri Gezgini** > **AzureSampleFamilyDatabase** > **öğelerini**seçin. Oluşturulan öğeleri görüntüleyin. Örneğin, Andersen ailesi için örnek bir JSON belgesi aşağıda verilmiştir:

```json
{
    "id": "Andersen-1569479288379",
    "lastName": "Andersen",
    "district": "WA5",
    "parents": [
        {
            "familyName": null,
            "firstName": "Thomas"
        },
        {
            "familyName": null,
            "firstName": "Mary Kay"
        }
    ],
    "children": null,
    "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
    },
    "registered": true,
    "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
    "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
    "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
    "_attachments": "attachments/",
    "_ts": 1569479288
}
```

## <a name="review-slas-in-the-azure-portal"></a>Azure portalında SLA'ları gözden geçirme

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta Azure Cosmos hesabı oluşturmayı, Veri Gezgini kullanarak bir kapsayıcı oluşturmayı ve bir uygulamayı çalıştırmayı öğrendiniz. Şimdi Cosmos DB hesabınıza ek veri aktarabilirsiniz. 

> [!div class="nextstepaction"]
> [SQL API'si için Azure Cosmos DB'ye veri aktarma](import-data.md)


