---
title: Azure Bloblarını Azure Veri Gezgini 'a alma
description: Bu makalede, Azure Veri Gezgini bir Event Grid aboneliği kullanarak depolama hesabı verilerinin nasıl gönderileceğini öğreneceksiniz.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: d3f5ef9d2c3359dc61c32d4971100b096b004f2f
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881558"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Event Grid bildirimlerine abone olarak Azure Veri Gezgini blob alma

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)

Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve ölçeklenebilir bir veri araştırma hizmetidir. Blob kapsayıcılarına yazılan bloblardan sürekli alma (veri yükleme) sağlar. 

Bu makalede, bir [Azure Event Grid](/azure/event-grid/overview) aboneliğini ayarlamayı ve olayları Azure Veri Gezgini bir olay hub 'ı aracılığıyla yönlendirmeyi öğreneceksiniz. Başlamak için, Azure Event Hubs bildirim gönderen bir Event Grid aboneliğine sahip bir depolama hesabınız olmalıdır. Daha sonra, bir Event Grid veri bağlantısı oluşturacak ve sistem genelinde veri akışını görebileceksiniz.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Ücretsiz bir [Azure hesabı](https://azure.microsoft.com/free/)oluşturun.
* [Bir küme ve veritabanı](create-cluster-database-portal.md).
* [Depolama hesabı](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Bir olay hub 'ı](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Depolama hesabınızda Event Grid aboneliği oluşturma

1. Azure portal depolama hesabınızı bulun.
1. **Olay aboneliği** > **olayları** seçin.

    ![Sorgu uygulama bağlantısı](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. **Temel** sekmesindeki **olay aboneliği oluştur** penceresinde aşağıdaki değerleri sağlayın:

    **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | Adı | *test-Grid-Connection* | Oluşturmak istediğiniz olay kılavuzunun adı.|
    | Olay şeması | *Event Grid şeması* | Olay Kılavuzu için kullanılması gereken şema. |
    | Konu türü | *Depolama hesabı* | Olay Kılavuzu konusunun türü. |
    | Konu kaynağı | *gridteststorage* | Depolama hesabınızın adı. |
    | Tüm olay türlerine abone ol | *lediğiniz* | Tüm olaylar hakkında bildirim alın. |
    | Tanımlı olay türleri | *Blob oluşturuldu* | Hangi belirli olaylara bildirim alınacak? |
    | Uç nokta türü | *Olay Hub 'ları* | Olayları göndereceğiniz uç noktanın türü. |
    | Uç nokta | *test-hub* | Oluşturduğunuz olay hub'ı. |
    | | |

1. Belirli bir kapsayıcıdan dosyaları izlemek istiyorsanız **Filtreler** sekmesini seçin. Bildirimlerin filtrelerini aşağıdaki gibi ayarlayın:
    * **Konu, alan Ile başlar** blob kapsayıcısının *değişmez* ön ekidir. Uygulanan model *StartsWith ile birlikte*birden çok kapsayıcıyı kapsayabilir. Joker karakterlere izin verilmez.
     Şu *şekilde ayarlanmalıdır* : *`/blobServices/default/containers/`* [kapsayıcı ön eki]
    * Alanla **Ilgili Konu** , blob 'un *sabit değer* sonekidir. Joker karakterlere izin verilmez.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Azure Veri Gezgini'nde hedef tablo oluşturma

Azure Veri Gezgini, Event Hubs veri göndereceği bir tablo oluşturun. Kümede ve ön koşullarda hazırlanan veritabanında tablo oluşturun.

1. Azure portalda kümenizin altında **Sorgu**'yu seçin.

    ![Sorgu uygulama bağlantısı](media/ingest-data-event-grid/query-explorer-link.png)

1. Aşağıdaki komutu pencereye kopyalayın ve alınan verileri alacak tabloyu (TestTable) oluşturmak için **Çalıştır** ' ı seçin.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Oluşturma sorgusunu çalıştırma](media/ingest-data-event-grid/run-create-table.png)

1. Aşağıdaki komutu pencereye kopyalayın ve gelen JSON verilerini tablonun sütun adlarıyla ve veri türleriyle (TestTable) eşlemek için **Çalıştır** ' ı seçin.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Azure Veri Gezgini Event Grid veri bağlantısı oluşturma

Bundan sonra blob kapsayıcısına akan verilerin test tablosuna akışı için Azure Veri Gezgini Event Grid bağlayın. 

1. Olay hub'ı dağıtımının başarılı olduğundan emin olmak için araç çubuğunda **Bildirimler**'i seçin.

1. Oluşturduğunuz küme altında **veritabanları** > **TestDatabase**' i seçin.

    ![Test veritabanını seçme](media/ingest-data-event-grid/select-test-database.png)

1. Veri **alımını** seçin > **veri bağlantısı ekleyin**.

    ![Veri alımı](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Bağlantı türünü seçin: **BLOB depolama**.

1. Formu aşağıdaki bilgilerle doldurun ve **Oluştur**' u seçin.

    ![Olay hub'ı bağlantısı](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Veri kaynağı:

    **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | Veri bağlantısı adı | *test-hub-connection* | Azure Veri Gezgini içinde oluşturmak istediğiniz bağlantının adı.|
    | Depolama hesabı aboneliği | Abonelik KIMLIĞINIZ | Depolama hesabınızın bulunduğu abonelik KIMLIĞI.|
    | Depolama hesabı | *gridteststorage* | Daha önce oluşturduğunuz depolama hesabının adı.|
    | Event Grid | *test-Grid-Connection* | Oluşturduğunuz olay kılavuzunun adı. |
    | Olay Hub'ı adı | *test-hub* | Oluşturduğunuz Olay Hub 'ı. Bu alan, bir olay Kılavuzu seçtiğinizde otomatik olarak doldurulur. |
    | Tüketici grubu | *test-group* | Oluşturduğunuz Olay Hub 'ında tanımlanan Tüketici grubu. |
    | | |

    Hedef tablo:

     **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | Tablo | *TestTable* | **TestDatabase** içinde oluşturduğunuz tablo. |
    | Veri biçimi | *JSON* | Desteklenen biçimler şunlardır avro, CSV, JSON, çok SATıRLı JSON, PSV, SOH, SCSV, TSV ve TXT. Desteklenen sıkıştırma seçenekleri: zip ve GZip |
    | Sütun eşleme | *TestMapping* | **TestDatabase** içinde oluşturduğunuz ve gelen JSON verilerini **TestTable** tablosunun sütun adları ve veri türleriyle eşleyen eşleme.|
    | | |
    
## <a name="generate-sample-data"></a>Örnek veri oluşturma

Artık Azure Veri Gezgini ve depolama hesabı bağlı olduğundan, örnek veri oluşturabilir ve BLOB depolamaya yükleyebilirsiniz.

Azure depolama kaynaklarıyla etkileşimde bulunmak için birkaç temel Azure CLı komutu veren küçük bir kabuk betiği ile çalışacağız. Bu betik depolama hesabınızda yeni bir kapsayıcı oluşturur, var olan bir dosyayı (blob olarak) bu kapsayıcıya yükler ve ardından kapsayıcıdaki Blobları listeler. Betiği doğrudan portalda yürütmek için [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) kullanabilirsiniz.

Verileri bir dosyaya kaydedin ve bu komut dosyasıyla karşıya yükleyin:

```Json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```bash
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

## <a name="review-the-data-flow"></a>Veri akışını inceleme

> [!NOTE]
> Azure Veri Gezgini, alım işlemini iyileştirmek için tasarlanan veri alımı için toplama (toplu işlem) ilkesine sahiptir.
Varsayılan olarak, ilke 5 dakikaya göre yapılandırılır.
Gerekirse ilkeyi daha sonra değiştirebilirsiniz. Bu makalede birkaç dakikalık bir gecikme süresi bekleyebilir.

1. Azure portal, olay kılavuzunuzun altında, uygulama çalışırken ani etkinliği görürsünüz.

    ![Olay Kılavuzu grafiği](media/ingest-data-event-grid/event-grid-graph.png)

1. Veritabanına ulaşan ileti sayısını denetlemek için test veritabanınızda aşağıdaki sorguyu çalıştırın.

    ```Kusto
    TestTable
    | count
    ```

1. İletilerin içeriğini görmek için test veritabanınızda aşağıdaki sorguyu çalıştırın.

    ```Kusto
    TestTable
    ```

    Sonuç kümesi aşağıdakine benzer olmalıdır.

    ![İleti sonuç kümesi](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Olay kılavuzunuzun yeniden kullanılmasını planlamıyorsanız, maliyetleri ortadan kaldırmak için **Test-Hub-RG**'yi temizleyin.

1. Azure portalında, en solda bulunan **Kaynak grupları**’nı ve ardından oluşturduğunuz kaynak grubunu seçin.  

    Soldaki menü daraltılmışsa, genişletmek için ![Genişletme düğmesi](media/ingest-data-event-grid/expand.png) öğesine tıklayın.

   ![Silinecek kaynak grubunu seçin](media/ingest-data-event-grid/delete-resources-select.png)

1. **test-resource-group** altında **Kaynak grubunu sil**'i seçin.

1. Yeni pencerede, silinecek kaynak grubunun adını girin (*Test-Hub-RG*) ve ardından **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Veri Gezgini 'de verileri sorgulama](web-query-data.md)
