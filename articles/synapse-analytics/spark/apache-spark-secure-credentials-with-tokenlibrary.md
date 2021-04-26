---
title: Azure SYNAPSE Analytics için Apache Spark bağlı hizmetlerle güvenli erişim kimlik bilgileri
description: Bu makalede, Azure SYNAPSE Analytics için Apache Spark bağlı hizmetleri ve belirteç kitaplığı 'nı kullanarak diğer hizmetlerle güvenli bir şekilde tümleştirme hakkında kavramlar sunulmaktadır
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: martinle
ms.reviewer: nirav
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: a588b37b270917524453419619fdad6f88f92338
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693586"
---
# <a name="secure-credentials-with-linked-services-using-the-tokenlibrary"></a>TokenLibrary kullanarak bağlı hizmetlerle güvenli kimlik bilgileri

Dış kaynaklardaki verilere erişmek ortak bir modeldir. Dış veri kaynağı anonim erişime izin veriyorsa, bağlantınızı kimlik bilgileri, gizli anahtar veya bağlantı dizesiyle güvenli hale getirmeniz gerekir.  

SYNAPSE, kaynaklar arasında kimlik doğrulaması için varsayılan olarak Azure Active Directory (AAD) geçiş kullanır.  Diğer kimlik bilgilerini kullanarak bir kaynağa bağlanmanız gerekirse, doğrudan TokenLibrary kullanın.  TokenLibrary, bağlı bir hizmette veya bir Azure Key Vault depolanan SAS belirteçlerini, AAD belirteçlerini, bağlantı dizelerini ve gizli dizileri alma sürecini basitleştirir.

Azure Key Vault parolaları alırken, Azure Key Vault bağlı bir hizmet oluşturmanızı öneririz.  SYNAPSE çalışma alanı yönetilen hizmet kimliğinin (MSI) Azure Key Vault gizli Get ayrıcalıklarına sahip olduğundan emin olun.  SYNAPSE, SYNAPSE çalışma alanı yönetilen hizmet kimliğini kullanarak Azure Key Vault kimliğini doğrular. Bağlı hizmet olmadan Azure Key Vault doğrudan bağlanıyorsanız, Kullanıcı Azure Active Directory kimlik bilgilerinizi kullanarak kimlik doğrulaması yapmanız gerekir.

