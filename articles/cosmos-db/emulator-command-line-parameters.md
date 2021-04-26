---
title: Azure Cosmos DB öykünücü için komut satırı ve PowerShell Başvurusu
description: Azure Cosmos DB öykünücüsü için komut satırı parametrelerini, PowerShell ile öykünücüyü denetlemeyi ve öykünücü dahilinde oluşturabileceğiniz kapsayıcıların sayısını değiştirmeyi öğrenin.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: e0c795484bf860402d05c1dc5779633962a44ec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979513"
---
# <a name="command-line-and-powershell-reference-for-azure-cosmos-db-emulator"></a>Azure Cosmos DB öykünücü için komut satırı ve PowerShell Başvurusu
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB öykünücüsü, yerel geliştirme amaçlarıyla Azure Cosmos DB hizmetine öykünen yerel bir ortam sağlar. [Öykünücüyü](local-emulator.md)yükledikten sonra, öykünücü komut satırı ve PowerShell komutlarıyla kontrol edebilirsiniz. Bu makalede, öykünücü başlatmak ve durdurmak, seçenekleri yapılandırmak ve diğer işlemleri gerçekleştirmek için komut satırı ve PowerShell komutlarının nasıl kullanılacağı açıklanır. Yükleme konumundan komutları çalıştırmanız gerekir.

##  <a name="manage-the-emulator-with-command-line-syntax"></a><a id="command-line"></a>Öykünücü komut satırı sözdizimiyle yönetme

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

Seçenek listesini görüntülemek için komut satırına `Microsoft.Azure.Cosmos.Emulator.exe /?` yazın.

