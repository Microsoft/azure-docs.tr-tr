---
title: Azure HDInsight 'a özel Apache Hadoop uygulamalarınızı yükler
description: Azure HDInsight 'ta Apache Hadoop kümeleri için HDInsight uygulamalarını yüklemeyi öğrenin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: e3211e799b0c2cb4c4c9aa2aabcd40d237875b3c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498000"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Azure HDInsight 'ta özel Apache Hadoop uygulamaları yüklemeyi

Bu makalede, Azure HDInsight 'ta Azure portal yayımlanmayan bir [Apache Hadoop](https://hadoop.apache.org/) uygulamasının nasıl yükleneceğini öğreneceksiniz. Bu makalede yükleyeceğiniz uygulama [Hue](https://gethue.com/) uygulamasıdır.

HDInsight uygulaması kullanıcıların Linux tabanlı HDInsight kümesine yükleyebileceği bir uygulamadır.  Bu uygulamalar Microsoft veya bağımsız yazılım satıcıları (ISV) tarafından ya da sizin tarafınızdan geliştirilebilir.  

Diğer ilgili makaleler:

* [HDInsight uygulamaları yükleme](hdinsight-apps-install-applications.md): HDInsight uygulamalarını kümelerinize yükleme hakkında bilgi alın.
* [HDInsight uygulamalarını yayımlama](hdinsight-apps-publish-applications.md): Özel HDInsight uygulamalarınızı Azure Marketi’nde nasıl yayımlayacağınızı öğrenin.
* [MSDN: HDInsight uygulaması yükleme](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight uygulamalarını nasıl tanımlayacağınızı öğrenin.

## <a name="prerequisites"></a>Önkoşullar
HDInsight uygulamalarını mevcut bir HDInsight kümesine yüklemek istiyorsanız bir HDInsight kümesine sahip olmanız gerekir. Küme oluşturmak için bkz. [Küme oluşturma](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). HDInsight uygulamalarını ayrıca bir HDInsight kümesi oluştururken yükleyebilirsiniz.

## <a name="install-hdinsight-applications"></a>HDInsight uygulamaları yükleme
HDInsight uygulamaları bir küme oluşturduğunuzda veya var olan bir HDInsight kümesine yüklenebilir. Azure Resource Manager şablonlarını tanımlamak için bkz. [MSDN: HDInsight uygulaması yükleme](https://msdn.microsoft.com/library/mt706515.aspx).

Bu uygulamayı (Hue) dağıtmak için gerekli dosyalar:

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): HDInsight uygulamasını yüklemek için Azure Resource Manager şablonu. Kendi Azure Resource Manager şablonunuzu geliştirmek için bkz. [MSDN: HDInsight uygulaması yükleme](https://msdn.microsoft.com/library/mt706515.aspx).
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Kenar düğümünü yapılandırmak üzere Resource Manager şablonu tarafından çağrılan Betik eylemi.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): hui-install_v0.sh dosyasından çağrılan Hue ikili dosyası.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): hui-install_v0.sh dosyasından çağrılan Hue ikili dosyası.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): hui-install_v0.sh dosyasından çağrılan bir örnek web uygulaması (Tomcat).

**Mevcut bir HDInsight kümesine Hue yüklemek için**

1. Aşağıdaki resme tıklayarak Azure'da oturum açın ve Azure portalında Resource Manager şablonunu açın.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Bu düğme Azure portalında bir Resource Manager şablonu açar.  Kaynak Yöneticisi şablonu [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue)konumunda bulunur.  Bu Resource Manager şablonunun nasıl yazılacağını öğrenmek için bkz. [MSDN: HDInsight uygulaması yükleme](https://msdn.microsoft.com/library/mt706515.aspx).
2. **Parametreler** dikey penceresinde aşağıdakileri girin:

   * **ClusterName**: Uygulamayı yüklemek istediğiniz kümenin adını girin. Bu küme var olan bir küme olmalıdır.
3. Parametreleri kaydetmek için **Tamam**’a tıklayın.
4. **Özel dağıtım** dikey penceresinden **Kaynak grubu** girin.  Kaynak grubu; küme, bağımlı depolama hesabı ve diğer kaynakları gruplandıran bir kapsayıcıdır. Aynı kaynak grubunun küme olarak kullanılması gereklidir.
5. **Yasal koşullar**’a ve ardından **Oluştur**’a tıklayın.
6. **Panoya sabitle** onay kutusunun seçili olduğundan emin olun ve ardından **Oluştur**’a tıklayın. Yükleme durumunu portal panosuna sabitlenmiş kutucuktan ve portal bildiriminden görebilirsiniz (portalın üst kısmındaki zil simgesine tıklayın).  Uygulamanın yüklenmesi yaklaşık 10 dakika sürer.

**Küme oluştururken Hue yüklemek için**

1. Aşağıdaki resme tıklayarak Azure'da oturum açın ve Azure portalında Resource Manager şablonunu açın.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Bu düğme Azure portalında bir Resource Manager şablonu açar.  Kaynak Yöneticisi şablonu [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json)konumunda bulunur.  Bu Resource Manager şablonunun nasıl yazılacağını öğrenmek için bkz. [MSDN: HDInsight uygulaması yükleme](https://msdn.microsoft.com/library/mt706515.aspx).
2. Küme oluşturmak ve Hue uygulamasını yüklemek için yönergeleri izleyin. HDInsight kümeleri oluşturma hakkında daha fazla bilgi için bkz. [HDInsight’ta Linux tabanlı Hadoop kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md).

Azure portal ek olarak, Kaynak Yöneticisi şablonlarını çağırmak için [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) ve [Azure klasik CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) 'yi de kullanabilirsiniz.

