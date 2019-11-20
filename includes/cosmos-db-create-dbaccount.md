---
title: include dosyası
description: include dosyası
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.author: rimman
ms.custom: include file
ms.openlocfilehash: 93652273dda16e93bdcf9123d7d4a9b3fc956fe9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466198"
---
1. Bir Azure Cosmos DB hesabı oluşturmak için [Azure Portal](https://portal.azure.com/) gidin. Arama yapın ve **Azure Cosmos DB**seçin.

   ![Azure portalındaki Veritabanları bölmesi](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. **Add (Ekle)** seçeneğini belirleyin.
1. **Azure Cosmos DB hesabı oluştur** sayfasında, yeni Azure Cosmos hesabı için temel ayarları girin. 

    |Ayar|Değer|Açıklama |
    |---|---|---|
    |Abonelik|Abonelik adı|Bu Azure Cosmos hesabı için kullanmak istediğiniz Azure aboneliğini seçin. |
    |Kaynak Grubu|Kaynak grubu adı|Bir kaynak grubu seçin veya **Yeni oluştur**' u seçip yeni kaynak grubu için benzersiz bir ad girin. |
    |Hesap Adı|Benzersiz bir ad|Azure Cosmos hesabınızı tanımlamak için bir ad girin. Girdiğiniz kimliğe *documents.azure.com* eklenerek URI'niz oluşturulacağından benzersiz bir kimlik kullanın.<br><br>KIMLIK yalnızca küçük harf, sayı ve kısa çizgi (-) karakterini içerebilir. 3-31 karakter uzunluğunda olmalıdır.|
    |API|Oluşturulacak hesabın türü|SQL sözdizimini kullanarak bir belge veritabanı ve sorgu oluşturmak için **Core (SQL)** seçeneğini belirleyin. <br><br>API, oluşturulacak hesap türünü belirler. Azure Cosmos DB beş API sağlar: belge verileri için çekirdek (SQL) ve MongoDB, Graf verileri için Gremlin, Azure tablosu ve Cassandra. Şu anda her API için ayrı bir hesap oluşturmanız gerekir. <br><br>[SQL API 'si hakkında daha fazla bilgi edinin](../articles/cosmos-db/documentdb-introduction.md).|
    |Konum|Kullanıcılarınıza en yakın bölge|Azure Cosmos DB hesabınızın barındırılacağı coğrafi konumu seçin. Verilere en hızlı erişim sağlamak için kullanıcılarınıza en yakın konumu kullanın.|

   ![Azure Cosmos DB için yeni hesap sayfası](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-account.png)

1. **İncele ve oluştur**’u seçin. **Ağ** ve **Etiketler** bölümlerini atlayabilirsiniz.

1. Hesap ayarlarını gözden geçirin ve ardından **Oluştur**' u seçin. Hesabın oluşturulması birkaç dakika sürer. Portalın, **dağıtımınızın**tamamlanmasını bekleyin. 

    ![Azure portalındaki Bildirimler bölmesi](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Azure Cosmos DB hesap sayfasına gitmek için **Kaynağa Git** ' i seçin. 

    ![Azure Cosmos DB hesabı sayfası](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
