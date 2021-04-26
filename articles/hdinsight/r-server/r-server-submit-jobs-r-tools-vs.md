---
title: Visual Studio için R Araçları Azure HDInsight 'tan iş gönderme
description: R işlerini yerel Visual Studio makinenizden bir HDInsight kümesine gönderme.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: ee984de22052076618728fbacfc31b73c18c073a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864695"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Visual Studio için R Araçları’ndan iş gönderme

[Visual Studio için R araçları](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (rtvs), hem [Visual Studio 2017](https://www.visualstudio.com/downloads/), hem de Visual [Studio 2015 güncelleştirme 3](https://go.microsoft.com/fwlink/?LinkId=691129) veya üzeri topluluk (ücretsiz), Professional ve Enterprise sürümleri için ücretsiz, açık kaynaklı bir uzantıdır. RTVS, [Visual Studio 2019](/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?preserve-view=true&view=vs-2019)için kullanılamaz.

RTVS, [R etkileşim penceresi](/visualstudio/rtvs/interactive-repl) (REPL), IntelliSense (kod tamamlama) gibi araçlar sunarak r iş akışınızı geliştirir, ggplot2 ve Ggviz, [r Code hata ayıklama](/visualstudio/rtvs/debugging)gibi r kitaplıkları aracılığıyla [görselleştirme çizmektedir](/visualstudio/rtvs/visualizing-data) .

## <a name="set-up-your-environment"></a>Ortamınızı ayarlama

1. [Visual Studio için R araçları](/visualstudio/rtvs/installing-r-tools-for-visual-studio)'i yükler.

    :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png" alt-text="Visual Studio 2017 ' de RTVS 'yi yükleme" border="true":::

2. *Veri bilimi ve analitik uygulamalar* iş yükünü seçin, ardından **r dil desteğini**, **r geliştirmesi için çalışma zamanı desteğini** ve **Microsoft R Client** seçenekleri seçin.

3. SSH kimlik doğrulaması için ortak ve özel anahtarlara sahip olmanız gerekir.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Makinenize [ml Server](/previous-versions/machine-learning-server/install/r-server-install-windows) . ML Server [`RevoScaleR`](/machine-learning-server/r-reference/revoscaler/revoscaler) ve işlevlerini sağlar `RxSpark` .

5. Yerel istemcinizdeki işlevleri HDInsight kümenize çalıştırmak için bir işlem bağlamı sağlamak üzere [Putty](https://www.putty.org/) ' i Yükle `RevoScaleR` .

6. R araçları için çalışma alanınız için yeni bir düzen sağlayan Visual Studio ortamınıza Veri Bilimi Ayarları uygulama seçeneğiniz vardır.
   1. Geçerli Visual Studio ayarlarınızı kaydetmek için **araçlar > içeri ve dışarı aktarma ayarları** komutunu kullanın, ardından **Seçili ortam ayarlarını dışarı aktar** ' ı seçin ve bir dosya adı belirtin. Bu ayarları geri yüklemek için aynı komutu kullanın ve **Seçili ortam ayarlarını Içeri aktar**' ı seçin.

   2. **R araçları** menü öğesine gidin ve **veri bilimi ayarları..**. seçeneğini belirleyin.

       :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png" alt-text="Visual Studio Veri Bilimi Ayarları" border="true":::

      > [!NOTE]  
      > 1. adımdaki yaklaşımı kullanarak, **veri bilimi ayarları** komutunu tekrarlamak yerine kişiselleştirilmiş veri bilimcu düzeninizi de kaydedebilir ve geri yükleyebilirsiniz.

## <a name="execute-local-r-methods"></a>Yerel R yöntemlerini yürütme

1. HDInsight ML Hizmetleri kümenizi oluşturun.
2. [Rtvs uzantısını](/visualstudio/rtvs/installation)yükler.
3. [Örnek ZIP dosyasını](https://github.com/Microsoft/RTVS-docs/archive/master.zip)indirin.
4. `examples/Examples.sln`Visual Studio 'da çözümü başlatmak için öğesini açın.
5. `1-Getting Started with R.R`Dosyayı `A first look at R` çözüm klasöründe açın.
6. Dosyanın en üstünden başlayarak, her satırı bir kez R Etkileşim penceresine göndermek için CTRL + ENTER tuşlarına basın. Bazı satırlar paketleri yükledikleri sürece biraz zaman alabilir.
    * Alternatif olarak, R dosyasındaki (CTRL + A) tüm satırları seçebilir, sonra tümünü yürütebilir (CTRL + ENTER) veya araç çubuğunda etkileşimli Yürüt simgesini seçebilirsiniz.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png" alt-text="Visual Studio yürütme etkileşimli" border="true":::

7. Betikteki tüm satırları çalıştırdıktan sonra şuna benzer bir çıktı görmeniz gerekir:

    :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png" alt-text="Visual Studio çalışma alanı R araçları" border="true":::

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Bir HDInsight ML Hizmetleri kümesine iş gönderme

PuTTY ile donatılmış bir Windows bilgisayarından Microsoft ML Server/Microsoft R Client kullanarak, `RevoScaleR` Yerel Istemcinizden HDInsight kümenize dağıtılmış işlevleri çalıştıracak bir işlem bağlamı oluşturabilirsiniz. `RxSpark`Kullanıcı adınızı, Apache Hadoop kümesinin Edge düğümünü, SSH anahtarlarını ve benzerlerini belirterek işlem bağlamını oluşturmak için kullanın.

1. HDInsight üzerindeki ML Hizmetleri kenar düğümü adresi, `CLUSTERNAME-ed-ssh.azurehdinsight.net` `CLUSTERNAME` ml Hizmetleri kümenizin adıdır.

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

   :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png" alt-text="Apache Spark, bağlamı ayarlama" border="true":::

1. R Etkileşim penceresinde aşağıdaki komutları yürütün:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Aşağıdakine benzer bir çıktı görmeniz gerekir:

    :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png" alt-text="Başarılı bir RX komutu yürütmesi" border="true":::
a
1. `rxHadoopCopy` `people.json` Dosyayı örnek veri klasöründen yeni oluşturulan klasöre başarıyla kopyalamadığını doğrulayın `/user/RevoShare/newUser` :

    1. Azure 'daki HDInsight ML Hizmetleri kümeniz bölmesinden, sol taraftaki menüden **depolama hesapları** ' nı seçin.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png" alt-text="Azure HDInsight depolama hesapları" border="true":::

    2. Kümenizin varsayılan depolama hesabını seçin ve kapsayıcı/dizin adı ' nı aklınızda yapın.

    3. Depolama hesabı bölmesinizdeki sol taraftaki menüden **kapsayıcılar** ' ı seçin.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png" alt-text="Azure HDInsight depolama kapsayıcıları" border="true":::

    4. Kümenizin kapsayıcı adını seçin, **Kullanıcı** klasörüne gidin (listenin en altında bulunan *daha fazla yükle* ' ye tıklamanız gerekebilir), ardından, *iptal edilebilir*' i ve ardından **Newuser**' ı seçin. `people.json`Dosya `newUser` klasöründe görüntülenmelidir.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png" alt-text="HDInsight dosya klasörü konumunu kopyaladı" border="true":::

1. Geçerli Apache Spark bağlamını kullanmayı bitirdikten sonra, onu durdurmanız gerekir. Aynı anda birden çok bağlam çalıştıramazsınız.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight üzerinde ML Hizmetleri için işlem bağlamı seçenekleri](r-server-compute-contexts.md)
* [Scaler ve parlak r birleştirmek](../hdinsight-hadoop-r-scaler-sparkr.md) hava yolu Uçuş gecikmesi tahminlerinin bir örneğini sağlar.