## <a name="validate-the-installation"></a>Yüklemeyi doğrulama
Uygulama yüklemesini doğrulamak için Azure portalında uygulama durumunu denetleyebilirsiniz. Ayrıca, tüm HTTP uç noktalarının beklenen şekilde geldiğini ve varsa web sayfasını doğrulayabilirsiniz:

**Hue portalını açmak için**

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Soldaki menüde **HDInsight Kümeleri**’ne tıklayın.  Bu seçeneği görmüyorsanız **Gözat**’a ve ardından **HDInsight Kümeleri**’ne tıklayın.
3. Uygulamayı yüklediğiniz kümeye tıklayın.
4. **Ayarlar** dikey penceresinde **Genel** kategorisi altındaki **Uygulamalar**’a tıklayın. **Yüklü Uygulamalar** dikey penceresinde **hue** uygulamasının listelendiğini görmeniz gerekir.
5. Özellikleri listelemek için listede **hue** seçeneğine tıklayın.  
6. Web sitesini doğrulamak için Web sayfası bağlantısına tıklayın; HTTP uç noktasını bir tarayıcıda açarak, ton Web Kullanıcı arabirimini doğrulayın, SSH kullanarak SSH uç noktasını açın. Bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="troubleshoot-the-installation"></a>Yükleme sorunlarını giderme
Uygulama yükleme durumunu portal bildiriminden denetleyebilirsiniz (Portalın üst kısmındaki zil simgesine tıklayın).

Bir uygulama yüklemesi başarısız olduysa 3 yerden hata iletileri ve hata ayıklama bilgileri görebilirsiniz:

* HDInsight Uygulamaları: genel hata bilgileri.

    Portaldan kümeyi açın ve Ayarlar dikey penceresinden Uygulamalar’a tıklayın:

    ![hdinsight applications application installation error](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)
* HDInsight betik eylemi: HDInsight Uygulamalarının hata iletisi bir betik eylemi hatası belirtiyorsa betik eylemleri bölmesinde betik hatasına ilişkin daha fazla bilgi sunulur.

    Ayarlar dikey penceresinden Betik Eylemi’ne tıklayın. Betik eylemi geçmişinde hata iletileri gösterilir

    ![hdinsight applications script action error](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)
* Ambari Web Kullanıcı Arabirimi: Hatanın nedeni yükleme betiği ise yükleme betikleri hakkında tam günlükleri denetlemek için Ambari Web Kullanıcı Arabirimini kullanın.

    Daha fazla bilgi için bkz. [Sorun giderme](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

## <a name="remove-hdinsight-applications"></a>HDInsight uygulamalarını kaldırma
HDInsight uygulamaları birkaç yöntemle silinebilir.

### <a name="use-portal"></a>Portal kullanma
**Portalı kullanarak bir uygulamayı kaldırmak için**

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Soldaki menüde **HDInsight Kümeleri**’ne tıklayın.  Bu seçeneği görmüyorsanız **Gözat**’a ve ardından **HDInsight Kümeleri**’ne tıklayın.
3. Uygulamayı yüklediğiniz kümeye tıklayın.
4. **Ayarlar** dikey penceresinde **Genel** kategorisi altındaki **Uygulamalar**’a tıklayın. Yüklü uygulamalar listesini görmeniz gerekir. Bu makalede, **yüklü uygulamalar** dikey penceresinde belirtilen **ton** .
5. Kaldırmak istediğiniz uygulamaya sağ tıklayın ve ardından **Sil**’e tıklayın.
6. Onaylamak için **Evet**’e tıklayın.

Portaldan kümeyi veya uygulamayı içeren kaynak grubunu da silebilirsiniz.

### <a name="use-azure-powershell"></a>Azure PowerShell’i kullanma
Azure PowerShell kullanarak kümeyi veya kaynak grubunu silebilirsiniz. Bkz. [Azure PowerShell kullanarak küme silme](hdinsight-administer-use-powershell.md#delete-clusters).

### <a name="use-azure-cli"></a>Azure CLI kullanma
Azure CLI kullanarak kümeyi veya kaynak grubunu silebilirsiniz. Bkz. [Azure CLI kullanarak küme silme](hdinsight-administer-use-command-line.md#delete-clusters).

## <a name="next-steps"></a>Sonraki adımlar
* [MSDN: HDInsight uygulaması yükleme](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight uygulamalarını dağıtmaya yönelik Resource Manager şablonlarını nasıl geliştireceğinizi öğrenin.
* [HDInsight uygulamaları yükleme](hdinsight-apps-install-applications.md): HDInsight uygulamalarını kümelerinize yükleme hakkında bilgi alın.
* [HDInsight uygulamalarını yayımlama](hdinsight-apps-publish-applications.md): Özel HDInsight uygulamalarınızı Azure Marketi’nde nasıl yayımlayacağınızı öğrenin.
* [Betik Eylemi kullanarak Linux tabanlı HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md): ek uygulamalar yüklemek için Betik Eyleminin nasıl kullanılacağını öğrenin.
* [HDInsight 'ta Kaynak Yöneticisi şablonları kullanarak Linux tabanlı Apache Hadoop kümeleri oluşturma](hdinsight-hadoop-create-linux-clusters-arm-templates.md): HDInsight kümeleri oluşturmak için Kaynak Yöneticisi şablonlarının nasıl çağrılacağını öğrenin.
* [HDInsight’ta boş kenar düğümleri kullanma](hdinsight-apps-use-edge-node.md): HDInsight kümesine erişmek, HDInsight uygulamalarını test etmek ve HDInsight uygulamalarını barındırmak için boş bir kenar düğümünü kullanmayı öğrenin.
