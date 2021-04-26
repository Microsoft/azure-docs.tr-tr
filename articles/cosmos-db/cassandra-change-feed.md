---
title: Cassandra için Azure Cosmos DB API 'sindeki akışı değiştirme
description: Veri üzerinde yapılan değişiklikleri almak için Cassandra için Azure Cosmos DB API 'sindeki değişiklik akışını nasıl kullanacağınızı öğrenin.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: 10f037dddcce43a1e023982af816660bd325d57f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97605102"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Cassandra için Azure Cosmos DB API 'sindeki akışı değiştirme
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra için Azure Cosmos DB API 'sindeki [akış desteğini değiştirme](change-feed.md) , Cassandra sorgu dilindeki (CQL) sorgu koşulları aracılığıyla kullanılabilir. Bu koşul koşullarını kullanarak, değişiklik akışı API 'sini sorgulayabilirsiniz. Uygulamalar, birincil anahtarı (bölüm anahtarı olarak da bilinir) kullanarak, CQL 'de gerekli olduğu gibi, bir tabloda yapılan değişiklikleri alabilir. Daha sonra sonuçlara göre daha fazla eylem gerçekleştirebilirsiniz. Tablodaki satırlarda yapılan değişiklikler, değiştirilme zamanı sırasına ve bölüm anahtarı başına sıralama sırasına göre yakalanır.

Aşağıdaki örnek .NET kullanarak bir Cassandra API keyspace tablosundaki tüm satırlarda nasıl değişiklik akışı alınacağını göstermektedir. COSMOS_CHANGEFEED_START_TIME () koşulu doğrudan CQL içinde, değişiklik akışındaki öğeleri belirli bir başlangıç zamanından (Bu durumda geçerli DateTime) sorgulamak için kullanılır. [Burada C# ve](/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/) [Java için](https://github.com/Azure-Samples/cosmos-changefeed-cassandra-java)tam örneği indirebilirsiniz.

Her yinelemede sorgu, sayfalama durumu kullanılarak yapılan son noktada devam ediyor. Keyspace 'teki tabloda yeni değişikliklerin sürekli akışını görebiliriz. Eklenen veya güncellenen satırlardaki değişiklikleri görüyoruz. Cassandra API değişiklik akışını kullanarak silme işlemleri için izleme şu anda desteklenmiyor.

# <a name="java"></a>[Java](#tab/java)

```java
    Session cassandraSession = utils.getSession();

    try {
        DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");  
        LocalDateTime now = LocalDateTime.now().minusHours(6).minusMinutes(30);  
        String query="SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME()='" 
            + dtf.format(now)+ "'";
        
        byte[] token=null; 
        System.out.println(query); 
        while(true)
        {
            SimpleStatement st=new  SimpleStatement(query);
            st.setFetchSize(100);
            if(token!=null)
                st.setPagingStateUnsafe(token);
            
            ResultSet result=cassandraSession.execute(st) ;
            token=result.getExecutionInfo().getPagingState().toBytes();
            
            for(Row row:result)
            {
                System.out.println(row.getString("user_name"));
            }
        }
    } finally {
        utils.close();
        LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL");
    }
```

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```
---

Birincil anahtara göre tek bir satırdaki değişiklikleri almak için sorguya birincil anahtar ekleyebilirsiniz. Aşağıdaki örnek, "user_id = 1" olduğu satırdaki değişikliklerin nasıl izleneceğini gösterir

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

# <a name="java"></a>[Java](#tab/java)

```java
    String query="SELECT * FROM uprofile.user where user_id=1 and COSMOS_CHANGEFEED_START_TIME()='" 
                       + dtf.format(now)+ "'";
    SimpleStatement st=new  SimpleStatement(query);
```
---
## <a name="current-limitations"></a>Geçerli sınırlamalar

Cassandra API ile değişiklik akışı kullanılırken aşağıdaki sınırlamalar geçerlidir:

* Ekler ve güncelleştirmeler şu anda destekleniyor. Silme işlemi henüz desteklenmiyor. Geçici bir çözüm olarak, silinmekte olan satırlara yumuşak bir işaretleyici ekleyebilirsiniz. Örneğin, "Deleted" adlı satıra bir alan ekleyin ve "true" olarak ayarlayın.
* Son güncelleştirme, çekirdek SQL API 'sinde olduğu gibi kalıcı hale getirilir ve varlık için ara güncelleştirmeler kullanılamaz.


## <a name="error-handling"></a>Hata işleme

Aşağıdaki hata kodları ve iletileri Cassandra API içindeki değişiklik akışı kullanılırken desteklenir:

* **Http hata kodu 429** -değişiklik akışı hız sınırlı olduğunda boş bir sayfa döndürür.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Resource Manager şablonları kullanarak Azure Cosmos DB Cassandra API kaynaklarını yönetme](./templates-samples-cassandra.md)