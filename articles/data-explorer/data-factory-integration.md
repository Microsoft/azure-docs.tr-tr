---
title: Azure Data Factory ile Azure Veri Gezgini tümleştirmesi
description: Bu konuda, kopyalama, arama ve komut etkinliklerini kullanmak için Azure Veri Gezgini Azure Data Factory ile tümleştirin
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: dd2b3bd584bb39810e0a5c9acde1a961330c273d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093767"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Azure Veri Gezgini Azure Data Factory ile tümleştirme

[Azure Data Factory](/azure/data-factory/) (ADF), farklı veri depolarını tümleştirmenize ve veriler üzerinde etkinlikler gerçekleştirmenize olanak tanıyan bulut tabanlı bir veri tümleştirme hizmetidir. ADF, veri taşıma ve veri dönüştürmeyi düzenlemek ve otomatikleştirmek için veri odaklı iş akışları oluşturmanıza olanak tanır. Azure Veri Gezgini, Azure Data Factory [desteklenen veri depolarından](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) biridir. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure Veri Gezgini Azure Data Factory etkinlikleri

Azure Veri Gezgini kullanıcıları için Azure Data Factory ile çeşitli tümleştirmeler sunulmaktadır:

### <a name="copy-activity"></a>Kopyalama etkinliği
 
Veri depoları arasında veri aktarmak için kopyalama etkinliği Azure Data Factory kullanılır. Azure Veri Gezgini, verilerin Azure Veri Gezgini desteklenen herhangi bir veri deposuna kopyalandığı bir kaynak olarak desteklenir ve verilerin desteklenen herhangi bir veri deposundan Azure Veri Gezgini 'e kopyalandığı bir havuz olur. Daha fazla bilgi için bkz. [Azure Data Factory kullanarak Azure Veri Gezgini veri kopyalama](/azure/data-factory/connector-azure-data-explorer). ayrıntılı bir adım adım için bkz. [Azure Data Factory verileri Azure Veri Gezgini 'a yükleme](data-factory-load-data.md).
Azure Veri Gezgini Azure IR (Integration Runtime) tarafından desteklenir, veriler Azure 'da kopyalanırken ve şirket içinde ya da bir Azure sanal ağı gibi erişim denetimi olan bir ağda veri depolarından veri kopyalarken kullanılan, şirket içinde barındırılan IR. Daha fazla bilgi için bkz. [kullanılacak IR](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> Kopyalama etkinliğini kullanırken ve **bağlı hizmet** ya da **veri kümesi**oluştururken, eski veri deposu **kusto**değil, veri deposu **Azure Veri Gezgini (kusto)** öğesini seçin.  

### <a name="lookup-activity"></a>Arama etkinliği
 
Arama etkinliği, Azure Veri Gezgini sorguları yürütmek için kullanılır. Sorgunun sonucu, arama etkinliğinin çıktısı olarak döndürülür ve [ADF arama belgelerinde](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity)açıklandığı şekilde işlem hattının bir sonraki etkinliğinde kullanılabilir.  
5\.000 satırı ve 2 MB yanıt boyutu sınırına ek olarak, etkinliğin ayrıca 1 saatlik bir sorgu zaman aşımı sınırı vardır.

### <a name="command-activity"></a>Komut etkinliği

