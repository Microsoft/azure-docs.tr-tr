---
title: Phoenix Query Server REST SDK-Azure HDInsight
description: Azure HDInsight 'ta Phoenix Query Server için REST SDK 'Yı yükleyip kullanın.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.openlocfilehash: c9e9258fb7ace93d0866463563d328456cbd1daa
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311685"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>Apache Phoenix sorgu sunucusu REST SDK 'Sı

[Apache Phoenix](https://phoenix.apache.org/) , [Apache HBase](apache-hbase-overview.md)üzerinde açık kaynaklı, yüksek düzeyde paralel ilişkisel veritabanı katmanıdır. Phoenix, SQL benzeri sorguları [Sqlline](apache-hbase-query-with-phoenix.md)gibi SSH araçları aracılığıyla HBase ile birlikte kullanmanıza olanak sağlar. Phoenix Ayrıca, istemci iletişimi için iki taşıma mekanizmasını destekleyen bir ince istemci olan Phoenix Query Server (PQS) adlı bir HTTP sunucusu da sağlar: JSON ve protokol arabellekleri. Protokol arabellekleri varsayılan mekanizmadır ve JSON 'dan daha verimli bir iletişim sağlar.

Bu makalede, PQS REST SDK 'sının tablo oluşturmak, tek tek ve toplu olarak satırlar oluşturmak ve SQL deyimlerini kullanarak veri seçmek için nasıl kullanılacağı açıklanır. Örnekler, [Apache Phoenix sorgu sunucusu için Microsoft .net sürücüsünü](https://www.nuget.org/packages/Microsoft.Phoenix.Client)kullanır. Bu SDK, bir serileştirme biçimi için özel olarak protokol arabellekleri kullanan [Apache calalaltik](https://calcite.apache.org/avatica/) API 'lerinde oluşturulmuştur.

Daha fazla bilgi için bkz. [Apache Cal, Avatika protokol arabellekleri başvurusu](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>SDK yükle

Apache Phoenix sorgu sunucusu için Microsoft .NET sürücüsü, Visual Studio **NuGet paket yöneticisi konsolundan** aşağıdaki komutla yüklenebilen bir NuGet paketi olarak sunulmaktadır:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Yeni PhoenixClient nesnesi örneği oluştur

Kitaplığı kullanmaya başlamak için yeni bir `PhoenixClient` nesnesi örneği oluşturun, `ClusterCredentials` ' in `Uri` ' yi kümenize ve kümenin Apache Hadoop Kullanıcı adı ve parolasına koyun.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

CLUSTERNAME değerini HDInsight HBase küme adınızla ve Kullanıcı adı ve parola ile küme oluşturma sırasında belirtilen Hadoop kimlik bilgileriyle değiştirin. Varsayılan Hadoop Kullanıcı adı **admin**' dir.

## <a name="generate-unique-connection-identifier"></a>Benzersiz bağlantı tanımlayıcısı oluştur

PQS 'ye bir veya daha fazla istek göndermek için, istek (ler) i bağlantıyla ilişkilendirmek üzere benzersiz bir bağlantı tanımlayıcısı eklemeniz gerekir.

```csharp
string connId = Guid.NewGuid().ToString();
```

Her örnek öncelikle `OpenConnectionRequestAsync` yöntemine bir çağrı yapar ve benzersiz bağlantı tanımlayıcısını geçer. Sonra, `ConnectionProperties` ve `RequestOptions` tanımlayın, bu nesneleri ve oluşturulan bağlantı tanımlayıcısını `ConnectionSyncRequestAsync` yöntemine geçirerek. PQS 'nin `ConnectionSyncRequest` nesnesi, hem istemci hem de sunucunun veritabanı özelliklerinin tutarlı bir görünümüne sahip olmasını sağlamaya yardımcı olur.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest ve ConnectionProperties

@No__t-0 ' ı çağırmak için bir `ConnectionProperties` nesnesi geçirin.

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

İlgilendiğiniz bazı özellikler şunlardır:

| Özellik | Açıklama |
| -- | -- |
| Otomatik yürütme | Phoenix işlemleri için `autoCommit` ' ın etkinleştirilip etkinleştirilmeyeceğini belirten bir Boole değeri. |
| ReadOnly | Bağlantının salt okunurdur olduğunu belirten bir Boole değeri. |
| Işlem yalıtımı | JDBC belirtimine göre işlem yalıtımı düzeyini belirten bir tamsayı-aşağıdaki tabloya bakın.|
| Katalog | Bağlantı özellikleri getirilirken kullanılacak kataloğun adı. |
| Şema | Bağlantı özellikleri getirilirken kullanılacak şemanın adı. |
| IsDirty ayarlanamıyor | Özelliklerin değiştirilip değiştirilmediğini belirten bir Boole değeri. |

@No__t-0 değerleri aşağıda verilmiştir:

| Yalıtım değeri | Açıklama |
| -- | -- |
| 0 | İşlemler desteklenmiyor. |
| 1 | Kirli okumalar, tekrarlanabilir olmayan okumalar ve hayalet okuma gerçekleşmeyebilir. |
| 2 | Kirli okumalar engellenir, ancak yinelenemeyen okuma ve hayalet okuma gerçekleşmeyebilir. |
| 4 | Kirli okumalar ve yinelenebilir olmayan okumalar engellenir, ancak hayalet okuma gerçekleşmeyebilir. |
| 8 | Kirli okuma, yinelenebilir olmayan okuma ve hayalet okuma engellenir. |

## <a name="create-a-new-table"></a>Yeni tablo oluştur

Diğer RDBMS gibi HBase, verileri tablolar halinde depolar. Phoenix, birincil anahtar ve sütun türlerini tanımlarken yeni tablolar oluşturmak için standart SQL sorguları kullanır.

Bu örnek ve sonraki tüm örnekler, [Yeni bir PhoenixClient nesnesi örneği oluşturma](#instantiate-new-phoenixclient-object)bölümünde tanımlandığı gibi örneklenmiş `PhoenixClient` nesnesini kullanır.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

Yukarıdaki örnek, `IF NOT EXISTS` seçeneğini kullanarak `Customers` adlı yeni bir tablo oluşturur. @No__t-0 çağrısı, Avitika (PQS) sunucusunda yeni bir ifade oluşturur. @No__t-0 bloğu döndürülen `CreateStatementResponse` ve `OpenConnectionResponse` nesnelerini kapatır.

## <a name="insert-data-individually"></a>Verileri ayrı olarak ekle

Bu örnekte, Amerikan devleti ve bölge kısaltmalarının `List<string>` koleksiyonuna başvuran tek bir veri ekleme gösterilmektedir:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

Tablonun `StateProvince` sütun değeri, sonraki bir SELECT işleminde kullanılacaktır.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

INSERT ifadesinin yürütülmesi için yapı, yeni bir tablo oluşturmaya benzer. @No__t-0 bloğunun sonunda, işlemin açıkça yürütüldüğü unutulmamalıdır. Bu örnek, bir INSERT TRANSACTION 300 kez yinelenir. Aşağıdaki örnekte, daha verimli bir toplu iş ekleme işlemi gösterilmektedir.

## <a name="batch-insert-data"></a>Toplu veri ekleme

Aşağıdaki kod, verileri ayrı olarak eklemeye yönelik kodla neredeyse aynıdır. Bu örnek, Prepared ifadesiyle `ExecuteRequestAsync` ' yi tekrar tekrar çağırmak yerine `ExecuteBatchRequestAsync` ' e yapılan çağrıda `UpdateBatch` nesnesini kullanır.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Tek bir test ortamında, tek tek 300 yeni kayıt eklemek neredeyse 2 dakika sürdü. Buna karşılık, toplu işlem olarak 300 kayıt eklemek yalnızca 6 saniye gerektirir.

## <a name="select-data"></a>Verileri seçme

Bu örnekte, birden çok sorguyu yürütmek için bir bağlantının nasıl yeniden kullanılacağı gösterilmektedir:

1. Tüm kayıtlar ' ı seçin ve ardından varsayılan en fazla 100 ' dan sonra kalan kayıtları getirin.
2. Tek skalar sonucu almak için bir toplam Row Count SELECT ifadesini kullanın.
3. Durum veya bölge başına toplam müşteri sayısını döndüren bir SELECT ifadesini yürütün.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

@No__t-0 deyimlerinin çıktısı aşağıdaki sonuç olmalıdır:

```
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

## <a name="next-steps"></a>Sonraki adımlar 

* [HDInsight 'ta Apache Phoenix](../hdinsight-phoenix-in-hdinsight.md)
* [Apache HBase REST SDK 'sını kullanma](apache-hbase-rest-sdk.md)
