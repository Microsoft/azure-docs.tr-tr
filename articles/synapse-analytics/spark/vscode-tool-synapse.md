---
title: VSCode için bkz. Spark & Hive araçları (Spark uygulaması)
description: Öğretici-Python 'da yazılmış Spark uygulamaları geliştirmek ve bunları sunucusuz bir Apache Spark havuzuna göndermek için VSCode için Spark & Hive araçları 'nı kullanın.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 09/03/2020
ms.openlocfilehash: e5d335ee14709ec330405419f5be8ac5fbd6ce75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943768"
---
# <a name="tutorial-create-an-apache-spark-applications-with-vscode-using-a-synapse-workspace"></a>Öğretici: SYNAPSE çalışma alanı kullanarak VSCode ile Apache Spark uygulamaları oluşturma

Visual Studio Code için Apache Spark & Hive araçlarını kullanmayı öğrenin. Apache Spark için Apache Hive Batch işleri, etkileşimli Hive sorguları ve PySpark betikleri oluşturmak ve göndermek için araçları kullanın. İlk olarak, Visual Studio Code ' de Spark & Hive araçlarının nasıl yükleneceğini açıklayacağız. Daha sonra Spark & Hive araçlarına nasıl iş gönderirsiniz.

Spark & Hive araçları, Visual Studio Code tarafından desteklenen platformlar üzerine yüklenebilir. Farklı platformlar için aşağıdaki önkoşullara göz önünde edin.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlamak için aşağıdaki öğeler gereklidir:

- Sunucusuz Apache Spark Havuzu. Sunucusuz Apache Spark havuzu oluşturmak için, bkz. [Azure Portal kullanarak Apache Spark havuzu oluşturma](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono yalnızca Linux ve macOS için gereklidir.
- [Visual Studio Code Için PySpark etkileşimli ortamı](../../hdinsight/set-up-pyspark-interactive-environment.md).
- Yerel bir dizin. Bu makalede  **C:\HD\Synaseexample** kullanılmıştır.

## <a name="install-spark--hive-tools"></a>Spark & Hive araçları 'nı yükler

Önkoşulları karşıladıktan sonra, aşağıdaki adımları izleyerek Visual Studio Code için Spark & Hive araçları 'nı yükleyebilirsiniz:

1. Visual Studio Code’u açın.

2. Menü çubuğundan **Görünüm**  >  **uzantıları**' na gidin.

3. Arama kutusuna **Spark & Hive** girin.

4. Arama sonuçlarından **Spark & Hive araçları** ' nı seçin ve ardından **Install**' ı seçin:

     ![Visual Studio Code Python yüklemesi için Spark & Hive](./media/vscode-tool-synapse/install-hdInsight-plugin.png)

5. Gerektiğinde **yeniden yükle** ' yi seçin.

## <a name="open-a-work-folder"></a>Bir iş klasörü açın

Bir iş klasörü açmak ve Visual Studio Code bir dosya oluşturmak için aşağıdaki adımları izleyin:

1. Menü çubuğundan **Dosya** aç klasörü ' ne gidin  >  **...**  >  **C:\HD\Synaseexample** ve ardından **Klasör Seç** düğmesini seçin. Klasör sol taraftaki **Gezgin** görünümünde görüntülenir.

2. **Gezgin** görünümü ' nde, **Synaseexample** klasörünü seçin ve sonra Iş klasörünün yanındaki **yeni dosya** simgesini seçin:

     ![Visual Studio Code yeni dosya simgesi](./media/vscode-tool-synapse/visual-studio-code-new-file.png)

3. Yeni dosyayı `.py` (Spark betiği) dosya uzantısını kullanarak adlandırın. Bu örnek, **HelloWorld.py** kullanır.

## <a name="connect-to-your-spark-pools"></a>Spark havuzlarınız ile bağlantı kurmak

Spark havuzlarınız ile bağlantı kurmak için Azure aboneliğinde oturum açın.

### <a name="sign-in-to-your-azure-subscription"></a>Azure aboneliğinizde oturum açın

Azure 'a bağlanmak için şu adımları izleyin:

1. Menü çubuğundan, **görüntüleme**  >  **komut paleti...**' a gidin ve **Azure: oturum aç**' ı girin:

     ![Visual Studio Code oturum açma için Spark & Hive araçları](./media/vscode-tool-synapse/hdinsight-for-vscode-extension-login.png)

2. Azure 'da oturum açmak için oturum açma yönergelerini izleyin. Bağlandıktan sonra, Azure hesabınızın adı Visual Studio Code penceresinin altındaki durum çubuğunda görüntülenir.

## <a name="set-the-default-spark-pool"></a>Varsayılan Spark havuzunu ayarlama

1. Kapalıysa [daha önce](#open-a-work-folder)açıklanan **Synaseexample** klasörünü yeniden açın.  

2. [Daha önce](#open-a-work-folder)oluşturulan **HelloWorld.py** dosyasını seçin. Betik düzenleyicisinde açılır.

3. Betik Düzenleyicisi ' ne sağ tıklayın ve ardından **SYNAPSE: varsayılan Spark havuzunu ayarla**' yı seçin.  

4. Henüz yapmadıysanız Azure hesabınıza [bağlanın](#connect-to-your-spark-pools) .

5. Geçerli betik dosyası için varsayılan Spark havuzu olarak bir Spark havuzu seçin. Araçlar yapılandırma dosyasında **.VSCode\settings.js** otomatik olarak güncelleştirir:

     ![Varsayılan küme yapılandırmasını ayarla](./media/vscode-tool-synapse/set-default-cluster-configuration.png)

## <a name="submit-interactive-synapse-pyspark-queries-to-spark-pool"></a>INTERACTIVE SYNAPSE PySpark sorgularını Spark havuzuna gönder

Kullanıcılar, Spark havuzunda SYNAPSE PySpark etkileşimli işlemini aşağıdaki yollarla gerçekleştirebilir:

### <a name="using-the-synapse-pyspark-interactive-command-in-py-file"></a>Kopyala dosyasında SYNAPSE PySpark etkileşimli komutunu kullanma
Sorguları göndermek için PySpark etkileşimli komutunu kullanarak şu adımları izleyin:

1. Kapalıysa [daha önce](#open-a-work-folder)açıklanan **Synaseexample** klasörünü yeniden açın.  

2. [Önceki](#open-a-work-folder) adımları izleyerek yeni bir **HelloWorld.py** dosyası oluşturun.

3. Aşağıdaki kodu kopyalayıp betik dosyasına yapıştırın:

```python
import sys
from operator import add
from pyspark.sql import SparkSession, Row
 
spark = SparkSession\
 .builder\
 .appName("PythonWordCount")\
 .getOrCreate()
 
data = [Row(col1='pyspark and spark', col2=1), Row(col1='pyspark', col2=2), Row(col1='spark vs hadoop', col2=2), Row(col1='spark', col2=2), Row(col1='hadoop', col2=2)]
df = spark.createDataFrame(data)
lines = df.rdd.map(lambda r: r[0])
 
counters = lines.flatMap(lambda x: x.split(' ')) \
 .map(lambda x: (x, 1)) \
 .reduceByKey(add)
 
output = counters.collect()
sortedCollection = sorted(output, key = lambda r: r[1], reverse = True)
 
for (word, count) in sortedCollection:
 print("%s: %i" % (word, count))
```

4. PySpark/SYNAPSE Pyspark çekirdeğini yüklemek için istem pencerenin sağ alt köşesinde görüntülenir. PySpark/SYNAPSE Pyspark yüklemelerine devam etmek için, **yükleme** düğmesine tıklayabilirsiniz; veya **Atla** düğmesine tıklayarak bu adımı atlayın.

     ![pyspark çekirdeğini yükler](./media/vscode-tool-synapse/install-the-pyspark-kernel.png)

5. Daha sonra yüklemeniz gerekiyorsa, **Dosya**  >  **tercihi**  >  **ayarları**' na gidebilir ve ardından **HDInsight: ayarlarda pyspark yükleme işlemini etkinleştir** ' i kaldırabilirsiniz. 
    
     ![pyspark yüklemesini atlamayı etkinleştir](./media/vscode-tool-synapse/enable-skip-pyspark-installation.png)

6. Yükleme adım 4 ' te başarılı olursa, pencerenin sağ alt köşesinde "PySpark/SYNAPSE Pyspark başarıyla yüklendi" ileti kutusu görüntülenir. Pencereyi yeniden yüklemek için **yeniden yükle** düğmesine tıklayın.

     ![pyspark başarıyla yüklendi](./media/vscode-tool-synapse/pyspark-kernel-installed-successfully.png)

7. Menü çubuğundan **görüntüle**  >  **komut paleti...** ' ya gidin veya **SHIFT + CTRL + P** klavye kısayolunu kullanın ve **Python: Select yorumlayıcı ' yı seçerek Jupyter sunucusunu başlatın**.

     ![jupyıter sunucusunu başlatmak için yorumlayıcı 'yı seçin](./media/vscode-tool-synapse/select-interpreter-to-start-jupyter-server.png)

8. Aşağıdaki Python seçeneğini belirleyin.

     ![aşağıdaki seçeneği seçin](./media/vscode-tool-synapse/choose-the-below-option.png)
    
9. Menü çubuğundan **Görünüm**  >  **komut paleti...** ' ya gidin veya **SHIFT + CTRL + P** klavye kısayolunu kullanın ve **Geliştirici: yeniden yükle penceresini** girin.

     ![pencereyi yeniden yükle](./media/vscode-tool-synapse/reload-window.png)

10. Henüz yapmadıysanız Azure hesabınıza [bağlanın](#connect-to-your-spark-pools) .

11. Tüm kodu seçin, betik düzenleyicisine sağ tıklayın ve **SYNAPSE: Pyspark Interactive** ' i seçerek sorguyu gönderebilirsiniz. 

     ![pyspark etkileşimli bağlam menüsü](./media/vscode-tool-synapse/pyspark-interactive-right-click.png)

12. Varsayılan bir Spark havuzu belirtmediyse Spark havuzunu seçin. Birkaç dakika sonra **Python etkileşimli** sonuçları yeni bir sekmede görüntülenir. **SYNAPSE PySpark** olarak değiştirmek Için pyspark öğesine tıklayın, ardından seçilen kodu tekrar gönderebilirsiniz ve kod başarıyla çalıştırılır. Araçlar ayrıca bağlam menüsünü kullanarak tüm betik dosyası yerine bir kod bloğu göndermenize olanak tanır:

     ![LiDE](./media/vscode-tool-synapse/pyspark-interactive-python-interactive-window.png)

### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>% #% Açıklaması kullanarak Kopyala dosyasında etkileşimli sorgu gerçekleştir

1. **#%%** Not defteri deneyimini almak için koddan önce ekleyin.

     ![% # Ekle](./media/vscode-tool-synapse/run-cell.png)

2. **Hücreyi Çalıştır**' a tıklayın. Birkaç dakika sonra Python etkileşimli sonuçları yeni bir sekmede görüntülenir. PySpark öğesine tıklayarak çekirdeği **SYNAPSE PySpark** olarak değiştirin, sonra **hücreyi yeniden çalıştır** ' a tıklayın ve kod başarıyla çalıştırılır. 

     ![hücre sonuçlarını Çalıştır](./media/vscode-tool-synapse/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>Python uzantısı 'ndan ıPYNB desteğinden yararlanın

1. Komut paletinden bir Jupyter Notebook komutuyla veya çalışma alanınızda yeni bir. ipynb dosyası oluşturarak oluşturabilirsiniz. Daha fazla bilgi için bkz. [Visual Studio Code Jupyıter Not defterleri Ile çalışma](https://code.visualstudio.com/docs/python/jupyter-support)

2. **Hücreyi Çalıştır** düğmesine tıklayın, **varsayılan Spark havuzunu** (bir not defteri 'ni açmadan önce her seferinde varsayılan kümeyi/havuzu ayarlamayı kesinlikle önerilir) ve ardından pencereyi **yeniden yükle** ' yi ayarlamak için istemleri izleyin.

     ![Varsayılan Spark havuzunu ayarlama ve yeniden yükleme](./media/vscode-tool-synapse/set-the-default-spark-pool-and-reload.png)

3. Bkz. PySpark 'a tıklayarak çekirdeği **SYNAPSE Pyspark** olarak değiştirin ve ardından **hücre Çalıştır**' a tıklayarak bir süre sonra sonuç görüntülenir.

     ![ipynb sonuçlarını Çalıştır](./media/vscode-tool-synapse/run-ipynb-file-results.png)


> [!NOTE]
>
>1. MS-Python >= 2020.5.78807 sürümü bu extentıon üzerinde desteklenmiyor, [bilinen bir sorundur](#known-issues).
>  
>2. SYNAPSE Pyspark çekirdeğine geçiş yapın, Azure portalında Otomatik ayarları devre dışı bırakmak önerilir. Aksi takdirde, kümeyi uyandırması ve ilk kez kullanılmak üzere SYNAPSE çekirdeğini ayarlamanız uzun sürebilir. 
>
>    ![otomatik ayarlar](./media/vscode-tool-synapse/auto-settings.png)

## <a name="submit-pyspark-batch-job-to-spark-pool"></a>Spark havuzuna PySpark Batch işi gönderme

1. Daha önce, kapalıysa, [daha önce](#open-a-work-folder)tartışılan **Synaseexample** klasörünü yeniden açın.  

2. [Önceki](#open-a-work-folder) adımları izleyerek yeni bir **BatchFile.py** dosyası oluşturun.

3. Aşağıdaki kodu kopyalayıp betik dosyasına yapıştırın:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. Henüz yapmadıysanız Azure hesabınıza [bağlanın](#connect-to-your-spark-pools) .

5. Betik düzenleyicisine sağ tıklayın ve ardından **SYNAPSE: PySpark Batch**' i seçin.

6. PySpark işinizi şu şekilde göndermek için bir Spark havuzu seçin:

     ![Python işi sonuç çıkışını gönder](./media/vscode-tool-synapse/submit-pythonjob-result.png)

Spark havuzuna bir toplu iş gönderdikten sonra, gönderim günlükleri Visual Studio Code **Çıkış** penceresinde görüntülenir. **Spark Kullanıcı arabirimi** URL 'Si ve **Spark iş uygulaması kullanıcı arabirimi** URL 'si de gösterilir. İşi durumunu izlemek için URL 'YI bir Web tarayıcısında açabilirsiniz.

## <a name="access-and-manage-synapse-workspace"></a>SYNAPSE çalışma alanına erişin ve yönetin

VSCode için bir Spark & Hive araçları içinde Azure Explorer 'da farklı işlemler yapabilirsiniz. Azure Gezgini 'nden.

![Azure görüntüsü](./media/vscode-tool-synapse/azure.png)

### <a name="launch-workspace"></a>Çalışma alanını Başlat

1. Azure Explorer 'da **SYNAPSE** adresine gidin, genişletin ve SYNAPSE abonelik listesini görüntüleyin.

     ![SYNAPSE Gezgini](./media/vscode-tool-synapse/synapse-explorer.png)

2. SYNAPSE çalışma alanı aboneliği ' ne tıklayın, genişletin ve çalışma alanı listesini görüntüleyin.

3. Bir çalışma alanına sağ tıklayın ve ardından **Apache Spark uygulamaları görüntüle**' yi seçin, SYNAPSE Studio web sitesindeki Apache Spark uygulama sayfası açılır.

     ![çalışma alanına sağ tıklayın](./media/vscode-tool-synapse/right-click-on-workspace.png)

     ![SYNAPSE Studio uygulaması](./media/vscode-tool-synapse/synapse-studio-application.png)

4. Bir çalışma alanını genişletin, **varsayılan depolama** ve **Spark havuzları** görüntülenir.

5. **Varsayılan depolama alanı**' na sağ tıklayın, **tam yolu Kopyala** ve **SYNAPSE Studio 'da aç** görüntülenir. 

     ![Varsayılan depolamaya sağ tıklayın](./media/vscode-tool-synapse/right-click-on-default-storage.png)

     - **Tam yolu Kopyala**' ya tıklayın, birincil ADLS 2. hesap URL 'si kopyalanacak,. gereken yere yapıştırabilirsiniz

     - **SYNAPSE Studio 'Da aç**' a tıkladığınızda, birincil depolama hesabı SYNAPSE Studio 'da açılır.

     ![SYNAPSE Studio 'da varsayılan depolama](./media/vscode-tool-synapse/default-storage-in-synapse-studio.png)

6. **Varsayılan depolamayı** genişletin, birincil depolama hesabı görüntülenir.

7. **Spark havuzlarını** genişletin, çalışma alanındaki tüm Spark havuzları görüntülenir.

     ![depolama havuzunu Genişlet](./media/vscode-tool-synapse/expand-storage-pool.png)


## <a name="known-issues"></a>Bilinen sorunlar

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>MS-Python >= 2020.5.78807 sürümü bu uzantı üzerinde desteklenmiyor 

"Jupyter not defteriyle bağlantı kurulamadı." Python sürümü >= 2020.5.78807 için bilinen bir sorundur. Bu sorundan kaçınmak için kullanıcıların MS-Python **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** sürümünü kullanması önerilir.

![bilinen sorunlar](./media/vscode-tool-synapse/known-issue.png)


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Synapse Analytics](../overview-what-is.md)
- [Azure SYNAPSE Analytics çalışma alanı için yeni bir Apache Spark havuzu oluşturma](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
