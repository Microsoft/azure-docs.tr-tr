---
title: Azure HDInsight 'a özel Apache Hadoop uygulamalarınızı yükler
description: Azure HDInsight 'ta Apache Hadoop kümeleri için HDInsight uygulamalarını yüklemeyi öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 19dd5bf94b524ff3eb6eb601c77b503a0040bd75
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867653"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Azure HDInsight 'ta özel Apache Hadoop uygulamaları yüklemeyi

Bu makalede, Azure HDInsight 'a Azure portal yayımlanmayan bir [Apache Hadoop](https://hadoop.apache.org/) uygulamasının nasıl yükleneceğini öğreneceksiniz. Bu makaleye yükleyeceğiniz uygulama [ton](https://gethue.com/)' dır.

An HDInsight uygulama, kullanıcıların bir HDInsight kümesine yükleyebir uygulamadır.  Bu uygulamalar Microsoft veya bağımsız yazılım satıcıları (ISV) tarafından ya da sizin tarafınızdan geliştirilebilir.  

## <a name="prerequisites"></a>Önkoşullar

HDInsight uygulamalarını mevcut bir HDInsight kümesine yüklemek istiyorsanız bir HDInsight kümesine sahip olmanız gerekir. Küme oluşturmak için bkz. [Küme oluşturma](hadoop/apache-hadoop-linux-tutorial-get-started.md). HDInsight uygulamalarını ayrıca bir HDInsight kümesi oluştururken yükleyebilirsiniz.

## <a name="install-hdinsight-applications"></a>HDInsight uygulamaları yükleme

HDInsight uygulamaları bir küme oluşturduğunuzda veya var olan bir HDInsight kümesine yüklenebilir. Azure Resource Manager şablonlarını tanımlamak için bkz. [MSDN: HDInsight uygulaması yükleme](/rest/api/hdinsight/hdinsight-application).

Bu uygulamayı (Hue) dağıtmak için gerekli dosyalar:

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): HDInsight uygulamasını yüklemek için Azure Resource Manager şablonu. Kendi Azure Resource Manager şablonunuzu geliştirmek için bkz. [MSDN: HDInsight uygulaması yükleme](/rest/api/hdinsight/hdinsight-application).
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Kenar düğümünü yapılandırmak üzere Resource Manager şablonu tarafından çağrılan Betik eylemi.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): hui-install_v0.sh dosyasından çağrılan Hue ikili dosyası.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): hui-install_v0.sh dosyasından çağrılan Hue ikili dosyası.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): hui-install_v0.sh dosyasından çağrılan bir örnek web uygulaması (Tomcat).

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>Var olan bir HDInsight kümesine Hue yüklemek için

1. Azure 'da oturum açmak için aşağıdaki görüntüyü seçin ve Azure portal Kaynak Yöneticisi şablonu açın.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Kaynak Yöneticisi şablonu konumunda bulunur [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue) .  Bu Resource Manager şablonunun nasıl yazılacağını öğrenmek için bkz. [MSDN: HDInsight uygulaması yükleme](/rest/api/hdinsight/hdinsight-application).

1. Açılır listeden kümenizi içeren mevcut **kaynak grubunu** seçin. Kümeyle aynı kaynak grubunu kullanmak gereklidir.

1. Uygulamayı yüklemek istediğiniz kümenin adını girin. Bu küme var olan bir küme olmalıdır.

1. **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum** onay kutusunu seçin.

1. **Satın al**'ı seçin.

Yükleme durumunu portal panosuna sabitlenmiş kutucuktan ve portal bildiriminden görebilirsiniz (portalın üst kısmındaki zil simgesine tıklayın).  Uygulamanın yüklenmesi yaklaşık 10 dakika sürer.

### <a name="to-install-hue-while-creating-a-cluster"></a>Küme oluştururken Hue yüklemek için

1. Azure 'da oturum açmak için aşağıdaki görüntüyü seçin ve Azure portal Kaynak Yöneticisi şablonu açın.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Kaynak Yöneticisi şablonu konumunda bulunur [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json) .  Bu Resource Manager şablonunun nasıl yazılacağını öğrenmek için bkz. [MSDN: HDInsight uygulaması yükleme](/rest/api/hdinsight/hdinsight-application).

