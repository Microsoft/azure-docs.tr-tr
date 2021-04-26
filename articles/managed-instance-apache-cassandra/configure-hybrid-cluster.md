---
title: Hızlı başlangıç-Apache Cassandra için Azure yönetilen örneği ile karma küme yapılandırma
description: Bu hızlı başlangıçta, Apache Cassandra için Azure yönetilen örneği ile karma bir kümenin nasıl yapılandırılacağı gösterilmektedir.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 9f3ad2a5d5b275ff611653855eff73bd36afda9f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379426"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Hızlı başlangıç: Apache Cassandra için Azure yönetilen örneği ile karma küme yapılandırma (Önizleme)

Apache Cassandra için Azure yönetilen örneği, yönetilen açık kaynaklı Apache Cassandra veri merkezleri için otomatik dağıtım ve ölçeklendirme işlemleri sağlar. Bu hizmet, karma senaryoları hızlandırmanıza ve devam eden bakımın azaltılmasına yardımcı olur.

> [!IMPORTANT]
> Apache Cassandra için Azure yönetilen örneği şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bu hızlı başlangıçta, Azure CLı komutlarının karma bir kümeyi yapılandırmak için nasıl kullanılacağı gösterilmektedir. Şirket içi veya şirket içinde barındırılan bir ortamda var olan Veri merkezleriniz varsa, bu kümeye başka veri merkezleri eklemek ve bunları sürdürmek üzere Apache Cassandra için Azure yönetilen örneğini kullanabilirsiniz.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Bu makale, Azure CLı sürüm 2.12.1 veya üstünü gerektirir. Azure Cloud Shell kullanıyorsanız, en son sürüm zaten yüklüdür.

* Şirket içinde barındırılan veya şirket içi ortamınıza bağlantısı olan [Azure sanal ağı](../virtual-network/virtual-networks-overview.md) . Şirket içi ortamları Azure 'a bağlama hakkında daha fazla bilgi için bkz. Şirket [içi ağı Azure 'A bağlama](/azure/architecture/reference-architectures/hybrid-networking/) makalesi.

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Karma küme yapılandırma

