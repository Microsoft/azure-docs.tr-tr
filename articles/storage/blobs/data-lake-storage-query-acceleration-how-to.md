---
title: Azure Data Lake Storage sorgu hızlandırmasını kullanarak verileri filtreleme | Microsoft Docs
description: Depolama hesabınızdan verilerin bir alt kümesini almak için sorgu hızlandırmasını kullanın.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp
ms.openlocfilehash: 756258db1c6e91002bf3a7c2bd0f71f921ce655d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769940"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Azure Data Lake Storage sorgu hızlandırmasını kullanarak verileri filtreleme

Bu makalede, depolama hesabınızdan verilerin bir alt kümesini almak için sorgu hızlandırmasının nasıl kullanılacağı gösterilir. 

Sorgu hızlandırma, uygulama ve analiz çerçevelerinin yalnızca belirli bir işlemi gerçekleştirmek için gereksinim duydukları verileri alarak veri işlemeyi önemli ölçüde iyileştirmesini sağlar. Daha fazla bilgi için bkz. [Azure Data Lake Storage sorgu hızlandırma](data-lake-storage-query-acceleration.md).

## <a name="prerequisites"></a>Önkoşullar

- Azure depolama 'ya erişmek için bir Azure aboneliğine sahip olmanız gerekir. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

- **Genel amaçlı v2** depolama hesabı. bkz. [depolama hesabı oluşturma](../common/storage-account-create.md).

