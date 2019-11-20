---
title: include dosyası
description: include dosyası
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.author: rimman
ms.custom: include file
ms.openlocfilehash: 33f08f22c3f09539dbe44098e3747f0ed7a8fe8c
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541561"
---
1. Yeni bir pencerede [Azure portalında](https://portal.azure.com/) oturum açın.
2. Sol taraftaki menüde **kaynak oluştur**' u seçin, **veritabanları**' nı seçin ve ardından **Azure Cosmos DB**altında **Oluştur**' u seçin.
   
   ![Azure portal ekran görüntüsü, daha fazla hizmet vurgulama ve Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. İçinde **Azure Cosmos DB hesabı oluştur** sayfasında, yeni Azure Cosmos DB hesabının ayarlarını girin. 
 
    Ayar|Değer|Açıklama
    ---|---|---
    Abonelik|Aboneliğiniz|Bu Azure Cosmos DB hesabı için kullanmak istediğiniz Azure aboneliğini seçin. 
    Kaynak Grubu|Yeni oluştur<br><br>Ardından Kimlikte sağlanan benzersiz adın aynısını girin|**Yeni oluştur**’u seçin. Ardından hesabınız için yeni bir kaynak grubu adı girin. Kolaylık olması için kimliğinizle aynı adı kullanın. 
    Hesap Adı|Benzersiz bir ad girin|Azure Cosmos DB hesabınızı tanımlayan benzersiz bir ad girin. Girdiğiniz kimliğe *documents.azure.com* eklenerek URI'niz oluşturulacağından benzersiz bir kimlik kullanın.<br><br>Kimlik yalnızca küçük harf, sayı ve kısa çizgi (-) karakterini kullanabilirsiniz. 3 ila 31 karakter uzunluğunda olmalıdır.
    API|MongoDB için Azure Cosmos DB API'si|API, oluşturulacak hesap türünü belirler. Azure Cosmos DB beş API sağlar: Belge veritabanları için çekirdek (SQL), grafik veritabanları için Gremlin, belge veritabanları, Azure tablosu ve Cassandra için API MongoDB Azure Cosmos DB. Şu anda, her bir API için ayrı bir hesap oluşturmanız gerekir. <br><br>**MongoDB** ' yi seçin. bu hızlı başlangıçta MongoDB ile birlikte çalışacak bir tablo oluşturuyorsunuz.|
    Location|Kullanıcılarınıza en yakın bölgeyi seçin|Azure Cosmos DB hesabınızın barındırılacağı coğrafi konumu seçin. Verilere en hızlı erişimi sağlamak için kullanıcılarınıza en yakın olan konumu kullanın.

    Seçin **gözden geçir + Oluştur**. Atlayabilirsiniz **ağ** ve **etiketleri** bölümü. 

    ![Azure Cosmos DB için yeni hesap sayfası](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Hesabın oluşturulması birkaç dakika sürer. Portalın **Tebrikler! MongoDB için tel protokol uyumluluğuna sahip Cosmos hesabınız hazırlanıyor** .

    ![Azure portalındaki Bildirimler bölmesi](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