Daha fazla bilgi için bkz. [bağlı hizmetler](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="usage"></a>Kullanım

### <a name="tokenlibraryhelp"></a>TokenLibrary. Help ()
Bu işlev, TokenLibrary için Yardım belgelerini görüntüler.

::: zone pivot = "programming-language-scala"

```scala
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
Console.WriteLine(TokenLibrary.help());
```

::: zone-end

## <a name="tokenlibrary-for-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. için TokenLibrary

#### <a name="adls-gen2-primary-storage"></a>Birincil depolama ADLS 2.

Birincil Azure Data Lake Storage dosyalara erişmek, varsayılan olarak kimlik doğrulaması için Azure Active Directory PASSTHROUGH kullanır ve TokenLibrary 'in açık kullanımını gerektirmez.

::: zone pivot = "programming-language-scala"

```scala
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")
display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')
display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-linked-services"></a>Bağlı hizmetlerle depolama ADLS 2.

SYNAPSE, Azure Data Lake Storage 2. bağlanırken tümleşik bir bağlı hizmetler deneyimi sağlar.  Bağlı hizmetler **hesap anahtarı**, **hizmet sorumlusu**, **yönetilen kimlik** veya **kimlik bilgileri** kullanılarak kimlik doğrulaması yapacak şekilde yapılandırılabilir.

Bağlı hizmet kimlik doğrulama yöntemi **hesap anahtarı** olarak ayarlandığında, bağlı hizmet, belirtilen depolama hesabı anahtarını kullanarak kimlik doğrular, bir SAS anahtarı Ister ve **Linkedservicebasedsasprovider** kullanarak depolama isteğine otomatik olarak uygular.

::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

Bağlı hizmet kimlik doğrulama yöntemi **yönetilen kimliğe** veya **Hizmet sorumlusuna** ayarlandığında, bağlantılı hizmet yönetilen kimliği veya hizmet sorumlusu belirtecini **linkedservicebasedtokenprovider** sağlayıcısıyla kullanacaktır.  


::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.oauth.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider") 
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

#### <a name="adls-gen2-storage-without-linked-services"></a>ADLS 2. depolama (bağlı hizmetler olmadan)

SAS anahtarını kullanarak doğrudan ADLS 2. depolamaya bağlanın **Confbasedsasprovider** 'ı kullanın ve The **spark. Storage. SYNAPSE. SAS** yapılandırma ayarına SAS anahtarını sağlayın.

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark

spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-azure-key-vault"></a>Azure Key Vault ile depolama ADLS 2.

Azure Key Vault gizli dizi içinde depolanan SAS belirtecini kullanarak ADLS 2. depolama alanına bağlanın.  

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

## <a name="tokenlibrary-for-other-linked-services"></a>Diğer bağlı hizmetler için TokenLibrary

Diğer bağlı hizmetlere bağlanmak için, TokenLibrary 'e doğrudan çağrı yapabilirsiniz.

#### <a name="getconnectionstring"></a>getConnectionString ()

 Bağlantı dizesini almak için **GetConnectionString** işlevini kullanın ve **bağlı hizmet adını** geçirin.

::: zone pivot = "programming-language-scala"

```scala
%%spark
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
%%csharp
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

::: zone-end

#### <a name="getconnectionstringasmap"></a>getConnectionStringAsMap()

GetConnectionStringAsMap, bağlantı dizesindeki bir _anahtar = değer_ çiftinden belirli değerleri ayrıştırmak Için Scala ve Python 'da kullanılabilen bir yardımcı işlevdir

_`DefaultEndpointsProtocol=https;AccountName=<ACCOUNT NAME>;AccountKey=<ACCOUNT KEY>`_

**getConnectionStringAsMap** işlevini kullanın ve değeri döndürmek için anahtarı geçirin.  Yukarıdaki bağlantı dizesi örneğinde, 

_**TokenLibrary. getConnectionStringAsMap ("DefaultEndpointsProtocol")**_

döndürür

**_'dir_**

::: zone pivot = "programming-language-scala"

```scala
// Linked services can be used for storing and retrieving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```
::: zone-end

::: zone pivot = "programming-language-python"

```python
# Linked services can be used for storing and retrieving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

::: zone-end

#### <a name="getsecret"></a>getSecret ()

Azure Key Vault depolanan bir gizli dizi almak için, SYNAPSE çalışma alanı içinde Azure Key Vault için bağlı bir hizmet oluşturmanız önerilir. SYNAPSE çalışma alanı yönetilen hizmet kimliğinin Azure Key Vault parolaları **Al** izni verilmesi gerekir.  Bağlı hizmet, gizli anahtarı almak için Azure Key Vault hizmetine bağlanmak üzere yönetilen hizmet kimliğini kullanır.  Aksi takdirde, Azure Key Vault doğrudan bağlantı, kullanıcının Azure Active Directory (AAD) kimlik bilgilerini kullanır.  Bu durumda, kullanıcıya Azure Key Vault gizli anahtar Al izinleri verilmelidir.

`TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>" [, <LINKED SERVICE NAME>])`

Azure Key Vault bir gizli dizi almak için **Tokenlibrary. getSecret ()** işlevini kullanın.

::: zone pivot = "programming-language-scala"

```scala
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
import sys
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

connection_string = token_library.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>");
Console.WriteLine(connectionString);
```

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

- [Adanmış SQL havuzuna yaz](./synapse-spark-sql-pool-import-export.md)
