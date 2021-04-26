---
title: Azure HDInsight 'ta Apache Hadoop kümelerinde boş kenar düğümlerini kullanma
description: HDInsight kümesine boş bir Edge düğümü ekleme. İstemci olarak kullanılır ve ardından HDInsight uygulamalarınızı test edin veya barındırın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: 717c3c8052c89825b161f77614d7411c5912d1cd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799913"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>HDInsight 'ta Apache Hadoop kümelerinde boş kenar düğümlerini kullanma

HDInsight kümesine boş bir Edge düğümü eklemeyi öğrenin. Boş bir Edge düğümü, aynı istemci araçlarının yüklü olduğu ve yayın düğümlerinde yapılandırıldığı bir Linux sanal makinedir. Ancak [Apache Hadoop](./hadoop/apache-hadoop-introduction.md) Hizmetleri çalışmıyor. Küme erişimi, istemci uygulamalarınızı test etmek ve istemci uygulamalarınızı barındırmak için kenar düğümünü kullanabilirsiniz.

Kümeyi oluştururken, mevcut bir HDInsight kümesine boş bir Edge düğümünü yeni bir kümeye ekleyebilirsiniz. Boş bir Edge düğümü eklemek Azure Resource Manager şablonu kullanılarak yapılır.  Aşağıdaki örnek, bir şablon kullanarak nasıl yapıldığını göstermektedir:

```json
"resources": [
    {
        "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
        "type": "Microsoft.HDInsight/clusters/applications",
        "apiVersion": "2015-03-01-preview",
        "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
        "properties": {
            "marketPlaceIdentifier": "EmptyNode",
            "computeProfile": {
                "roles": [{
                    "name": "edgenode",
                    "targetInstanceCount": 1,
                    "hardwareProfile": {
                        "vmSize": "{}"
                    }
                }]
            },
            "installScriptActions": [{
                "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                "uri": "[parameters('installScriptAction')]",
                "roles": ["edgenode"]
            }],
            "uninstallScriptActions": [],
            "httpsEndpoints": [],
            "applicationType": "CustomApplication"
        }
    }
],
```

Örnekte gösterildiği gibi, isteğe bağlı olarak ek yapılandırma yapmak için bir [betik eylemi](hdinsight-hadoop-customize-cluster-linux.md) çağırabilirsiniz. Edge düğümüne [Apache ton](hdinsight-hadoop-hue-linux.md) yükleme gibi. Betik eylem betiği Web üzerinde herkese açık bir şekilde erişilebilir olmalıdır.  Örneğin, komut dosyası Azure depolama 'da depolanıyorsa, ortak kapsayıcıları veya genel Blobları kullanın.

