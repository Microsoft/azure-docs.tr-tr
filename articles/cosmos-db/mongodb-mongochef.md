---
title: MongoDB için Azure Cosmos DB API 'sine bağlanmak için Studio 3T kullanın
description: Studio 3T kullanarak MongoDB için Azure Cosmos DB API 'sine nasıl bağlanacağınızı öğrenin.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: a02aaadf8c774557eb182acf041b6f19337a0de8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096529"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Studio 3T kullanarak bir Azure Cosmos hesabına bağlanma
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Studio 3T kullanarak MongoDB için Azure Cosmos DB API 'sine bağlanmak için şunları yapmanız gerekir:

* [Studio 3T](https://studio3t.com/)'yi indirip yükleyin.
* Azure Cosmos hesabınızın [bağlantı dizesi](connect-mongodb-account.md) bilgilerine sahip olmanız gerekir.

## <a name="create-the-connection-in-studio-3t"></a>Studio 3T 'de bağlantı oluşturma

Azure Cosmos hesabınızı Studio 3T bağlantı Yöneticisi 'ne eklemek için aşağıdaki adımları kullanın:

1. MongoDB uygulaması için Azure Cosmos DB API 'sine yönelik bağlantı bilgilerini [Azure Cosmos DB uygulamasına bağlama](connect-mongodb-account.md) makalesindeki yönergeleri kullanarak alın.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="Bağlantı dizesi sayfasının ekran görüntüsü":::

2. **Bağlan** ' a tıklayarak bağlantı yöneticisini açın ve ardından **Yeni bağlantı** ' ya tıklayın.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="Yeni bağlantı düğmesini vurgulayan Studio 3T bağlantı Yöneticisi ekran görüntüsü.":::
3. **Yeni bağlantı** penceresindeki **sunucu** sekmesinde, Azure Cosmos hesabının (FQDN) ana bilgisayarını ve bağlantı noktasını girin.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="Studio 3T bağlantı Yöneticisi sunucu sekmesinin ekran görüntüsü":::
4. **Yeni bağlantı** penceresinde, **kimlik doğrulama** sekmesinde, kimlik doğrulama modu **temel (MongoDB-CR veya scarm-SHA-1)** SEÇENEĞINI belirleyin ve Kullanıcı adını ve parolayı girin.  Varsayılan kimlik doğrulama DB 'yi (admin) kabul edin veya kendi değerini sağlayın.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="Studio 3T bağlantı Yöneticisi kimlik doğrulama sekmesinin ekran görüntüsü":::
5. **Yeni bağlantı** penceresinde, **SSL** SEKMESINDE, **bağlanmak için SSL protokolünü kullan** onay kutusunu ve **sunucuyu otomatik olarak imzalanan SSL sertifikalarını kabul et** radyo düğmesini işaretleyin.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="Studio 3T bağlantı Yöneticisi SSL sekmesinin ekran görüntüsü":::
6. Bağlantı bilgilerini doğrulamak için **Bağlantıyı Sına** düğmesine tıklayın, yeni bağlantı penceresine dönüp **Tamam** ' a tıklayın ve ardından **Kaydet**' e tıklayın.

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="Studio 3T test bağlantı penceresinin ekran görüntüsü":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Bir veritabanı, koleksiyon ve belge oluşturmak için Studio 3T kullanın
Studio 3T kullanarak bir veritabanı, koleksiyon ve belgeler oluşturmak için aşağıdaki adımları uygulayın:

1. **Bağlantı Yöneticisi**'nde bağlantıyı vurgulayın ve **Bağlan**' a tıklayın.

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="Studio 3T bağlantı Yöneticisi ekran görüntüsü":::
2. Konağa sağ tıklayın ve **veritabanı Ekle**' yi seçin.  Bir veritabanı adı girin ve **Tamam 'a** tıklayın.

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="Studio 3T veritabanı ekleme seçeneğinin ekran görüntüsü":::
3. Veritabanına sağ tıklayın ve **koleksiyon Ekle**' yi seçin.  Bir koleksiyon adı girin ve **Oluştur**' a tıklayın.

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="Studio 3T koleksiyon ekle seçeneğinin ekran görüntüsü":::
4. **Koleksiyon** menü öğesine tıklayın ve **Belge Ekle**' ye tıklayın.

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="Studio 3T belge Ekle menü öğesinin ekran görüntüsü":::
5. Belge Ekle iletişim kutusunda, aşağıdakileri yapıştırın ve **Belge Ekle**' ye tıklayın.

    ```json
    {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
            { "firstName": "Thomas" },
            { "firstName": "Mary Kay"}
        ],
        "children": [
            {
                "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
                "pets": [{ "givenName": "Fluffy" }]
            }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
    }
    ```
    
6. Şu içeriğe sahip başka bir belge ekleyin:

    ```json
    {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                "givenName": "Lisa",
                "gender": "female",
                "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    }
    ```

7. Örnek sorgu yürütün. Örneğin, son adı ' Andersen ' olan aileleri arayın ve üst ve durum alanlarını döndürün.

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="Mongo Chef sorgu sonuçlarının ekran görüntüsü":::

## <a name="next-steps"></a>Sonraki adımlar

- MongoDB için Azure Cosmos DB API 'SI ile [Robo 3T kullanmayı](mongodb-robomongo.md) öğrenin.
- MongoDB için Azure Cosmos DB API 'siyle MongoDB [örneklerini](mongodb-samples.md) gezin.
