---
title: Azure HDInsight 'ta Apache Hadoop kümelerinde boş kenar düğümlerini kullanma
description: İstemci olarak kullanılabilecek bir HDInsight kümesine boş bir Edge düğümü ekleme ve ardından HDInsight uygulamalarınızı test etme/barındırma.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: hrasheed
ms.openlocfilehash: b39d30248e9aedc82b5da1e479a383e0c9bfbdc4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498023"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>HDInsight 'ta Apache Hadoop kümelerinde boş kenar düğümlerini kullanma

HDInsight kümesine boş bir Edge düğümü eklemeyi öğrenin. Boş bir Edge düğümü, aynı istemci araçlarının yüklü ve farklı şekilde yapılandırılmış, ancak [Apache Hadoop](https://hadoop.apache.org/) Hizmetleri çalıştırmayan bir Linux sanal makinedir. Küme erişimi, istemci uygulamalarınızı test etmek ve istemci uygulamalarınızı barındırmak için kenar düğümünü kullanabilirsiniz. 

Kümeyi oluştururken, mevcut bir HDInsight kümesine boş bir Edge düğümünü yeni bir kümeye ekleyebilirsiniz. Boş bir Edge düğümü eklemek Azure Resource Manager şablonu kullanılarak yapılır.  Aşağıdaki örnek, bir şablon kullanarak nasıl yapıldığını göstermektedir:

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

Örnekte gösterildiği gibi, isteğe bağlı olarak, kenar düğümüne [Apache ton](hdinsight-hadoop-hue-linux.md) yükleme gibi ek yapılandırma gerçekleştirmek için bir [betik eylemi](hdinsight-hadoop-customize-cluster-linux.md) çağırabilirsiniz. Betik eylem betiği Web üzerinde herkese açık bir şekilde erişilebilir olmalıdır.  Örneğin, komut dosyası Azure depolama 'da depolanıyorsa, ortak kapsayıcıları veya genel Blobları kullanın.

Uç düğüm sanal makine boyutu, HDInsight kümesi çalışan düğümü VM boyut gereksinimlerini karşılamalıdır. Önerilen çalışan düğümü VM boyutları için bkz. [HDInsight 'ta Apache Hadoop kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Bir Edge düğümü oluşturduktan sonra, SSH kullanarak kenar düğümüne bağlanabilir ve HDInsight 'ta Hadoop kümesine erişmek için istemci araçları çalıştırabilirsiniz.

> [!WARNING]   
> Edge düğümüne yüklenen özel bileşenler, Microsoft 'tan ticari olarak makul bir destek alır. Bu, karşılaştığınız sorunların çözümlenmesinde ortaya çıkabilir. Ya da, daha fazla yardım için topluluk kaynaklarına da başvurulabilir. Topluluktan yardım almak için en etkin sitelerin bazıları aşağıda verilmiştir:
>
> * [HDInsight için MSDN Forumu](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Apache teknolojisi kullanıyorsanız, [https://apache.org](https://apache.org) [Apache Hadoop](https://hadoop.apache.org/) sitesi gibi Apache proje siteleri aracılığıyla yardım bulabilirsiniz.

> [!IMPORTANT]
> Ubuntu görüntüleri, yayımlanmakta olan 3 ay içinde yeni HDInsight kümesi oluşturma için kullanılabilir hale gelir. 2019 Ocak itibariyle, çalışan kümeler (kenar düğümleri dahil) Otomatik Düzeltme Eki **uygulanmaz** . Müşterilerin çalışan bir kümeyi yaması için betik eylemleri veya diğer mekanizmaları kullanması gerekir.  Daha fazla bilgi için bkz. [HDInsight Için Işletim sistemi düzeltme eki uygulama](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Var olan bir kümeye kenar düğümü ekleme
Bu bölümde, var olan bir HDInsight kümesine Edge düğümü eklemek için bir Kaynak Yöneticisi şablonu kullanırsınız.  Kaynak Yöneticisi şablonu [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/)' da bulunabilir. Kaynak Yöneticisi şablonu https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.shkonumunda bulunan bir betik eylemini çağırır. Betik herhangi bir eylem gerçekleştirmez.  Bu, bir Kaynak Yöneticisi şablonundan betik eylemi çağırma işlemini gösterir.

**Var olan bir kümeye boş bir Edge düğümü eklemek için**

1. Aşağıdaki resme tıklayarak Azure 'da oturum açın ve Azure portal Azure Resource Manager şablonu açın. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>
3. Aşağıdaki özellikleri yapılandırın:
   
   * **Abonelik**: kümeyi oluşturmak için kullanılan bir Azure aboneliği seçin.
   * **Kaynak grubu**: var olan HDInsight kümesi için kullanılan kaynak grubunu seçin.
   * **Konum**: mevcut HDInsight kümesinin konumunu seçin.
   * **Küme adı**: var olan bir HDInsight kümesinin adını girin.
   * **Kenar düğümü boyutu**: VM boyutlarından birini seçin. VM boyutunun, çalışan düğümü VM boyut gereksinimlerini karşılaması gerekir. Önerilen çalışan düğümü VM boyutları için bkz. [HDInsight 'ta Apache Hadoop kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Edge düğümü ön eki**: varsayılan değer **yenidir**.  Varsayılan değeri kullanarak, kenar düğümü adı **New-edgenode**' dır.  Ön eki portaldan özelleştirebilirsiniz. Ayrıca, şablondan tam adı özelleştirebilirsiniz.

4. **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**' u işaretleyin ve ardından, kenar düğümünü oluşturmak Için **satın al** ' a tıklayın.

>[!IMPORTANT]  
> Mevcut HDInsight kümesi için Azure kaynak grubunu seçtiğinizden emin olun.  Aksi halde, "iç içe kaynak üzerinde istenen işlem gerçekleştirilemiyor" hata iletisini alırsınız. '&lt;ClusterName > ' üst kaynağı bulunamadı. "

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Küme oluştururken kenar düğümü ekleme
Bu bölümde, bir Edge düğümü ile HDInsight kümesi oluşturmak için bir Kaynak Yöneticisi şablonu kullanırsınız.  Kaynak Yöneticisi şablonu, [Azure hızlı başlangıç şablonları galerisinde](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)bulunabilir. Kaynak Yöneticisi şablonu https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.shkonumunda bulunan bir betik eylemini çağırır. Betik herhangi bir eylem gerçekleştirmez.  Bu, bir Kaynak Yöneticisi şablonundan betik eylemi çağırma işlemini gösterir.

**Edge düğümüyle bir HDInsight kümesi oluşturmak için**

1. Henüz yoksa bir HDInsight kümesi oluşturun.  Bkz. [HDInsight 'Ta Hadoop kullanmaya başlama](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Aşağıdaki resme tıklayarak Azure 'da oturum açın ve Azure portal Azure Resource Manager şablonu açın. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>
3. Aşağıdaki özellikleri yapılandırın:
   
   * **Abonelik**: kümeyi oluşturmak için kullanılan bir Azure aboneliği seçin.
   * **Kaynak grubu**: küme için kullanılan yeni bir kaynak grubu oluşturun.
   * **Konum**: Kaynak grubu için bir konum seçin.
   * **Küme adı**: oluşturulacak yeni küme için bir ad girin.
   * **Küme oturum açma Kullanıcı adı**: Hadoop http Kullanıcı adını girin.  Varsayılan ad, **admin** şeklindedir.
   * **Küme oturum açma parolası**: Hadoop http Kullanıcı parolasını girin.
   * **SSH Kullanıcı adı**: SSH kullanıcı adını girin. Varsayılan ad **sshuser**' dır.
   * **SSH parolası**: SSH kullanıcı parolasını girin.
   * **Betik eylemini yükler**: Bu makaleye gitmek için varsayılan değeri saklayın.
     
     Şablonda bazı özellikler zaten kodlanmış: küme türü, küme çalışan düğümü sayısı, uç düğüm boyutu ve kenar düğümü adı.
4. **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**' u işaretleyin ve ardından **satın al** ' a tıklayarak kümeyi kenar düğümü ile oluşturun.

## <a name="add-multiple-edge-nodes"></a>Birden çok Edge düğümü ekleme

Bir HDInsight kümesine birden çok Edge düğümü ekleyebilirsiniz.  Birden çok Edge düğümleri yapılandırması yalnızca Azure Resource Manager şablonları kullanılarak yapılabilir.  Bu makalenin başındaki şablon örneğine bakın.  **Targetınstancecount** öğesini oluşturmak istediğiniz kenar düğümleri sayısını yansıtacak şekilde güncelleştirmeniz gerekir.

## <a name="access-an-edge-node"></a>Kenar düğümüne erişme
Edge düğümü SSH uç noktası &lt;EdgeNodeName >.&lt;ClusterName >-ssh.azurehdinsight.net:22.  Örneğin, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Edge düğümü Azure portal bir uygulama olarak görünür.  Portal, size SSH kullanarak Edge düğümüne erişme bilgileri verir.

**Edge düğümü SSH uç noktasını doğrulamak için**

1. [Azure portalı](https://portal.azure.com) üzerinde oturum açın.
2. HDInsight kümesini bir Edge düğümü ile açın.
3. **Uygulamalar**'a tıklayın. Edge düğümünü görürsünüz.  Varsayılan ad **New-edgenode**' dır.
4. Kenar düğümüne tıklayın. SSH uç noktasını görürsünüz.

**Hive 'yi kenar düğümünde kullanmak için**

1. Kenar düğümüne bağlanmak için SSH kullanın. Bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

2. SSH kullanarak kenar düğümüne bağlandıktan sonra Hive konsolunu açmak için aşağıdaki komutu kullanın:
   
        hive
3. Kümede Hive tablolarını göstermek için aşağıdaki komutu çalıştırın:
   
        show tables;

## <a name="delete-an-edge-node"></a>Kenar düğümünü silme
Bir kenar düğümünü Azure portal silebilirsiniz.

**Kenar düğümüne erişmek için**

1. [Azure portalı](https://portal.azure.com) üzerinde oturum açın.
2. HDInsight kümesini bir Edge düğümü ile açın.
3. **Uygulamalar**'a tıklayın. Kenar düğümlerinin bir listesini görürsünüz.  
4. Silmek istediğiniz kenar düğümüne sağ tıklayın ve ardından **Sil**' e tıklayın.
5. Onaylamak için **Evet**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, kenar düğümü ekleme ve kenar düğümüne erişme hakkında daha fazla öğrendiniz. Daha fazla bilgi için aşağıdaki makalelere bakın:

* [HDInsight uygulamaları yükleme](hdinsight-apps-install-applications.md): HDInsight uygulamalarını kümelerinize yükleme hakkında bilgi alın.
* [Özel HDInsight uygulamaları yüklemek](hdinsight-apps-install-custom-applications.md): yayımlanmamış bir HDInsight uygulamasını HDInsight 'a dağıtmayı öğrenin.
* [HDInsight uygulamalarını yayımlama](hdinsight-apps-publish-applications.md): Özel HDInsight uygulamalarınızı Azure Marketi’nde nasıl yayımlayacağınızı öğrenin.
* [MSDN: HDInsight uygulaması yükleme](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight uygulamalarını nasıl tanımlayacağınızı öğrenin.
* [Betik Eylemi kullanarak Linux tabanlı HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md): ek uygulamalar yüklemek için Betik Eyleminin nasıl kullanılacağını öğrenin.
* [HDInsight 'ta Kaynak Yöneticisi şablonları kullanarak Linux tabanlı Apache Hadoop kümeleri oluşturma](hdinsight-hadoop-create-linux-clusters-arm-templates.md): HDInsight kümeleri oluşturmak için Kaynak Yöneticisi şablonlarının nasıl çağrılacağını öğrenin.

