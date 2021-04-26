---
title: Hızlı başlangıç-Azure portal Apache Cassandra kümesi için Azure yönetilen örneği oluşturma
description: Bu hızlı başlangıçta, Azure portal kullanılarak Apache Cassandra kümesi için Azure yönetilen örneği oluşturma gösterilmektedir.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 315b505e9de853fbe4663eacdfe929149dbaa458
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873154"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Hızlı başlangıç: Azure portal (Önizleme) Apache Cassandra kümesi için Azure yönetilen örneği oluşturma
 
Apache Cassandra için Azure yönetilen örneği, yönetilen açık kaynaklı Apache Cassandra veri merkezleri için otomatik dağıtım ve ölçeklendirme işlemleri sağlar, karma senaryoları hızlandırarak sürekli bakımı azaltır.

> [!IMPORTANT]
> Apache Cassandra için Azure yönetilen örneği şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bu hızlı başlangıçta, Apache Cassandra kümesi için Azure yönetilen örneği oluşturmak üzere Azure portal nasıl kullanılacağı gösterilmektedir.

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Yönetilen örnek kümesi oluşturma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Arama çubuğundan **Apache Cassandra Için yönetilen örnek** araması yapın ve sonucu seçin.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Apache Cassandra için yönetilen örnek arayın." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. **Apache Cassandra kümesi Için yönetilen örnek oluştur** ' u seçin.

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Kümeyi oluşturun." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. **Apache Cassandra Için yönetilen örnek oluştur** bölmesine, aşağıdaki ayrıntıları girin:

   * **Abonelik** -açılan listeden Azure aboneliğinizi seçin.
   * **Kaynak grubu**-yeni bir kaynak grubu oluşturmak veya var olan bir kaynak grubunu kullanmak isteyip istemediğinizi belirtin. Kaynak grubu, bir Azure çözümüne ilişkin kaynakları tutan bir kapsayıcıdır. Daha fazla bilgi için bkz. [Azure Kaynak grubuna](../azure-resource-manager/management/overview.md) genel bakış makalesi.
   * **Küme adı** -kümeniz için bir ad girin.
   * Kümenizin dağıtılacağı **konum** konumu.
   * **SKU** -KÜMENIZ için SKU türü.
   * **Hayır. düğümler**-bir kümedeki düğüm sayısı. Bu düğümler verileriniz için çoğaltmalar görevi görür.
   * **Ilk Cassandra yönetici parolası** -kümeyi oluşturmak için kullanılan parola.
   * **Cassandra yönetici parolasını onaylayın** -parolanızı yeniden girin.

    > [!NOTE]
    > Genel Önizleme sırasında, yönetilen örnek kümesini *Doğu ABD, Batı ABD, Doğu ABD 2, Batı ABD 2, Orta ABD, Orta Güney ABD, Kuzey Avrupa, Batı Avrupa, güney Doğu Asya ve Avustralya Doğu* bölgelerinde oluşturabilirsiniz.

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Küme oluştur formunu doldurun." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Sonra **ağ** sekmesini seçin.

1. **Ağ** bölmesinde, **sanal ağ** adını ve **alt ağı** seçin. Var olan bir sanal ağı seçebilir veya yeni bir ağ oluşturabilirsiniz.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Ağ ayrıntılarını yapılandırın." lightbox="./media/create-cluster-portal/networking.png" border="true":::

    > [!NOTE]
    > Apache Cassandra için Azure yönetilen örneğinin dağıtımı, internet erişimi gerektirir. İnternet erişiminin kısıtlandığı ortamlarda dağıtım başarısız olur. VNet 'iniz içindeki erişimi, yönetilen Cassandra 'nin düzgün çalışması için gerekli olan aşağıdaki önemli Azure hizmetlerine engellemediğinizden emin olun:
    > - Azure Storage
    > - Azure KeyVault
    > - Azure Sanal Makine Ölçek Kümeleri
    > - Azure İzleme
    > - Azure Active Directory
    > - Azure Güvenlik

1. Son adımda yeni bir VNet oluşturduysanız, adım 8 ' e atlayın. Mevcut bir VNet seçtiyseniz, kümenizi oluşturmadan önce, sanal ağa ve alt ağa bazı özel izinler uygulamanız gerekir. Bunu yapmak için,, `az role assignment create` `<subscription ID>` `<resource group name>` ve yerine uygun değerleri kullanarak komutunu kullanın `<VNet name>` :

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > `assignee` `role` Önceki komutta ve değerleri sabit değerlerdir, bu değerleri tam olarak komutta belirtildiği gibi girin. Bunu yapmadığınızda, küme oluşturulurken hatalara neden olur. Bu komutu yürütürken herhangi bir hatayla karşılaşırsanız, çalıştırma izniniz olmayabilir, lütfen izinler için yöneticinize ulaşın.

1. Ağ ile işiniz bittiğinde, **gözden geçir +**  >  **Oluştur** oluştur ' a tıklayın.

    > [!NOTE]
    > Kümenin oluşturulması 15 dakika kadar sürebilir.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Kümeyi oluşturmak için Özeti gözden geçirin." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. Dağıtım tamamlandıktan sonra, yeni oluşturulan yönetilen örnek kümesini görmek için kaynak grubunuzu kontrol edin:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Küme oluşturulduktan sonra genel bakış sayfası." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Küme düğümlerine göz atarak, kümeyi oluşturmak için kullandığınız sanal ağ bölmesine gidin ve bunları görüntülemek için **genel bakış** bölmesini açın:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Küme kaynaklarını görüntüleyin." lightbox="./media/create-cluster-portal/resources.png" border="true":::


## <a name="connecting-to-your-cluster"></a>Kümenize bağlanma

Apache Cassandra için Azure yönetilen örneği, genel IP adresleriyle düğüm oluşturmaz, bu nedenle yeni oluşturulan Cassandra kümenize bağlanmak için VNet içinde başka bir kaynak oluşturmanız gerekir. Bu bir uygulama veya Apache 'in açık kaynaklı sorgu aracı [Csqlsh](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) 'in yüklü olduğu bir sanal makine olabilir. Bir Ubuntu sanal makinesini dağıtmak için bir [şablon](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) kullanabilirsiniz. Dağıtıldığında, makineye bağlanmak için SSH kullanın ve aşağıdaki komutları kullanarak CSQLSH 'i yüklemek için:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
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
1. Sonraki pencerede, silinecek kaynak grubunun adını girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure portal kullanarak Apache Cassandra kümesi için Azure yönetilen örneği oluşturmayı öğrendiniz. Artık kümeyle çalışmaya başlayabilirsiniz:

> [!div class="nextstepaction"]
> [Azure Databricks ile yönetilen Apache Spark kümesi dağıtma](deploy-cluster-databricks.md)
