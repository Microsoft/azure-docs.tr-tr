---
title: HBase .NET SDK 'sını kullanma-Azure HDInsight
description: Tablo oluşturmak ve silmek ve veri okumak ve yazmak için HBase .NET SDK 'sını kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/02/2019
ms.openlocfilehash: 70c1dcc133f01cf72392f444192d977440c55cf4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866633"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Apache HBase için .NET SDK 'sını kullanma

[Apache HBase](apache-hbase-overview.md) , verileriniz ile çalışmak için iki birincil seçenek sunar: [sorgular Apache Hive ve HBase 'in tekrar eden API 'si çağrıları](apache-hbase-tutorial-get-started-linux.md). `curl`Komutunu veya benzer bir yardımcı programını kullanarak doğrudan REST API çalışabilirsiniz.

C# ve .NET uygulamaları için, [.net Için Microsoft HBASE Rest Istemci kitaplığı](https://www.nuget.org/packages/Microsoft.HBase.Client/) , hbase REST API en üstünde bir istemci kitaplığı sağlar.

## <a name="install-the-sdk"></a>SDK Yükleme

HBase .NET SDK 'Sı, Visual Studio **NuGet paket yöneticisi konsolundan** aşağıdaki komutla yüklenebilen bir NuGet paketi olarak sunulmaktadır:

```console
Install-Package Microsoft.HBase.Client
```

## <a name="instantiate-a-new-hbaseclient-object"></a>Yeni bir HBaseClient nesnesi örneği oluştur

SDK 'yı kullanmak için, yeni bir nesne örneği oluşturun, `HBaseClient` `ClusterCredentials` `Uri` kümelerinizi kümenize geçirerek, Hadoop Kullanıcı adını ve parolasını kullanın.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

CLUSTERNAME değerini HDInsight HBase küme adınızla ve Kullanıcı adı ve parola ile küme oluşturma sırasında belirtilen Apache Hadoop kimlik bilgileriyle değiştirin. Varsayılan Hadoop Kullanıcı adı **admin**' dir.

## <a name="create-a-new-table"></a>Yeni tablo oluşturma

HBase verileri tablolarda depolar. Tablo bir *Rowkey*, birincil anahtar ve *sütun aileleri* olarak adlandırılan bir veya daha fazla sütun grubundan oluşur. Her tablodaki veri, *bölgelere* bir rowkey aralığı tarafından yatay olarak dağıtılır. Her bölgenin bir başlangıç ve bitiş anahtarı vardır. Bir tabloda bir veya daha fazla bölge olabilir. Tablodaki veriler büyüdükçe HBase, büyük bölgeleri daha küçük bölgelere ayırır. Bölgeler, tek bir bölge sunucusunun birden çok bölgeyi depolayabileceği *bölge sunucularında* depolanır.

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

Veri eklemek için, satır tanımlayıcısı olarak benzersiz bir satır anahtarı belirtirsiniz. Tüm veriler bir `byte[]` dizide depolanır. Aşağıdaki kod, `title` `director` `release_date` en sık erişilen sütunlar olduğundan T1 sütun ailesine, ve sütunlarını tanımlar ve ekler. `description`Ve `tagline` sütunları T2 sütun ailesine eklenir. Verilerinizi gerektiği gibi sütun ailelerine göre bölümleyebilirsiniz.

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

HBase, [Cloud BigTable](https://cloud.google.com/bigtable/)' ı uygular, bu nedenle veri biçimi aşağıdaki görüntüye benzer şekilde görünür:

:::image type="content" source="./media/apache-hbase-rest-sdk/hdinsight-table-roles.png" alt-text="Apache HBase örnek veri çıktısı" border="true":::

## <a name="select-data"></a>Verileri seçme

Bir HBase tablosundan veri okumak için, tablo adını ve satır anahtarını `GetCellsAsync` döndürmek üzere yöntemine geçirin `CellSet` .

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

Bu durumda, yalnızca benzersiz bir anahtar için yalnızca bir satır olması gerektiği için kod yalnızca ilk eşleşen satırı döndürür. Döndürülen değer diziden biçim olarak değiştirildi `string` `byte[]` . Ayrıca, filmin Yayın tarihi için bir tamsayı gibi diğer türlere de değeri dönüştürebilirsiniz:

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

* [HDInsight'ta Apache HBase örneğiyle çalışmaya başlama](apache-hbase-tutorial-get-started-linux.md)
* [Apache HBase ile gerçek zamanlı Twitter yaklaşımını çözümle](./apache-hbase-tutorial-get-started-linux.md) ile uçtan uca bir uygulama oluşturun