2. Küme oluşturmak ve Hue uygulamasını yüklemek için yönergeleri izleyin. HDInsight kümeleri oluşturma hakkında daha fazla bilgi için bkz. [HDInsight’ta Linux tabanlı Hadoop kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="other-installation-methods"></a>Diğer yükleme yöntemleri

Resource Manager şablonlarını çağırmak için Azure portalına ek olarak [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) ve [Azure CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) kullanabilirsiniz.

## <a name="validate-the-installation"></a>Yüklemeyi doğrulama

Uygulama yüklemesini doğrulamak için Azure portalında uygulama durumunu denetleyebilirsiniz. Ayrıca, tüm HTTP uç noktalarını beklenen şekilde ve varsa Web sayfasını da doğrulayabilirsiniz.

**Ton** için aşağıdaki adımları kullanabilirsiniz:

### <a name="azure-portal"></a>Azure portalı

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Uygulamayı yüklediğiniz kümeyi seçin.
1. **Ayarlar** menüsünde, **uygulamalar**' ı seçin.
1. Özellikleri görüntülemek için listeden **ton** ' ı seçin.  
1. Web sitesini doğrulamak için Web sayfası bağlantısını seçin.

### <a name="azure-cli"></a>Azure CLI’si

`CLUSTERNAME`Ve `RESOURCEGROUP` değerlerini ilgili değerlerle değiştirin ve ardından aşağıdaki komutları girin:

* HDInsight kümesinin tüm uygulamalarını listeler.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* Belirtilen uygulamanın özelliklerini almak için.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>Yükleme sorunlarını giderme

Uygulama yükleme durumunu portal bildiriminden denetleyebilirsiniz (Portalın üst kısmındaki zil simgesine tıklayın).

Uygulama yüklemesi başarısız olursa, üç konumdan hata iletilerini ve hata ayıklama bilgilerini görebilirsiniz:

* HDInsight Uygulamaları: genel hata bilgileri.

    Portaldan kümeyi açın ve ayarlardan uygulamalar ' ı seçin:

    :::image type="content" source="./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png" alt-text="hdinsight applications application installation error":::

* HDInsight betik eylemi: HDInsight Uygulamalarının hata iletisi bir betik eylemi hatası belirtiyorsa betik eylemleri bölmesinde betik hatasına ilişkin daha fazla bilgi sunulur.

    Ayarlardan betik eylemi ' ni seçin. Betik eylemi geçmişinde hata iletileri gösterilir

    :::image type="content" source="./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png" alt-text="hdinsight applications script action error":::

* Apache ambarı Web Kullanıcı arabirimi: Install betiği hatanın nedeniydi, Install betiklerine ilişkin tam günlükleri denetlemek için, ambarı Web Kullanıcı arabirimini kullanın.

    Daha fazla bilgi için bkz. [betik eylemlerine sorun giderme](./troubleshoot-script-action.md).

## <a name="remove-hdinsight-applications"></a>HDInsight uygulamalarını kaldırma

### <a name="azure-portal"></a>Azure portalı

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Uygulamayı yüklediğiniz kümeyi seçin.
1. **Ayarlar** menüsünde, **uygulamalar**' ı seçin.
1. Kaldırmak istediğiniz uygulamaya sağ tıklayın ve ardından **Sil**' i seçin.
1. Onaylamak için **Evet**’i seçin.

### <a name="azure-cli"></a>Azure CLI’si

`NAME`, `CLUSTERNAME` Ve `RESOURCEGROUP` değerlerini ilgili değerlerle değiştirin ve aşağıdaki komutu girin:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>Sonraki adımlar

* [MSDN: HDInsight uygulaması yükleme](/rest/api/hdinsight/hdinsight-application): HDInsight uygulamalarını dağıtmaya yönelik Resource Manager şablonlarını nasıl geliştireceğinizi öğrenin.
* [HDInsight uygulamaları yükleme](hdinsight-apps-install-applications.md): HDInsight uygulamalarını kümelerinize yükleme hakkında bilgi alın.
* [HDInsight uygulamalarını yayımlama](hdinsight-apps-publish-applications.md): Özel HDInsight uygulamalarınızı Azure Marketi’nde nasıl yayımlayacağınızı öğrenin.
* [Betik Eylemi kullanarak Linux tabanlı HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md): ek uygulamalar yüklemek için Betik Eyleminin nasıl kullanılacağını öğrenin.
* [HDInsight 'ta Kaynak Yöneticisi şablonları kullanarak Linux tabanlı Apache Hadoop kümeleri oluşturma](hdinsight-hadoop-create-linux-clusters-arm-templates.md): HDInsight kümeleri oluşturmak için Kaynak Yöneticisi şablonlarının nasıl çağrılacağını öğrenin.
* [HDInsight’ta boş kenar düğümleri kullanma](hdinsight-apps-use-edge-node.md): HDInsight kümesine erişmek, HDInsight uygulamaları test etmek ve HDInsight uygulamalarını barındırmak için boş bir kenar düğümünü kullanmayı öğrenin.
