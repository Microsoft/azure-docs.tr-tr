---
title: HDInsight 'ta ML hizmetlerini çalıştırma-Azure
description: Azure HDInsight 'ta ML Hizmetleri ile tahmine dayalı hale getirmek için veri modelinizi nasıl gerçekleştireceğinizi öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: a05bcdef2b7456fbab852e9728c156e57f847f57
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123559"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Azure HDInsight 'ta ML hizmetleri kümesini operationleştir

HDInsight 'ta ML hizmetleri kümesini kullandıktan sonra veri modelinizi tamamladıktan sonra, tahmine dayalı hale getirmek için modeli çalıştırabilirsiniz. Bu makalede, bu görevin nasıl gerçekleştirileceği hakkında yönergeler sağlanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir ML Hizmetleri kümesi. Bkz. [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md) ve **küme türü**için **ml Hizmetleri** seçme.

* Bir Secure Shell (SSH) istemcisi: Bir SSH istemcisi HDInsight kümesine uzaktan bağlanmak ve komutları doğrudan küme üzerinde çalıştırmak için kullanılır. Daha fazla bilgi için bkz. [HDInsight ile SSH kullanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Tek Box yapılandırmasıyla ML hizmetleri kümesini çalıştırma

> [!NOTE]  
> Aşağıdaki adımlar R Server 9,0 ve 9,1 ML Server için geçerlidir. ML Server 9,3 için, işlem [kullanım yapılandırmasını yönetmek üzere yönetim aracını kullanma](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch)konusuna bakın.

1. Kenar düğümüne SSH uygulayın.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Azure HDInsight ile SSH kullanma hakkında yönergeler için bkz [. HDInsight Ile SSH kullanma.](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. İlgili sürümün dizinini değiştirin ve net dll 'yi sudo edin: 

    - Microsoft ML Server 9,1 için:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Microsoft R Server 9.0 için:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Seçim yapabileceğiniz seçeneklerle karşılaşırsınız. **Operationalization için ml Server yapılandırmak**üzere aşağıdaki ekran görüntüsünde gösterildiği gibi ilk seçeneği belirleyin.

    ![R Server yönetim yardımcı programı seçme](./media/r-server-operationalize/admin-util-one-box-1.png)

1. Artık ML Server nasıl kullanıma sunmayı istediğinizi belirleme seçeneği sunulur. Görüntülenen seçeneklerden ilk birini girerek **seçin.**

    ![R Server yönetim yardımcı programı operationınitialize](./media/r-server-operationalize/admin-util-one-box-2.png)

1. İstendiğinde, bir yerel yönetici kullanıcısının parolasını girin ve yeniden girin.

1. İşlemin başarılı olduğunu öneren çıktılar görmeniz gerekir. Ayrıca, menüden başka bir seçenek de seçmeniz istenir. Ana menüye dönmek için E ' yi seçin.

    ![R Server yönetim yardımcı programı başarılı](./media/r-server-operationalize/admin-util-one-box-3.png)

1. İsteğe bağlı olarak, aşağıdaki gibi bir tanılama testi çalıştırarak tanılama denetimleri gerçekleştirebilirsiniz:

    a. Tanılama testlerini çalıştırmak için ana menüden **6** ' yı seçin.

    ![R Server yönetim yardımcı programı tanılaması](./media/r-server-operationalize/hdinsight-diagnostic1.png)

    b. Tanılama Testleri menüsünden **bir**seçin. İstendiğinde, yerel yönetici kullanıcısı için verdiğiniz parolayı girin.

    ![R Server yönetim yardımcı programı sınaması](./media/r-server-operationalize/hdinsight-diagnostic2.png)

    c. Çıktıda genel sistem durumunun bir pass olduğunu gösterdiğini doğrulayın.

    ![R Server yönetim yardımcı programı başarılı](./media/r-server-operationalize/hdinsight-diagnostic3.png)

    d. Gösterilen menü seçeneklerinde, ana menüye dönmek için **E** yazın ve ardından Yönetici yardımcı programından çıkmak için **8** yazın.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Apache Spark Web hizmetini tükettiren uzun gecikmeler

Apache Spark işlem bağlamında mrsdeploy işlevleriyle oluşturulmuş bir Web hizmetini kullanmaya çalışırken uzun gecikmeler yaşarsanız, bazı eksik klasörleri eklemeniz gerekebilir. Spark uygulaması mrsdeoploy işlevleri kullanılarak bir web hizmetinden çağrıldığında '*rserve2*' adlı bir kullanıcıya ait oluyor. Bu soruna geçici bir çözüm olarak:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Bu aşamada kullanıma hazır hale getirme yapılandırması tamamlanmıştır. Artık, uç düğümünde işlem `mrsdeploy` kullanımına bağlanmak ve [Uzaktan yürütme](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) ile [Web Hizmetleri](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)gibi özellikleri kullanmaya başlamak için ristemcinizdeki paketi kullanabilirsiniz. Kümenizin bir sanal ağda ayarlanıp ayarlanmamasına bağlı olarak, SSH oturumu aracılığıyla bağlantı noktası iletme tüneli ayarlamanız gerekebilir. Aşağıdaki bölümlerde bu tüneli nasıl kuracağınız açıklanmaktadır.

### <a name="ml-services-cluster-on-virtual-network"></a>Sanal ağdaki ML Hizmetleri kümesi

12800 numaralı bağlantı noktası üzerinden kenar düğümüne trafik akışına izin verdiğinizden emin olun. Bu şekilde, Kullanıma Hazır Hale Getirme özelliğine bağlanmak için kenar düğümünü kullanabilirsiniz.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


`remoteLogin()` kenar düğümüne bağlanamadığı halde kenar düğümüne SSH uygulayabiliyorsanız, 12800 numaralı bağlantı noktası üzerinde trafiğe izin veren kuralın doğru şekilde ayarlanıp ayarlanmadığını doğrulamanız gerekir. Sorunla karşılaşmaya devam ederseniz, SSH üzerinden bağlantı noktası iletme tüneli oluşturarak bir geçici çözüm uygulayabilirsiniz. Yönergeler için aşağıdaki bölüme bakın:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>ML Hizmetleri kümesi sanal ağda ayarlanmadı

Kümeniz sanal üzerinde ayarlanmamışsa veya sanal ağ üzerinden bağlantı kurma sorunları yaşıyorsanız, SSH bağlantı noktası iletme tünelini kullanabilirsiniz:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

SSH oturumunuz etkin olduktan sonra, yerel makinenizin bağlantı noktası 12800 trafiği, SSH oturumunda sınır düğümünün bağlantı noktası 12800 ' e iletilir. `remoteLogin()` yönteminizde `127.0.0.1:12800` kullandığınızdan emin olun. Bu işlem, bağlantı noktası iletme yoluyla kenar düğümünün işlemleştirme 'e oturum açar.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>HDInsight çalışan düğümlerinde operationınılan işlem düğümlerini ölçeklendirme

İşlem düğümlerini ölçeklendirmek için, önce çalışan düğümlerinin yetkisini alın ve ardından kullanımdan kaldırılan çalışan düğümlerinde işlem düğümlerini yapılandırın.

### <a name="step-1-decommission-the-worker-nodes"></a>1\. adım: Çalışan düğümlerinin yetkisini alma

ML Hizmetleri kümesi [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)aracılığıyla yönetilmiyor. Çalışan düğümlerinin yetkisi yoksa YARN Kaynak Yöneticisi, sunucu tarafından alınan kaynakların farkında olmadığı için beklendiği gibi çalışmaz. Bu durumu önlemek için, işlem düğümlerini ölçeklendirmeden önce çalışan düğümlerinin yetkisinin alınması önerilir.

Çalışan düğümlerinin yetkisini almak için aşağıdaki adımları izleyin:

1. Kümenin ambarı konsolunda oturum açın ve **konaklar** sekmesine tıklayın.

1. Çalışan düğümlerini seçin (kullanımdan kaldırılacak).

1. **Eylemler**seçiliKonaklar > Konaklarbakımmodunuaç > ' a tıklayın. >  Örneğin, aşağıdaki görüntüde yetkisini almak üzere wn3 ve wn4 seçilmiştir.  

   ![Apache ambarı bakım modunu aç](./media/r-server-operationalize/get-started-operationalization.png)  

* **Seçili** > Eylemler ' i seçin ve sonra da yük al ' a tıklayın > > 
* Seçili **Eylemler** > seçin**NodeManager** **Konakları** > > **Al**' a tıklayın.
* **Seçili** > eylemler seçin >, gün sonra Durdur ' a tıklayın. > 
* Seçili **Eylemler** > ' i seçin**nodeyöneticileri** **Konakları** > > **Durdur**' a tıklayın.
* **Eylemler** > **Seçili** konaklar Konaklar ' ı seçin > tüm bileşenleri Durdur ' a tıklayın. > 
* Çalışan düğümlerinin seçimini kaldırın ve baş düğümleri seçin.
*  >  **Eylemler** seçili Konaklar ' ı seçin > "konaklar tüm bileşenleri yeniden Başlat. > 

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>2\. adım: Her kullanımdan kaldırılan çalışan düğümlerinde işlem düğümlerini yapılandırın

1. Yetkisi alınan her çalışan düğümüne SSH uygulayın.

1. Sahip olduğunuz ML Hizmetleri kümesi için ilgili DLL 'yi kullanarak yönetici yardımcı programını çalıştırın. ML Server 9,1 için aşağıdakileri çalıştırın:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. **Operationalization için ml Server yapılandırma**seçeneğini belirlemek üzere **1** girin.

1. Seçeneği`C. Compute node`belirlemek için **C** girin. Bu işlem çalışan düğümündeki işlem düğümünü yapılandırır.

1. Yönetim Yardımcı Programından çıkın.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>3\. adım: Web düğümüne işlem düğümleri ayrıntıları ekleme

Kullanımdan kaldırılan tüm çalışan düğümleri işlem düğümünü çalıştıracak şekilde yapılandırıldıktan sonra, kenar düğümüne geri dönün ve kullanımdan kaldırılan çalışan düğümlerinin IP adreslerini ML Server Web düğümünün yapılandırmasına ekleyin:

1. Kenar düğümüne SSH uygulayın.

1. `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json` öğesini çalıştırın.

1. "URI 'Ler" bölümünü arayın ve çalışan düğümünün IP ve bağlantı noktası ayrıntılarını ekleyin.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight üzerinde ML Services kümesini yönetme](r-server-hdinsight-manage.md)
* [HDInsight üzerinde ML Services kümesi için işlem bağlamı seçenekleri](r-server-compute-contexts.md)
* [HDInsight üzerinde ML Services kümesi için Azure Depolama seçenekleri](r-server-storage.md)
