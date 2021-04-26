---
title: Visual Studio Code için Azure HDInsight
description: Visual Studio Code için Spark & Hive araçları 'nı (Azure HDInsight) nasıl kullanacağınızı öğrenin. Sorguları ve betikleri oluşturmak ve göndermek için araçları kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: devx-track-python
ms.openlocfilehash: d7e2bd2987e7eaf7798093f3918f72b7e09a3cb0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313481"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Visual Studio Code için Spark & Hive araçlarını kullanma

Visual Studio Code için Apache Spark & Hive araçlarını kullanmayı öğrenin. Apache Spark için Apache Hive Batch işleri, etkileşimli Hive sorguları ve PySpark betikleri oluşturmak ve göndermek için araçları kullanın. İlk olarak, Visual Studio Code ' de Spark & Hive araçlarının nasıl yükleneceğini açıklayacağız. Daha sonra Spark & Hive araçlarına nasıl iş gönderirsiniz.  

Spark & Hive araçları, Visual Studio Code tarafından desteklenen platformlar üzerine yüklenebilir. Farklı platformlar için aşağıdaki önkoşullara göz önünde edin.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlamak için aşağıdaki öğeler gereklidir:

- Bir Azure HDInsight kümesi. Bir küme oluşturmak için bkz. [HDInsight kullanmaya başlama](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Veya Apache Livy uç noktasını destekleyen bir Spark ve Hive kümesi kullanın.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono yalnızca Linux ve macOS için gereklidir.
- [Visual Studio Code Için PySpark etkileşimli ortamı](set-up-pyspark-interactive-environment.md).
- Yerel bir dizin. Bu makalede  **C:\hd\hdexbol** kullanılır.

## <a name="install-spark--hive-tools"></a>Spark & Hive araçları 'nı yükler

Önkoşulları karşıladıktan sonra, aşağıdaki adımları izleyerek Visual Studio Code için Spark & Hive araçları 'nı yükleyebilirsiniz:

1. Visual Studio Code’u açın.

2. Menü çubuğundan **Görünüm**  >  **uzantıları**' na gidin.

3. Arama kutusuna **Spark & Hive** girin.

4. Arama sonuçlarından **Spark & Hive araçları** ' nı seçin ve ardından **Install**' ı seçin:

   :::image type="content" source="./media/hdinsight-for-vscode/install-hdInsight-plugin.png" alt-text="Visual Studio Code Python yüklemesi için Spark & Hive":::

5. Gerektiğinde **yeniden yükle** ' yi seçin.

## <a name="open-a-work-folder"></a>Bir iş klasörü açın

Bir iş klasörü açmak ve Visual Studio Code bir dosya oluşturmak için aşağıdaki adımları izleyin:

1. Menü çubuğundan **Dosya** aç klasörü ' ne gidin  >  **...**  >  **C:\hd\hdex,** ve ardından **Klasör Seç** düğmesini seçin. Klasör sol taraftaki **Gezgin** görünümünde görüntülenir.

2. **Gezgin** görünümü ' nde, **hdexas** klasörünü seçin ve sonra Iş klasörünün yanındaki **yeni dosya** simgesini seçin:

   :::image type="content" source="./media/hdinsight-for-vscode/visual-studio-code-new-file.png" alt-text="Visual Studio Code yeni dosya simgesi":::

3. `.hql`(Hive sorguları) ya da `.py` (Spark betiği) dosya uzantısını kullanarak yeni dosyayı adlandırın. Bu örnek **HelloWorld. HQL** kullanır.

## <a name="set-the-azure-environment"></a>Azure ortamını ayarlama

Ulusal bir bulut kullanıcısı için, önce Azure ortamını ayarlamak için aşağıdaki adımları izleyin ve ardından Azure **: oturum aç** komutunu kullanarak Azure 'da oturum açın:

1. **Dosya**  >  **tercihleri**  >  **ayarları**' na gidin.
2. Şu dizeyi arayın: **Azure: Cloud**.
3. Listeden Ulusal bulutu seçin:

   :::image type="content" source="./media/hdinsight-for-vscode/set-default-login-entry-configuration.png" alt-text="Varsayılan oturum açma girdisi yapılandırmasını ayarla":::

## <a name="connect-to-an-azure-account"></a>Azure hesabına bağlanma

Visual Studio Code kümenize komut dosyaları gönderebilmeniz için önce Azure aboneliğinde oturum açabilir ya da [bir HDInsight kümesini bağlayabilirsiniz](#link-a-cluster). HDInsight kümenize bağlanmak için, bir ESP kümesi için ambarı Kullanıcı adı/parolası veya etki alanına katılmış kimlik bilgilerini kullanın. Azure 'a bağlanmak için şu adımları izleyin:

1. Menü çubuğundan, **görüntüleme**  >  **komut paleti...**' a gidin ve **Azure: oturum aç**' ı girin:

   :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png" alt-text="Visual Studio Code oturum açma için Spark & Hive araçları":::

2. Azure 'da oturum açmak için oturum açma yönergelerini izleyin. Bağlandıktan sonra, Azure hesabınızın adı Visual Studio Code penceresinin altındaki durum çubuğunda görüntülenir.  

## <a name="link-a-cluster"></a>Bir kümeyi bağlama

### <a name="link-azure-hdinsight"></a>Bağlantı: Azure HDInsight

[Apache ambarı](https://ambari.apache.org/)ile yönetilen bir Kullanıcı adı kullanarak normal kümeyi bağlayabilir veya bir etki alanı Kullanıcı adı (örneğin:) kullanarak bir kurumsal güvenlik paketi güvenli Hadoop kümesini bağlayabilirsiniz `user1@contoso.com` .

1. Menü çubuğundan, **görüntüleme**  >  **komut paleti...**' a gidin ve **Spark/Hive: bir kümeyi bağla**' yı girin.

   :::image type="content" source="./media/hdinsight-for-vscode/link-cluster-command.png" alt-text="Komut paleti bağlantı kümesi komutu":::

2. Bağlı küme türü **Azure HDInsight**' ı seçin.

3. HDInsight kümesi URL 'sini girin.

4. Ambarı Kullanıcı adınızı girin; **yönetici** varsayılandır.

5. Ambarı parolanızı girin.

6. Küme türünü seçin.

7. Kümenin görünen adını ayarlayın (isteğe bağlı).

8. Doğrulama için **Çıkış** görünümünü gözden geçirin.

   > [!NOTE]  
   > Küme hem Azure abonelikte hem de bir kümeye bağlandığında, bağlantılı Kullanıcı adı ve parola kullanılır.  

### <a name="link-generic-livy-endpoint"></a>Bağlantı: genel Livy uç noktası

1. Menü çubuğundan, **görüntüleme**  >  **komut paleti...**' a gidin ve **Spark/Hive: bir kümeyi bağla**' yı girin.

2. Bağlı küme türü **genel Livy uç noktası** seçin.

3. Genel Livy uç noktasını girin. Örneğin: http \: //10.172.41.42:18080.

4. Yetkilendirme türü **temel** veya **yok**' u seçin.  **Temel**' i seçerseniz:  
   
   1. Ambarı Kullanıcı adınızı girin; **yönetici** varsayılandır.  

   2. Ambarı parolanızı girin.

5. Doğrulama için **Çıkış** görünümünü gözden geçirin.

## <a name="list-clusters"></a>Kümeleri Listele

1. Menü çubuğunda, **Görünüm**  >  **komut paleti...**' a gidin ve **Spark/Hive: List kümesini** girin.

2. İstediğiniz aboneliği seçin.

3. **Çıktı** görünümünü gözden geçirin. Bu görünüm, bağlantılı kümenizi (veya kümelerinizi) ve Azure aboneliğinizdeki tüm kümeleri gösterir:

   :::image type="content" source="./media/hdinsight-for-vscode/list-cluster-result1.png" alt-text="Varsayılan küme yapılandırması ayarlama":::

## <a name="set-the-default-cluster"></a>Varsayılan kümeyi ayarlama

1. Daha önce, kapatıldıysa, [daha önce](#open-a-work-folder)ele alınan **hdexon** klasörünü yeniden açın.  

2. [Daha önce](#open-a-work-folder)oluşturulmuş **HelloWorld. HQL** dosyasını seçin. Betik düzenleyicisinde açılır.

3. Betik düzenleyicisine sağ tıklayın ve ardından **Spark/Hive: varsayılan kümeyi ayarla**' yı seçin.  

4. Azure hesabınıza [bağlanın](#connect-to-an-azure-account) veya henüz yapmadıysanız bir kümeyi bağlayın.

5. Geçerli betik dosyası için varsayılan küme olarak bir küme seçin. Araçlar yapılandırma dosyasında **.VSCode\settings.js** otomatik olarak güncelleştirir:

   :::image type="content" source="./media/hdinsight-for-vscode/set-default-cluster-configuration.png" alt-text="Varsayılan küme yapılandırmasını ayarla":::

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Etkileşimli Hive sorguları ve Hive toplu iş betikleri gönder

Visual Studio Code için Spark & Hive araçları ile, kümelerinize etkileşimli Hive sorguları ve Hive toplu iş betikleri gönderebilirsiniz.

1. Daha önce, kapatıldıysa, [daha önce](#open-a-work-folder)ele alınan **hdexon** klasörünü yeniden açın.  

2. [Daha önce](#open-a-work-folder)oluşturulmuş **HelloWorld. HQL** dosyasını seçin. Betik düzenleyicisinde açılır.

3. Aşağıdaki kodu kopyalayıp Hive dosyanıza yapıştırın ve kaydedin:

   ```hiveql
   SELECT * FROM hivesampletable;
   ```

4. Azure hesabınıza [bağlanın](#connect-to-an-azure-account) veya henüz yapmadıysanız bir kümeyi bağlayın.

5. Betik düzenleyicisine sağ tıklayın ve **Hive: etkileşimli** ' i seçerek sorguyu gönderebilirsiniz veya Ctrl + alt + ı klavye kısayolunu kullanın.  Hive seçin: betiği göndermek için **toplu işlem** veya Ctrl + Alt + H klavye kısayolunu kullanın.  

6. Varsayılan bir küme belirtmediyseniz, bir küme seçin. Araçlar ayrıca bağlam menüsünü kullanarak tüm betik dosyası yerine bir kod bloğu göndermenize olanak tanır. Birkaç dakika sonra sorgu sonuçları yeni bir sekmede görünür:

   :::image type="content" source="./media/hdinsight-for-vscode/interactive-hive-result.png" alt-text="Etkileşimli Apache Hive sorgu sonucu":::

   - **Sonuçlar** paneli: tüm sonucu bir CSV, JSON veya Excel dosyası olarak yerel bir yola kaydedebilir veya yalnızca birden çok satır seçebilirsiniz.

   - **İletiler** paneli: bir **satır** numarası seçtiğinizde, çalışan betiğin ilk satırına atlar.

## <a name="submit-interactive-pyspark-queries"></a>Etkileşimli PySpark sorguları gönder

Kullanıcılar, aşağıdaki yollarla PySpark etkileşimli gerçekleştirebilir:

### <a name="using-the-pyspark-interactive-command-in-py-file"></a>Kopyala dosyasında PySpark etkileşimli komutunu kullanma
Sorguları göndermek için PySpark etkileşimli komutunu kullanarak şu adımları izleyin:

1. Daha önce, kapatıldıysa, [daha önce](#open-a-work-folder)ele alınan **hdexon** klasörünü yeniden açın.  

2. [Önceki](#open-a-work-folder) adımları izleyerek yeni bir **HelloWorld.py** dosyası oluşturun.

3. Aşağıdaki kodu kopyalayıp betik dosyasına yapıştırın:

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. PySpark/SYNAPSE Pyspark çekirdeğini yüklemek için istem pencerenin sağ alt köşesinde görüntülenir. PySpark/SYNAPSE Pyspark yüklemelerine devam etmek için, **yükleme** düğmesine tıklayabilirsiniz; veya **Atla** düğmesine tıklayarak bu adımı atlayın.

   :::image type="content" source="./media/hdinsight-for-vscode/install-the-pyspark-kernel.png" alt-text="Ekran görüntüsünde, PySpark yüklemesini atlama seçeneği gösterilmektedir.":::

5. Daha sonra yüklemeniz gerekiyorsa, **Dosya**  >  **tercihi**  >  **ayarları**' na gidebilir ve ardından **HDInsight: ayarlarda pyspark yükleme işlemini etkinleştir** ' i kaldırabilirsiniz. 
    
    :::image type="content" source="./media/hdinsight-for-vscode/enable-skip-pyspark-installation.png" alt-text="Ekran görüntüsünde, Pyspark yüklemesinin atlanmasını etkinleştirme seçeneği gösterilir.":::

6. Yükleme adım 4 ' te başarılı olursa, pencerenin sağ alt köşesinde "PySpark başarıyla yüklendi" ileti kutusu görüntülenir. Pencereyi yeniden yüklemek için **yeniden yükle** düğmesine tıklayın.

   :::image type="content" source="./media/hdinsight-for-vscode/pyspark-kernel-installed-successfully.png" alt-text="pyspark başarıyla yüklendi":::


7. Menü çubuğundan **görüntüle**  >  **komut paleti...** ' ya gidin veya **SHIFT + CTRL + P** klavye kısayolunu kullanın ve **Python: Select yorumlayıcı ' yı seçerek Jupyter sunucusunu başlatın**.

   :::image type="content" source="./media/hdinsight-for-vscode/select-interpreter-to-start-jupyter-server.png" alt-text="jupyıter sunucusunu başlatmak için yorumlayıcı 'yı seçin":::

8. Aşağıdaki Python seçeneğini belirleyin.

   :::image type="content" source="./media/hdinsight-for-vscode/choose-the-below-option.png" alt-text="aşağıdaki seçeneği seçin":::
    
9. Menü çubuğundan **Görünüm**  >  **komut paleti...** ' ya gidin veya **SHIFT + CTRL + P** klavye kısayolunu kullanın ve **Geliştirici: yeniden yükle penceresini** girin.

    :::image type="content" source="./media/hdinsight-for-vscode/reload-window.png" alt-text="pencereyi yeniden yükle":::

10. Azure hesabınıza [bağlanın](#connect-to-an-azure-account) veya henüz yapmadıysanız bir kümeyi bağlayın.

11. Tüm kodu seçin, komut dosyası düzenleyicisine sağ tıklayın ve **Spark: PySpark Interactive/SYNAPSE: Pyspark Interactive** ' i seçerek sorguyu gönderebilirsiniz. 

    :::image type="content" source="./media/hdinsight-for-vscode/pyspark-interactive-right-click.png" alt-text="pyspark etkileşimli bağlam menüsü":::

12. Varsayılan bir küme belirtmediyse kümeyi seçin. Birkaç dakika sonra **Python etkileşimli** sonuçları yeni bir sekmede görüntülenir. PySpark 'a tıklayarak çekirdeği **pyspark/SYNAPSE Pyspark** olarak değiştirin ve kod başarıyla çalıştırılır. SYNAPSE Pyspark çekirdeğine geçmek istiyorsanız, Azure portal otomatik ayarların devre dışı bırakılması önerilir. Aksi takdirde, kümeyi uyandırması ve ilk kez kullanılmak üzere SYNAPSE çekirdeğini ayarlamanız uzun sürebilir. Araçlar ayrıca bağlam menüsünü kullanarak tüm betik dosyası yerine bir kod bloğu göndermenize izin verir:

    :::image type="content" source="./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png" alt-text="pyspark etkileşimli Python etkileşimli penceresi":::

13. **%% Info** girin ve sonra iş bilgilerini görüntülemek için SHIFT + enter tuşlarına basın (isteğe bağlı):

    :::image type="content" source="./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png" alt-text="pyspark etkileşimli görüntüleme işi bilgileri":::

Araç **Spark SQL** sorgusunu da destekler:

  :::image type="content" source="./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png" alt-text="pyspark etkileşimli görünüm sonucu":::


### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>% #% Açıklaması kullanarak Kopyala dosyasında etkileşimli sorgu gerçekleştir

1. **#%%** Not defteri deneyimi almak için, Kopyala kodundan önce ekleyin.

   :::image type="content" source="./media/hdinsight-for-vscode/run-cell.png" alt-text="% # Ekle":::

2. **Hücreyi Çalıştır**' a tıklayın. Birkaç dakika sonra Python etkileşimli sonuçları yeni bir sekmede görüntülenir. PySpark 'a tıklayarak çekirdeği PySpark/SYNAPSE PySpark olarak değiştirin, sonra **hücreyi yeniden çalıştır** ' a tıklayın ve kod başarıyla çalıştırılır.

   :::image type="content" source="./media/hdinsight-for-vscode/run-cell-get-results.png" alt-text="hücre sonuçlarını Çalıştır":::

## <a name="leverage-ipynb-support-from-python-extension"></a>Python uzantısı 'ndan ıPYNB desteğinden yararlanın

1. Komut paletinden bir Jupyter Notebook komutuyla veya çalışma alanınızda yeni bir. ipynb dosyası oluşturarak oluşturabilirsiniz. Daha fazla bilgi için bkz. [Visual Studio Code Jupyıter Not defterleri Ile çalışma](https://code.visualstudio.com/docs/python/jupyter-support)

2. **Hücreyi Çalıştır** düğmesine tıklayın, **varsayılan Spark havuzunu** (bir not defteri 'ni açmadan önce her seferinde varsayılan kümeyi/havuzu ayarlamayı kesinlikle önerilir) ve ardından pencereyi **yeniden yükle** ' yi ayarlamak için istemleri izleyin.

   :::image type="content" source="./media/hdinsight-for-vscode/set-the-default-spark-pool-and-reload.png" alt-text="Varsayılan Spark havuzunu ayarlama ve yeniden yükleme":::

3. Bkz. PySpark **/SYNAPSE pyspark** ve sonra **hücreyi Çalıştır**' a tıklayarak bir süre sonra, sonuç görüntülenir.

   :::image type="content" source="./media/hdinsight-for-vscode/run-ipynb-file-results.png" alt-text="ipynb sonuçlarını Çalıştır":::


> [!NOTE]
>
> [MS-toolsai. jupyıter >2021.3.684299474 sürümü, bu uzantıda](#known-issues) bilinen bir sorundur. Lütfen Microsoft Jupyıter 2021.3.684299474 'e tıklayarak SYNAPSE çekirdeğini kullanın.

## <a name="submit-pyspark-batch-job"></a>PySpark Batch işini gönder

1. Daha önce, kapatılmışsa, [daha önce](#open-a-work-folder)tartışılan **hdex,** klasörünü yeniden açın.  

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

4. Azure hesabınıza [bağlanın](#connect-to-an-azure-account) veya henüz yapmadıysanız bir kümeyi bağlayın.

5. Betik düzenleyicisine sağ tıklayın ve **Spark: Pyspark Batch** veya * * SYNAPSE: pyspark Batch * * * öğesini seçin.

6. PySpark işinizi göndermek için bir küme/Spark havuzu seçin:

   :::image type="content" source="./media/hdinsight-for-vscode/submit-pythonjob-result.png" alt-text="Python işi sonuç çıkışını gönder":::

Bir Python işi gönderdikten sonra, gönderim günlükleri Visual Studio Code **Çıkış** penceresinde görüntülenir. Spark Kullanıcı arabirimi URL 'SI ve Yarn UI URL 'SI de gösterilir. Toplu işi bir Apache Spark havuzuna gönderirseniz, Spark geçmişi Kullanıcı arabirimi URL 'SI ve Spark Iş uygulaması kullanıcı arabirimi URL 'SI de gösterilir. İşi durumunu izlemek için URL 'YI bir Web tarayıcısında açabilirsiniz.

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>HDInsight Identity broker (HIB) ile tümleştirme

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>HDInsight ESP kümenize KIMLIK broker (HIB) ile bağlanma

KIMLIK broker (HIB) ile HDInsight ESP kümenize bağlanmak için, Azure aboneliğinde oturum açmak üzere normal adımları izleyebilirsiniz. Oturum açtıktan sonra, Azure Explorer 'da küme listesini görürsünüz. Daha fazla yönerge için bkz. [HDInsight kümenize bağlanma](#connect-to-an-azure-account).

### <a name="run-a-hivepyspark-job-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>KIMLIK broker (HIB) ile bir HDInsight ESP kümesinde Hive/PySpark işi çalıştırma

Hive işi çalıştırmak için, KIMLIK broker (HIB) ile HDInsight ESP kümesine iş göndermek için normal adımları izleyebilirsiniz. Daha fazla yönerge için [etkileşimli Hive sorguları ve Hive toplu iş betikleri gönderme](#submit-interactive-hive-queries-and-hive-batch-scripts) bölümüne bakın.

Etkileşimli bir PySpark işi çalıştırmak için, KIMLIK broker (HIB) ile HDInsight ESP kümesine iş göndermek için normal adımları izleyebilirsiniz. Daha fazla yönerge için [etkileşimli PySpark sorgularını gönderme](#submit-interactive-pyspark-queries) bölümüne bakın.

PySpark toplu işi çalıştırmak için, KIMLIK broker (HIB) ile HDInsight ESP kümesine iş göndermek için normal adımları izleyebilirsiniz. Daha fazla yönerge için [PySpark toplu Işi gönderme](#submit-pyspark-batch-job) bölümüne bakın.

## <a name="apache-livy-configuration"></a>Apache Livy yapılandırması

[Apache Livy](https://livy.incubator.apache.org/) yapılandırması desteklenir. Bunu, çalışma alanı klasöründeki **.VSCode\settings.jsdosya üzerinde** yapılandırabilirsiniz. Şu anda, Livy yapılandırma yalnızca Python betiğini destekliyor. Daha fazla bilgi için bkz. [Livy Benioku](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Livy yapılandırması tetiklenmesi**

### <a name="method-1"></a>1. Yöntem  

1. Menü çubuğundan **Dosya**  >  **tercihleri**  >  **ayarları**' na gidin.
2. **Arama ayarları** kutusuna **HDInsight Iş gönderimi: Livy conf** yazın.  
3. İlgili arama sonucu için **üzerinde settings.jsDüzenle** ' yi seçin.

### <a name="method-2"></a>2. Yöntem

Bir dosya gönderir ve `.vscode` klasörün otomatik olarak iş klasörüne eklendiğinden emin olun. **.vscode\settings.jsaçık '** i seçerek Livy yapılandırmasını görebilirsiniz.

- Proje ayarları:

  :::image type="content" source="./media/hdinsight-for-vscode/hdi-apache-livy-config.png" alt-text="HDInsight Apache Livy yapılandırması":::

  >[!NOTE]
  >**Drivermemory** ve **executormemory** ayarları için değeri ve birimi ayarlayın. Örneğin: 1G veya 1024m.

- Desteklenen Livy yapılandırması:

  **/Toplu iş SONRASı**
  
  **İstek gövdesi**

  | name | açıklama | tür |
  | --- | --- | --- |
  |  dosyası | Yürütülecek uygulamayı içeren dosya | Yol (gerekli) |
  | proxyUser | İşi çalıştırırken taklit edilecek Kullanıcı | Dize |
  | Sınıf | Uygulama Java/Spark ana sınıfı | Dize |
  | args | Uygulama için komut satırı bağımsız değişkenleri | Dizelerin listesi |
  | jar dosyaları dışındaki | Bu oturumda kullanılacak jars | Dizelerin listesi | 
  | pyFiles | Bu oturumda kullanılacak Python dosyaları | Dizelerin listesi |
  | files | Bu oturumda kullanılacak dosyalar | Dizelerin listesi |
  | driverMemory | Sürücü işlemi için kullanılacak bellek miktarı | Dize |
  | Driverçekirdekler | Sürücü işlemi için kullanılacak çekirdek sayısı | int |
  | Yürütişbelleği | Yürütücü işlemi başına kullanılacak bellek miktarı | Dize |
  | Executorçekirdekler | Her yürütücü için kullanılacak çekirdek sayısı | int |
  | numExecutors | Bu oturum için başlatılacak yürüticilerinin sayısı | int |
  | Arşivi | Bu oturumda kullanılacak Arşivler | Dizelerin listesi |
  | kuyruk | Gönderilecek YARN kuyruğunun adı| Dize |
  | name | Bu oturumun adı | Dize |
  | conf | Spark yapılandırma özellikleri | Anahtar = Val eşlemesi |

  **Yanıt gövdesi** Oluşturulan Batch nesnesi.

  | name | açıklama | tür |
  | --- | ---| --- |
  | ID | Oturum Kimliği | int |
  | appId | Bu oturumun uygulama KIMLIĞI | Dize |
  | appInfo | Ayrıntılı uygulama bilgileri | Anahtar = Val eşlemesi |
  | açmasını | Günlük satırları | Dizelerin listesi |
  | state |Toplu iş durumu | Dize |

  > [!NOTE]
  > Atanan Livy yapılandırması, betiği gönderdiğinizde çıkış bölmesinde görüntülenir.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Gezgin 'den Azure HDInsight ile tümleştirme

Doğrudan **Azure HDInsight** Gezgini aracılığıyla kümelerinizde Hive tablosunun önizlemesini yapabilirsiniz:

1. Henüz yapmadıysanız Azure hesabınıza [bağlanın](#connect-to-an-azure-account) .

2. En soldaki sütundan **Azure** simgesini seçin.

3. Sol bölmeden **Azure: HDInsight**' ı genişletin. Kullanılabilir abonelikler ve kümeler listelenir.

4. Hive meta veri veritabanını ve tablo şemasını görüntülemek için kümeyi genişletin.

5. Hive tablosuna sağ tıklayın. Örneğin: **hivesampletable**. **Önizleme**' yi seçin.

   :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png" alt-text="Visual Studio Code Preview Hive tablosu için Spark & Hive":::

6. **Önizleme sonuçları** penceresi açılır:

   :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png" alt-text="Visual Studio Code Önizleme sonuçları penceresi için Spark & Hive":::

- SONUÇLAR paneli

   Tüm sonucu bir CSV, JSON veya Excel dosyası olarak yerel bir yola kaydedebilir veya yalnızca birden fazla satır seçebilirsiniz.

- ILETILER bölmesi

  1. Tablodaki satır sayısı 100 ' den büyükse şu iletiyi görürsünüz: "Hive tablosu için ilk 100 satır görüntülenir."
  2. Tablodaki satır sayısı 100 ' den az veya bu değere eşit olduğunda şu iletiyi görürsünüz: "60 satır Hive tablosu için görüntülenir."
  3. Tabloda içerik yoksa şu iletiyi görürsünüz: " `0 rows are displayed for Hive table.` "

     >[!NOTE]
     >
     >Linux 'ta, kopyalama tablosu verilerini etkinleştirmek için xclip ' i de yüklersiniz.
     >
     >:::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png" alt-text="Linux 'ta Visual Studio Code için Spark & Hive":::

## <a name="additional-features"></a>Ek özellikler

Visual Studio Code için Spark & Hive aşağıdaki özellikleri de destekler:

- **IntelliSense otomatik tamamlama**. Anahtar sözcükler, Yöntemler, değişkenler ve diğer programlama öğeleri için açılan öneriler. Farklı simgeler farklı nesne türlerini temsil eder:

    :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png" alt-text="Visual Studio Code IntelliSense nesneleri için Spark & Hive araçları":::

- **IntelliSense hata işaretleyicisi**. Dil hizmeti, Hive betiğinin düzenlenme hatalarının altını çizer.     
- **Söz dizimi önemli**. Dil hizmeti değişkenleri, anahtar sözcükleri, veri türünü, işlevleri ve diğer programlama öğelerini ayırt etmek için farklı renkler kullanır:

    :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png" alt-text="Visual Studio Code sözdizimi için Spark & Hive araçları vurguları":::

## <a name="reader-only-role"></a>Yalnızca okuyucu rolü

Küme için yalnızca okuyucu rolü atanan kullanıcılar, HDInsight kümesine iş gönderemez ve Hive veritabanını görüntüleyemez. Rolünüzü [Azure Portal](https://portal.azure.com/) [**HDInsight kümesi işlecine**](./hdinsight-migrate-granular-access-cluster-configurations.md#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) yükseltmek için küme yöneticisine başvurun. Geçerli bir ambarı kimlik bilgileriniz varsa, aşağıdaki kılavuzu kullanarak kümeyi el ile bağlayabilirsiniz.

### <a name="browse-the-hdinsight-cluster"></a>HDInsight kümesine gözatın  

Bir HDInsight kümesini genişletmek için Azure HDInsight Gezginini seçtiğinizde, küme için yalnızca okuyucu rolüne sahipseniz kümeyi bağlamanız istenir. Ambarı kimlik bilgilerinizi kullanarak kümeye bağlamak için aşağıdaki yöntemi kullanın.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>İşi HDInsight kümesine gönder

İş bir HDInsight kümesine gönderilirken, küme için yalnızca okuyucu rolünde olduğunuzda kümeyi bağlamanız istenir. Ambarı kimlik bilgilerini kullanarak kümeye bağlamak için aşağıdaki adımları kullanın.

### <a name="link-to-the-cluster"></a>Kümeye bağlantı

1. Geçerli bir ambarı Kullanıcı adı girin.
2. Geçerli bir parola girin.

   :::image type="content" source="./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png" alt-text="Visual Studio Code Kullanıcı adı için Spark & Hive araçları":::

   :::image type="content" source="./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png" alt-text="Visual Studio Code parola için Spark & Hive araçları":::

   > [!NOTE]
   >
   >`Spark / Hive: List Cluster`Bağlantılı kümeyi denetlemek için ' i kullanabilirsiniz:
   >
   >:::image type="content" source="./media/hdinsight-for-vscode/list-cluster-result1.png" alt-text="Visual Studio Code okuyucusu bağlantılı için Spark & Hive araçları":::

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Data Lake Storage 2. hesaba gözatıp

Data Lake Storage 2. bir hesabı genişletmek için Azure HDInsight Gezginini seçin. Azure hesabınızın Gen2 depolama alanına erişimi yoksa, depolama erişim anahtarını girmeniz istenir. Erişim anahtarı doğrulandıktan sonra, Data Lake Storage 2. hesabı otomatik genişletilir.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Data Lake Storage 2. olan bir HDInsight kümesine iş gönderme

Data Lake Storage 2. kullanarak bir HDInsight kümesine iş gönderme. Azure hesabınızın Gen2 Storage 'a yazma erişimi yoksa, depolama erişim anahtarını girmeniz istenir. Erişim anahtarı doğrulandıktan sonra, iş başarılı bir şekilde gönderilir.

:::image type="content" source="./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png" alt-text="Visual Studio Code AccessKey için Spark & Hive araçları":::

> [!NOTE]
>
> Depolama hesabı için erişim anahtarını Azure portal alabilirsiniz. Daha fazla bilgi için bkz. [depolama hesabı erişim anahtarlarını yönetme](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Kümenin bağlantısını kaldır

1. Menü çubuğundan **Görünüm**  >  **komut paleti**' ne gidin ve **Spark/Hive: bir kümenin bağlantısını kaldır**' ı girin.  

2. Bağlantısını kaldırmak için bir küme seçin.  

3. Doğrulama için **Çıkış** görünümüne bakın.  

## <a name="sign-out"></a>Oturumu kapat  

Menü çubuğundan **Görünüm**  >  **komut paleti**' ne gidin ve **Azure: oturumu Kapat**' ı girin.

## <a name="known-issues"></a>Bilinen Sorunlar 

 MS-toolsai. jupyıter >2021.3.684299474 sürümü bu uzantıda desteklenmiyor, lütfen Microsoft jupi 2021.3.684299474 'ye tıklayarak SYNAPSE çekirdeğini kullanın.

 1. Otomatik güncelleştirme uzantısını devre dışı bırakın.

      ![Otomatik güncelleştirme uzantısını devre dışı bırak](./media/hdinsight-for-vscode/disable-auto-updating-extension.png)

2. Microsoft Jupyter 'ın seçili bir sürümünü yükler.

      ![Microsoft Jupyter 'ın seçili sürümü](./media/hdinsight-for-vscode/selected-version-of-microsoft-jupyter.png)

3. Microsoft Jupyıter sürüm 2021.3.684299474 'i yükler


## <a name="next-steps"></a>Sonraki adımlar

Visual Studio Code için Spark & Hive kullanmayı gösteren bir video için bkz. [spark & Hive for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).