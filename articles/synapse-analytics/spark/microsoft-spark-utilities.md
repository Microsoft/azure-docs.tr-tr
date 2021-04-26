---
title: Microsoft Spark yardımcı programlarına giriş
description: 'Öğretici: Azure SYNAPSE Analytics not defterlerinde Msmini yardımcı programları'
author: ruixinxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 8b3bc99d4391e2079d1b0ecc39011f1b2afc4440
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096045"
---
# <a name="introduction-to-microsoft-spark-utilities"></a>Microsoft Spark yardımcı programlarına giriş

Microsoft Spark Utilities (Msparlak utils), ortak görevleri kolayca gerçekleştirmenize yardımcı olan yerleşik bir pakettir. Msmini yardımcı programları kullanarak dosya sistemleriyle çalışabilir, ortam değişkenlerini alabilir ve gizli dosyalarla çalışabilirsiniz. Msmini yardımcı programları,, `PySpark (Python)` `Scala` , ve `.NET Spark (C#)` dizüstü bilgisayarlarda ve SYNAPSE işlem hatları 'nda mevcuttur.

## <a name="pre-requisites"></a>Ön koşullar

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. erişimi yapılandırma 

SYNAPSE Not defterleri, ADLS 2. hesaplarına erişmek için Azure Active Directory (Azure AD) geçişini kullanır. ADLS 2. hesabına (veya klasöre) erişmek için bir **Depolama Blobu veri katılımcısı** olmanız gerekir. 

SYNAPSE işlem hatları, depolama hesaplarına erişmek için çalışma alanı kimliğini (MSI) kullanır. İşlem hattı etkinliklerinizde Msmini yardımcı programları kullanmak için, ADLS 2. hesabına (veya klasöre) erişmek için çalışma alanı kimliğinizin **Depolama Blobu veri katılımcısı** olması gerekir.

