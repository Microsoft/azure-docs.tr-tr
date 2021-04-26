---
title: Hızlı başlangıç-Azure Databricks ile yönetilen Apache Spark kümesi dağıtma
description: Bu hızlı başlangıçta, Azure portal kullanarak Azure Databricks ile yönetilen Apache Spark kümesinin nasıl dağıtılacağı gösterilmektedir.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: fd0d5c5b73ae1fb1eae7f38a22913018555ebe11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748964"
---
# <a name="quickstart-deploy-a-managed-apache-spark-cluster-preview-with-azure-databricks"></a>Hızlı başlangıç: Azure Databricks ile yönetilen Apache Spark kümesi (Önizleme) dağıtma

Apache Cassandra için Azure yönetilen örneği, yönetilen açık kaynaklı Apache Cassandra veri merkezleri için otomatik dağıtım ve ölçeklendirme işlemleri sağlar, karma senaryoları hızlandırarak sürekli bakımı azaltır.

> [!IMPORTANT]
> Apache Cassandra için Azure yönetilen örneği şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bu hızlı başlangıçta, Apache Cassandra kümesi için Azure yönetilen örneğinizin Azure sanal ağı içinde tam olarak yönetilen bir Apache Spark kümesi oluşturmak üzere Azure portal nasıl kullanılacağı gösterilmektedir. Spark kümesini Azure Databricks ' de oluşturacaksınız. Daha sonra kümeye Not defterleri oluşturabilir veya ekleyebilir, farklı veri kaynaklarından verileri okuyabilir ve öngörüleri çözümleyebilirsiniz.

[Azure sanal ağınızda (sanal ağ ekleme) Azure Databricks dağıtma](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)hakkında ayrıntılı yönergeler için de daha fazla bilgi edinebilirsiniz.

## <a name="create-an-azure-databricks-cluster"></a>Azure Databricks kümesi oluşturma

Apache Cassandra için Azure yönetilen örneği olan bir sanal ağda Azure Databricks kümesi oluşturmak için aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Sol gezinti bölmesinde **kaynak gruplarını** bulun ve yönetilen örneğinizin dağıtıldığı sanal ağı içeren kaynak grubunuza gidin.

1. **Sanal ağ** kaynağını açın ve **Adres alanını** bir yere göz önünde oluşturun:

    :::image type="content" source="./media/deploy-cluster-databricks/virtual-network-address-space.png" alt-text="Sanal ağınızın adres alanını alın." border="true":::

1. Kaynak grubundan, **Ekle** ' yi seçin ve arama alanında **Azure Databricks** ara ' yı seçin:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks.png" alt-text="Azure Databricks arayın." border="true":::

1. Azure Databricks hesap oluşturmak için **Oluştur** ' u seçin:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks-create.png" alt-text="Azure Databricks hesabı oluşturun." border="true":::

1. Aşağıdaki değerleri girin:

   * **Çalışma alanı adı** -Databricks çalışma alanınız için bir ad sağlayın.
   * **Bölge** -sanal ağınızla aynı bölgeyi seçtiğinizden emin olun.
   * **Fiyatlandırma katmanı** -standart, Premium veya deneme arasında seçim yapın. Bu katmanlar hakkında daha fazla bilgi için bkz. [Databricks fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/databricks/).

    :::image type="content" source="./media/deploy-cluster-databricks/select-name.png" alt-text="Databricks hesabı için çalışma alanı adı, bölge ve fiyatlandırma katmanını doldurun." border="true":::

1. Sonra, **ağ** sekmesini seçin ve aşağıdaki ayrıntıları girin:

   * **Sanal ağınızda (VNet) Azure Databricks çalışma alanı dağıtın** - **Evet**' i seçin.
   * **Sanal ağ** -açılan listeden, yönetilen örneğinizin bulunduğu sanal ağı seçin.
   * **Ortak alt ağ adı** -genel alt ağ için bir ad girin.
   * **Ortak alt ağ CIDR aralığı** -genel alt ağ IÇIN bir IP aralığı girin.
   * **Özel alt ağ adı** -özel alt ağ için bir ad girin.
   * **Özel alt ağ CIDR aralığı** -özel alt ağ IÇIN bir IP aralığı girin.

   Aralık çakışmalarını önlemek için, daha yüksek aralıklar seçtiğinizden emin olun. Gerekirse, aralıkları bölmek için bir [görsel alt ağ Hesaplayıcısı](https://www.fryguy.net/wp-content/tools/subnets.html) kullanın:

   :::image type="content" source="./media/deploy-cluster-databricks/subnet-calculator.png" alt-text="Sanal ağ alt ağı hesaplayıcısını kullanın." border="true":::

   Aşağıdaki ekran görüntüsünde ağ bölmesine ilişkin örnek Ayrıntılar gösterilmektedir:

   :::image type="content" source="./media/deploy-cluster-databricks/subnets.png" alt-text="Ortak ve özel alt ağ adlarını belirtin." border="true":::

1. **Gözden geçir ve oluştur** ' u seçin ve sonra çalışma alanını dağıtmak için **oluşturun** .

1. Oluşturulduktan sonra **çalışma alanını başlatın** .

1. Azure Databricks portalına yönlendirilirsiniz. Portaldan **Yeni küme**' yi seçin.

1. **Yeni küme** bölmesinde, aşağıdaki alanlardan farklı tüm alanlar için varsayılan değerleri kabul edin:

   * **Küme adı** -küme için bir ad girin.
   * **Databricks Runtime sürümü** -Cassandra Bağlayıcısı tarafından desteklenen scala 2,11 veya daha düşük bir sürüm seçin.

    :::image type="content" source="./media/deploy-cluster-databricks/spark-cluster.png" alt-text="Databricks çalışma zamanı sürümünü ve Spark kümesini seçin." border="true":::

1. **Gelişmiş Seçenekler** ' i genişletin ve aşağıdaki yapılandırmayı ekleyin. Düğüm IP 'lerini ve kimlik bilgilerini değiştirdiğinizden emin olun:

    ```java
    spark.cassandra.connection.host <node1 IP>,<node 2 IP>, <node IP>
    spark.cassandra.auth.password cassandra
    spark.cassandra.connection.port 9042
    spark.cassandra.auth.username cassandra
    spark.cassandra.connection.ssl.enabled true
    ```

1. **Kitaplıklar** sekmesinde, Cassandra (*Spark-Cassandra-Connector*) için Spark bağlayıcısının en son sürümünü yükleyip kümeyi yeniden başlatın:

    :::image type="content" source="./media/deploy-cluster-databricks/connector.png" alt-text="Cassandra bağlayıcısını yükler." border="true":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu yönetilen örnek kümesini kullanmaya devam edemeyecekiyorsa, aşağıdaki adımlarla silin:

1. Azure portal sol taraftaki menüden **kaynak grupları**' nı seçin.
1. Listeden, bu hızlı başlangıç için oluşturduğunuz kaynak grubunu seçin.
1. Kaynak grubuna **genel bakış** bölmesinde **kaynak grubunu sil**' i seçin.
3. Sonraki pencerede, silinecek kaynak grubunun adını girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Apache Cassandra kümesi için Azure yönetilen örneğinizin sanal ağı içinde tam olarak yönetilen Apache Spark kümesi oluşturmayı öğrendiniz. Daha sonra, küme ve veri merkezi kaynaklarını yönetmeyi öğrenebilirsiniz: 

> [!div class="nextstepaction"]
> [Azure CLı kullanarak Apache Cassandra kaynakları için Azure yönetilen örneğini yönetme](manage-resources-cli.md)

