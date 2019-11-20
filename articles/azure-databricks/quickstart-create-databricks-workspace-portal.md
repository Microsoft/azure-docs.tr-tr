---
title: Azure portal kullanarak Azure Databricks Spark işi çalıştırma
description: Bu hızlı başlangıçta Azure portalını kullanarak bir Azure Databricks çalışma alanı, bir Apache Spark kümesi oluşturma ve bir Spark işi çalıştırma işlemi gösterilmektedir.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 004557ab3eb73f3f793b0514913260a414645d40
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129378"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Hızlı Başlangıç: Azure portalını kullanarak Databricks üzerinde bir Spark işi çalıştırma

Bu hızlı başlangıçta, bir Apache Spark kümesiyle Azure Databricks çalışma alanı oluşturmak için Azure portal kullanırsınız. Bir işi kümede çalıştırır ve Boston güvenliği verilerinden gerçek zamanlı raporlar oluşturmak için özel grafikleri kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com) oturum açın.

> [!Note]
> Bu öğretici **Azure Ücretsiz deneme aboneliği**kullanılarak gerçekleştirilemez.
> Ücretsiz hesabınız varsa, profilinize gidin ve aboneliğinizi **Kullandıkça Öde**ile değiştirin. Daha fazla bilgi için bkz. [Ücretsiz Azure hesabı](https://azure.microsoft.com/free/). Ardından, [harcama limitini kaldırın](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)ve bölgenizdeki vCPU 'lar için [bir kota artışı isteyin](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) . Azure Databricks çalışma alanınızı oluşturduğunuzda, çalışma alanına 14 gün boyunca ücretsiz Premium Azure Databricks DBUs erişimi sağlamak için **deneme (Premium-14 gün ücretsiz DBUs)** fiyatlandırma katmanını seçebilirsiniz.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks çalışma alanı oluşturma

Bu bölümde Azure portalını kullanarak bir Azure Databricks çalışma alanı oluşturursunuz.

1. Azure portalında **Kaynak oluşturun** > **Analiz** > **Azure Databricks** seçeneklerini belirleyin.

    ![Azure portal databricks](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Azure portal databricks")

2. **Azure Databricks Hizmeti** bölümünde, Databricks çalışma alanı oluşturmak için değerler sağlayın.

    ![Azure Databricks çalışma alanı oluşturma](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Azure Databricks çalışma alanı oluşturma")

    Aşağıdaki değerleri sağlayın:
    
    |Özellik  |Açıklama  |
    |---------|---------|
    |**Çalışma alanı adı**     | Databricks çalışma alanınız için bir ad sağlayın        |
    |**Abonelik**     | Açılan listeden Azure aboneliğinizi seçin.        |
    |**Kaynak grubu**     | Yeni bir kaynak grubu oluşturmayı veya mevcut bir kaynak grubunu kullanmayı seçin. Kaynak grubu, bir Azure çözümü için ilgili kaynakları bir arada tutan kapsayıcıdır. Daha fazla bilgi için bkz. [Azure Kaynak Grubuna genel bakış](../azure-resource-manager/resource-group-overview.md). |
    |**Konum**     | **Batı ABD 2**'yi seçin. Kullanılabilir diğer bölgeler için bkz. [Bölgeye göre kullanılabilir Azure hizmetleri](https://azure.microsoft.com/regions/services/).        |
    |**Fiyatlandırma Katmanı**     |  **Standart**, **Premium**veya **deneme**arasında seçim yapın. Bu katmanlar hakkında daha fazla bilgi için bkz. [Databricks fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/databricks/).       |
    |**Sanal Ağ**     |  Bir Azure Databricks çalışma alanını kendi sanal ağınıza (VNet) dağıtmayı seçin. Daha fazla bilgi için bkz. [Azure sanal ağınızda Azure Databricks dağıtma (VNet ekleme)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).        |

    **Oluştur**'u seçin.

4. Çalışma alanının oluşturulması birkaç dakika sürer. Çalışma alanı oluşturma sırasında, **Bildirimler**' de dağıtım durumunu görüntüleyebilirsiniz.

    ![Databricks dağıtım kutucuğu](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Databricks dağıtım kutucuğu")

## <a name="create-a-spark-cluster-in-databricks"></a>Databricks’te Spark kümesi oluşturma

> [!NOTE]
> Azure Databricks kümesini oluşturmak için ücretsiz hesap oluşturmak istiyorsanız kümeyi oluşturmadan önce profilinize gidin ve aboneliğini **kullandıkça öde** modeline geçirin. Daha fazla bilgi için bkz. [Ücretsiz Azure hesabı](https://azure.microsoft.com/free/).

1. Azure portalında, oluşturduğunuz Databricks çalışma alanına gidin ve sonra **Çalışma Alanını Başlat**’a tıklayın.

2. Azure Databricks portalına yönlendirilirsiniz. Portalda **Yeni küme**' ya tıklayın.

    ![Azure 'da databricks](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Azure 'da databricks")

3. **Yeni küme** sayfasında, bir küme oluşturmak için değerleri girin.

    ![Azure 'da Databricks Spark kümesi oluşturma](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Azure 'da Databricks Spark kümesi oluşturma")

    Aşağıdakiler dışında diğer tüm varsayılan değerleri kabul edin:

   * Küme için bir ad girin.
   * Bu makalede, **5,3** çalışma zamanına sahip bir küme oluşturun.
   * **\_\_ dakika işlem yapılmadığında sonlandır** onay kutusunu seçtiğinizden emin olun. Küme kullanılmazsa kümenin sonlandırılması için biz süre (dakika cinsinden) belirtin.
    
     **Küme oluştur**’u seçin. Küme çalışmaya başladıktan sonra kümeye not defterleri ekleyebilir ve Spark işleri çalıştırabilirsiniz.

Küme oluşturma hakkında daha fazla bilgi için bkz. [Azure Databricks üzerinde Spark kümesi oluşturma](/azure/databricks/clusters/create).

## <a name="run-a-spark-sql-job"></a>Spark SQL işi çalıştırma

Databricks içinde bir not defteri oluşturmak, Not defterini Azure açık veri kümelerinden verileri okuyacak şekilde yapılandırmak ve sonra veriler üzerinde bir Spark SQL işi çalıştırmak için aşağıdaki görevleri gerçekleştirin.

1. Sol bölmede **Azure Databricks**' yi seçin. **Ortak görevlerden** **Yeni Not defteri**' ni seçin.

    ![Databricks 'te Not defteri oluşturma](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Databricks 'te Not defteri oluşturma")

2. **Not Defteri Oluştur** iletişim kutusuna bir ad girin, dil olarak **Python** ' ı seçin ve daha önce oluşturduğunuz Spark kümesini seçin.

    ![Databricks 'te Not defteri oluşturma](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Databricks 'te Not defteri oluşturma")

    **Oluştur**'u seçin.

3. Bu adımda, [Azure açık veri kümelerinde](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks)Boston Güvenlik verileriyle bir Spark dataframe oluşturun ve verileri sorgulamak için SQL kullanın.

   Aşağıdaki komut, Azure depolama erişim bilgilerini ayarlar. Bu PySpark kodunu ilk hücreye yapıştırın ve kodu çalıştırmak için **SHIFT + enter** tuşlarını kullanın.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   Aşağıdaki komut Spark 'ın blob depolamadan uzaktan okunmasını sağlar. Bu PySpark kodunu bir sonraki hücreye yapıştırın ve kodu çalıştırmak için **SHIFT + enter** tuşlarını kullanın.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   Aşağıdaki komut bir DataFrame oluşturur. Bu PySpark kodunu bir sonraki hücreye yapıştırın ve kodu çalıştırmak için **SHIFT + enter** tuşlarını kullanın.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Bir SQL ifadesini çalıştırmak, **kaynak**adlı geçici görünümden ilk 10 veri satırı döndürür. Bu PySpark kodunu bir sonraki hücreye yapıştırın ve kodu çalıştırmak için **SHIFT + enter** tuşlarını kullanın.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. Aşağıdaki ekran görüntüsünde gösterildiği gibi bir tablo çıktısı görürsünüz (yalnızca bazı sütunlar gösterilmiştir):

    ![Örnek veriler](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Örnek JSON verileri")

6. Artık bu verilerin görsel bir gösterimini oluşturarak, diğer kaynaklar yerine vatandaşları Connect app ve City çalışan uygulaması kullanılarak kaç tane güvenlik olayının raporlanacağı gösterilir. Tablo çıkışının alt kısmından **çubuk grafik** simgesini seçin ve ardından **Çizim seçenekleri**' ne tıklayın.

    ![Çubuk grafik oluştur](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Çubuk grafik oluştur")

8. **Çizimi Özelleştir** menüsünde, değerleri ekran görüntüsünde gösterilen şekilde sürükleyip bırakın.

    ![Pasta grafiğini özelleştirme](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Çubuk grafiği Özelleştir")

   * **Anahtarları** **kaynağa**ayarlayın.
   * **Değerleri** **\ıd > <** olarak ayarlayın.
   * **Toplama**’yı **SAYI** olarak ayarlayın.
   * **Görüntü türünü** **pasta grafik**olarak ayarlayın.

     **Uygula**'ya tıklayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Makaleyi tamamladıktan sonra kümeyi sonlandırabilirsiniz. Bunu yapmak için Azure Databricks çalışma alanında sol bölmedeki **Kümeler**’i seçin. Sonlandırmak istediğiniz küme için imleci **Eylemler** sütunu altındaki üç noktanın üzerine taşıyın ve **Sonlandır** simgesini seçin.

![Databricks kümesini durdurma](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Databricks kümesini durdurma")

El ile otomatik olarak durdurur küme sonlandırmazsanız, seçtiğiniz sağlanan **sonra Sonlandır \_ \_ yapılmadan geçecek dakika cinsinden** küme oluşturulurken onay kutusu. Böyle bir durumda, belirtilen süre boyunca etkin olmaması durumunda küme otomatik olarak durdurulur.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Databricks 'de bir Spark kümesi oluşturdunuz ve Azure açık veri kümelerinden verileri kullanarak bir Spark işi çalıştırdınız. Diğer veri kaynaklarından Azure Databricks’e verileri aktarma hakkında bilgi almak için [Spark veri kaynakları](/azure/databricks/data/data-sources/index) bölümüne de bakabilirsiniz. Azure Databricks kullanılarak bir ETL işleminin (verileri ayıklama, dönüştürme ve yükleme) nasıl gerçekleştirileceğini öğrenmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
>[Azure Databricks kullanarak verileri ayıklama, dönüştürme ve yükleme](databricks-extract-load-sql-data-warehouse.md)
