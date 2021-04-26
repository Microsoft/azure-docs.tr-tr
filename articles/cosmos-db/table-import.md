---
title: Mevcut verileri Azure Cosmos DB bir Tablo API'si hesabına geçirin
description: Şirket içi veya bulut verilerinin Azure Cosmos DB bir Azure Tablo API'si hesabına nasıl geçireceğinizi veya içeri aktarılacağını öğrenin.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 77f4c928db695bd4193ad46c93e0efbd16decf29
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443364"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>Verilerinizi bir Azure Cosmos DB Tablo API'si hesabına geçirin
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Bu öğreticide, Azure Cosmos DB [tablo API'si](table-introduction.md)kullanım için verileri içeri aktarmaya yönelik yönergeler sağlanmaktadır. Azure Tablo depolamada depolanmış verileriniz varsa, verilerinizi Azure Cosmos DB Tablo API'si aktarmak için veri geçiş aracı veya AzCopy kullanabilirsiniz. 

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Veri geçiş aracı ile veri aktarma
> * AzCopy ile veri içeri aktarma

## <a name="prerequisites"></a>Önkoşullar

* **Aktarım hızını artırma:** Veri geçişinizin süresi, tek bir kapsayıcı veya bir kapsayıcı kümesi için ayarladığınız aktarım hızı miktarına bağlıdır. Büyük veri geçişleri için aktarım hızını artırdığınızdan emin olun. Geçişi tamamladıktan sonra maliyet tasarrufu sağlamak için aktarım hızını azaltın.

* **Azure Cosmos DB kaynakları oluşturma:** Verileri geçirmeye başlamadan önce, Azure portal tüm tablolarınızı oluşturun. Veritabanı düzeyinde aktarım hızı olan bir Azure Cosmos DB hesabına geçiş yapıyorsanız, Azure Cosmos DB tabloları oluştururken bir bölüm anahtarı sağladığınızdan emin olun.

## <a name="data-migration-tool"></a>Veri geçiş aracı

Mevcut Azure Tablo depolama verilerinizi bir Tablo API'si hesabına aktarmak için Azure Cosmos DB komut satırı veri geçiş aracı 'nı (dt.exe) kullanabilirsiniz. 

Tablo verilerini geçirmek için:

1. Geçiş aracını [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool)'dan indirin.
2. `dt.exe`Senaryonuz için komut satırı bağımsız değişkenlerini kullanarak çalıştırın. `dt.exe` aşağıdaki biçimde bir komut alır:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Bu komut için desteklenen seçenekler şunlardır:

* **/Errorlog:** Seçim. Veri aktarımı hatalarının yeniden yönlendirileceği CSV dosyasının adı.
* **/Overwritehata günlüğü:** Seçim. Hata günlüğü dosyasının üzerine yazın.
* **/ProgressUpdateInterval:** İsteğe bağlı, varsayılan `00:00:01` . Ekrandaki veri aktarımı ilerlemesini yenileme zaman aralığı.
* **/ErrorDetails:** İsteğe bağlı, varsayılan `None` . Ayrıntılı hata bilgilerinin aşağıdaki hatalar için görüntüleneceğini belirtir: `None` , `Critical` , veya `All` .
* **/Enablecosmostablelog:** Seçim. Günlüğü bir Azure Cosmos DB tablo hesabına yönlendirin. Ayarlanırsa, bu varsayılan olarak hedef hesap bağlantı dizesine `/CosmosTableLogConnectionString` de sağlanmamıştır. Bu, aracın birden çok örneği aynı anda çalıştırıldığında yararlıdır.
* **/Cosmostablelogconnectionstring:** Seçim. Günlüğü uzak Azure Cosmos DB tablo hesabına yönlendirecek bağlantı dizesi.

### <a name="command-line-source-settings"></a>Komut satırı kaynak ayarları

Geçiş kaynağı olarak Azure Tablo Depolamayı tanımlarken aşağıdaki kaynak seçeneklerini kullanın.

* **/s: AzureTable:** Tablo depolamadan verileri okur.
* **/S.ConnectionString:** Tablo uç noktası için bağlantı dizesi. Bunu Azure portal elde edebilirsiniz.
* **/S.locationmode:** İsteğe bağlı, varsayılan `PrimaryOnly` . Tablo depolamaya bağlanırken kullanılacak konum modunu belirtir: `PrimaryOnly` , `PrimaryThenSecondary` , `SecondaryOnly` , `SecondaryThenPrimary` .
* **/S.Table:** Azure tablosunun adı.
* **/S.InternalFields:** `All` , `RowKey` Ve `PartitionKey` içeri aktarma için gerekli olduğundan tablo geçişi için olarak ayarlayın.
* **/S.Filter:** Seçim. Filtre dizesini Uygula.
* **/S.Projection:** Seçim. Seçilecek sütunların listesi,

Tablo depolamadan içeri aktardığınızda kaynak bağlantı dizesini almak için Azure portal açın. **Depolama hesapları**  >  **Hesap**  >  **erişim anahtarları**' nı seçin ve **bağlantı dizesini** kopyalayın.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="> hesabı > erişim tuşları seçeneklerini gösteren ve kopyalama simgesini vurgulayan ekran görüntüsü.":::

### <a name="command-line-target-settings"></a>Komut satırı hedefi ayarları

Azure Cosmos DB Tablo API'si geçişin hedefi olarak tanımlarken aşağıdaki hedef seçenekleri kullanın.

* **/t: Tabloapitoplu:** Azure Cosmos DB Tablo API'si toplu işlemlere veri yükler.
* **/T.exe:** Tablo uç noktası için bağlantı dizesi.
* **/T/TableName:** Yazılacak tablonun adını belirtir.
* **/T.exe üzerine yaz:** İsteğe bağlı, varsayılan `false` . Varolan değerlerin üzerine yazılıp yazılmayacağını belirtir.
* **/T.exe:** İsteğe bağlı, varsayılan `1GB` . Havuza veri reçeteye göre verileri kapatmadan önce arabelleğe giriş baytlarının yaklaşık tahmini tahminidir.
* **/T/aktarım hızı:** İsteğe bağlı, hizmet varsayılan olarak belirlenir. Tablo için yapılandırılacak aktarım hızını belirtir.
* **/T3batchsize:** İsteğe bağlı, varsayılan `2MB` . Toplu iş boyutunu bayt cinsinden belirtin.

### <a name="sample-command-source-is-table-storage"></a>Örnek komut: kaynak tablo depolama alanı

Aşağıda, tablo depolamadan Tablo API'si nasıl içeri aktarılacağını gösteren bir komut satırı örneği verilmiştir:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>AzCopy'yi kullanarak veri geçirme

Ayrıca, verileri tablo depolamadan Azure Cosmos DB Tablo API'si geçirmek için AzCopy komut satırı yardımcı programını da kullanabilirsiniz. AzCopy kullanmak için, ilk olarak verilerinizi [tablo depolamadan dışarı aktarma](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage)başlığı altında açıklandığı gibi dışarı aktarabilirsiniz. Ardından, [Azure Cosmos DB tablo API'si](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage)açıklandığı gibi verileri Azure Cosmos DB içeri aktarırsınız.

Azure Cosmos DB içine aktarırken aşağıdaki örneğe bakın. `/Dest`Değerin `cosmosdb` , değil, kullandığını unutmayın `core` .

Örnek içeri aktarma komutu:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB Tablo API'si kullanarak verileri sorgulamayı öğrenin. 

> [!div class="nextstepaction"]
>[Veriler nasıl sorgulanır?](../cosmos-db/tutorial-query-table.md)




