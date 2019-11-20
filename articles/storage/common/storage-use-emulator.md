---
title: Geliştirme ve test için Azure depolama öykünücüsünü kullanma | Microsoft Docs
description: Azure depolama öykünücüsü, Azure depolama uygulamalarınızı geliştirmek ve test etmek için ücretsiz bir yerel geliştirme ortamı sağlar.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1f13efeb5c2ebcb8b23dea6f9ae997039972f089
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428307"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Geliştirme ve test için Azure depolama öykünücüsünü kullanma

Microsoft Azure depolama öykünücüsü, yerel geliştirme amaçlarıyla Azure blob, kuyruk ve tablo hizmetlerine öykünen bir araçtır. Uygulamanızı, Azure aboneliği oluşturmadan veya herhangi bir ücret ödemeden yerel olarak depolama hizmetlerine karşı test edebilirsiniz. Uygulamanızın öykünücü üzerinde nasıl çalıştığı konusunda memnun olduğunuzda, bulutta bir Azure depolama hesabı kullanmaya geçiş yapın.

## <a name="get-the-storage-emulator"></a>Depolama öykünücüsünü al

Depolama öykünücüsü [MICROSOFT Azure SDK](https://azure.microsoft.com/downloads/)'nın bir parçası olarak kullanılabilir. Depolama öykünücüsünü [tek başına yükleyiciyi](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (doğrudan indirme) kullanarak da yükleyebilirsiniz. Depolama öykünücüsünü yüklemek için bilgisayarınızda yönetici ayrıcalıklarına sahip olmanız gerekir.

Depolama öykünücüsü Şu anda yalnızca Windows üzerinde çalışır. Linux için bir depolama öykünücüsünün olması gerekiyorsa, topluluk tarafından tutulan, açık kaynaklı depolama öykünücü [Azıite](https://github.com/azure/azurite)olan bir seçenek vardır.

> [!NOTE]
> Depolama öykünücüsünün bir sürümünde oluşturulan verilerin farklı bir sürüm kullanılırken erişilebilir olması garanti edilmez. Verilerinizi uzun vadede kalıcı hale getirmeniz gerekiyorsa, bu verileri depolama öykünücüsü yerine bir Azure depolama hesabında depolamanızı öneririz.
> 
> Depolama öykünücüsü, OData kitaplıklarının belirli sürümlerine bağlıdır. Depolama öykünücüsü tarafından kullanılan OData dll 'Leri diğer sürümlerle değiştirmek desteklenmez ve beklenmeyen davranışlara neden olabilir. Ancak, depolama hizmeti tarafından desteklenen herhangi bir OData sürümü, istekleri öykünücüye göndermek için kullanılabilir.

## <a name="how-the-storage-emulator-works"></a>Depolama öykünücüsünün nasıl çalıştığı

Depolama öykünücüsü, Azure depolama hizmetlerine öykünmek için yerel bir Microsoft SQL Server 2012 Express LocalDB örneğini kullanır. Depolama öykünücüsünü, LocalDB örneği yerine SQL Server yerel bir örneğine erişecek şekilde yapılandırmayı tercih edebilirsiniz. Daha fazla bilgi edinmek için bu makalenin devamındaki [depolama öykünücüsünü başlatma ve başlatma](#start-and-initialize-the-storage-emulator) bölümüne bakın.

Depolama öykünücüsü, Windows kimlik doğrulaması kullanarak SQL Server veya LocalDB 'ye bağlanır.

Depolama öykünücüsü ile Azure depolama hizmetleri arasında bazı işlevlerde farklılık vardır. Bu farklılıklar hakkında daha fazla bilgi için bu makalenin ilerleyen kısımlarında bulunan [depolama öykünücüsü Ile Azure depolama bölümü arasındaki farklara](#differences-between-the-storage-emulator-and-azure-storage) bakın.

## <a name="start-and-initialize-the-storage-emulator"></a>Depolama öykünücüsünü başlatma ve başlatma

Azure Storage öykünücüsü 'nü başlatmak için:

1. **Başlat** düğmesini seçin veya **Windows** tuşuna basın.
2. @No__t yazmaya başlayın-0.
3. Görüntülenmiş uygulamalar listesinden öykünücü ' ı seçin.

Depolama öykünücüsü başladığında, bir komut Istemi penceresi görüntülenir. Depolama öykünücüsünü başlatmak ve durdurmak için bu konsol penceresini kullanabilirsiniz. Ayrıca, komut isteminden verileri temizleyebilir, durum alabilir ve öykünücü başlatabilirsiniz. Daha fazla bilgi için bu makalenin ilerleyen bölümlerindeki [depolama öykünücüsü komut satırı araç başvurusu](#storage-emulator-command-line-tool-reference) bölümüne bakın.

> [!NOTE]
> Sistemde Azurite gibi başka bir depolama öykünücüsü çalışıyorsa, Azure depolama öykünücüsü düzgün başlatılamayabilir.

Öykünücü çalıştırıldığında, Windows görev çubuğu bildirim alanında bir simge görürsünüz.

Depolama öykünücüsü komut Istemi penceresini kapattığınızda, depolama öykünücüsü çalışmaya devam edecektir. Depolama öykünücüsü konsol penceresini tekrar açmak için, depolama öykünücüsünü başlatma gibi önceki adımları izleyin.

Depolama öykünücüsünü ilk kez çalıştırdığınızda yerel depolama ortamı sizin için başlatılır. Başlatma işlemi, LocalDB 'de bir veritabanı oluşturur ve her yerel depolama hizmeti için HTTP bağlantı noktalarını ayırır.

Depolama öykünücüsü varsayılan olarak `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator` ' a yüklenir.

> [!TIP]
> Yerel depolama öykünücü kaynaklarıyla çalışmak için [Microsoft Azure Depolama Gezgini](https://storageexplorer.com) kullanabilirsiniz. Depolama öykünücüsünü yükledikten ve başlattıktan sonra Depolama Gezgini kaynakları ağacındaki "yerel & ekli" altında "(öykünücü-varsayılan bağlantı noktaları) (anahtar)" bölümüne bakın.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Depolama öykünücüsünü farklı bir SQL veritabanı kullanacak şekilde başlatma

Depolama öykünücüsünü varsayılan LocalDB örneğinden farklı bir SQL veritabanı örneğine işaret etmek üzere başlatmak için depolama öykünücüsü komut satırı aracını kullanabilirsiniz:

1. Depolama [öykünücüsünü başlatma ve başlatma](#start-and-initialize-the-storage-emulator) bölümünde açıklandığı gibi depolama öykünücüsü konsol penceresini açın.
1. Konsol penceresinde, aşağıdaki komutu yazın; burada `<SQLServerInstance>` SQL Server örneğinin adıdır. LocalDB 'yi kullanmak için SQL Server örneği olarak `(localdb)\MSSQLLocalDb` belirtin.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Ayrıca, öykünücüyü varsayılan SQL Server örneğini kullanacak şekilde yönlendiren aşağıdaki komutu da kullanabilirsiniz:

   `AzureStorageEmulator.exe init /server .`

   Ya da, veritabanını varsayılan LocalDB örneğine yeniden başlatan aşağıdaki komutu kullanabilirsiniz:

   `AzureStorageEmulator.exe init /forceCreate`

Bu komutlar hakkında daha fazla bilgi için bkz. [depolama öykünücüsü komut satırı araç başvurusu](#storage-emulator-command-line-tool-reference).

> [!TIP]
> LocalDB yüklemesi de dahil olmak üzere SQL Server örneklerinizi yönetmek için [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) kullanabilirsiniz. SMSS **sunucuya Bağlan** Iletişim kutusunda **sunucu adı:** alanındaki `(localdb)\MSSQLLocalDb` ' i belirtin ve LocalDB örneğine bağlanın.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Depolama öykünücüsünde istekleri kimlik doğrulama

Depolama öykünücüsünü yükleyip başlattığınızda, kodunuzu buna karşı test edebilirsiniz. Anonim bir istek olmadığı takdirde, depolama öykünücüsüyle karşı yaptığınız her isteği yetkilendirmelidir. Paylaşılan anahtar kimlik doğrulamasını veya paylaşılan erişim imzasını (SAS) kullanarak depolama öykünücüsünde istekleri yetkilendirebilirsiniz.

### <a name="authorize-with-shared-key-credentials"></a>Paylaşılan anahtar kimlik bilgileriyle yetkilendir

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Bağlantı dizeleri hakkında daha fazla bilgi için bkz. [Azure Storage bağlantı dizelerini yapılandırma](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Paylaşılan erişim imzasıyla yetkilendirme

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Xamarin kitaplığı gibi bazı Azure depolama istemci kitaplıkları yalnızca paylaşılan erişim imzası (SAS) belirteciyle kimlik doğrulamasını destekler. [Depolama Gezgini](https://storageexplorer.com/) veya paylaşılan anahtar kimlik doğrulamasını destekleyen başka bir uygulama kullanarak SAS belirtecini oluşturabilirsiniz.

Azure PowerShell kullanarak bir SAS belirteci de oluşturabilirsiniz. Aşağıdaki örnek bir blob kapsayıcısına tam izinlerle bir SAS belirteci üretir:

1. Henüz yapmadıysanız Azure PowerShell yükleyebilirsiniz (Azure PowerShell cmdlet 'lerinin en son sürümünü kullanmanız önerilir). Yükleme yönergeleri için bkz. [Azure PowerShell yükleme ve yapılandırma](/powershell/azure/install-Az-ps).
2. Azure PowerShell açın ve aşağıdaki komutları çalıştırarak `CONTAINER_NAME` ' ı seçtiğiniz adla değiştirin:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

Yeni kapsayıcı için ortaya çıkan paylaşılan erişim imzası URI 'SI şuna benzer olmalıdır:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

Bu örnekle oluşturulan paylaşılan erişim imzası bir gün için geçerlidir. İmza, kapsayıcı içindeki bloblara tam erişim (okuma, yazma, silme, listeleme) verir.

Paylaşılan erişim imzaları hakkında daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](storage-sas-overview.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Depolama öykünücüsünde kaynakları adresleme

Depolama öykünücüsünün hizmet uç noktaları, Azure depolama hesabının uç noktalarından farklıdır. Yerel bilgisayar, etki alanı adı çözümlemesi yapmaz ve depolama öykünücü uç noktalarının yerel adresler olmasını gerektirir.

Bir Azure depolama hesabındaki bir kaynağı adresettiğiniz zaman, aşağıdaki düzeni kullanırsınız. Hesap adı URI ana bilgisayar adının bir parçası ve değinmekte olan kaynak URI yolunun bir parçası:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Örneğin, aşağıdaki URI, Azure depolama hesabındaki bir blob için geçerli bir adrestir:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Yerel bilgisayar etki alanı adı çözümlemesi gerçekleştirmediğinden, hesap adı konak adı yerine URI yolunun bir parçasıdır. Depolama öykünücüsünde bir kaynak için aşağıdaki URI biçimini kullanın:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Örneğin, aşağıdaki adres, depolama öykünücüsünde bir bloba erişmek için kullanılabilir:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Depolama öykünücüsünün hizmet uç noktaları şunlardır:

* Blob hizmeti: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Kuyruk hizmeti: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Tablo hizmeti: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>RA-GRS ile hesap ikincil adresi oluşturma

Sürüm 3,1 ' den başlayarak, depolama öykünücüsü Okuma Erişimli Coğrafi olarak yedekli çoğaltmayı (RA-GRS) destekler. İkincil konuma, hesap adına ikincil ekleyerek erişebilirsiniz. Örneğin, aşağıdaki adres, depolama öykünücüsünde salt okunurdur ikincil kullanarak bir bloba erişmek için kullanılabilir:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Depolama öykünücüsüyle ikincil öğesine programlı erişim için, .NET sürüm 3,2 veya üzeri için depolama Istemcisi kitaplığı 'nı kullanın. Ayrıntılar için bkz. [.NET için Microsoft Azure depolama Istemci kitaplığı](https://msdn.microsoft.com/library/azure/dn261237.aspx) .
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Depolama öykünücüsü komut satırı araç başvurusu

Sürüm 3,0 ' den başlayarak, depolama öykünücüsünü başlattığınızda bir konsol penceresi görüntülenir. Öykünücüyü başlatmak ve durdurmak için konsol penceresindeki komut satırını kullanın. Ayrıca, durumu sorgulayabilir ve komut satırından başka işlemler yapabilirsiniz.

> [!NOTE]
> Microsoft Azure işlem öykünücüsü yüklüyse, depolama öykünücüsünü başlattığınızda bir sistem tepsisi simgesi görüntülenir. Depolama öykünücüsünü başlatmak ve durdurmak için grafiksel bir yol sağlayan menüyü açığa çıkarmak için simgeye sağ tıklayın.
>
>

### <a name="command-line-syntax"></a>Komut satırı sözdizimi

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Seçenekler

Seçenek listesini görüntülemek için komut satırına `/help` yazın.

| Seçenek | Açıklama | Komut | Bağımsız Değişkenler |
| --- | --- | --- | --- |
| **Start** |Depolama öykünücüsünü başlatır. |`AzureStorageEmulator.exe start [-inprocess]` |*-Reprocess*: yeni bir işlem oluşturmak yerine öykünücüyü geçerli işlemde başlatın. |
| **Durdurulması** |Depolama öykünücüsünü sonlandırır. |`AzureStorageEmulator.exe stop` | |
| **Durum** |Depolama öykünücüsünün durumunu yazdırır. |`AzureStorageEmulator.exe status` | |
| **Lediğiniz** |Komut satırında belirtilen tüm hizmetlerde verileri temizler. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*BLOB*: blob verilerini temizler. <br/>*kuyruk*: kuyruk verilerini temizler. <br/>*tablo*: tablo verilerini temizler. <br/>*Tümü*: tüm hizmetlerde tüm verileri temizler. |
| **Dengeleyici** |Öykünücüyü ayarlamak için bir kerelik başlatma işlemi yapar. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-Server sunucuadi InstanceName*: SQL örneğini barındıran sunucuyu belirtir. <br/>*-SQLInstance InstanceName*: varsayılan sunucu ÖRNEĞINDE kullanılacak SQL örneğinin adını belirtir. <br/>*-forcecoluştur*: zaten mevcut olsa bile SQL veritabanı oluşturmayı zorlar. <br/>*-skipcreate*: SQL veritabanı oluşturmayı atlar. Bu,-forcecoluştur 'a göre önceliklidir.<br/>*-reserveports*: HIZMETLERLE ilişkili http bağlantı noktalarını ayırmaya çalışır.<br/>*-unreserveports*: HIZMETLERLE ilişkili http bağlantı noktaları için ayırmaları kaldırmaya çalışır. Bu,-reserveports üzerinden önceliklidir.<br/>*-InProcess*: yeni bir işlem oluşturmak yerine geçerli işlemde başlatmayı gerçekleştirir. Bağlantı noktası ayırmalarını değiştirmek için geçerli işlem yükseltilmiş izinlerle başlatılmalıdır. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Depolama öykünücüsü ile Azure depolama arasındaki farklar

Depolama öykünücüsü yerel bir Öykünülmüş ortam olduğundan, öykünücü ve buluttaki bir Azure depolama hesabı ile arasında farklılıklar vardır:

* Depolama öykünücüsü yalnızca tek bir sabit hesabı ve iyi bilinen bir kimlik doğrulama anahtarını destekler.
* Depolama öykünücüsü ölçeklenebilir bir depolama hizmeti değildir ve çok sayıda eşzamanlı istemciyi desteklemez.
* [Depolama öykünücüsünde kaynakları adresleme](#addressing-resources-in-the-storage-emulator)bölümünde açıklandığı gibi, kaynaklar depolama öykünücüsünde bir Azure depolama hesabına göre farklı şekilde ele alınır. Bunun farkı, etki alanı adı çözümlemenin yerel bilgisayarda değil bulutta kullanılabilir olmasından kaynaklanır.
* Sürüm 3,1 ' den başlayarak, depolama öykünücü hesabı Okuma Erişimli Coğrafi olarak yedekli çoğaltmayı (RA-GRS) destekler. Öykünücüde tüm hesaplarda RA-GRS etkinleştirilmiştir ve birincil ve ikincil çoğaltmalar arasında hiç gecikme yoktur. Blob hizmeti alma Istatistiklerini al, kuyruk hizmet Istatistiklerini al ve tablo hizmeti Istatistikleri alma işlemleri, hesap ikincil üzerinde desteklenir ve her zaman temel alınan SQL veritabanına göre `LastSyncTime` yanıt öğesinin değerini geçerli zaman olarak döndürür.
* Dosya hizmeti ve SMB protokol hizmeti uç noktaları şu anda depolama öykünücüsünde desteklenmiyor.
* Öykünücü tarafından desteklenmeyen Depolama Hizmetleri sürümünü kullanıyorsanız, öykünücü bir VersionNotSupportedByEmulator hatası döndürür (HTTP durum kodu 400-Hatalı Istek).

### <a name="differences-for-blob-storage"></a>BLOB depolama farklılıkları

Öykünücüdeki blob depolaması için aşağıdaki farklar geçerlidir:

* Depolama öykünücüsü yalnızca 2 GB 'a kadar blob boyutunu destekler.
* Depolama öykünücüsünde bir blob adının maksimum uzunluğu 256 karakterdir, ancak Azure Storage 'da bir blob adı en fazla uzunluğu 1024 karakterdir.
* Artımlı kopyalama, geçersiz kılınabilen Blobların kopyalanmasını sağlar ve bu da hizmette bir hata döndürür.
* Sayfa aralıklarını al fark, artımlı kopya blobu kullanılarak kopyalanmış anlık görüntüler arasında çalışmıyor.
* Bir put blobu işlemi, istekte kira KIMLIĞI belirtilmese bile, etkin bir kiralama ile depolama öykünücüsünde bulunan bir blob 'a karşı başarılı olabilir.
* Ekleme blobu işlemleri öykünücü tarafından desteklenmez. Bir ekleme blobu üzerinde bir işlemin deneneceği bir FeatureNotSupportedByEmulator hatası (HTTP durum kodu 400-Hatalı Istek) döndürüyor.

### <a name="differences-for-table-storage"></a>Tablo depolama farklılıkları

Öykünücüdeki tablo depolaması için aşağıdaki farklar geçerlidir:

* Depolama öykünücüsünde tablo hizmetindeki Tarih özellikleri yalnızca SQL Server 2005 tarafından desteklenen aralığı destekler (1 Ocak 1753 ' den sonra olması gerekir). 1 Ocak 1753 tarihinden önceki tüm tarihler bu değere dönüştürülür. Tarihlerin duyarlığı, SQL Server 2005 ' lik duyarlığından sınırlıdır, Yani tarihin saniyenin 1/300 ' e kadar kesin olduğu anlamına gelir.
* Depolama öykünücüsü, her biri 512 bayttan az olan bölüm anahtarını ve satır anahtarı özellik değerlerini destekler. Hesap adı, tablo adı ve anahtar özelliği adlarının toplam boyutu 900 baytı aşamaz.
* Depolama öykünücüsünde bir tablodaki bir satırın toplam boyutu 1 MB 'tan az olacak şekilde sınırlıdır.
* Depolama öykünücüsünde, `Edm.Guid` veya `Edm.Binary` veri türünün özellikleri yalnızca sorgu filtresi dizelerindeki `Equal (eq)` ve `NotEqual (ne)` karşılaştırma işleçlerini destekler.

### <a name="differences-for-queue-storage"></a>Kuyruk depolama farkları

Öykünücüde kuyruk depolamaya özgü fark yoktur.

## <a name="storage-emulator-release-notes"></a>Depolama öykünücüsü sürüm notları

### <a name="version-510"></a>Sürüm 5,10

* Depolama öykünücüsü, blob, kuyruk ve tablo hizmeti uç noktalarında depolama hizmetlerinin 2019-07-07 sürümünü reddeder.

### <a name="version-59"></a>Sürüm 5,9

* Depolama öykünücüsü, blob, kuyruk ve tablo hizmeti uç noktalarında depolama hizmetlerinin 2019-02-02 sürümünü reddeder.

### <a name="version-58"></a>Sürüm 5,8

* Depolama öykünücüsü, blob, kuyruk ve tablo hizmeti uç noktalarında depolama hizmetlerinin 2018-11-09 sürümünü reddeder.

### <a name="version-57"></a>Sürüm 5,7

* Günlüğe kaydetme etkinleştirildiyse çökmeye neden olacak bir hata düzeltildi.

### <a name="version-56"></a>Sürüm 5,6

* Depolama öykünücüsü artık BLOB, kuyruk ve tablo hizmeti uç noktalarında depolama hizmetlerinin 2018-03-28 sürümünü desteklemektedir.

### <a name="version-55"></a>Sürüm 5,5

* Depolama öykünücüsü artık BLOB, kuyruk ve tablo hizmeti uç noktalarında depolama hizmetlerinin 2017-11-09 sürümünü desteklemektedir.
* Blob 'un oluşturulma zamanını döndüren blob **oluşturuldu** özelliği için destek eklenmiştir.

### <a name="version-54"></a>Sürüm 5,4

* Yükleme kararlılığını artırmak için, öykünücü artık yükleme zamanında bağlantı noktası ayırma girişiminde bulunmamaktadır. Bağlantı noktası ayırmalarını isterseniz, bunu belirtmek için **Init** komutunun *-reserveports* seçeneğini kullanın.

### <a name="version-53"></a>Sürüm 5,3

* Depolama öykünücüsü artık BLOB, kuyruk ve tablo hizmeti uç noktalarında depolama hizmetlerinin 2017-07-29 sürümünü desteklemektedir.

### <a name="version-52"></a>Sürüm 5,2

* Depolama öykünücüsü artık BLOB, kuyruk ve tablo hizmeti uç noktalarında depolama hizmetlerinin 2017-04-17 sürümünü desteklemektedir.
* Tablo özelliği değerlerinin düzgün şekilde kodlanamayan bir hata düzeltildi.

### <a name="version-51"></a>Sürüm 5,1

* Depolama öykünücüsünün, hizmetin olmadığı bazı yanıtlara `DataServiceVersion` üst bilgisini döndürdüğü bir hata düzeltildi.

### <a name="version-50"></a>Sürüm 5,0

* Depolama öykünücüsü yükleyicisi artık var olan MSSQL ve .NET Framework yüklemelerini kontrol eder.
* Depolama öykünücüsü yükleyicisi, artık veritabanını yükleme kapsamında oluşturmayacaktır. Başlatma kapsamında gerekirse veritabanı yine de oluşturulacaktır.
* Veritabanı oluşturma artık yükseltme gerektirmez.
* Bağlantı noktası ayırmaları artık başlangıç için gerekli değildir.
* @No__t-0: `-reserveports` (yükseltme gerektirir), `-unreserveports` (yükseltme gerektirir), `-skipcreate` ' e aşağıdaki seçenekleri ekler.
* Sistem tepsisi simgesinde depolama öykünücüsü Kullanıcı Arabirimi seçeneği artık komut satırı arabirimini başlatır. Eski GUI artık kullanılamıyor.
* Bazı dll 'Ler kaldırıldı veya yeniden adlandırıldı.

### <a name="version-46"></a>Sürüm 4,6

* Depolama öykünücüsü artık BLOB, kuyruk ve tablo hizmeti uç noktalarında depolama hizmetlerinin 2016-05-31 sürümünü desteklemektedir.

### <a name="version-45"></a>Sürüm 4,5

* Yedekleme veritabanı yeniden adlandırıldığında yükleme ve başlatmanın başarısız olmasına neden olan bir hata düzeltildi.

### <a name="version-44"></a>Sürüm 4,4

* Depolama öykünücüsü artık BLOB, kuyruk ve tablo hizmeti uç noktalarında depolama hizmetlerinin 2015-12-11 sürümünü desteklemektedir.
* Depolama öykünücüsünün blob verileri çöp toplama işlemi, çok sayıda blob ile ilgilenirken artık daha etkilidir.
* Kapsayıcı ACL XML 'sinin depolama hizmetinin nasıl yaptığından farklı bir şekilde doğrulanmasının neden olduğu bir hata düzeltildi.
* Bazen büyük ve küçük tarih saat değerlerinin yanlış saat diliminden raporlanmasının neden olduğu bir hata düzeltildi.

### <a name="version-43"></a>Sürüm 4,3

* Depolama öykünücüsü artık BLOB, kuyruk ve tablo hizmeti uç noktalarında depolama hizmetlerinin 2015-07-08 sürümünü desteklemektedir.

### <a name="version-42"></a>Sürüm 4,2

* Depolama öykünücüsü artık BLOB, kuyruk ve tablo hizmeti uç noktalarında depolama hizmetlerinin 2015-04-05 sürümünü desteklemektedir.

### <a name="version-41"></a>Sürüm 4,1

* Depolama öykünücüsü artık BLOB, kuyruk ve tablo hizmeti uç noktalarında depolama hizmetlerinin 2015-02-21 sürümünü desteklemektedir. Yeni ekleme blobu özelliklerini desteklemez.
* Öykünücü, artık desteklenmeyen Depolama Hizmetleri sürümleri için anlamlı bir hata iletisi döndürüyor. Öykünücü 'un en son sürümünü kullanmanızı öneririz. Bir VersionNotSupportedByEmulator hatası (HTTP durum kodu 400-Hatalı Istek) alırsanız, öykünücü 'nın en son sürümünü indirin.
* Yarış durumunda olan bir hata düzeltildiğinde, eşzamanlı birleştirme işlemleri sırasında tablo varlık verilerinin hatalı olması neden oldu.

### <a name="version-40"></a>Sürüm 4,0

* Depolama öykünücüsü yürütülebilir dosyası, *Azurestorampaemulator. exe*olarak yeniden adlandırıldı.

### <a name="version-32"></a>Sürüm 3,2

* Depolama öykünücüsü artık BLOB, kuyruk ve tablo hizmeti uç noktalarında depolama hizmetlerinin 2014-02-14 sürümünü desteklemektedir. Dosya hizmeti uç noktaları şu anda depolama öykünücüsünde desteklenmiyor. Sürüm 2014-02-14 hakkındaki ayrıntılar için bkz. [Azure depolama hizmetleri Için sürüm oluşturma](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) .

### <a name="version-31"></a>Sürüm 3,1

* Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) depolama öykünücüsünde destekleniyor. @No__t-0, `Get Queue Service Stats` ve `Get Table Service Stats` API 'Ler hesap ikincil için desteklenir ve arka plandaki SQL veritabanına göre her zaman LastSyncTime yanıt öğesinin değerini geçerli saat olarak döndürür. Depolama öykünücüsüyle ikincil öğesine programlı erişim için, .NET sürüm 3,2 veya üzeri için depolama Istemcisi kitaplığı 'nı kullanın. Ayrıntılar için bkz. .NET için Microsoft Azure Depolama Istemci kitaplığı başvurusu.

### <a name="version-30"></a>Sürüm 3,0

* Azure depolama öykünücüsü artık işlem öykünücüsü ile aynı pakette yer alınmaz.
* Depolama öykünücüsü grafik kullanıcı arabirimi kullanım dışı bırakılmıştır. Bu, betik oluşturulmuş bir komut satırı arabirimi ile değiştirilmiştir. Komut satırı arabirimi hakkında daha fazla bilgi için bkz. depolama öykünücüsü komut satırı araç başvurusu. Grafik arabirimi sürüm 3,0 ' de mevcut olmaya devam edecektir, ancak yalnızca Işlem öykünücüsü, sistem tepsisi simgesine sağ tıklayıp depolama öykünücüsü Kullanıcı arabirimini göster ' i seçerek erişilebilir.
* Azure Depolama hizmetlerinin 2013-08-15 sürümü artık tam olarak desteklenmektedir. (Daha önce bu sürüm yalnızca depolama öykünücü sürümü 2.2.1 Preview tarafından desteklenmektedir.)

## <a name="next-steps"></a>Sonraki adımlar

* Platformlar arası, topluluk tarafından tutulan açık kaynaklı depolama öykünücüsü [Azurıite](https://github.com/arafato/azurite)'yi değerlendirin. 
* [.NET kullanan Azure depolama örnekleri](../storage-samples-dotnet.md) , uygulamanızı geliştirirken kullanabileceğiniz çeşitli kod örneklerinin bağlantılarını içerir.
* Bulut depolama hesabınızda ve depolama öykünücüsünde kaynaklarla çalışmak için [Microsoft Azure Depolama Gezgini](https://storageexplorer.com) kullanabilirsiniz.
