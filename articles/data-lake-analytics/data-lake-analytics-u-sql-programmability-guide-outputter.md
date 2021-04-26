---
title: Azure Data Lake için U-SQL Kullanıcı tanımlı outputter Programlama Kılavuzu
description: U-SQL UDO programlama yoluyla Kullanıcı tanımlı outputter hakkında bilgi edinin.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 56b104b5cc8f8923445455c71fe2418e39539b8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512634"
---
# <a name="use-user-defined-outputter"></a>Kullanıcı tarafından tanımlanan çıkış aracı kullanma

## <a name="u-sql-udo-user-defined-outputter"></a>U-SQL UDO: Kullanıcı tanımlı outputter
Kullanıcı tanımlı outputter, yerleşik U-SQL işlevlerini genişletmenizi sağlayan başka bir U-SQL UDO. Extractor ile benzer şekilde, çeşitli yerleşik outputler vardır.

* *Outputters. Text ()*: verileri farklı kodlamalara ait sınırlandırılmış metin dosyalarına yazar.
* *Outputters.Csv ()*: verileri farklı kodlamalar için virgülle ayrılmış değer (CSV) dosyalarına yazar.
* *Outputters. TSV ()*: verileri farklı kodlamalara ait sekmeyle ayrılmış değer (TSV) dosyalarına yazar.

Özel çıktıcısı, verileri özel bir tanımlı biçimde yazmanızı sağlar. Bu, aşağıdaki görevler için yararlı olabilir:

* Yarı yapılandırılmış veya yapılandırılmamış dosyalara veri yazma.
* Veri yazma desteklenmeyen kodlamalar.
* Çıktı verilerini değiştirme veya özel öznitelikler ekleme.

## <a name="how-to-define-and-use-user-defined-outputter"></a>Kullanıcı tanımlı outputter tanımlama ve kullanma
Kullanıcı tanımlı outputter tanımlamak için arabirimini oluşturuyoruz `IOutputter` .

Temel `IOutputter` sınıf uygulamasını aşağıda verilmiştir:

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Outputter için sütun/satır sınırlayıcıları, kodlama vb. gibi tüm giriş parametrelerinin, sınıfın oluşturucusunda tanımlanması gerekir. `IOutputter`Arabirim ayrıca geçersiz kılma için bir tanım içermelidir `void Output` . Öznitelik, `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` atomik dosya işleme için isteğe bağlı olarak ayarlanabilir. Daha fazla bilgi için aşağıdaki ayrıntılara bakın.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` her giriş satırı için çağrılır. `IUnstructuredWriter output`Satır kümesini döndürür.
* Oluşturucu sınıfı, parametreleri Kullanıcı tanımlı outputter 'a geçirmek için kullanılır.
* `Close` , pahalı durum yayınlamak veya son satırın ne zaman yazıldığını belirleyebilmek için isteğe bağlı olarak geçersiz kılmak için kullanılır.

**SqlUserDefinedOutputter** özniteliği, türün Kullanıcı tanımlı bir outputter olarak kaydedilmesi gerektiğini belirtir. Bu sınıf devralınamaz.

SqlUserDefinedOutputter, Kullanıcı tanımlı bir outputter tanımı için isteğe bağlı bir özniteliktir. AtomicFileProcessing özelliğini tanımlamak için kullanılır.

* bool AtomicFileProcessing   

* **true** = bu outputter 'ın atomik çıkış dosyaları gerektirdiğini BELIRTIR (JSON, XML,...)
* **false** = bu outputter 'ın bölünmüş/dağıtılmış DOSYALARLA (CSV, seq,...) ilgilenebileceğini belirtir

Ana programlama nesneleri **satır** ve **çıktıdır**. **Satır** nesnesi, çıkış verilerini arabirim olarak listelemek için kullanılır `IRow` . Çıktı verilerini hedef dosyaya ayarlamak için **Çıkış** kullanılır.

Çıkış verilerine `IRow` arabiriminden erişilir. Çıkış verileri bir seferde bir satıra geçirilir.

Bağımsız değerler, IRow arabiriminin get yöntemi çağırarak numaralandırılır:

```csharp
row.Get<string>("column_name")
```

Tek tek sütun adları, çağırarak belirlenebilir `row.Schema` :

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Bu yaklaşım, tüm meta veri şemaları için esnek bir outputderme oluşturmanızı sağlar.

Çıktı verileri kullanılarak dosyaya yazılır `System.IO.StreamWriter` . Stream parametresi `output.BaseStream` öğesinin parçası olarak olarak ayarlanır `IUnstructuredWriter output` .

Her satır yinelemesi sonrasında dosyaya veri arabelleğini temizleme önemli olduğunu unutmayın. Ayrıca, `StreamWriter` nesnesi atılabilir özniteliğiyle (varsayılan) ve **using** anahtar sözcüğüyle birlikte kullanılmalıdır:

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Aksi takdirde, her yinelemeden sonra açıkça flush () yöntemini çağırın. Bu, aşağıdaki örnekte gösterilmektedir.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Kullanıcı tanımlı outputter için üst bilgileri ve altbilgileri ayarlama
Bir üst bilgi ayarlamak için tek yineleme yürütme akışını kullanın.

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

İlk `if (isHeaderRow)` bloktaki kod yalnızca bir kez yürütülür.

Alt bilgi için, `System.IO.Stream` Object () örneğine başvuruyu kullanın `output.BaseStream` . Alt bilgisini arabirimin Close () yöntemine yazın `IOutputter` .  (Daha fazla bilgi için aşağıdaki örneğe bakın.)

Aşağıda, Kullanıcı tanımlı bir outputter örneği verilmiştir:

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

Ve U-SQL taban betiği:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Bu, tablo verileri içeren bir HTML dosyası oluşturan bir HTML çıktıdır.

### <a name="call-outputter-from-u-sql-base-script"></a>U-SQL taban betiğiyle çağrı çıktıcısı
Temel U-SQL betiğinden özel bir outputter çağırmak için, outputter nesnesinin yeni örneğinin oluşturulması gerekir.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Temel betikte nesnenin bir örneğini oluşturmaktan kaçınmak için, önceki örnekte gösterildiği gibi bir işlev sarmalayıcı oluşturuyoruz:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

Bu durumda, özgün çağrı aşağıdaki gibi görünür:

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="next-steps"></a>Sonraki adımlar
* [U-SQL programlama kılavuzu-genel bakış](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programlama kılavuzu-UDT ve UDADGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)