Uç düğüm sanal makine boyutu, HDInsight kümesi çalışan düğümü VM boyut gereksinimlerini karşılamalıdır. Önerilen çalışan düğümü VM boyutları için bkz. [HDInsight 'ta Apache Hadoop kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md#cluster-type).

Edge düğümü oluşturduktan sonra, SSH kullanarak kenar düğümüne bağlanabilir ve HDInsight 'ta Hadoop kümesine erişmek için istemci araçları çalıştırabilirsiniz.

> [!WARNING]
> Edge düğümüne yüklenen özel bileşenler, Microsoft 'tan ticari olarak makul bir destek alır. Bu, karşılaştığınız sorunların çözümlenmesinde ortaya çıkabilir. Ya da, daha fazla yardım için topluluk kaynaklarına da başvurulabilir. Topluluktan yardım almak için en etkin sitelerin bazıları aşağıda verilmiştir:
>
> * [HDInsight için Microsoft Q&soru sayfası](/answers/topics/azure-hdinsight.html)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Apache teknolojisi kullanıyorsanız, üzerinde Apache Hadoop sitesi gibi Apache proje siteleri aracılığıyla yardım bulabilirsiniz [https://apache.org](https://apache.org) . [](https://hadoop.apache.org/)

> [!IMPORTANT]
> Ubuntu görüntüleri, yayımlanmakta olan 3 ay içinde yeni HDInsight kümesi oluşturma için kullanılabilir hale gelir. 2019 Ocak itibariyle, çalışan kümeler (kenar düğümleri dahil) Otomatik Düzeltme Eki **uygulanmaz** . Müşterilerin çalışan bir kümeyi yaması için betik eylemleri veya diğer mekanizmaları kullanması gerekir.  Daha fazla bilgi için bkz. [HDInsight Için Işletim sistemi düzeltme eki uygulama](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Var olan bir kümeye kenar düğümü ekleme

Bu bölümde, var olan bir HDInsight kümesine Edge düğümü eklemek için bir Kaynak Yöneticisi şablonu kullanırsınız.  Kaynak Yöneticisi şablonu [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/)' da bulunabilir. Kaynak Yöneticisi şablonu, konumunda bulunan bir betik eylemini çağırır https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh . Betik herhangi bir eylem yapmaz.  Bu, bir Kaynak Yöneticisi şablonundan betik eylemini çağırma işlemini gösterir.

1. Azure 'da oturum açmak için aşağıdaki görüntüyü seçin ve Azure portal Azure Resource Manager şablonu açın.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Aşağıdaki özellikleri yapılandırın:

    |Özellik |Açıklama |
    |---|---|
    |Abonelik|Kümeyi oluşturmak için kullanılan bir Azure aboneliği seçin.|
    |Kaynak grubu|Mevcut HDInsight kümesi için kullanılan kaynak grubunu seçin.|
    |Konum|Mevcut HDInsight kümesinin konumunu seçin.|
    |Küme Adı|Mevcut bir HDInsight kümesinin adını girin.|

1. **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**' u işaretleyin ve ardından kenar düğümünü oluşturmak Için **satın al** ' ı seçin.

> [!IMPORTANT]  
> Mevcut HDInsight kümesi için Azure kaynak grubunu seçtiğinizden emin olun.  Aksi halde, "iç içe kaynak üzerinde istenen işlem gerçekleştirilemiyor" hata iletisini alırsınız. ' &lt; ClusterName> ' üst kaynağı bulunamadı. "

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Küme oluştururken kenar düğümü ekleme

Bu bölümde, bir Edge düğümü ile HDInsight kümesi oluşturmak için bir Kaynak Yöneticisi şablonu kullanırsınız.  Kaynak Yöneticisi şablonu, [Azure hızlı başlangıç şablonları galerisinde](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)bulunabilir. Kaynak Yöneticisi şablonu, konumunda bulunan bir betik eylemini çağırır https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh . Betik herhangi bir eylem yapmaz.  Bu, bir Kaynak Yöneticisi şablonundan betik eylemini çağırma işlemini gösterir.

1. Henüz yoksa bir HDInsight kümesi oluşturun.  Bkz. [HDInsight 'Ta Hadoop kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md).

1. Azure 'da oturum açmak için aşağıdaki görüntüyü seçin ve Azure portal Azure Resource Manager şablonu açın.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Aşağıdaki özellikleri yapılandırın:

    |Özellik |Açıklama |
    |---|---|
    |Abonelik|Kümeyi oluşturmak için kullanılan bir Azure aboneliği seçin.|
    |Kaynak grubu|Küme için kullanılan yeni bir kaynak grubu oluşturun.|
    |Konum|Kaynak grubu için bir konum seçin.|
    |Küme Adı|Oluşturulacak yeni küme için bir ad girin.|
    |Küme Oturum Açma Kullanıcı Adı|Hadoop HTTP Kullanıcı adını girin.  Varsayılan ad, **admin** şeklindedir.|
    |Küme Oturum Açma Parolası|Hadoop HTTP Kullanıcı parolasını girin.|
    |SSH Kullanıcı adı|SSH kullanıcı adını girin. Varsayılan ad **sshuser**' dır.|
    |SSH parolası|SSH kullanıcı parolasını girin.|
    |Betik eylemini yükler|Bu makaleye gitmek için varsayılan değeri saklayın.|

    Şablonda bazı özellikler zaten kodlanmış: küme türü, küme çalışan düğümü sayısı, uç düğüm boyutu ve kenar düğümü adı.

1. **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**' u işaretleyin ve sonra da uç düğümü olan kümeyi oluşturmak Için **satın al** ' ı seçin.

## <a name="add-multiple-edge-nodes"></a>Birden çok Edge düğümü ekleme

Bir HDInsight kümesine birden çok Edge düğümü ekleyebilirsiniz.  Birden çok Edge düğümleri yapılandırması yalnızca Azure Resource Manager şablonları kullanılarak yapılabilir.  Bu makalenin başındaki şablon örneğine bakın.  **Targetınstancecount** öğesini oluşturmak istediğiniz kenar düğümü sayısını yansıtacak şekilde güncelleştirin.

## <a name="access-an-edge-node"></a>Kenar düğümüne erişme

Edge düğümü SSH uç noktası &lt; edgenodename>. &lt; ClusterName>-ssh.azurehdinsight.net:22.  Örneğin, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Edge düğümü Azure portal bir uygulama olarak görünür.  Portal, size SSH kullanarak Edge düğümüne erişme bilgileri verir.

**Edge düğümü SSH uç noktasını doğrulamak için**

1. [Azure Portal](https://portal.azure.com)oturum açın.
2. HDInsight kümesini bir Edge düğümü ile açın.
3. **Uygulamalar**' ı seçin. Edge düğümünü görürsünüz.  Varsayılan ad **New-edgenode**' dır.
4. Kenar düğümünü seçin. SSH uç noktasını görürsünüz.

**Hive 'yi kenar düğümünde kullanmak için**

1. Kenar düğümüne bağlanmak için SSH kullanın. Bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

2. SSH kullanarak kenar düğümüne bağlandıktan sonra Hive konsolunu açmak için aşağıdaki komutu kullanın:

    ```console
    hive
    ```

3. Kümede Hive tablolarını göstermek için aşağıdaki komutu çalıştırın:

    ```hiveql
    show tables;
    ```

## <a name="delete-an-edge-node"></a>Kenar düğümünü silme

Bir kenar düğümünü Azure portal silebilirsiniz.

1. [Azure Portal](https://portal.azure.com)oturum açın.
2. HDInsight kümesini bir Edge düğümü ile açın.
3. **Uygulamalar**' ı seçin. Kenar düğümlerinin bir listesini görürsünüz.  
4. Silmek istediğiniz kenar düğümüne sağ tıklayın ve ardından **Sil**' i seçin.
5. Onaylamak için **Evet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, kenar düğümü ekleme ve kenar düğümüne erişme hakkında daha fazla öğrendiniz. Daha fazla bilgi için aşağıdaki makalelere bakın:

* [HDInsight uygulamaları yükleme](hdinsight-apps-install-applications.md): HDInsight uygulamalarını kümelerinize yükleme hakkında bilgi alın.
* [Özel HDInsight uygulamaları yüklemek](hdinsight-apps-install-custom-applications.md): yayımlanmamış bir HDInsight uygulamasını HDInsight 'a dağıtmayı öğrenin.
* [HDInsight uygulamalarını yayımlama](hdinsight-apps-publish-applications.md): Özel HDInsight uygulamalarınızı Azure Marketi’nde nasıl yayımlayacağınızı öğrenin.
* [MSDN: HDInsight uygulaması yükleme](/rest/api/hdinsight/hdinsight-application): HDInsight uygulamalarını nasıl tanımlayacağınızı öğrenin.
* [Betik Eylemi kullanarak Linux tabanlı HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md): ek uygulamalar yüklemek için Betik Eyleminin nasıl kullanılacağını öğrenin.
* [HDInsight 'ta Kaynak Yöneticisi şablonları kullanarak Linux tabanlı Apache Hadoop kümeleri oluşturma](hdinsight-hadoop-create-linux-clusters-arm-templates.md): HDInsight kümeleri oluşturmak için Kaynak Yöneticisi şablonlarının nasıl çağrılacağını öğrenin.
