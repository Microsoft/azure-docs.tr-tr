---
title: Visual Studio için R Araçları Azure HDInsight 'tan iş gönderme
description: R işlerini yerel Visual Studio makinenizden bir HDInsight kümesine gönderme.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 1323e46d734d047f68652d0b21902e03182e6b62
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130248"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Visual Studio için R Araçları’ndan iş gönderme

[Visual Studio için R araçları](https://www.visualstudio.com/vs/rtvs/) (RTVS), hem [Visual studio 2017](https://www.visualstudio.com/downloads/), hem de [Visual Studio 2015 güncelleştirme 3](https://go.microsoft.com/fwlink/?LinkId=691129) veya üzeri için topluluk (ücretsiz), Professional ve Enterprise sürümleri için ücretsiz, açık kaynaklı bir uzantıdır. RTVS, [Visual Studio 2019](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019)için kullanılamaz.

RTVS, [R etkileşim penceresi](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), IntelliSense (kod tamamlama) gibi araçlar sunarak r iş akışınızı geliştirir, ggplot2 ve Ggviz, [r Code hata ayıklama](https://docs.microsoft.com/visualstudio/rtvs/debugging)gibi r kitaplıkları aracılığıyla [görselleştirme çizmektedir](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) .

## <a name="set-up-your-environment"></a>Ortamınızı ayarlama

1. [Visual Studio için R araçları](https://docs.microsoft.com/visualstudio/rtvs/installation)'i yükler.

    ![Visual Studio 2017 ' de RTVS 'yi yükleme](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. *Veri bilimi ve analitik uygulamalar* iş yükünü seçin, ardından **r dil desteğini**, **r geliştirmesi için çalışma zamanı desteğini**ve **Microsoft R Client** seçenekleri seçin.

3. SSH kimlik doğrulaması için ortak ve özel anahtarlara sahip olmanız gerekir.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Makinenize [ml Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) . ML Server [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) ve`RxSpark` işlevlerini sağlar.

5. Yerel istemcinizdeki işlevleri HDInsight kümenize çalıştırmak `RevoScaleR` için bir işlem bağlamı sağlamak üzere [Putty](https://www.putty.org/) ' i yükle.

6. R araçları için çalışma alanınız için yeni bir düzen sağlayan Visual Studio ortamınıza Veri Bilimi Ayarları uygulama seçeneğiniz vardır.
   1. Geçerli Visual Studio ayarlarınızı kaydetmek için **araçlar > içeri ve dışarı aktarma ayarları** komutunu kullanın, ardından **Seçili ortam ayarlarını dışarı aktar** ' ı seçin ve bir dosya adı belirtin. Bu ayarları geri yüklemek için aynı komutu kullanın ve **Seçili ortam ayarlarını Içeri aktar**' ı seçin.

   2. **R araçları** menü öğesine gidin ve **veri bilimi ayarları..** . seçeneğini belirleyin.

       ![Visual Studio Veri Bilimi Ayarları](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > 1\. adımdaki yaklaşımı kullanarak, **veri bilimi ayarları** komutunu tekrarlamak yerine kişiselleştirilmiş veri bilimcu düzeninizi de kaydedebilir ve geri yükleyebilirsiniz.

## <a name="execute-local-r-methods"></a>Yerel R yöntemlerini yürütme

1. HDInsight ML Hizmetleri kümenizi oluşturun.
2. [Rtvs uzantısını](https://docs.microsoft.com/visualstudio/rtvs/installation)yükler.
3. [Örnek ZIP dosyasını](https://github.com/Microsoft/RTVS-docs/archive/master.zip)indirin.
4. Visual `examples/Examples.sln` Studio 'da çözümü başlatmak için öğesini açın.
5. `1-Getting Started with R.R` Dosyayı`A first look at R` çözüm klasöründe açın.
6. Dosyanın en üstünden başlayarak, her satırı bir kez R Etkileşim penceresine göndermek için CTRL + ENTER tuşlarına basın. Bazı satırlar paketleri yükledikleri sürece biraz zaman alabilir.
    * Alternatif olarak, R dosyasındaki (CTRL + A) tüm satırları seçebilir, sonra tümünü yürütebilir (CTRL + ENTER) veya araç çubuğunda etkileşimli Yürüt simgesini seçebilirsiniz.

        ![Visual Studio yürütme etkileşimli](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. Betikteki tüm satırları çalıştırdıktan sonra şuna benzer bir çıktı görmeniz gerekir:

    ![Visual Studio çalışma alanı R araçları](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Bir HDInsight ML Hizmetleri kümesine iş gönderme

Putty ile donatılmış bir Windows bilgisayarından Microsoft ml Server/Microsoft R Client kullanarak, yerel istemcinizden HDInsight kümenize dağıtılmış `RevoScaleR` işlevleri çalıştıracak bir işlem bağlamı oluşturabilirsiniz. Kullanıcı `RxSpark` adınızı, Apache Hadoop kümesinin Edge düğümünü, SSH anahtarlarını ve benzerlerini belirterek işlem bağlamını oluşturmak için kullanın.

1. `CLUSTERNAME-ed-ssh.azurehdinsight.net` HDInsight`CLUSTERNAME` üzerindeki ml Hizmetleri kenar düğümü adresi, ml Hizmetleri kümenizin adıdır.

1. Aşağıdaki kodu Visual Studio 'daki R Etkileşim penceresine yapıştırarak, kurulum değişkenlerinin değerlerini ortamınızla eşleşecek şekilde değiştirerek.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   ![Apache Spark, bağlamı ayarlama](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. R Etkileşim penceresinde aşağıdaki komutları yürütün:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Aşağıdakine benzer bir çıktı görmeniz gerekir:

    ![Başarılı bir RX komutu](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) yürütme a
1. Dosyayı örnek veri klasöründen yeni oluşturulan `/user/RevoShare/newUser` klasöre başarıylakopyalamadığınıdoğrulayın:`rxHadoopCopy` `people.json`

    1. Azure 'daki HDInsight ML Hizmetleri kümeniz bölmesinden, sol taraftaki menüden **depolama hesapları** ' nı seçin.

        ![Azure HDInsight depolama hesapları](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Kümenizin varsayılan depolama hesabını seçin ve kapsayıcı/dizin adı ' nı aklınızda yapın.

    3. Depolama hesabı bölmesinizdeki sol taraftaki menüden **kapsayıcılar** ' ı seçin.

        ![Azure HDInsight depolama kapsayıcıları](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Kümenizin kapsayıcı adını seçin, **Kullanıcı** klasörüne gidin (listenin en altında bulunan *daha fazla yükle* ' ye tıklamanız gerekebilir), ardından, *iptal edilebilir*' i ve ardından **Newuser**' ı seçin. `people.json` Dosya `newUser` klasöründe görüntülenmelidir.

        ![HDInsight dosya klasörü konumunu kopyaladı](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Geçerli Apache Spark bağlamını kullanmayı bitirdikten sonra, onu durdurmanız gerekir. Aynı anda birden çok bağlam çalıştıramazsınız.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight üzerinde ML Hizmetleri için işlem bağlamı seçenekleri](r-server-compute-contexts.md)
* [Scaler ve parlak r birleştirmek](../hdinsight-hadoop-r-scaler-sparkr.md) hava yolu Uçuş gecikmesi tahminlerinin bir örneğini sağlar.
