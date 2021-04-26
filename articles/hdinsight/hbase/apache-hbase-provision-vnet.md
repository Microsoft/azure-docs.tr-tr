---
title: Sanal ağda HBase kümeleri oluşturma-Azure
description: Azure HDInsight 'ta HBase 'i kullanmaya başlayın. Azure sanal ağında HDInsight HBase kümeleri oluşturmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: a9a1788473cb31f8e78aac0bbd5979b3d681ad32
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867602"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Azure sanal ağı 'nda HDInsight 'ta Apache HBase kümeleri oluşturma

Azure [sanal ağında](https://azure.microsoft.com/services/virtual-network/)Azure HDInsight Apache HBase kümeleri oluşturmayı öğrenin.

Sanal ağ tümleştirmesiyle, uygulamaların HBase ile doğrudan iletişim kurabilmesi için Apache HBase kümeleri, uygulamalarınızla aynı sanal ağa dağıtılabilir. Avantajlara şunlar dahildir:

* HBase Java uzak yordam çağrısı (RPC) API 'Leri aracılığıyla iletişim sağlayan HBase kümesinin düğümlerine doğrudan Web uygulamasının bağlantısı.
* Trafiğinizi birden çok ağ geçidi ve yük dengeleyicileri üzerinden hareket ederek iyileştirilmiş performans.
* Gizli bilgileri genel bir uç nokta görüntülenmeden daha güvenli bir şekilde işleme özelliği.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Sanal ağa Apache HBase kümesi oluşturma

Bu bölümde, bir Azure sanal ağında [Azure Resource Manager şablonu](../../azure-resource-manager/templates/deploy-powershell.md)kullanarak, bağımlı Azure depolama hesabı ile Linux tabanlı bir Apache HBase kümesi oluşturacaksınız. Diğer küme oluşturma yöntemleri ve ayarları anlamak için bkz. [HDInsight kümeleri oluşturma](../hdinsight-hadoop-provision-linux-clusters.md). HDInsight 'ta Apache Hadoop kümeleri oluşturmak için şablon kullanma hakkında daha fazla bilgi için, bkz. [Azure Resource Manager şablonları kullanarak HDInsight 'ta Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Bazı özellikler şablona sabit olarak kodlanmıştır. Örnek:
>
> * **Konum**: Doğu ABD 2
> * **Küme sürümü**: 3.6
> * **Küme çalışan düğümü sayısı**: 2
> * **Varsayılan depolama hesabı**: benzersiz bir dize
> * **Sanal ağ adı**: clustername-VNET
> * **Sanal ağ adres alanı**: 10.0.0.0/16
> * **Alt ağ adı**: subnet1
> * **Alt ağ adres aralığı**: 10.0.0.0/24
>
> `CLUSTERNAME` , şablonu kullanırken sağladığınız küme adı ile değiştirilmiştir.

1. Azure portal şablonu açmak için aşağıdaki görüntüyü seçin. Şablon, [Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/)bulunur.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. **Özel dağıtım** iletişim kutusunda **Şablonu Düzenle**' yi seçin.

1. Satır 165 ' de değeri `Standard_A3` olarak değiştirin `Standard_A4_V2` . Sonra **Kaydet**'i seçin.

1. Kalan şablonu aşağıdaki bilgilerle doldurun:

    |Özellik |Değer |
    |---|---|
    |Abonelik|HDInsight kümesi, bağımlı depolama hesabı ve Azure sanal ağı oluşturmak için kullanılan bir Azure aboneliğini seçin.|
    Kaynak grubu|**Yeni oluştur**' u seçin ve yeni bir kaynak grubu adı belirtin.|
    |Konum|Kaynak grubu için bir konum seçin.|
    |Küme Adı|Oluşturulacak Hadoop kümesi için bir ad girin.|
    |Küme oturum açma Kullanıcı adı ve parolası|Varsayılan Kullanıcı adı **admin**' dir. Bir parola belirtin.|
    |SSH Kullanıcı adı ve parolası|Varsayılan Kullanıcı adı **sshuser**' dır.  Bir parola belirtin.|

    **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum '** u seçin.

1. **Satın al**'ı seçin. Bir küme oluşturmak yaklaşık 20 dakika sürer. Küme oluşturulduktan sonra, portalda açmak için kümeyi seçebilirsiniz.

Makaleyi tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile, verileriniz Azure Storage’da depolanır, böylece kullanılmadığında bir kümeyi güvenle silebilirsiniz. Ayrıca, kullanılmıyorken dahi HDInsight kümesi için sizden ücret kesilir. Küme ücretleri depolama ücretlerinin birkaç katı olduğundan, kullanılmadığında kümelerin silinmesi mantıklı olandır. Bir kümeyi silme yönergeleri için [Azure Portal kullanarak HDInsight 'ta Apache Hadoop kümelerini yönetme](../hdinsight-administer-use-portal-linux.md#delete-clusters)konusuna bakın.

Yeni HBase kümeniz ile çalışmaya başlamak için [HDInsight 'ta Apache Hadoop Ile Apache HBase 'i kullanmaya başlama](./apache-hbase-tutorial-get-started-linux.md)bölümünde bulunan yordamları kullanabilirsiniz.

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Apache HBase Java RPC API 'Lerini kullanarak Apache HBase kümesine bağlanma

### <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Aynı Azure sanal ağına ve aynı alt ağa hizmet olarak altyapı (IaaS) sanal makinesi oluşturun. Yeni bir IaaS sanal makinesi oluşturma yönergeleri için bkz. [Windows Server çalıştıran bir sanal makine oluşturma](../../virtual-machines/windows/quick-create-portal.md). Bu belgedeki adımları izleyerek, ağ yapılandırması için aşağıdaki değerleri kullanmanız gerekir:

* **Sanal ağ**: clustername-VNET
* **Alt ağ**: subnet1

> [!IMPORTANT]  
> `CLUSTERNAME`Önceki adımlarda HDInsight kümesini oluştururken kullandığınız adla değiştirin.

Bu değerleri kullanarak, sanal makine HDInsight kümesiyle aynı sanal ağa ve alt ağa yerleştirilir. Bu yapılandırma, bunların birbirleriyle doğrudan iletişim kurmasına olanak tanır. Boş bir Edge düğümüyle HDInsight kümesi oluşturmanın bir yolu vardır. Uç düğümü, kümeyi yönetmek için kullanılabilir.  Daha fazla bilgi için bkz. [HDInsight 'ta boş kenar düğümlerini kullanma](../hdinsight-apps-use-edge-node.md).

### <a name="obtain-fully-qualified-domain-name"></a>Tam etki alanı adını al

Bir Java uygulamasını kullanarak HBase 'e uzaktan bağlanmak için tam etki alanı adını (FQDN) kullanmanız gerekir. Bunu öğrenmek için HBase kümesinin bağlantıya özgü DNS sonekini almalısınız. Bunu yapmak için, aşağıdaki yöntemlerden birini kullanabilirsiniz:

* Bir [Apache ambarı](https://ambari.apache.org/) çağrısı yapmak Için bir Web tarayıcısı kullanın:

    `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true` adresine gidin. DNS son eklerine sahip bir JSON dosyası döndürür.

* Ambarı Web sitesini kullanın:

    1. `https://CLUSTERNAME.azurehdinsight.net` adresine gidin.
    2. Üstteki menüden **konaklar** ' ı seçin.

* REST çağrısı yapmak için kıvrımlı kullanın:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

Döndürülen JavaScript Nesne Gösterimi (JSON) verilerinde "host_name" girişini bulun. Kümedeki düğümlerin FQDN 'sini içerir. Örnek:

```json
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

Etki alanı adının küme adı ile başlayan bölümü DNS son ekidir. Örneğin, `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

### <a name="verify-communication-inside-virtual-network"></a>Sanal ağ içinde iletişimi doğrula

Sanal makinenin HBase kümesiyle iletişim kurabildiğini doğrulamak için, `ping headnode0.<dns suffix>` sanal makinedeki komutunu kullanın. Örneğin, `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Bu bilgileri bir Java uygulamasında kullanmak için, bir uygulama oluşturmak üzere [HDInsight (Hadoop) Ile Apache HBase kullanan Java uygulamaları oluşturmak Için Apache Maven kullanma](./apache-hbase-build-java-maven-linux.md) bölümündeki adımları izleyebilirsiniz. Uygulamanın uzak bir HBase sunucusuna bağlanmasını sağlamak için bu örnekteki **hbase-site.xml** dosyasını Zookeeper FQDN 'sini kullanacak şekilde değiştirin. Örnek:

```xml
<property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
</property>
```

> [!NOTE]  
> Azure sanal ağlarında ad çözümlemesi hakkında daha fazla bilgi için, kendi DNS sunucunuzu kullanma da dahil olmak üzere, bkz. [ad çözümlemesi (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Apache HBase kümesi oluşturmayı öğrendiniz. Daha fazla bilgi edinmek için şu makalelere bakın:

* [HDInsight'ı kullanmaya başlama](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight 'ta boş kenar düğümlerini kullanma](../hdinsight-apps-use-edge-node.md)
* [HDInsight 'ta Apache HBase çoğaltmasını yapılandırma](apache-hbase-replication.md)
* [HDInsight 'ta Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight 'ta Apache Hadoop ile Apache HBase kullanmaya başlama](./apache-hbase-tutorial-get-started-linux.md)
* [Sanal ağa genel bakış](../../virtual-network/virtual-networks-overview.md)
