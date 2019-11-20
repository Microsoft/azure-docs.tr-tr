---
title: Azure Resource Manager ile Azure Databricks çalışma alanı oluşturma
description: Bu hızlı başlangıçta, Azure Resource Manager şablonunu kullanarak bir Azure Databricks çalışma alanı oluşturma, ardından bir Apache Spark kümesi oluşturma ve bir Spark işi çalıştırma işlemi gösterilmektedir.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/12/2019
ms.openlocfilehash: 83dc18424bc8606eff9a735b76cdaa09e381d588
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129361"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-resource-manager-template"></a>Hızlı Başlangıç: Azure Resource Manager şablonunu kullanarak Azure Databricks’te Spark işini çalıştırma

Bu hızlı başlangıçta, bir Apache Spark kümesiyle Azure Databricks çalışma alanı oluşturmak için bir Azure Resource Manager şablonu kullanırsınız. Küme üzerinde bir işi çalıştırır ve demografik tabanlı ücretsiz/ücretli kullanımdan gerçek zamanlı raporlar oluşturmak için özel grafikleri kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com) oturum açın. 

> [!Note]
> Bu öğretici **Azure Ücretsiz deneme aboneliği**kullanılarak gerçekleştirilemez.
> Ücretsiz hesabınız varsa, profilinize gidin ve aboneliğinizi **Kullandıkça Öde**ile değiştirin. Daha fazla bilgi için bkz. [Ücretsiz Azure hesabı](https://azure.microsoft.com/free/). Ardından, [harcama limitini kaldırın](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)ve bölgenizdeki vCPU 'lar için [bir kota artışı isteyin](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) . Azure Databricks çalışma alanınızı oluşturduğunuzda, çalışma alanına 14 gün boyunca ücretsiz Premium Azure Databricks DBUs erişimi sağlamak için **deneme (Premium-14 gün ücretsiz DBUs)** fiyatlandırma katmanını seçebilirsiniz.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks çalışma alanı oluşturma

Bu bölümde, Azure Resource Manager şablonunu kullanarak bir Azure Databricks çalışma alanı oluşturursunuz.

1. Azure Portal'da bir şablonu açmak için aşağıdaki görüntüye tıklayın.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-databricks-workspace%2Fazuredeploy.json" target="_blank"><img src="./media/quickstart-create-databricks-workspace-resource-manager-template/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Azure Databricks çalışma alanınızı oluşturmak için gerekli değerleri sağlayın

   ![Azure Resource Manager şablonu kullanarak Azure Databricks çalışma alanı oluşturma](./media/quickstart-create-databricks-workspace-resource-manager-template/create-databricks-workspace-using-resource-manager-template.png "Azure Resource Manager şablonu kullanarak Azure Databricks çalışma alanı oluşturma")

   Aşağıdaki değerleri sağlayın:

   |Özellik  |Açıklama  |
   |---------|---------|
   |**Abonelik**     | Açılan listeden Azure aboneliğinizi seçin.        |
   |**Kaynak grubu**     | Yeni bir kaynak grubu oluşturmayı veya mevcut bir kaynak grubunu kullanmayı seçin. Kaynak grubu, bir Azure çözümü için ilgili kaynakları bir arada tutan kapsayıcıdır. Daha fazla bilgi için bkz. [Azure Kaynak Grubuna genel bakış](../azure-resource-manager/resource-group-overview.md). |
   |**Konum**     | **Doğu ABD 2**’yi seçin. Kullanılabilir diğer bölgeler için bkz. [Bölgeye göre kullanılabilir Azure hizmetleri](https://azure.microsoft.com/regions/services/).        |
   |**Çalışma alanı adı**     | Databricks çalışma alanınız için bir ad sağlayın        |
   |**Fiyatlandırma Katmanı**     |  **Standart** veya **Premium** arasında seçim yapın. Bu katmanlar hakkında daha fazla bilgi için bkz. [Databricks fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/databricks/).       |

3. **Yukarıdaki hüküm ve koşulları kabul ediyorum**'u ve ardından **Panoya sabitle**'yi seçip **Satın al**'a tıklayın.

4. Çalışma alanının oluşturulması birkaç dakika sürer. Çalışma alanı oluşturma sırasında portal sağ tarafta **Azure Databricks için dağıtım gönderiliyor** kutucuğunu gösterir. Kutucuğu görmek için panonuzu sağa kaydırmanız gerekebilir. Ayrıca, ekranın üst kısmında gösterilen bir ilerleme çubuğu vardır. İlerleme durumu için her iki alanı da izleyebilirsiniz.

   ![Databricks dağıtım kutucuğu](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-deployment-tile.png "Databricks dağıtım kutucuğu")

## <a name="create-a-spark-cluster-in-databricks"></a>Databricks’te Spark kümesi oluşturma

1. Azure portalında, oluşturduğunuz Databricks çalışma alanına gidin ve sonra **Çalışma Alanını Başlat**’a tıklayın.

2. Azure Databricks portalına yönlendirilirsiniz. Portaldan **Küme**’ye tıklayın.

   ![Azure 'da databricks](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-on-azure.png "Azure 'da databricks")

3. **Yeni küme** sayfasında, bir küme oluşturmak için değerleri girin.

   ![Azure 'da Databricks Spark kümesi oluşturma](./media/quickstart-create-databricks-workspace-resource-manager-template/create-databricks-spark-cluster.png "Azure 'da Databricks Spark kümesi oluşturma")

   Aşağıdakiler dışında diğer tüm varsayılan değerleri kabul edin:

   * Küme için bir ad girin.
   * Bu makale için **4.0** çalışma zamanıyla bir küme oluşturun.
   * **\_\_ dakika işlem yapılmadığında sonlandır** onay kutusunu seçtiğinizden emin olun. Küme kullanılmazsa kümenin sonlandırılması için biz süre (dakika cinsinden) belirtin.

   **Küme oluştur**’u seçin. Küme çalışmaya başladıktan sonra kümeye not defterleri ekleyebilir ve Spark işleri çalıştırabilirsiniz.

Küme oluşturma hakkında daha fazla bilgi için bkz. [Azure Databricks üzerinde Spark kümesi oluşturma](/azure/databricks/clusters/create).

## <a name="run-a-spark-sql-job"></a>Spark SQL işi çalıştırma

Bu bölüme başlamadan önce aşağıdaki önkoşulları tamamlamanız gerekir:

* [Azure Blob depolama hesabı oluşturma](../storage/common/storage-quickstart-create-account.md).
* [GitHub 'dan](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json)örnek bir JSON dosyası indirin.
* Örnek JSON dosyasını, oluşturduğunuz Azure Blob depolama hesabına yükleyin. Dosyaları karşıya yüklemek için [Microsoft Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md)’ni kullanabilirsiniz.

Databricks içinde bir not defteri oluşturmak, not defterini bir Azure Blob depolama hesabındaki verileri okuyacak şekilde yapılandırmak ve sonra veriler üzerinde bir Spark SQL işi çalıştırmak için aşağıdaki görevleri gerçekleştirin.

1. Sol bölmedeki **Çalışma Alanı**'na tıklayın. **Çalışma Alanı** açılır listesinden **Oluştur**’a ve sonra **Not Defteri**’ne tıklayın.

   ![Databricks 'te Not defteri oluşturma](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-create-notebook.png "Databricks 'te Not defteri oluşturma")

2. **Not Defteri Oluşturma** iletişim kutusuna bir ad girin, dil olarak **Scala**’yı seçin ve daha önce oluşturduğunuz Spark kümesini seçin.

   ![Databricks 'te Not defteri oluşturma](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-notebook-details.png "Databricks 'te Not defteri oluşturma")

   **Oluştur**'a tıklayın.

3. Bu adımda Azure Depolama hesabını Databricks Spark kümesiyle ilişkilendirin. Bu ilişkilendirmeyi gerçekleştirmenin iki yolu vardır. Azure Depolama hesabını Databricks Dosya Sistemine (DBFS) bağlayabilir veya Azure Depolama hesabına doğrudan oluşturduğunuz uygulamadan erişebilirsiniz.

   > [!IMPORTANT]
   >Bu makalede **depolamayı DBFS'ye bağlama** yaklaşımı kullanılır. Bu yaklaşım, bağlı deponun küme dosya sistemiyle ilişkilendirilmesini sağlar. Bu sayede kümeye erişen tüm uygulamalar ilişkilendirilmiş depolamayı da kullanabilir. Doğrudan erişim yaklaşımı, erişimi yapılandırdığınız uygulamayla kısıtlıdır.
   >
   > Bağlama yaklaşımını kullanmak için bu makalede seçtiğiniz Databricks çalışma zamanı sürüm **4.0** ile bir Spark kümesi oluşturmanız gerekir.

   Aşağıdaki kod parçacığında `{YOUR CONTAINER NAME}`, `{YOUR STORAGE ACCOUNT NAME}` ve`{YOUR STORAGE ACCOUNT ACCESS KEY}` değerlerini Azure Depolama hesabınız için uygun değerlerle değiştirin. Kod parçacığını not defterindeki boş bir hücreye yapıştırın ve sonra kod hücresini çalıştırmak için SHIFT + ENTER tuşlarına basın.

   * **Depolama hesabını DBFS'ye bağlayın (önerilen)** . Kod parçacığında Azure Depolama hesabı yolu `/mnt/mypath` yoluna bağlanır. Böylece ilerde Azure Depolama hesabına eriştiğiniz her durumda tam yolu vermenize gerek kalmaz. Yalnızca `/mnt/mypath` yolunu kullanabilirsiniz.

          dbutils.fs.mount(
            source = "wasbs://{YOUR CONTAINER NAME}@{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net/",
            mountPoint = "/mnt/mypath",
            extraConfigs = Map("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net" -> "{YOUR STORAGE ACCOUNT ACCESS KEY}"))

   * **Depolama hesabına doğrudan erişme**

          spark.conf.set("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net", "{YOUR STORAGE ACCOUNT ACCESS KEY}")

     Depolama hesabı anahtarınızı alma yönergeleri için bkz. [Depolama erişim anahtarlarınızı yönetme](../storage/common/storage-account-manage.md#access-keys).

   > [!NOTE]
   > Azure Data Lake Store’u Azure Databricks üzerine bir Spark kümesi ile de kullanabilirsiniz. Yönergeler için bkz. [Data Lake Store’u Azure Databricks ile Kullanma](/azure/databricks/data/data-sources/azure/azure-datalake-gen2).

4. **small_radio_json.json** adlı örnek JSON veri dosyasındaki verileri kullanarak geçici tablo oluşturmak için bir SQL deyimi çalıştırın. Aşağıdaki kod parçacığında yer tutucu değerlerini kapsayıcınızın adı ve depolama hesabı adı ile değiştirin. Kod parçacığını not defterindeki bir kod hücresine yapıştırın ve sonra SHIFT + ENTER tuşlarına basın. Kod parçacığında `path` değeri, Azure Depolama hesabınıza yüklediğiniz örnek JSON dosyasının konumunu gösterir.

   ```sql
   %sql
   DROP TABLE IF EXISTS radio_sample_data;
   CREATE TABLE radio_sample_data
   USING json
   OPTIONS (
    path "/mnt/mypath/small_radio_json.json"
   )
   ```

   Komut başarıyla tamamlandıktan sonra, JSON dosyasındaki tüm verileri Databricks kümesinde tablo olarak görüntüleyebilirsiniz.

   `%sql` dili sihirli komutu, not defteri başka bir türde olsa bile not defterinden bir SQL kodu çalıştırmanızı sağlar. Daha fazla bilgi için bkz. [Bir not defterinde dilleri karıştırma](/azure/databricks/notebooks/index).

5. Çalıştırdığınız sorguyu daha iyi anlamak için örnek JSON verilerinin bir anlık görüntüsüne bakalım. Kod hücresine aşağıdaki kod parçacığını yapıştırın ve **SHIFT + ENTER** tuşuna basın.

   ```sql
   %sql
   SELECT * from radio_sample_data
   ```

6. Aşağıdaki ekran görüntüsünde gösterildiği gibi bir tablo çıktısı görürsünüz (yalnızca bazı sütunlar gösterilmiştir):

   ![Örnek JSON verileri](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-sample-csv-data.png "Örnek JSON verileri")

   Diğer ayrıntıların yanı sıra, örnek veriler bir radyo kanalının (sütun adı, **cinsiyet**) hedef kitlesinin cinsiyetini ve aboneliğin ücretsiz mi yoksa ücretli mi (sütun adı, **düzey**) olduğunu yakalar.

7. Bu durumda, bu verilerin her bir cinsiyet, ücretsiz hesaba sahip kullanıcı sayısı ve ücretli hesabı olan abone sayısı için gösterilecek görsel bir açıklamasını oluşturursunuz. Tablo çıktısının alt kısmında bulunan **Çubuk grafik** simgesine ve sonra **Çizim Seçenekleri**’ne tıklayın.

   ![Çubuk grafik oluştur](./media/quickstart-create-databricks-workspace-resource-manager-template/create-plots-databricks-notebook.png "Çubuk grafik oluştur")

8. **Çizimi Özelleştir** menüsünde, değerleri ekran görüntüsünde gösterilen şekilde sürükleyip bırakın.

   ![Çubuk grafiği Özelleştir](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-notebook-customize-plot.png "Çubuk grafiği Özelleştir")

   * **Anahtarlar**’ı **cinsiyet** olarak ayarlayın.
   * **Seri gruplandırmalar**’ı **düzey** olarak ayarlayın.
   * **Değerler**’ı **düzey** olarak ayarlayın.
   * **Toplama**’yı **SAYI** olarak ayarlayın.

   **Uygula**'ya tıklayın.

9. Çıktı aşağıdaki ekran görüntüsünde gösterildiği gibi görsel açıklamayı gösterir:

   ![Çubuk grafiği Özelleştir](./media/quickstart-create-databricks-workspace-resource-manager-template/databricks-sql-query-output-bar-chart.png "Çubuk grafiği Özelleştir")

## <a name="clean-up-resources"></a>Kaynakları temizleme

Makaleyi tamamladıktan sonra kümeyi sonlandırabilirsiniz. Bunu yapmak için Azure Databricks çalışma alanında sol bölmedeki **Kümeler**’i seçin. Sonlandırmak istediğiniz küme için imleci **Eylemler** sütunu altındaki üç noktanın üzerine taşıyın ve **Sonlandır** simgesini seçin.

![Databricks kümesini durdurma](./media/quickstart-create-databricks-workspace-resource-manager-template/terminate-databricks-cluster.png "Databricks kümesini durdurma")

El ile otomatik olarak durdurur küme sonlandırmazsanız, seçtiğiniz sağlanan **sonra Sonlandır \_ \_ yapılmadan geçecek dakika cinsinden** küme oluşturulurken onay kutusu. Böyle bir durumda, belirtilen süre boyunca etkin olmaması durumunda küme otomatik olarak durdurulur.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Databricks’te bir Spark kümesi oluşturdunuz ve Azure depolama alanındaki verileri kullanarak bir Spark işi çalıştırdınız. Diğer veri kaynaklarından Azure Databricks’e verileri aktarma hakkında bilgi almak için [Spark veri kaynakları](/azure/databricks/data/data-sources/index) bölümüne de bakabilirsiniz. [Özel VNET adresi ile Azure Databricks çalışma alanı oluşturmak](https://github.com/Azure/azure-quickstart-templates/tree/master/101-databricks-workspace-with-custom-vnet-address) için Resource Manager şablonuna da bakabilirsiniz. Bir şablonda kullanılacak JSON sözdizimi ve özellikler için bkz. [Microsoft. Databricks/Workspaces](/azure/templates/microsoft.databricks/workspaces) şablon başvurusu.

Azure Databricks kullanılarak bir ETL işleminin (verileri ayıklama, dönüştürme ve yükleme) nasıl gerçekleştirileceğini öğrenmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Azure Databricks kullanarak verileri ayıklama, dönüştürme ve yükleme](databricks-extract-load-sql-data-warehouse.md)