Komut etkinliği, Azure Veri Gezgini [Denetim komutlarının](/azure/kusto/concepts/#control-commands)yürütülmesine izin verir. Sorguların aksine, denetim komutları büyük olasılıkla verileri veya meta verileri değiştirebilir. Denetim komutlarının bazıları, verileri Azure Veri Gezgini 'a almak, `.ingest`veya `.set-or-append`gibi komutları kullanarak Azure Veri Gezgini ve `.export`gibi komutları kullanarak verileri dış veri depolarına kopyalamak için hedeflenmiştir.
Komut etkinliğine ilişkin ayrıntılı bir yol için bkz. [Azure Veri Gezgini denetim komutlarını çalıştırmak için Azure Data Factory komut etkinliğini kullanma](data-factory-command-activity.md).  Verileri kopyalamak için bir denetim komutu kullanmak, kopyalama etkinliğinden daha hızlı ve ucuz seçeneği de olabilir. Komut etkinliğinin kopyalama etkinliğine göre ne zaman kullanılacağını belirlemek için, bkz. [veri kopyalanırken kopyalama ve komut etkinlikleri arasında seçim](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data)yapın.

### <a name="copy-in-bulk-from-a-database-template"></a>Veritabanı şablonundan toplu olarak Kopyala

[Bir veritabanından Azure Veri Gezgini Azure Data Factory şablonu kullanılarak toplu kopyalama](data-factory-template.md) , önceden tanımlanmış bir Azure Data Factory işlem hattı olur. Şablon, daha hızlı veri kopyalama için veritabanı başına veya tablo başına çok sayıda işlem hattı oluşturmak için kullanılır. 

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Veri kopyalama sırasında Kopyala ve Azure Veri Gezgini komut etkinlikleri arasında seçim yapın 

Bu bölüm, veri kopyalama gereksinimleriniz için doğru etkinliği seçerken size yardımcı olur.

Veya Azure Veri Gezgini veri kopyalarken, Azure Data Factory iki kullanılabilir seçenek vardır:
* Kopyalama etkinliği.
* Azure Veri Gezgini 'da veri aktarımı yapan denetim komutlarından birini çalıştıran Azure Veri Gezgini komut etkinliği. 

### <a name="copy-data-from-azure-data-explorer"></a>Azure Veri Gezgini veri kopyalama
  
Kopyalama etkinliğini veya [`.export`](/azure/kusto/management/data-export/) komutunu kullanarak Azure Veri Gezgini 'tan veri kopyalayabilirsiniz. `.export` komutu bir sorgu yürütür ve sonra sorgunun sonuçlarını dışarı aktarır. 

Kopyalama etkinliğinin ve Azure Veri Gezgini verileri kopyalamak için `.export` komutunun karşılaştırması için aşağıdaki tabloya bakın.

| | Kopyalama etkinliği | . export komutu |
|---|---|---|
| **Akış açıklaması** | ADF kusto üzerinde bir sorgu yürütür, sonucu işler ve hedef veri deposuna gönderir. <br>(**ADX > ADF > havuz veri deposu**) | ADF, komutu yürüten ve verileri doğrudan hedef veri deposuna gönderen Azure Veri Gezgini `.export` bir denetim komutu gönderir. <br>(**ADX > havuz veri deposu**) |
| **Desteklenen hedef veri depoları** | Desteklenen çok çeşitli [veri depoları](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, Azure blob, SQL veritabanı |
| **Performans** | Merkez | <ul><li>Dağıtılmış (varsayılan), birden çok düğümden eşzamanlı olarak veri dışarı aktarılıyor</li><li>Daha hızlı ve SMM etkin.</li></ul> |
| **Sunucu limitleri** | [Sorgu sınırları](/azure/kusto/concepts/querylimits) Genişletilebilir/devre dışı olabilir. Varsayılan olarak, ADF sorguları şunları içerir: <ul><li>500.000 kayıt veya 64 MB boyut limiti.</li><li>10 dakikalık zaman sınırı.</li><li>`noTruncation` false olarak ayarlandı.</li></ul> | Varsayılan olarak, sorgu sınırlarını genişletir veya devre dışı bırakır: <ul><li>Boyut sınırları devre dışı bırakıldı.</li><li>Sunucu zaman aşımı, 1 saate genişletilir.</li><li>`MaxMemoryConsumptionPerIterator` ve `MaxMemoryConsumptionPerQueryPerNode` en fazla (5 GB, TotalPhysicalMemory/2) olarak genişletilir.</li></ul>

> [!TIP]
> Kopyalama hedefi `.export` komutu tarafından desteklenen veri depolarından biri ise ve kopyalama etkinliği özelliklerinin hiçbiri gereksinimlerinize göre önemli değilse, `.export` komutunu seçin.

### <a name="copying-data-to-azure-data-explorer"></a>Azure Veri Gezgini veri kopyalama

Veri kopyalama (`.set-or-append`, `.set-or-replace`, `.set`, `.replace)`ve [depolama 'dan](/azure/kusto/management/data-ingestion/ingest-from-storage) alma (`.ingest`[) gibi kopyalama](/azure/kusto/management/data-ingestion/ingest-from-query) etkinliği veya alma komutlarını kullanarak verileri Azure Veri Gezgini kopyalayabilirsiniz. 

Kopyalama etkinliğinin karşılaştırması için aşağıdaki tabloya ve Azure Veri Gezgini veri kopyalamaya yönelik giriş komutlarına bakın.

| | Kopyalama etkinliği | Sorgudan al<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Depolama alanından alma <br> `.ingest` |
|---|---|---|---|
| **Akış açıklaması** | ADF, kaynak veri deposundan verileri alır, tablo biçimine dönüştürür ve gerekli şema eşleme değişiklikleri yapar. ADF daha sonra verileri Azure bloblarına yükler, parçalara ayırır, sonra bunları ADX tablosuna almak için Blobları indirir. <br> (**Kaynak veri deposu > ADF > Azure Blob 'ları > ADX**) | Bu komutlar bir sorgu veya `.show` komutu yürütebilir ve sorgunun sonuçlarını bir tabloya (**adx > ADX**) alabilir. | Bu komut, verileri bir veya daha fazla bulut depolama yapıtlarından "çekerek" bir tabloya dönüştürür. |
| **Desteklenen kaynak veri depoları** |  [çeşitli seçenekler](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Azure blob, SQL (sql_request eklentisi kullanılarak), Cosmos (cosmosdb_sql_request eklentisini kullanarak) ve HTTP veya Python API 'Leri sağlayan diğer tüm veri depolarını kullanın. | Dosya sistemi, Azure Blob depolama, ADLS Gen 1, ADLS Gen 2 |
| **Performans** | Yük Dengeleme, yeniden denemeler ve hata işleme sağlayarak küçük boyut alımı sağlayan ve yüksek kullanılabilirlik sağlayan alma işlemleri kuyruğa alınır ve yönetilir. | <ul><li>Bu komutlar, yüksek hacimli verileri içeri aktarma için tasarlanmamıştır.</li><li>Beklenen ve Cheaper olarak çalışarak. Ancak, üretim senaryolarında ve trafik oranları ve veri boyutları büyükse kopyalama etkinliğini kullanın.</li></ul> |
| **Sunucu limitleri** | <ul><li>Boyut sınırı yok.</li><li>En fazla zaman aşımı sınırı: alınan blob başına 1 saat. |<ul><li>Sorgu bölümünde yalnızca bir boyut sınırı vardır ve bu, `noTruncation=true`belirtilerek atlanabilir.</li><li>En fazla zaman aşımı sınırı: 1 saat.</li></ul> | <ul><li>Boyut sınırı yok.</li><li>En fazla zaman aşımı sınırı: 1 saat.</li></ul>|

> [!TIP]
> * ADF 'den Azure 'a veri kopyalarken Veri Gezgini `ingest from query` komutlarını kullanın.  
> * Büyük veri kümeleri (> 1 GB) için kopyalama etkinliğini kullanın.  

## <a name="required-permissions"></a>Gerekli izinler

Aşağıdaki tabloda Azure Data Factory ile tümleştirmede çeşitli adımlar için gerekli izinler listelenmektedir.

| Adım | İşlem | En düşük izin düzeyi | Notlar |
|---|---|---|---|
| **Bağlı hizmet oluşturma** | Veritabanı gezintisi | *veritabanı Görüntüleyicisi* <br>ADF kullanan oturum açmış kullanıcının veritabanı meta verilerini okuma yetkisi olmalıdır. | Kullanıcı, veritabanı adını el ile sağlayabilir. |
| | Bağlantıyı Sına | *veritabanı İzleyicisi* veya *tablo* alımı <br>Hizmet sorumlusu, veritabanı düzeyinde `.show` komutları veya tablo düzeyi alımı yürütme yetkisine sahip olmalıdır. | <ul><li>TestConnection, veritabanına değil, kümeyle olan bağlantıyı doğrular. Veritabanı mevcut olmasa bile başarılı olabilir.</li><li>Tablo Yöneticisi izinleri yeterli değildir.</li></ul>|
| **Veri kümesi oluşturma** | Tablo gezintisi | *Veritabanı izleyicisi* <br>ADF kullanan oturum açmış kullanıcının veritabanı düzeyinde `.show` komutlarını yürütmesi için yetkilendirilmiş olması gerekir. | Kullanıcı, tablo adını el ile sağlayabilir.|
| **Veri kümesi** veya **kopyalama etkinliği** oluşturma | Veri önizleme | *veritabanı Görüntüleyicisi* <br>Hizmet sorumlusu, veritabanı meta verilerini okumak için yetkilendirilmiş olmalıdır. | | 
|   | Şemayı içeri aktar | *veritabanı Görüntüleyicisi* <br>Hizmet sorumlusu, veritabanı meta verilerini okumak için yetkilendirilmiş olmalıdır. | ADX tablosal tablolu kopyalamanın kaynağı olduğunda, Kullanıcı şemayı açıkça içeri aktarmasa da ADF otomatik olarak şemayı içeri aktarır. |
| **Havuz olarak ADX** | Ada göre sütun eşlemesi oluşturma | *Veritabanı izleyicisi* <br>Hizmet sorumlusu, veritabanı düzeyinde `.show` komutlarının yürütülmesi için yetkilendirilmiş olmalıdır. | <ul><li>Tüm zorunlu işlemler *tablo*alımı ile birlikte çalışacaktır.</li><li> Bazı isteğe bağlı işlemler başarısız olabilir.</li></ul> |
|   | <ul><li>Tabloda CSV eşlemesi oluşturma</li><li>Eşlemeyi bırak</li></ul>| *tablo* alımı veya *veritabanı yöneticisi* <br>Hizmet sorumlusu bir tabloda değişiklik yapmak için yetkilendirilmiş olmalıdır. | |
|   | Veriyi çekme | *tablo* alımı veya *veritabanı yöneticisi* <br>Hizmet sorumlusu bir tabloda değişiklik yapmak için yetkilendirilmiş olmalıdır. | | 
| **Kaynak olarak ADX** | Sorguyu Yürüt | *veritabanı Görüntüleyicisi* <br>Hizmet sorumlusu, veritabanı meta verilerini okumak için yetkilendirilmiş olmalıdır. | |
| **Kusto komutu** | | Her komutun izin düzeyine göre. |

## <a name="performance"></a>Performans 

Azure Veri Gezgini kaynak ise ve bir sorgu içeren arama, kopyalama veya komut etkinliğini kullanıyorsanız, bu performans bilgileri için [sorgu en iyi uygulamaları](/azure/kusto/query/best-practices) ve [kopyalama etkinliği için ADF belgeleri](/azure/data-factory/copy-activity-performance)bölümüne bakın.
  
Bu bölüm, Azure Veri Gezgini havuz olan kopyalama etkinliğinin kullanımını ele alınmaktadır. Azure Veri Gezgini havuzu için tahmini verimlilik 11-13 MB/sn 'dir. Aşağıdaki tabloda, Azure Veri Gezgini havuzunun performansını etkileyen parametrelerin ayrıntıları verilmiştir.

| Parametre | Notlar |
|---|---|
| **Bileşenler coğrafi yakınlık** | Tüm bileşenleri aynı bölgeye Yerleştir:<ul><li>Kaynak ve havuz veri depoları.</li><li>ADF tümleştirme çalışma zamanı.</li><li>ADX kümeniz.</li></ul>En azından tümleştirme çalışma zamanının ADX kümeniz ile aynı bölgede olduğundan emin olun. |
| **DIUs sayısı** | ADF tarafından kullanılan her 4 DIUs için 1 VM. <br>DIUs 'in artırılması, yalnızca kaynağınız birden çok dosya içeren dosya tabanlı bir depo ise yardımcı olur. Böylece her VM, paralel olarak farklı bir dosya işler. Bu nedenle, tek bir büyük dosyayı kopyalamak birden çok küçük dosyayı kopyalamaya kıyasla daha yüksek bir gecikme süresine sahip olur.|
|**ADX kümenizin miktarı ve SKU 'SU** | Büyük sayıda ADX düğümü, alma işlemi süresini artırır.|
| **Paralellik** | Bir veritabanından çok büyük miktarda veri kopyalamak için verilerinizi bölümleyip her bölümü paralel olarak kopyalayan bir ForEach Döngüsü kullanın veya [veritabanından Azure Veri Gezgini şablonu ' na toplu kopyalama](data-factory-template.md)kullanın. Note: kopyalama etkinliğinde **paralellik derecesi** > **Ayarlar** ADX ile ilgili değildir. |
| **Veri işleme karmaşıklığı** | Gecikme, kaynak dosya biçimine, sütun eşlemesine ve sıkıştırmaya göre farklılık gösterir.|
| **Tümleştirme çalışma zamanı 'nı çalıştıran VM** | <ul><li>Azure kopyası için, ADF VM 'Ler ve makine SKU 'Ları değiştirilemez.</li><li> Şirket içi Azure kopyalama için, şirket içinde barındırılan IR 'yi barındıran VM 'nin yeterince güçlü olduğunu saptayın.</li></ul>|

## <a name="monitor-activity-progress"></a>Etkinlik ilerlemesini izleme

* Etkinlik ilerlemesini izlerken, *okunan* veriler ikili dosya boyutuna göre *hesaplandığı için veri* *okuma özelliğinden çok* daha büyük olabilir, çünkü *yazılan* veriler, veri serileştirilip sıkıştırması açıldıktan sonra bellek içi boyuta göre hesaplanır.

* Etkinlik ilerlemesini izlerken, verilerin Azure Veri Gezgini havuzuna yazıldığını görebilirsiniz. Azure Veri Gezgini tablosunu sorgularken verilerin ulaştığını görürsünüz. Bunun nedeni, Azure Veri Gezgini kopyalanırken iki aşamada oluşur. 
    * İlk aşama kaynak verileri okur, 900 MB parçalara ayırır ve her bir öbeği bir Azure Blobuna yükler. İlk aşama ADF etkinlik ilerleme görünümü tarafından görülür. 
    * İkinci aşama, tüm veriler Azure Bloblarına yüklendiğinde başlar. Azure Veri Gezgini altyapısı düğümleri blob 'ları indirir ve verilerin havuz tablosuna alınması. Veriler daha sonra Azure Veri Gezgini tablonuzda görülür.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Factory kullanarak verileri Azure Veri Gezgini kopyalama](data-factory-load-data.md)hakkında bilgi edinin.
* [Veritabanından Azure Veri Gezgini toplu kopyalama için Azure Data Factory şablonu](data-factory-template.md)kullanma hakkında bilgi edinin.
* [Azure Veri Gezgini denetim komutlarını çalıştırmak için Azure Data Factory komut etkinliğini](data-factory-command-activity.md)kullanma hakkında bilgi edinin.
* Veri sorgulama için [Azure Veri Gezgini sorguları](/azure/data-explorer/web-query-data) hakkında bilgi edinin.



