---
title: HBase .NET SDK 'sını kullanma-Azure HDInsight
description: Tablo oluşturmak ve silmek ve veri okumak ve yazmak için HBase .NET SDK 'sını kullanın.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/13/2017
ms.author: ashishth
ms.openlocfilehash: d998ff44804a2dcd2b3282679a9cb53f893991e3
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077161"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Apache HBase için .NET SDK 'sını kullanma

[Apache HBase](apache-hbase-overview.md) , verilerle çalışmak için iki birincil seçenek sunar: [Sorgu Apache Hive ve HBase 'In yeniden takip eden API 'si çağrıları](apache-hbase-tutorial-get-started-linux.md). `curl` Komutunu veya benzer bir yardımcı programını kullanarak doğrudan REST API çalışabilirsiniz.

Ve C# .NET uygulamaları için, [.net Için MICROSOFT HBase Rest Istemci kitaplığı](https://www.nuget.org/packages/Microsoft.HBase.Client/) , HBase REST API en üstünde bir istemci kitaplığı sağlar.

## <a name="install-the-sdk"></a>SDK yükle

HBase .NET SDK 'Sı, Visual Studio **NuGet paket yöneticisi konsolundan** aşağıdaki komutla yüklenebilen bir NuGet paketi olarak sunulmaktadır:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Yeni bir HBaseClient nesnesi örneği oluştur

SDK 'yı kullanmak için, yeni `HBaseClient` bir nesne örneği oluşturun, kümelerinizi kümenize `ClusterCredentials` geçirerek `Uri` , Hadoop Kullanıcı adını ve parolasını kullanın.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

CLUSTERNAME değerini HDInsight HBase küme adınızla ve Kullanıcı adı ve parola ile küme oluşturma sırasında belirtilen Apache Hadoop kimlik bilgileriyle değiştirin. Varsayılan Hadoop Kullanıcı adı **admin**' dir.

## <a name="create-a-new-table"></a>Yeni tablo oluştur

HBase verileri tablolarda depolar. Tablo bir *Rowkey*, birincil anahtar ve *sütun aileleri*olarak adlandırılan bir veya daha fazla sütun grubundan oluşur. Her tablodaki veri, *bölgelere*bir rowkey aralığı tarafından yatay olarak dağıtılır. Her bölgenin bir başlangıç ve bitiş anahtarı vardır. Bir tabloda bir veya daha fazla bölge olabilir. Tablodaki veriler büyüdükçe HBase, büyük bölgeleri daha küçük bölgelere ayırır. Bölgeler, tek bir bölge sunucusunun birden çok bölgeyi depolayabileceği *bölge sunucularında*depolanır.

Veriler, fiziksel olarak *Hfiles*'da depolanır. Tek bir HFile, bir tablo, bir bölge ve bir sütun ailesi için veri içerir. HFile içindeki satırlar Rowkey üzerinde sıralanmış olarak depolanır. Her HFile, satırları hızlı bir şekilde almak için bir *B + ağaç* dizinine sahiptir.

Yeni bir tablo oluşturmak için bir `TableSchema` ve sütunları belirtin. Aşağıdaki kod, ' RestSDKTable ' tablosunun zaten mevcut olup olmadığını denetler. yoksa tablo oluşturulur.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

Bu yeni tabloda iki sütunlu aileler, T1 ve T2 vardır. Sütun aileleri farklı HFiles 'lar üzerinde ayrı olarak depolandığından, sık sorgulanan veriler için ayrı bir sütun ailesine sahip olmak mantıklı olur. Aşağıdaki [veri Ekle](#insert-data) örneğinde, sütunlar T1 sütun ailesine eklenir.

## <a name="delete-a-table"></a>Bir tablo silme

Bir tabloyu silmek için:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Veri ekleme

Veri eklemek için, satır tanımlayıcısı olarak benzersiz bir satır anahtarı belirtirsiniz. Tüm veriler bir `byte[]` dizide depolanır. Aşağıdaki kod, en sık erişilen sütunlar `title`olduğundan `director`T1 sütun `release_date` ailesine, ve sütunlarını tanımlar ve ekler. `description` Ve`tagline` sütunları T2 sütun ailesine eklenir. Verilerinizi gerektiği gibi sütun ailelerine göre bölümleyebilirsiniz.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

HBase, [Cloud BigTable](https://cloud.google.com/bigtable/)' ı uygular, bu nedenle veri biçimi şöyle görünür:

![Apache HBase örnek veri çıktısı](./media/apache-hbase-rest-sdk/hdinsight-table-roles.png)

## <a name="select-data"></a>Verileri seçme

Bir HBase tablosundan veri okumak için, tablo adını ve satır anahtarını `GetCellsAsync` `CellSet`döndürmek üzere yöntemine geçirin.

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

Bu durumda, yalnızca benzersiz bir anahtar için yalnızca bir satır olması gerektiği için kod yalnızca ilk eşleşen satırı döndürür. Döndürülen değer `string` `byte[]` diziden biçim olarak değiştirildi. Ayrıca, filmin Yayın tarihi için bir tamsayı gibi diğer türlere de değeri dönüştürebilirsiniz:

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>Satırları üzerinde Tara

HBase, `scan` bir veya daha fazla satır almak için kullanır. Bu örnek 10 toplu işlem halinde birden çok satır ister ve anahtar değerleri 25 ile 35 arasında olan verileri alır. Tüm satırları aldıktan sonra, kaynakları temizlemek için tarayıcıyı silin.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight 'ta Apache HBase örneği ile çalışmaya başlama](apache-hbase-tutorial-get-started-linux.md)
* [Apache HBase ile gerçek zamanlı Twitter yaklaşımını çözümle](../hdinsight-hbase-analyze-twitter-sentiment.md) ile uçtan uca bir uygulama oluşturun