1. [Azure Portal](https://portal.azure.com/) oturum açın ve sanal ağ kaynağına gidin.

1. **Alt ağlar** sekmesini açın ve yeni bir alt ağ oluşturun. **Alt ağ ekle** formundaki alanlar hakkında daha fazla bilgi edinmek için, bkz. [sanal ağ](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) makalesi:

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Sanal ağınıza yeni bir alt ağ ekleyin." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

    > [!NOTE]
    > Apache Cassandra için Azure yönetilen örneğinin dağıtımı, internet erişimi gerektirir. İnternet erişiminin kısıtlandığı ortamlarda dağıtım başarısız olur. VNet 'iniz içindeki erişimi, yönetilen Cassandra 'nin düzgün çalışması için gerekli olan aşağıdaki önemli Azure hizmetlerine engellemediğinizden emin olun:
    > - Azure Storage
    > - Azure KeyVault
    > - Azure Sanal Makine Ölçek Kümeleri
    > - Azure İzleme
    > - Azure Active Directory
    > - Azure Güvenlik

1. Şimdi, Cassandra yönetilen örneğinin gerektirdiği VNet ve alt ağa bazı özel izinler uygulayacağız ve Azure CLı 'yı kullanmaktır. Komutunu, `az role assignment create` `<subscription ID>` `<resource group name>` ve yerine `<VNet name>` uygun değerleri kullanarak kullanın:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > `assignee` `role` Önceki komutta ve değerleri sırasıyla sabit hizmet ilkesi ve rol tanımlayıcılarıdır.

1. Daha sonra, karma kümeimiz için kaynakları yapılandıracağız. Zaten bir kümeniz olduğundan, buradaki küme adı yalnızca mevcut kümenizin adını belirlemek için bir mantıksal kaynak olacak. Aşağıdaki komut dosyasında tanımlarken ve değişkenlerinde mevcut kümenizin adını kullandığınızdan emin olun `clusterName` `clusterNameOverride` . Ayrıca, çekirdek düğümlerine, genel istemci sertifikalarına (Cassandra uç noktanıza ortak/özel bir anahtar yapılandırdıysanız) ve mevcut kümenizin GOSSIP sertifikalarına ihtiyacınız vardır.

   > [!NOTE]
   > `delegatedManagementSubnetId`Aşağıda sağlayacaksınız değişkeninin değeri, `--scope` Yukarıdaki komutta sağladığınız değerle tamamen aynıdır:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --external-seed-nodes 10.52.221.2,10.52.221.3,10.52.221.4
      --client-certificates 'BEGIN CERTIFICATE-----\n...PEM format..\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format...\n-----END CERTIFICATE-----' \
      --external-gossip-certificates 'BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format 2...\n-----END CERTIFICATE-----'
   ```

    > [!NOTE]
    > Mevcut genel ve/veya GOSSIP sertifikalarınızın nerede tutulur emin olmalısınız. Emin değilseniz, `keytool -list -keystore <keystore-path> -rfc -storepass <password>` sertifikaları yazdırmak için öğesini çalıştırabilmelisiniz. 

1. Küme kaynağı oluşturulduktan sonra, küme kurulum ayrıntılarını almak için aşağıdaki komutu çalıştırın:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. Önceki komut, yönetilen örnek ortamı hakkında bilgi döndürür. Mevcut veri merkezinizdeki düğümlere yükleyebilmek için GOSSIP sertifikalarına ihtiyaç duyarsınız. Aşağıdaki ekran görüntüsünde, önceki komutun çıkışı ve sertifika biçimi gösterilmektedir:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Kümeden sertifika ayrıntılarını alın." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

1. Ardından karma kümede yeni bir veri merkezi oluşturun. Değişken değerlerini küme ayrıntılarınız ile değiştirdiğinizden emin olun:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedManagementSubnetId \
       --node-count 9 
   ```

1. Yeni veri merkezi oluşturuldığına göre, ayrıntılarını görüntülemek için veri merkezini göster komutunu çalıştırın:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. Önceki komut, yeni veri merkezinin çekirdek düğümlerini çıktı. Yeni veri merkezinin çekirdek düğümlerini, *Cassandra. YAML* dosyası içindeki mevcut veri merkezinizin yapılandırmasına ekleyin. Ve daha önce topladığınız yönetilen örnek GOSSIP sertifikalarını yükleyebilirsiniz:

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Veri merkezi ayrıntılarını alın." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

    > [!NOTE]
    > Daha fazla veri merkezi eklemek istiyorsanız Yukarıdaki adımları tekrarlayabilirsiniz, ancak yalnızca çekirdek düğümlerine ihtiyacınız vardır. 

1. Son olarak, kümedeki tüm veri merkezlerini dahil etmek için her bir anahtar alanı 'teki çoğaltma stratejisini güncelleştirmek üzere aşağıdaki CQL sorgusunu kullanın:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3};
   ```
   Parola tablolarını da güncelleştirmeniz gerekir:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3}
   ```

## <a name="troubleshooting"></a>Sorun giderme

Sanal ağınıza izin uygularken bir hatayla karşılaşırsanız (örneğin, *' e5007d2c-4b13-4a74-9b6a-605d99f03501 ' için grafik veritabanında kullanıcı veya hizmet sorumlusu bulunamıyor*), Azure Portal aynı izni el ile uygulayabilirsiniz. Portaldan izinleri uygulamak için, var olan sanal ağınızın **erişim denetimi (IAM)** bölmesine gidin ve "Azure Cosmos DB" Için "Ağ Yöneticisi" rolüne bir rol ataması ekleyin. "Azure Cosmos DB" araması yaptığınızda iki giriş görünürse, aşağıdaki görüntüde gösterildiği gibi her iki girdiyi de ekleyin: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="İzinleri Uygula" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Azure Cosmos DB rol ataması yalnızca dağıtım amacıyla kullanılır. Apache Cassandra için Azure yönetilen ınstanced Azure Cosmos DB için arka uç bağımlılığı yok.  

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu yönetilen örnek kümesini kullanmaya devam edemeyecekiyorsa, aşağıdaki adımlarla silin:

1. Azure portal sol taraftaki menüden **kaynak grupları**' nı seçin.
1. Listeden, bu hızlı başlangıç için oluşturduğunuz kaynak grubunu seçin.
1. Kaynak grubuna **genel bakış** bölmesinde **kaynak grubunu sil**' i seçin.
3. Sonraki pencerede, silinecek kaynak grubunun adını girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure CLı ve Apache Cassandra için Azure yönetilen örneği kullanarak karma küme oluşturmayı öğrendiniz. Artık kümeyle çalışmaya başlayabilirsiniz.

> [!div class="nextstepaction"]
> [Azure CLı kullanarak Apache Cassandra kaynakları için Azure yönetilen örneğini yönetme](manage-resources-cli.md)
