---
title: Azure Data Factory Azure Veri Gezgini denetim komutlarını kullanma
description: Bu konuda, Azure Data Factory içindeki Azure Veri Gezgini denetim komutlarını kullanın
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264496"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Azure Veri Gezgini denetim komutlarını çalıştırmak için Azure Data Factory komut etkinliğini kullanma

[Azure Data Factory](/azure/data-factory/) (ADF), veriler üzerinde etkinliklerin birleşimini gerçekleştirmenize olanak tanıyan bulut tabanlı bir veri tümleştirme hizmetidir. Veri taşıma ve veri dönüştürmeyi düzenlemek ve otomatikleştirmek için veri odaklı iş akışları oluşturmak üzere ADF 'yi kullanın. Azure Data Factory **azure Veri Gezgini komut** etkinliği, bir ADF iş akışı içinde [Azure Veri Gezgini denetim komutlarını](/azure/kusto/concepts/#control-commands) çalıştırmanıza olanak sağlar. Bu makalede, bir arama etkinliği ve bir Azure Veri Gezgini komut etkinliği içeren ForEach etkinliği ile işlem hattı oluşturma öğretilir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* [Azure Veri Gezgini kümesi ve veritabanı](create-cluster-database-portal.md)
* Veri kaynağı.
* [Veri Fabrikası](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Yeni işlem hattı oluşturma

1. **Yazar** Kurşun Kalem aracını seçin. 
1. **@No__t-1** ' i seçerek yeni bir işlem hattı oluşturun ve ardından açılır listeden işlem **hattı** ' nı seçin.

   ![Yeni işlem hattı oluştur](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Arama etkinliği oluşturma

Bir [arama etkinliği](/azure/data-factory/control-flow-lookup-activity) , Azure Data Factory tarafından desteklenen herhangi bir veri kaynağından bir veri kümesi alabilir. Arama etkinliğinin çıktısı bir ForEach veya diğer bir etkinlikte kullanılabilir.

1. **Etkinlikler** bölmesinde, **genel**altında, **arama** etkinliğini seçin. Sağ taraftaki ana tuvale sürükleyip bırakın.
 
    ![arama etkinliği Seç](media/data-factory-command-activity/select-activity.png)

1. Tuval şimdi oluşturduğunuz arama etkinliğini içerir. İlgili parametreleri değiştirmek için tuvalin altındaki sekmeleri kullanın. **Genel**olarak, etkinliği yeniden adlandırın. 

    ![Arama etkinliğini Düzenle](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Ardışık düzen özelliklerini görüntülemek için boş tuval alanına tıklayın. İşlem hattını yeniden adlandırmak için **genel** sekmesini kullanın. İşlem hatmız, *ardışık düzen-4-docs*olarak adlandırılır.

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Arama etkinliğinde Azure Veri Gezgini veri kümesi oluşturma

1. **Ayarlar**' da, önceden oluşturulmuş Azure Veri Gezgini **kaynak veri kümenizi**seçin veya yeni bir veri kümesi oluşturmak için **+ Yeni** ' yi seçin.
 
    ![Arama ayarlarına veri kümesi Ekle](media/data-factory-command-activity/lookup-settings.png)

1. **Yeni veri kümesi** penceresinden **Azure Veri Gezgini (kusto)** veri kümesini seçin. Yeni veri kümesini eklemek için **devam** ' ı seçin.

   ![Yeni veri kümesi seçin](media/data-factory-command-activity/select-new-dataset.png) 

1. Yeni Azure Veri Gezgini veri kümesi parametreleri **Ayarlar**' da görünür. Parametreleri güncelleştirmek için **Düzenle**' yi seçin.

    ![Azure Veri Gezgini veri kümesiyle arama ayarları](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. **AzureDataExplorerTable** yeni sekmesi ana tuvalde açılır. 
    * **Genel** ' i seçin ve veri kümesi adını düzenleyin. 
    * Veri kümesi özelliklerini düzenlemek için **bağlantı** ' yı seçin. 
    * Açılan listeden **bağlı hizmeti** seçin veya yeni bir bağlı hizmet oluşturmak Için **+ Yeni** ' yi seçin.

    ![Azure Veri Gezgini veri kümesi özelliklerini düzenleme](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Yeni bir bağlı hizmet oluştururken **yeni bağlı hizmet (Azure Veri Gezgini)** sayfası açılır:

    ![ADX yeni bağlı hizmet](media/data-factory-command-activity/adx-new-linked-service.png)

   * Azure Veri Gezgini bağlı hizmeti için **ad** ' ı seçin. Gerekirse **Açıklama** ekleyin.
   * **Tümleştirme çalışma zamanı aracılığıyla Bağlan**bölümünde, gerekirse geçerli ayarları değiştirin. 
   * **Hesap seçim yönteminde** , iki yöntemden birini kullanarak kümenizi seçin: 
        * **Azure aboneliği** radyo düğmesini seçin ve **Azure abonelik** hesabınızı seçin. Sonra, **kümenizi**seçin. Açılan listede yalnızca kullanıcıya ait kümeler listelenir.
        * Bunun yerine **el ile** radyo düğmesini girin ' i seçin ve **uç** NOKTANıZA (küme URL 'si) girin.
    * **Kiracıyı**belirtin.
    * **Hizmet sorumlusu kimliğini**girin. Asıl KIMLIK, kullanılan komutun gerektirdiği izin düzeyine göre yeterli izinlere sahip olmalıdır.
    * **Hizmet sorumlusu anahtar** düğmesini seçin ve **hizmet sorumlusu anahtarını**girin.
    * Açılır menüden **veritabanınızı** seçin. Alternatif olarak, **Düzenle** onay kutusunu seçin ve veritabanınızın adını girin.
    * Oluşturduğunuz bağlı hizmet bağlantısını test etmek için **Bağlantıyı Sına** ' yı seçin. Kuruluma bağlanıyorsanız, bir yeşil onay işareti **bağlantısı başarılı** olur.
    * Bağlı hizmet oluşturmayı tamamladıktan sonra **son** ' u seçin.

1. Bağlı bir hizmet ayarladıktan sonra, **AzureDataExplorerTable** > **bağlantısı**' nda **tablo** adı ekleyin. Verilerin düzgün şekilde sunulduğundan emin olmak için **Verileri Önizle**' yi seçin.

   Veri kümeniz artık hazır ve işlem hattınızı düzenlemeyle devam edebilirsiniz.

### <a name="add-a-query-to-your-lookup-activity"></a>Arama etkinliğinizi bir sorgu ekleme

1. İşlem **hattı-4-docs** > **ayarları** **sorgu** metin kutusuna bir sorgu ekleyin, örneğin:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Gerektiğinde **sorgu zaman aşımını** veya **kesme** ve **yalnızca ilk satır** özelliklerini değiştirin. Bu akışta varsayılan **sorgu zaman aşımını** tutar ve onay kutularının işaretini kaldırın. 

    ![Arama etkinliğinin son ayarları](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Her etkinlik Için bir oluşturma 

[For-each](/azure/data-factory/control-flow-for-each-activity) etkinliği bir koleksiyon üzerinde yinelemek ve belirtilen etkinlikleri bir döngüde yürütmek için kullanılır. 

1. Artık işlem hattına bir for-each etkinliği eklersiniz. Bu etkinlik, arama etkinliğinden döndürülen verileri işleyecek. 
    * **Etkinlikler** bölmesinde, **yineleme & conditionals**altında, **foreach** etkinliğini seçin ve sürükleyip tuvale bırakın.
    * Arama etkinliğinin çıkışı ile, bunları bağlamak için tuvaldeki ForEach etkinliğinin girişi arasına bir çizgi çizin.

        ![ForEach etkinliği](media/data-factory-command-activity/for-each-activity.png)

1.  Tuvalde ForEach etkinliğini seçin. Aşağıdaki **Ayarlar** sekmesinde:
    * Arama sonuçlarının sıralı bir işlem için **sıralı** onay kutusunu işaretleyin veya paralel işleme oluşturmak için işaretini işaretsiz bırakın.
    * **Toplu iş sayısını**ayarlayın.
    * **Öğeler**' de çıkış değerine şu başvuruyu sağlayın: *@activity (' Lookup1 '). Output. Value*

       ![ForEach etkinliği ayarları](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>ForEach etkinliği içinde Azure Veri Gezgini komut etkinliği oluşturma

1. ForEach içindeki etkinlikleri belirtmek için tuvaldeki ForEach etkinliğine çift tıklayın ve yeni bir tuvalde açın.
1. **Etkinlikler** bölmesinde, **Azure Veri Gezgini**altında **Azure Veri Gezgini komut** etkinliğini seçin ve sürükleyip tuvale bırakın.

    ![Azure Veri Gezgini komut etkinliği](media/data-factory-command-activity/adx-command-activity.png)

1.  **Bağlantı** sekmesinde, daha önce oluşturulmuş bağlı hizmeti seçin.

    ![Azure Data Explorer komut etkinlik bağlantısı sekmesi](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. **Komut** sekmesinde, aşağıdaki komutu sağlayın:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    **Komut** , Azure Veri Gezgini, belirli bir sorgunun sonuçlarını bir BLOB depolama alanına sıkıştırılmış bir biçimde dışarı aktarmak için yönlendirir. Zaman uyumsuz olarak çalışır (zaman uyumsuz değiştirici kullanılarak).
    Sorgu, arama etkinlik sonucunda her bir satırın veritabanı sütununu ele alınmaktadır. **Komut zaman aşımı** değişmeden bırakılabilir.

    ![komut etkinliği](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > Komut etkinliği aşağıdaki sınırlara sahiptir:
    > * Boyut sınırı: 1 MB yanıt boyutu
    > * Süre sınırı: 20 dakika (varsayılan), 1 saat (en fazla).
    > * Gerekirse, sonuç boyutunu/süreyi azaltmak için [Adminthenquery](/azure/kusto/management/index#combining-queries-and-control-commands)kullanarak sonuca bir sorgu ekleyebilirsiniz.

1.  Artık işlem hattı hazır. Ardışık düzen adına tıklayarak ana ardışık düzen görünümüne dönebilirsiniz.

    ![Azure Veri Gezgini komut işlem hattı](media/data-factory-command-activity/adx-command-pipeline.png)

1. İşlem hattını yayımlamadan önce **Hata Ayıkla** ' yı seçin. Ardışık düzen ilerlemesi, **Çıkış** sekmesinde izlenebilir.

    ![Azure Data Explorer komut etkinliği çıkışı](media/data-factory-command-activity/command-activity-output.png)

1. İşlem hattını çalıştırmak için **Tümünü yayımlayabilir** ve sonra **tetikleyici ekleyebilirsiniz** . 

## <a name="control-command-outputs"></a>Komut çıktılarını denetleme

Komut etkinlik çıktısının yapısı aşağıda ayrıntılı olarak verilmiştir. Bu çıktı, ardışık düzendeki bir sonraki etkinlik tarafından kullanılabilir.

### <a name="returned-value-of-a-non-async-control-command"></a>Async olmayan bir denetim komutunun değeri döndürüldü

Async olmayan bir denetim komutunda, döndürülen değerin yapısı, arama etkinliği sonucunun yapısına benzerdir. @No__t-0 alanı döndürülen kayıt sayısını gösterir. @No__t-0 sabit dizi alanı bir kayıt listesi içerir. 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>Zaman uyumsuz denetim komutunun değeri döndürüldü

Zaman uyumsuz denetim komutunda, etkinlik zaman uyumsuz işlem tamamlanana veya zaman aşımına uğrayana kadar, arka planda işlemler tablosunu yoklar. Bu nedenle, döndürülen değer söz konusu **operationId** özelliği için `.show operations OperationId` sonucunu içerecektir. İşlemin başarıyla tamamlandığını doğrulamak için **durum** ve **durum** özelliklerinin değerlerini denetleyin.

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Factory kullanarak Azure Veri Gezgini veri kopyalama](data-factory-load-data.md)hakkında bilgi edinin.
* [Veritabanından Azure Veri Gezgini toplu kopyalama için Azure Data Factory şablonu](data-factory-template.md)kullanma hakkında bilgi edinin.