- SDK 'ya özgü herhangi bir önkoşulları görüntülemek için bir sekme seçin.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  Uygulanamaz

  ### <a name="net"></a>[.NET](#tab/dotnet)

  [.NET SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java Development Kit (JDK)](/java/azure/jdk/) sürüm 8 veya üstü

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > Bu makalede, Apache Maven kullanarak bir Java projesi oluşturduğunuzu varsayılmaktadır. Apache Maven kullanarak bir projenin nasıl oluşturulacağı hakkında bir örnek için bkz. [ayarlama](storage-quickstart-blobs-java.md#setting-up).
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3,8 veya üzeri.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Node.js SDK 'Yı kullanmak için ek önkoşul yoktur.

---

## <a name="enable-query-acceleration"></a>Sorgu hızlandırmasını etkinleştir

Sorgu hızlandırmasını kullanmak için, aboneliğiniz ile sorgu hızlandırma özelliğini kaydetmeniz gerekir. Özelliğin kaydedildiğini doğruladıktan sonra Azure depolama kaynak sağlayıcısı 'nı kaydetmeniz gerekir. 

### <a name="step-1-register-the-query-acceleration-feature"></a>1. Adım: sorgu hızlandırma özelliğini kaydetme

Sorgu hızlandırmasını kullanmak için, öncelikle aboneliğinizle birlikte sorgu hızlandırma özelliğini kaydetmeniz gerekir. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Bir Windows PowerShell komut penceresi açın.

1. `Connect-AzAccount` komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

   ```powershell
   Connect-AzAccount
   ```

2. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi ayarlayın.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>`Yer tutucu değerini ABONELIĞINIZIN kimliğiyle değiştirin.

3. Sorgu hızlandırma özelliğini [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) komutunu kullanarak kaydedin.

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](../../cloud-shell/overview.md)açın veya Azure CLI 'yı yerel olarak [yüklediyseniz](/cli/azure/install-azure-cli) , Windows PowerShell gibi bir komut konsol uygulaması açın.

2. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi depolama hesabının aboneliğine ayarlayın.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>`Yer tutucu değerini ABONELIĞINIZIN kimliğiyle değiştirin.

3. Sorgu hızlandırma özelliğini [az Feature Register](/cli/azure/feature#az_feature_register) komutunu kullanarak kaydedin.

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>2. Adım: özelliğin kaydedildiğini doğrulama

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Kaydın tamamlandığını doğrulamak için [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) komutunu kullanın.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kaydın tamamlandığını doğrulamak için [az Feature](/cli/azure/feature#az_feature_show) komutunu kullanın.

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>3. Adım: Azure depolama kaynak sağlayıcısını kaydetme

Kaydınız onaylandıktan sonra Azure depolama kaynak sağlayıcısı 'nı yeniden kaydetmeniz gerekir. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Kaynak sağlayıcısını kaydetmek için [register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) komutunu kullanın.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kaynak sağlayıcısını kaydetmek için [az Provider Register](/cli/azure/provider#az_provider_register) komutunu kullanın.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>Ortamınızı ayarlama

### <a name="step-1-install-packages"></a>1. Adım: paketleri yükler 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az Module Version 4.6.0 veya üstünü yükler.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Az önceki bir sürümünü güncelleştirmek için aşağıdaki komutu çalıştırın:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Bir komut istemi açın ve dizini ( `cd` ) proje klasörünüze değiştirin örneğin:

   ```console
   cd myProject
   ```

2. `12.5.0-preview.6`Komutunu kullanarak .net Için Azure Blob Storage istemci Kitaplığı ' nın sürümünü veya üstünü yükler `dotnet add package` . 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.8.0
   ```

3. Bu makalede görüntülenen örnekler, CSV dosyasını [Csvhelper](https://www.nuget.org/packages/CsvHelper/) kitaplığı kullanarak ayrıştırır. Bu kitaplığı kullanmak için aşağıdaki komutu kullanın.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Projenizin *pom.xml* dosyasını bir metin düzenleyicisinde açın. Aşağıdaki bağımlılık öğelerini bağımlılıklar grubuna ekleyin. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

[PIP](https://pypi.org/project/pip/)'Yi kullanarak Python için Azure Data Lake Storage istemci kitaplığı 'nı kullanın.

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Bir Terminal penceresi açarak ve ardından aşağıdaki komutu yazarak JavaScript için Data Lake istemci kitaplığı 'nı yüklersiniz.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>2. Adım: deyim ekleme

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Uygulanamaz

#### <a name="net"></a>[.NET](#tab/dotnet)

Bu `using` deyimleri, kod dosyanızın en üstüne ekleyin.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

Sorgu hızlandırma CSV ve JSON biçimli verileri alır. Bu nedenle, kullanmayı seçtiğiniz CSV veya JSON ayrıştırma kitaplıklarının using deyimlerini eklediğinizden emin olun. Bu makalede görüntülenen örneklerde, NuGet 'de bulunan [Csvhelper](https://www.nuget.org/packages/CsvHelper/) kitaplığını kullanarak bir CSV dosyası ayrıştırılabilir. Bu nedenle, bu `using` deyimleri kod dosyasının en üstüne ekleyeceğiz.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Bu makalede sunulan örnekleri derlemek için de bu deyimleri de eklemeniz gerekir `using` .

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Bu `import` deyimleri, kod dosyanızın en üstüne ekleyin.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

Bu import deyimlerini, kod dosyanızın en üstüne ekleyin.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

`storage-blob`Bu ifadeyi kod dosyanızın en üstüne yerleştirerek modülü dahil edin. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

Sorgu hızlandırma CSV ve JSON biçimli verileri alır. Bu nedenle, kullanmayı seçtiğiniz CSV veya JSON ayrıştırma modülleri için deyimler eklediğinizden emin olun. Bu makalede görüntülenen örneklerde, [Fast-CSV](https://www.npmjs.com/package/fast-csv) modülünü kullanarak bir CSV dosyası ayrıştırılabilir. Bu nedenle, bu ifadeyi kod dosyasının en üstüne ekleyeceğiz.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Filtre kullanarak veri alma

Bir sorgu hızlandırma isteğindeki satır filtresi koşullarını ve sütun projeksiyonlarını belirtmek için SQL 'i kullanabilirsiniz. Aşağıdaki kod, depolama alanındaki bir CSV dosyasını sorgular ve üçüncü sütunun değerle eşleştiği tüm veri satırlarını döndürür `Hemingway, Ernest` . 

- SQL sorgusunda, `BlobStorage` sorgulanmakta olan dosyayı belirtmek için anahtar sözcüğü kullanılır.

- Sütun başvuruları, `_N` ilk sütunun bulunduğu konum olarak belirtilir `_1` . Kaynak dosya bir başlık satırı içeriyorsa, üst bilgi satırında belirtilen ada göre sütunlara başvurabilirsiniz. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

Async yöntemi sorguyu `BlobQuickQueryClient.QueryAsync` sorgu HıZLANDıRMA API 'sine gönderir ve ardından sonuçları bir [Stream](/dotnet/api/system.io.stream) nesnesi olarak uygulamaya geri akış sağlar.

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture, hasHeaderRecord: true) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Parser.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

Yöntemi sorguyu `BlobQuickQueryClient.openInputStream()` sorgu HıZLANDıRMA API 'sine gönderir ve ardından sonuçları `InputStream` başka bir InputStream nesnesi gibi okuyabilen bir nesne olarak uygulamaya geri gönderir.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Bu örnek sorguyu sorgu hızlandırma API 'sine gönderir ve ardından sonuçları geri akıp. `blob` `queryHemingway` Yardımcı işlevine geçirilen nesne [blockblobclient](/javascript/api/@azure/storage-blob/blockblobclient)türündedir. [Blockblobclient](/javascript/api/@azure/storage-blob/blockblobclient) nesnesinin nasıl alınacağı hakkında daha fazla bilgi edinmek için bkz. [hızlı başlangıç: JavaScript V12 SDK Ile blobları yönetme Node.js](storage-quickstart-blobs-nodejs.md).

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>Belirli sütunları al

Sonuçlarınızı bir sütun alt kümesiyle kapsamını belirleyebilirsiniz. Bu şekilde, yalnızca belirli bir hesaplamayı gerçekleştirmek için gereken sütunları elde edersiniz. Bu, uygulama performansını geliştirir ve ağ üzerinden daha az veri aktarıldığından maliyeti azaltır. 

Bu kod, `BibNum` veri kümesindeki tüm kitaplar için yalnızca sütunu alır. Ayrıca, sorgudaki sütunlara başvurmak için kaynak dosyasındaki üst bilgi satırındaki bilgileri de kullanır.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

Aşağıdaki kod, satır filtrelemeyi ve sütun projeksiyonlarını aynı sorguyla birleştirir. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Lake Storage sorgu hızlandırma](data-lake-storage-query-acceleration.md)
- [Sorgu hızlandırma SQL dil başvurusu](query-acceleration-sql-reference.md)
