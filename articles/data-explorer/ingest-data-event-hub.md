---
title: Olay Hub 'ından Azure Veri Gezgini veri alma
description: Bu makalede, Olay Hub 'ından Azure Veri Gezgini veri alma (yükleme) hakkında bilgi edineceksiniz.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 102cfa81c6093ff1aeefdd8d1937143a25cf76f5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028482"
---
# <a name="ingest-data-from-event-hub-into-azure-data-explorer"></a>Olay Hub 'ından Azure Veri Gezgini veri alma

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)

Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve üst düzeyde ölçeklenebilir veri keşfetme hizmetidir. Azure Veri Gezgini, büyük veri akış platformu ve olay ekleme hizmeti olan Event Hubs'dan veri eklemeyi (veri yüklemeyi) destekler. [Event Hubs](/azure/event-hubs/event-hubs-about) , yaklaşık gerçek zamanlı olarak saniyede milyonlarca olayı işleyebilir. Bu makalede, bir olay hub 'ı oluşturur, Azure Veri Gezgini 'a bağlanırsınız ve sistem aracılığıyla veri akışını görürsünüz.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

* [Bir test kümesi ve veritabanı](create-cluster-database-portal.md).

* Veri üreten ve onu bir olay hub 'ına Gönderen [örnek bir uygulama](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) . Örnek uygulamayı sisteminize indirin.