|**Seçenek** | **Açıklama** | **Komut**| **Bağımsız değişkenler**|
|---|---|---|---|
|[Bağımsız değişken yok] | Varsayılan ayarlarla Azure Cosmos DB Öykünücüsünü başlatır. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Yardım] |Desteklenen komut satırı bağımsız değişkenleri listesini görüntüler.|Microsoft.Azure.Cosmos.Emulator.exe/? | |
| GetStatus |Azure Cosmos DB Öykünücüsü’nün durumunu alır. Durum, çıkış kodu tarafından belirtilir: 1 = Başlatılıyor, 2 = Çalıştırılıyor, 3 = Durduruldu. Negatif çıkış kodu, bir hata oluştuğunu gösterir. Başka bir çıktı üretilmez. | Microsoft.Azure.Cosmos.Emulator.exe/GetStatus| |
| Kapat| Azure Cosmos DB Öykünücüsü’nü kapatır.| Microsoft.Azure.Cosmos.Emulator.exe/Shutdown | |
|DataPath | Veri dosyalarının depolanacağı yolu belirtir. Varsayılan değer%LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe/DataPath =\<datapath\> | \<datapath\>: Erişilebilir bir yol |
|Bağlantı noktası | Öykünücü için kullanılacak bağlantı noktası numarasını belirtir. Varsayılan değer 8081 ' dir. |Microsoft.Azure.Cosmos.Emulator.exe/Port =\<port\> | \<port\>: Tek bağlantı noktası numarası |
| ComputePort | Compute Interop Gateway hizmeti için kullanılacak bağlantı noktası numarası belirtildi. Ağ geçidinin HTTP uç nokta araştırması bağlantı noktası ComputePort + 79 olarak hesaplanır. Bu nedenle, ComputePort ve ComputePort + 79 açık ve kullanılabilir olmalıdır. Varsayılan değer 8900 ' dir. | Microsoft.Azure.Cosmos.Emulator.exe/ComputePort =\<computeport\> | \<computeport\>: Tek bağlantı noktası numarası |
| EnableMongoDbEndpoint = 3.2 | MongoDB API 3,2 ' i sunar | Microsoft.Azure.Cosmos.Emulator.exe/EnableMongoDbEndpoint = 3.2 | |
| EnableMongoDbEndpoint = 3.6 | MongoDB API 3,6 ' i sunar | Microsoft.Azure.Cosmos.Emulator.exe/EnableMongoDbEndpoint = 3.6 | |
| MongoPort | MongoDB uyumluluk API’si için kullanılacak bağlantı noktası numarasını belirtir. Varsayılan değer 10255 ' dir. |Microsoft.Azure.Cosmos.Emulator.exe/MongoPort =\<mongoport\>|\<mongoport\>: Tek bağlantı noktası numarası|
| EnableCassandraEndpoint | Cassandra API etkinleştirilir | Microsoft.Azure.Cosmos.Emulator.exe/EnableCassandraEndpoint | |
| CassandraPort | Cassandra uç noktası için kullanılacak bağlantı noktası numarasını belirtir. Varsayılan değer 10350 ' dir. | Microsoft.Azure.Cosmos.Emulator.exe/CassandraPort =\<cassandraport\> | \<cassandraport\>: Tek bağlantı noktası numarası |
| EnableGremlinEndpoint | Gremlin API 'sini sunar | Microsoft.Azure.Cosmos.Emulator.exe/EnableGremlinEndpoint | |
| GremlinPort | Gremlin uç noktası için kullanılacak bağlantı noktası numarası. Varsayılan değer 8901 ' dir. | Microsoft.Azure.Cosmos.Emulator.exe/GremlinPort =\<port\> | \<port\>: Tek bağlantı noktası numarası |
|EnableTableEndpoint | Azure Tablo API'si etkinleştirilir | Microsoft.Azure.Cosmos.Emulator.exe/EnableTableEndpoint | |
|TablePort | Azure Tablo uç noktası için kullanılacak bağlantı noktası numarası. Varsayılan değer 8902 ' dir. | Microsoft.Azure.Cosmos.Emulator.exe/TablePort =\<port\> | \<port\>: Tek bağlantı noktası numarası|
| Dosyasına | Belirtilen dosyadan yetkilendirme anahtarını okuyun. Bir keyfile oluşturmak için/GenKeyFile seçeneğini kullanın | Microsoft.Azure.Cosmos.Emulator.exe/KeyFile =\<file_name\> | \<file_name\>: Dosyanın yolu |
| ResetDataPath | Belirtilen yoldaki tüm dosyaları yinelemeli olarak kaldırır. Bir yol belirtmezseniz, varsayılan olarak%LOCALAPPDATA%\CosmosDbEmulator olur | Microsoft.Azure.Cosmos.Emulator.exe/ResetDataPath =\<path> | \<path\>: Dosya yolu  |
| Başlangıçizler  |  LOGMAN kullanarak hata ayıklama izleme günlüklerini toplamaya başla. | Microsoft.Azure.Cosmos.Emulator.exe/Startizlemeler | |
| Durma Izlemeleri     | LOGMAN kullanarak hata ayıklama izleme günlüklerini toplamayı durdur. | Microsoft.Azure.Cosmos.Emulator.exe/Stopizlemeleri  | |
| Startwprizleri  |  Windows performans kayıt aracı 'nı kullanarak hata ayıklama izleme günlüklerini toplamaya başlayın. | Microsoft.Azure.Cosmos.Emulator.exe/Startwprizlemler | |
| Stopwprizleri     | Windows performans kayıt aracı 'nı kullanarak hata ayıklama izleme günlüklerini toplamayı durdurun. | Microsoft.Azure.Cosmos.Emulator.exe/Stopwprizlemleri  | |
|Failonsslcertificatenameuyuşmazlığı | Varsayılan olarak, sertifika SAN 'ı öykünücü konağın etki alanı adı, yerel IPv4 adresi, ' localhost ' ve ' 127.0.0.1 ' içermiyorsa, öykünücü otomatik olarak imzalanan TLS/SSL sertifikasını yeniden oluşturur. Bu seçenekle öykünücü, bunun yerine başlangıçta başarısız olur. Ardından, otomatik olarak imzalanan yeni bir TLS/SSL sertifikası oluşturmak ve yüklemek için/GenCert seçeneğini kullanmanız gerekir. | Microsoft.Azure.Cosmos.Emulator.exe/Failonsslcertificatenameuyuşmazlığı  | |
| GenCert | Otomatik olarak imzalanan yeni bir TLS/SSL sertifikası oluşturun ve yükler. isteğe bağlı olarak, ağ üzerinden öykünücüye erişmek için ek DNS adlarının virgülle ayrılmış listesini de içeren bir liste. | Microsoft.Azure.Cosmos.Emulator.exe/GenCert =\<dns-names\> |\<dns-names\>: Ek DNS adlarının isteğe bağlı virgülle ayrılmış listesi  |
| DirectPorts |Doğrudan bağlantı için kullanılacak bağlantı noktalarını belirtir. Varsayılan değerler: 10251,10252,10253,10254. | Microsoft.Azure.Cosmos.Emulator.exe/DirectPort:\<directports\> | \<directports\>: Virgülle ayrılmış 4 bağlantı noktası listesi |
| Anahtar |Öykünücü için yetkilendirme anahtarı. Anahtar, 64 bayt vektörün base 64 kodlaması olmalıdır. | Microsoft.Azure.Cosmos.Emulator.exe/Key:\<key\> | \<key\>: Anahtar, 64 baytlık bir Vector öğesinin Base-64 kodlaması olmalıdır|
| EnableRateLimiting | İstek oranını sınırlama davranışının etkinleştirildiğini belirtir. |Microsoft.Azure.Cosmos.Emulator.exe/Enableratesýnýrlama | |
| DisableRateLimiting |İstek oranını sınırlama davranışının devre dışı bırakıldığını belirtir. |Microsoft.Azure.Cosmos.Emulator.exe/Disableratesýnýrlama | |
| NoUI | Öykünücü kullanıcı arabirimini gösterme. | Microsoft.Azure.Cosmos.Emulator.exe/NOUI | |
| NoExplorer | Başlangıçta veri gezginini gösterme. |Microsoft.Azure.Cosmos.Emulator.exe/NoExplorer | | 
| PartitionCount | En fazla bölümlenmiş kapsayıcı sayısını belirtir. Daha fazla bilgi için bkz. [kapsayıcı sayısını değiştirme](#set-partitioncount) . | Microsoft.Azure.Cosmos.Emulator.exe/PartitionCount =\<partitioncount\> | \<partitioncount\>: İzin verilen en fazla tek bölüm kapsayıcısı sayısı. Varsayılan değer 25’tir. Maksimum izin verilen: 250.|
| DefaultPartitionCount| Bölümlenmiş bir kapsayıcı için varsayılan bölüm sayısını belirtir. | Microsoft.Azure.Cosmos.Emulator.exe/DefaultPartitionCount =\<defaultpartitioncount\> | \<defaultpartitioncount\> Varsayılan değer 25 ' tir.|
| AllowNetworkAccess | Bir ağ üzerinden öykünücüye erişilmesini sağlar. \<key_string\>Ağ erişimini etkinleştirmek için/Key = veya/KeyFile = geçişini de yapmanız gerekir \<file_name\> . | Microsoft.Azure.Cosmos.Emulator.exe/AllowNetworkAccess/Key = \<key_string\> veya Microsoft.Azure.Cosmos.Emulator.exe/AllowNetworkAccess/KeyFile =\<file_name\>| |
| NoFirewall | /AllowNetworkAccess seçeneği kullanıldığında güvenlik duvarı kurallarını ayarlamadığınızda. |Microsoft.Azure.Cosmos.Emulator.exe/Nofireduvar | |
| GenKeyFile | Yeni bir yetkilendirme anahtarı oluşturun ve belirtilen dosyaya kaydedin. Oluşturulan anahtar, /Key veya /KeyFile seçenekleri ile kullanılabilir. | Microsoft.Azure.Cosmos.Emulator.exe/GenKeyFile =\<path to key file\> | |
| Tutarlılık | Hesap için varsayılan tutarlılık düzeyini ayarlayın. | Microsoft.Azure.Cosmos.Emulator.exe/tutarlılığı =\<consistency\> | \<consistency\>: Değer şu [tutarlılık düzeylerinden](consistency-levels.md)biri olmalıdır: oturum, güçlü, nihai veya sıçrama durumu. Varsayılan değer: Oturum. |
| ? | Yardım iletisini gösterin.| | |

## <a name="manage-the-emulator-with-powershell"></a>Öykünücüyü PowerShell ile yönetme

Öykünücü, hizmetin durumunu başlatmak, durdurmak, kaldırmak ve almak için bir PowerShell modülü ile birlikte gelir. PowerShell modülünü kullanmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

ya da `PSModules` dizini bu dizine yerleştirip `PSModulePath` aşağıdaki komutta gösterildiği gibi içeri aktarın:

```powershell
$env:PSModulePath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Aşağıda, PowerShell’den öykünücüyü denetlemeye ilişkin komutların özeti verilmiştir:

### `Get-CosmosDbEmulatorStatus`

**Syntax**

`Get-CosmosDbEmulatorStatus`

**Açıklamalar**

Şu ServiceControllerStatus değerlerinden birini döndürür: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running veya ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Syntax**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Açıklamalar**

Öykünücüyü başlatır. Varsayılan olarak komut, öykünücünün istekleri kabul etmeye hazır olmasını bekler. Cmdlet’in öykünücüyü başlattığı anda geri dönmesini istiyorsanız -NoWait seçeneğini kullanın.

### `Stop-CosmosDbEmulator`

**Syntax**

 `Stop-CosmosDbEmulator [-NoWait]`

**Açıklamalar**

Öykünücüyü durdurur. Varsayılan olarak bu komut, öykünücünün tamamen kapanmasını bekler. Cmdlet’in öykünücü kapanmaya başladığı anda geri dönmesini istiyorsanız -NoWait seçeneğini kullanın.

### `Uninstall-CosmosDbEmulator`

**Syntax**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Açıklamalar**

Öykünücüyü kaldırır ve isteğe bağlı olarak $env:LOCALAPPDATA\CosmosDbEmulator içeriklerinin tamamını kaldırır.
Cmdlet, öykünücüyü kaldırmadan önce öykünücünün tamamen durdurulduğundan emin olur.

## <a name="change-the-number-of-default-containers"></a><a id="set-partitioncount"></a>Varsayılan kapsayıcıların sayısını değiştirme

Varsayılan olarak, en fazla 25 sabit boyut kapsayıcısı (yalnızca Azure Cosmos DB SDK 'Ları kullanılarak desteklenir) veya Azure Cosmos DB öykünücüsünü kullanarak 5 sınırsız kapsayıcı oluşturabilirsiniz. **PartitionCount** değerini değiştirerek, en fazla 250 sabit boyut kapsayıcısı veya 50 sınırsız kapsayıcı oluşturabilir ya da iki değerin herhangi bir bileşimini (sınırsız kapsayıcı = 5 sabit boyut kapsayıcısı) 250 aşamaz. Ancak, öykünücü 200 ' den fazla sabit boyutlu kapsayıcıyla çalışacak şekilde ayarlanmalıdır. Disk GÇ işlemlerine eklediği ek yük nedeniyle, uç nokta API 'Leri kullanılırken öngörülemeyen zaman aşımları ile sonuçlanır.

Geçerli bölüm sayısı aşıldıktan sonra bir kapsayıcı oluşturmaya çalışırsanız, öykünücü aşağıdaki iletiyle bir ServiceUnavailable özel durumu atar.

> Ne yazık ki şu anda bu bölgede yüksek taleple karşılaşıyoruz ve isteğinizi Şu anda gerçekleştiremiyor. Daha fazla ve daha fazla kapasiteyi çevrimiçi hale getirmek için sürekli olarak çalıştık ve yeniden denemeniz önerilir.
> Etkinlik kimliği: 12345678-1234-1234-1234-123456789ABC

Azure Cosmos DB öykünücüsünde bulunan kapsayıcıların sayısını değiştirmek için aşağıdaki adımları çalıştırın:

1. Sistem tepsisindeki **Azure Cosmos DB Öykünücüsü** simgesine sağ tıklayıp **Verileri Sıfırla…** seçeneğine tıklayarak tüm yerel Azure Cosmos DB Öykünücüsü verilerini silin.

1. Bu klasördeki tüm öykünücü verilerini silin `%LOCALAPPDATA%\CosmosDBEmulator` .

1. Sistem tepsisindeki **Azure Cosmos DB Öykünücüsü** simgesine sağ tıklayıp **Çıkış**’a tıklayarak tüm açık örneklerden çıkın. Tüm örneklerin çıkması bir dakika sürebilir.

1. [Azure Cosmos DB Öykünücüsü](https://aka.ms/cosmosdb-emulator)’nün en son sürümünü yükleyin.

1. 250 veya daha düşük bir değer ayarlayarak PartitionCount bayrağı ile öykünücüyü başlatın. Örneğin: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.
 
## <a name="next-steps"></a>Sonraki adımlar

* [Java, Python ve Node.js uygulamalarıyla kullanmak üzere Azure Cosmos DB öykünücü sertifikalarını dışarı aktarın](local-emulator-export-ssl-certificates.md)
* [Öykünücü ile ilgili sorunları ayıklama](troubleshoot-local-emulator.md)
