---
title: include dosyası
description: include dosyası
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 08/19/2020
ms.custom: include file
ms.openlocfilehash: efdd4a065e1eab55f5af420585a44754d42a43e9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96010388"
---
1. Azure portal menüsünde veya **giriş sayfasında**, **kaynak oluştur**' u seçin.

1. **Yeni** sayfasında, **Azure Cosmos DB** bulun ve seçin.

1. **Azure Cosmos DB** sayfasında **Oluştur**' u seçin.

1. **Azure Cosmos DB Hesabı Oluştur** sayfasında, yeni Azure Cosmos hesabına yönelik temel ayarları girin. 

    |Ayar|Değer|Açıklama |
    |---|---|---|
    |Abonelik|Abonelik adı|Bu Azure Cosmos hesabı için kullanmak istediğiniz Azure aboneliğini seçin. |
    |Kaynak Grubu|Kaynak grubu adı|Bir kaynak grubu seçin veya **Yeni oluştur** seçeneğini belirleyin ve yeni kaynak grubu için benzersiz bir ad girin. |
    |Hesap Adı|Benzersiz bir ad|Azure Cosmos hesabınızı tanımlayan bir ad girin. URI’nizi oluşturmak için sağladığınız ada *documents.azure.com* ekleneceği için benzersiz bir ad kullanın.<br><br>Ad yalnızca küçük harf, sayı ve kısa çizgi (-) karakterini içerebilir. 3-44 karakter uzunluğunda olmalıdır.|
    |API|Oluşturulacak hesap türü|SQL söz dizimini kullanarak belge veritabanı ve sorgusu oluşturmak için **Cekirdek (SQL)** seçeneğini belirleyin. <br><br>API, oluşturulacak hesap türünü belirler. Azure Cosmos DB beş API sağlar: belge verileri için çekirdek (SQL) ve MongoDB, Graf verileri için Gremlin, Azure tablosu ve Cassandra. Şu anda, her API için farklı bir hesap oluşturmanız gerekir. |
    |Kapasite modu|Sağlanan aktarım hızı veya sunucusuz|[Sağlanan aktarım](../articles/cosmos-db/set-throughput.md) hızı modunda bir hesap oluşturmak için **sağlanan aktarım hızı** ' nı seçin. [Sunucusuz modda bir](../articles/cosmos-db/serverless.md) hesap oluşturmak Için **sunucusuz** ' ı seçin.|
    |Ücretsiz Katman İndirimi Uygula|Uygula veya Uygula|Azure Cosmos DB ücretsiz katman sayesinde ilk 400 RU/sn ve 5 GB depolama alanını hesapta ücretsiz olarak alırsınız. [Ücretsiz katman](https://azure.microsoft.com/pricing/details/cosmos-db/)hakkında daha fazla bilgi edinin.|
    |Konum|Kullanıcılarınıza en yakın bölge|Azure Cosmos DB hesabınızın barındırılacağı coğrafi konumu seçin. Verilere en hızlı erişimi sağlamak için kullanıcılarınıza en yakın olan konumu kullanın.|
    |Hesap Türü|Üretim veya üretim dışı|Hesap bir üretim iş yükü için kullanılacaksa, **Üretim** ' ı seçin. Hesap üretim dışı, örneğin geliştirme, test, QA veya hazırlama için kullanılacaksa, **Üretim dışı** seçeneğini belirleyin. Bu, Portal deneyimini denetleyen ancak temel alınan Azure Cosmos DB hesabını etkilemeyen bir Azure Kaynak etiketi ayarıdır. Bu değeri dilediğiniz zaman değiştirebilirsiniz.|

    > [!NOTE]
    > Azure aboneliği başına en fazla bir ücretsiz katman Azure Cosmos DB hesabınız olabilir ve hesabı oluştururken kabul etmeniz gerekir. Ücretsiz katman indirimi uygulama seçeneğini görmüyorsanız bu, abonelikteki başka bir hesabın ücretsiz katmanla zaten etkinleştirildiği anlamına gelir.
   
    > [!NOTE]
    > **Kapasite modu** olarak **sunucusuz** ' i seçerseniz aşağıdaki seçenekler kullanılamaz:
    > - Ücretsiz Katman İndirimi Uygula
    > - Coğrafi yedeklilik
    > - Birden Çok Bölgeli Yazmalar
    
    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png" alt-text="Azure Cosmos DB için yeni hesap sayfası":::

1. **Gözden geçir ve oluştur**’u seçin. **Ağ** ve **Etiketler** bölümlerini atlayabilirsiniz.

1. Hesap ayarlarını gözden geçirip **Oluştur** seçeneğini belirleyin. Hesabın oluşturulması birkaç dakika sürer. Portal sayfasında **Dağıtımınız tamamlandı** iletisinin görüntülenmesini bekleyin. 

    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png" alt-text="Azure portaldaki Bildirimler bölmesi":::

1. Azure Cosmos DB hesabı sayfasına gitmek için **Kaynağa git** seçeneğini belirleyin. 

    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png" alt-text="Azure Cosmos DB hesabı sayfası":::