Azure AD ve çalışma alanı MSI ADLS 2. hesabına erişimi olduğundan emin olmak için şu adımları izleyin:
1. [Azure Portal](https://portal.azure.com/) ve erişmek istediğiniz depolama hesabını açın. Erişmek istediğiniz belirli bir kapsayıcıya gidebilirsiniz.
2. Sol paneldeki **erişim denetimini (IAM)** seçin.
3. **Azure AD hesabınızı** ve **çalışma alanı kimliğinizi** (çalışma alanı adınızla aynı), henüz atanmamışsa depolama hesabındaki **Depolama Blobu veri katılımcısı** rolüne atayın. 
4. **Kaydet**’i seçin.

ADLS 2. verilerine aşağıdaki URL aracılığıyla SYNAPSE Spark ile erişebilirsiniz:

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>Azure Blob depolamaya erişimi yapılandırma  

SYNAPSE Azure Blob depolamaya erişmek için **paylaşılan erişim imzasını (SAS)** kullanın. Kodda SAS anahtarlarının açığa çıkarılması önlemek için, SYNAPSE çalışma alanında erişmek istediğiniz Azure Blob depolama hesabına yeni bir bağlı hizmet oluşturmanız önerilir.

Azure Blob depolama hesabı için yeni bir bağlı hizmet eklemek için aşağıdaki adımları izleyin:

1. [Azure SYNAPSE Studio 'yu](https://web.azuresynapse.net/)açın.
2. Sol panelden **Yönet** ' i seçin ve **dış bağlantılar** altında **bağlı hizmetler** ' i seçin.
3. Sağ taraftaki **yeni bağlı hizmet** panelinde **Azure Blob Storage** 'ı arayın.
4. **Devam**’ı seçin.
5. Bağlı hizmet adına erişmek ve yapılandırmak için Azure Blob Depolama hesabını seçin. **Kimlik doğrulama yöntemi** için **hesap anahtarını** kullanmayı önerin.
6. Ayarların doğru olduğunu doğrulamak için **Bağlantıyı Sına** ' yı seçin.
7. Önce **Oluştur** ' u seçin ve değişikliklerinizi kaydetmek Için **Tümünü Yayımla** ' ya tıklayın. 

Aşağıdaki URL aracılığıyla SYNAPSE Spark ile Azure Blob depolama üzerindeki verilere erişebilirsiniz:

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

Aşağıda bir kod örneği verilmiştir:

:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val blob_account_name = "" // replace with your blob name
val blob_container_name = "" //replace with your container name
val blob_relative_path = "/" //replace with your relative folder path
val linked_service_name = "" //replace with your linked service name


val blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

val wasbs_path = f"wasbs://$blob_container_name@$blob_account_name.blob.core.windows.net/$blob_relative_path"
spark.conf.set(f"fs.azure.sas.$blob_container_name.$blob_account_name.blob.core.windows.net",blob_sas_token)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var blob_account_name = "";  // replace with your blob name
var blob_container_name = "";     // replace with your container name
var blob_relative_path = "";  // replace with your relative folder path
var linked_service_name = "";    // replace with your linked service name
var blob_sas_token = Credentials.GetConnectionStringOrCreds(linked_service_name);

spark.SparkContext.GetConf().Set($"fs.azure.sas.{blob_container_name}.{blob_account_name}.blob.core.windows.net", blob_sas_token);

var wasbs_path = $"wasbs://{blob_container_name}@{blob_account_name}.blob.core.windows.net/{blob_relative_path}";

Console.WriteLine(wasbs_path);

```

::: zone-end 
 
###  <a name="configure-access-to-azure-key-vault"></a>Azure Key Vault erişimi yapılandırma

SYNAPSE ' de kimlik bilgilerinizi yönetmek için bağlı hizmet olarak Azure Key Vault ekleyebilirsiniz. Bir Azure Key Vault SYNAPSE bağlı hizmeti olarak eklemek için aşağıdaki adımları izleyin:
1. [Azure SYNAPSE Studio 'yu](https://web.azuresynapse.net/)açın.
2. Sol panelden **Yönet** ' i seçin ve **dış bağlantılar** altında **bağlı hizmetler** ' i seçin.
3. Sağ taraftaki **yeni bağlı hizmet** panelinde **Azure Key Vault** arama yapın.
4. Bağlı hizmet adına erişmek ve yapılandırmak için Azure Key Vault hesabını seçin.
5. Ayarların doğru olduğunu doğrulamak için **Bağlantıyı Sına** ' yı seçin.
6. Önce **Oluştur** ' u seçin ve değişikliklerinizi kaydetmek Için **Tümünü Yayımla** ' ya tıklayın. 

SYNAPSE Not defterleri Azure Key Vault erişmek için Azure Active Directory (Azure AD) geçişli geçiş kullanır. SYNAPSE işlem hatları Azure Key Vault erişmek için çalışma alanı kimliğini (MSI) kullanır. Kodunuzun hem not defterinde hem de SYNAPSE işlem hattında çalıştığından emin olmak için hem Azure AD hesabınız hem de çalışma alanı Kimliğiniz için gizli erişim izni verilmesi önerilir.

Çalışma alanı kimliğinize gizli erişim sağlamak için aşağıdaki adımları izleyin:
1. [Azure Portal](https://portal.azure.com/) ve erişmek istediğiniz Azure Key Vault açın. 
2. Sol panelden **erişim ilkeleri** ' ni seçin.
3. **Erişim Ilkesi Ekle**' yi seçin: 
    - **Anahtar, gizli dizi & Sertifika yönetimini** yapılandırma şablonu olarak seçin.
    - Sorumluyu Seç ' de **Azure AD hesabınızı** ve **çalışma alanı kimliğinizi** (çalışma alanı adınızla aynı) seçin veya zaten atanmış olduğundan emin olun. 
4. **Seç** ve **Ekle**' yi seçin.
5. Değişiklikleri uygulamak için **Kaydet** düğmesini seçin.  

## <a name="file-system-utilities"></a>Dosya sistemi yardımcı programları

`mssparkutils.fs` Azure Data Lake Storage 2. (ADLS 2.) ve Azure Blob depolama gibi çeşitli dosya sistemleriyle çalışmak için yardımcı programlar sağlar. [Azure Data Lake Storage 2.](#configure-access-to-azure-data-lake-storage-gen2) ve [Azure Blob depolamaya](#configure-access-to-azure-blob-storage) erişimi uygun şekilde yapılandırmadığınızdan emin olun.

Kullanılabilir yöntemlere genel bir bakış için aşağıdaki komutları çalıştırın:

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

Sonuç:
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>Dosyaları listeleme
Bir dizinin içeriğini listeleyin.


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>Dosya özelliklerini görüntüle
Dosya adı, dosya yolu, dosya boyutu ve bir dizin ve bir dosya olup olmadığı dahil dosya özelliklerini döndürür.

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>Yeni dizin oluştur

Mevcut dizin yoksa ve gerekli üst dizinler varsa, bu dizini oluşturur.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>Dosyayı Kopyala

Bir dosya veya dizini kopyalar. Dosya sistemleri arasında kopyalamayı destekler.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>Dosya içeriğini Önizle

Belirtilen dosyanın ilk ' Maxbyte ' baytını UTF-8 ' de kodlanmış bir dize olarak döndürür.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>Dosyayı taşı

Bir dosya veya dizin taşıdıkça. Dosya sistemleri arasında taşımayı destekler.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>Dosya yaz

Verilen dizeyi UTF-8 ile kodlanmış bir dosyaya yazar.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>Bir dosyaya içerik Ekle

Verilen dizeyi UTF-8 ile kodlanmış bir dosyaya ekler.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append('file path','content to append',True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>Dosya veya dizini Sil

Bir dosyayı veya dizini kaldırır.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end


## <a name="credentials-utilities"></a>Kimlik bilgileri yardımcı programları

Msparlak utils kimlik bilgileri yardımcı programlarını, bağlı hizmetlerin erişim belirteçlerini almak ve Azure Key Vault gizli dizileri yönetmek için kullanabilirsiniz. 

Kullanılabilir yöntemlere genel bir bakış almak için aşağıdaki komutu çalıştırın:

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.Help()
```

::: zone-end

Sonuç al:

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>Belirteç al

Belirli bir hedef kitle için Azure AD belirtecini döndürür, ad (isteğe bağlı). Aşağıdaki tabloda, kullanılabilir tüm hedef kitle türleri listelenmektedir: 

|Hedef kitle türü|Hedef kitle anahtarı|
|--|--|
|Hedef kitle çözümleme türü|Grubu|
|Depolama hedef kitlesi kaynağı|Depo|
|Veri ambarı hedef kitle kaynağı|DW|
|Data Lake hedef kitle kaynağı|'AzureManagement'|
|Kasa Izleyici kaynağı|DataLakeStore|
|Azure OSSDB hedef kitle kaynağı|'AzureOSSDB'|
|Azure SYNAPSE kaynağı|'Synapse'|
|Azure Data Factory kaynağı|TARAYıCıNıZDA|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetToken("audience Key")
```

::: zone-end


### <a name="validate-token"></a>Belirteci doğrula

Belirtecin süresinin dolmadığı durumlarda true döndürür.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.IsValidToken("your token")
```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>Bağlı hizmet için bağlantı dizesini veya kimlik bilgilerini al

Bağlı hizmet için bağlantı dizesini veya kimlik bilgilerini döndürür. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetConnectionStringOrCreds("linked service name")
```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>Çalışma alanı kimliğini kullanarak gizli dizi al

Verilen bir Azure Key Vault adı, gizli adı ve çalışma alanı kimliği kullanarak bağlı hizmet adı için Azure Key Vault gizli dizisi döndürür. [Azure Key Vault](#configure-access-to-azure-key-vault) erişimi uygun şekilde yapılandırdığınızdan emin olun.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name","linked service name")
```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>Kullanıcı kimlik bilgilerini kullanarak gizli dizi al

Belirli bir Azure Key Vault adı, gizli adı ve Kullanıcı kimlik bilgilerini kullanarak bağlı hizmet adı için Azure Key Vault gizli dizisi döndürür. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name")
```

::: zone-end

<!-- ### Put secret using workspace identity

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using workspace identity. Make sure you configure the access to [Azure Key Vault](#configure-access-to-azure-key-vault) appropriately. -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-workspace-identity"></a>Çalışma alanı kimliği kullanarak gizli dizi koy

, Belirli bir Azure Key Vault adı, gizli adı ve çalışma alanı kimliği kullanarak bağlı hizmet adı için Azure Key Vault gizli dizisi koyar. [Azure Key Vault](#configure-access-to-azure-key-vault) erişimi uygun şekilde yapılandırdığınızdan emin olun.

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-workspace-identity"></a>Çalışma alanı kimliği kullanarak gizli dizi koy

, Belirli bir Azure Key Vault adı, gizli adı ve çalışma alanı kimliği kullanarak bağlı hizmet adı için Azure Key Vault gizli dizisi koyar. [Azure Key Vault](#configure-access-to-azure-key-vault) erişimi uygun şekilde yapılandırdığınızdan emin olun.

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


<!-- ### Put secret using user credentials

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using user credentials.  -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-user-credentials"></a>Kullanıcı kimlik bilgilerini kullanarak gizli dizi koy

Kullanıcı kimlik bilgilerini kullanarak belirli bir Azure Key Vault adı, gizli adı ve bağlı hizmet adı için Azure Key Vault gizli dizisi koyar. 

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-user-credentials"></a>Kullanıcı kimlik bilgilerini kullanarak gizli dizi koy

Kullanıcı kimlik bilgilerini kullanarak belirli bir Azure Key Vault adı, gizli adı ve bağlı hizmet adı için Azure Key Vault gizli dizisi koyar. 

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


## <a name="environment-utilities"></a>Ortam yardımcı programları 

Kullanılabilir yöntemlere genel bir bakış almak için aşağıdaki komutları çalıştırın:

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.Help()
```

::: zone-end

Sonuç al:
```
GetUserName(): returns user name
GetUserId(): returns unique user id
GetJobId(): returns job id
GetWorkspaceName(): returns workspace name
GetPoolName(): returns Spark pool name
GetClusterId(): returns cluster id
```

### <a name="get-user-name"></a>Kullanıcı adını al

Geçerli Kullanıcı adını döndürür.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserName()
```

::: zone-end

### <a name="get-user-id"></a>Kullanıcı KIMLIĞINI al

Geçerli kullanıcı KIMLIĞINI döndürür.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserId()
```

::: zone-end

### <a name="get-job-id"></a>İş KIMLIĞINI al

İş KIMLIĞINI döndürür.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetJobId()
```

::: zone-end

### <a name="get-workspace-name"></a>Çalışma alanı adını al

Çalışma alanı adını döndürür.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetWorkspaceName()
```

::: zone-end

### <a name="get-pool-name"></a>Havuz adını al

Spark havuz adını döndürür.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetPoolName()
```

::: zone-end

### <a name="get-cluster-id"></a>Küme KIMLIĞINI al

Geçerli küme KIMLIĞINI döndürür.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetClusterId()
```

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

- [SYNAPSE örnek not defterlerine göz atın](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Hızlı başlangıç: Web araçlarını kullanarak Azure SYNAPSE Analytics 'te Apache Spark havuzu oluşturma](../quickstart-apache-spark-notebook.md)
- [Azure SYNAPSE Analytics 'te Apache Spark nedir?](apache-spark-overview.md)
- [Azure Synapse Analytics](../index.yml)