* Örnek uygulamayı çalıştırmak için [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) .

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure Portal](https://portal.azure.com/)’ında oturum açın.

## <a name="create-an-event-hub"></a>Olay hub’ı oluşturma

Bu makalede, örnek veri oluşturur ve bir olay hub 'ına gönderebilirsiniz. İlk adım bir olay hub'ı oluşturmaktır. Bunun için Azure portalda bir Azure Resource Manager şablonu kullanacaksınız.

1. Bir olay hub 'ı oluşturmak için, dağıtımı başlatmak üzere aşağıdaki düğmeyi kullanın. Sağ tıklayıp **Yeni pencerede aç**' ı seçerek bu makaledeki adımların geri kalanını izleyebilirsiniz.

    [![Azure’a dağıtma](media/ingest-data-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    **Azure'a dağıtma** düğmesi Azure portalda doldurmanız gereken bir form sayfasını açar.

    ![Azure'a Dağıt](media/ingest-data-event-hub/deploy-to-azure.png)

1. Olay hub'ının oluşturulmasını istediğiniz aboneliği seçin ve *test-hub-rg* adlı bir kaynak grubu oluşturun.

    ![Kaynak grubu oluşturma](media/ingest-data-event-hub/create-resource-group.png)

1. Formu aşağıdaki bilgilerle doldurun.

    ![Dağıtım formu](media/ingest-data-event-hub/deployment-form.png)

    Aşağıdaki tabloda yer almayan ayarlar için varsayılan değerleri kullanın.

    **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | Abonelik | Aboneliğiniz | Olay hub'ınız için kullanmak istediğiniz Azure aboneliğini seçin.|
    | Kaynak grubu | *test-hub-rg* | Yeni bir kaynak grubu oluşturun. |
    | Konum | *Batı ABD* | Bu makale için *Batı ABD* seçin. Üretim sisteminde ihtiyaçlarınıza en uygun bölgeyi seçmeniz gerekir. En iyi performansı elde etmek için kusto kümesiyle aynı konumda Olay Hub 'ı ad alanı oluşturun (yüksek aktarım hızı olan olay hub 'ı ad alanları için en önemli).
    | Ad alanı adı | Benzersiz bir ad alanı adı | Ad alanınızı tanımlayan benzersiz bir ad seçin. Örneğin, *mytestnamespace*. Girdiğiniz adın sonuna *servicebus.windows.net* etki alanı adı eklenir. Ad yalnızca küçük harf, sayı ve kısa çizgi içerebilir. Ad bir harf ile başlamalı ve harf veya sayı ile bitmelidir. Değer uzunluğu 6 ile 50 karakter arasında olmalıdır.
    | Olay hub'ı adı | *test-hub* | Olay hub'ı benzersiz bir kapsayıcı kapsamı sunan ad alanında bulunur. Olay hub'ı adının ad alanında benzersiz olması gerekir. |
    | Tüketici grubu adı | *test-group* | Tüketici grupları birden fazla tüketici uygulamasının ayrı olay akışı görünümüne sahip olmasını sağlar. |
    | | |

1. **Satın al**'ı seçerek aboneliğinizde kaynak oluşturduğunuzu onaylayın.

1. Araç çubuğunda **Bildirimler**’i seçip sağlama işlemini izleyin. Dağıtımın başarıyla tamamlanması birkaç dakika sürebilir ancak beklemeden bir sonraki adıma geçebilirsiniz.

    ![Bildirimler](media/ingest-data-event-hub/notifications.png)

## <a name="create-a-target-table-in-azure-data-explorer"></a>Azure Veri Gezgini'nde hedef tablo oluşturma

Şimdi Azure Veri Gezgini'nde Event Hubs tarafından gönderilen verilerin ekleneceği tabloyu oluşturacaksınız. Tabloyu **Önkoşullar** bölümünde sağlanan kümede ve veritabanında oluşturacaksınız.

1. Azure portalda kümenize gidip **Sorgu**'yu seçin.

    ![Sorgu uygulama bağlantısı](media/ingest-data-event-hub/query-explorer-link.png)

1. Aşağıdaki komutu pencereye kopyalayın ve alınan verileri alacak tabloyu (TestTable) oluşturmak için **Çalıştır** ' ı seçin.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Oluşturma sorgusunu çalıştırma](media/ingest-data-event-hub/run-create-query.png)

1. Aşağıdaki komutu pencereye kopyalayın ve gelen JSON verilerini tablonun sütun adlarıyla ve veri türleriyle (TestTable) eşlemek için **Çalıştır** ' ı seçin.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

## <a name="connect-to-the-event-hub"></a>Olay hub'ına bağlanma

Şimdi Azure Veri Gezgini'nden olay hub'ına bağlanabilirsiniz. Bağlantı kurulduğunda olay hub'ına giden veriler bu makalede oluşturduğunuz test tablosuna iletilir.

1. Olay hub'ı dağıtımının başarılı olduğundan emin olmak için araç çubuğunda **Bildirimler**'i seçin.

1. Oluşturduğunuz kümenin altında **Veritabanları**'nı ve ardından **TestDatabase** girişini seçin.

    ![Test veritabanını seçme](media/ingest-data-event-hub/select-test-database.png)

1. **Veri** alımı ' nı seçin ve **veri bağlantısı ekleyin**. Ardından, aşağıdaki bilgilerle formu doldurun. İşiniz bittiğinde **Oluştur** ' u seçin.

    ![Olay hub'ı bağlantısı](media/ingest-data-event-hub/event-hub-connection.png)

    Veri kaynağı:

    **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | Veri bağlantısı adı | *test-hub-connection* | Azure Veri Gezgini'nde oluşturmak istediğiniz bağlantının adı.|
    | Olay hub’ı ad alanı | Benzersiz bir ad alanı adı | Önceden seçtiğiniz ve ad alanınızı tanımlayan ad. |
    | Olay hub'ı | *test-hub* | Oluşturduğunuz olay hub'ı. |
    | Tüketici grubu | *test-group* | Oluşturduğunuz olay hub'ında tanımlanan tüketici grubu. |
    | Olay sistemi özellikleri | İlgili özellikleri seçin | [Olay Hub 'ı sistem özellikleri](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations). Olay iletisi başına birden çok kayıt varsa, sistem özellikleri ilk birine eklenir. Sistem Özellikleri eklenirken, tablo şemasını [oluşturun](/azure/kusto/management/tables#create-table) veya [güncelleştirin](/azure/kusto/management/tables#alter-table-and-alter-merge-table) ve seçili özellikleri dahil etmek için [eşleme](/azure/kusto/management/mappings) yapın. |
    | | |

    Hedef tablo:

    Alınan verileri yönlendirmeye yönelik iki seçenek vardır: *statik* ve *dinamik*. 
    Bu makalede, tablo adını, veri biçimini ve eşlemeyi belirttiğiniz statik yönlendirme kullanırsınız. Bu nedenle, **verilerim yönlendirme bilgilerini içeren verileri** işaretsiz olarak bırak.

     **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | Tablo | *TestTable* | **TestDatabase** içinde oluşturduğunuz tablo. |
    | Veri biçimi | *JSON* | Desteklenen biçimler şunlardır avro, CSV, JSON, çok SATıRLı JSON, PSV, SOHSV, SCSV, TSV, TSVE ve TXT. Desteklenen sıkıştırma seçenekleri: GZip |
    | Sütun eşleme | *TestMapping* | **TestDatabase**'te oluşturduğunuz ve gelen JSON verilerini **TestTable**'ın sütun adlarıyla ve veri türleriyle eşleyen [eşleme](/azure/kusto/management/mappings) . JSON, çok SATıRLı JSON veya AVRO için gereklidir ve diğer biçimler için isteğe bağlıdır.|
    | | |

    > [!NOTE]
    > * Verilerim ' i seçin, verilerin [örnek uygulama](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) açıklamalarında görüldüğü gibi gerekli yönlendirme bilgilerini içerdiği dinamik yönlendirmeyi kullanmak için **yönlendirme bilgilerini içerir** . Hem statik hem de dinamik özellikler ayarlandıysa, dinamik özellikler statik olanları geçersiz kılar. 
    > * Yalnızca veri bağlantısını oluşturduktan sonra sıraya alınan olaylar alınır.

## <a name="copy-the-connection-string"></a>Bağlantı dizesini kopyalayın

Önkoşullarda listelenen [örnek uygulamayı](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) çalıştırdığınızda olay hub'ı ad alanının bağlantı dizesine ihtiyacınız olacaktır.

1. Oluşturduğunuz olay hub'ı ad alanında **Paylaşılan erişim ilkeleri**'ni ve ardından **RootManageSharedAccessKey** girişini seçin.

    ![Paylaşılan erişim ilkeleri](media/ingest-data-event-hub/shared-access-policies.png)

1. **Bağlantı dizesi - birincil anahtar** değerini kopyalayın. Bir sonraki bölümde bunu yapıştıracaksınız.

    ![Bağlantı dizesi](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Örnek veri oluşturma

Veri oluşturmak için indirdiğiniz [Örnek uygulamayı](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) kullanın.

1. Örnek uygulama çözümünü Visual Studio'da açın.

1. *program.cs* dosyasında `connectionString` sabitini kopyaladığınız olay hub'ı ad alanı bağlantı dizesi ile güncelleştirin.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Uygulamayı derleyin ve çalıştırın. Uygulama olay hub'ına ileti gönderir ve o da on saniyede bir durumu yazdırır.

1. Uygulama birkaç ileti gönderdikten sonra bir sonraki adıma geçerek olay hub'ına ve test tablosuna veri akışını inceleyin.

## <a name="review-the-data-flow"></a>Veri akışını inceleme

Uygulama tarafından oluşturulan verilerin olay hub'ından kümenizdeki tabloya iletildiğini görebilirsiniz.

1. Uygulama çalışırken Azure portalda olay hub'ınızın etkinliğinin zirve yaptığını görürsünüz.

    ![Olay hub'ı grafiği](media/ingest-data-event-hub/event-hub-graph.png)

1. Veritabanına ulaşan ileti sayısını denetlemek için test veritabanınızda aşağıdaki sorguyu çalıştırın.

    ```Kusto
    TestTable
    | count
    ```

1. İletilerin içeriğini görmek için aşağıdaki sorguyu çalıştırın:

    ```Kusto
    TestTable
    ```

    Sonuç kümesi aşağıdaki gibi görünmelidir:

    ![İleti sonuç kümesi](media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    > * Azure Veri Gezgini, alım işlemini iyileştirmek için tasarlanan veri alımı için toplama (toplu işlem) ilkesine sahiptir. İlke, varsayılan olarak 5 dakika veya 500 MB veri olarak yapılandırılır, bu nedenle bir gecikmeyle karşılaşabilirsiniz. Toplama seçenekleri için bkz. [toplu işlem ilkesi](/azure/kusto/concepts/batchingpolicy) . 
    > * Olay Hub 'ı alımı, 10 saniyelik veya 1 MB Olay Hub 'ı yanıt süresini içerir. 
    > * Tablonuzu, akışı destekleyecek şekilde yapılandırın ve yanıt süresi içinde gecikme süresini kaldırın. [Akış ilkesi](/azure/kusto/concepts/streamingingestionpolicy)bölümüne bakın. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Olay hub'ınızı daha sonra kullanmayı planlamıyorsanız ek maliyet oluşmasını önlemek için **test-hub-rg** adlı kaynak grubunu kaldırın.

1. Azure portalında, en solda bulunan **Kaynak grupları**’nı ve ardından oluşturduğunuz kaynak grubunu seçin.  

    Soldaki menü daraltılmışsa, genişletmek için ![Genişletme düğmesi](media/ingest-data-event-hub/expand.png) öğesine tıklayın.

   ![Silinecek kaynak grubunu seçin](media/ingest-data-event-hub/delete-resources-select.png)

1. **test-resource-group** altında **Kaynak grubunu sil**'i seçin.

1. Yeni pencerede silinecek kaynak grubunun adını yazın (*test-hub-rg*) ve **Sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Veri Gezgini 'de verileri sorgulama](web-query-data.md)
