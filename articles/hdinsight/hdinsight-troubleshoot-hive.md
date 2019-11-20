---
title: Azure HDInsight kullanarak Hive sorunlarını giderme
description: Apache Hive ve Azure HDInsight ile çalışma hakkında sık sorulan soruların yanıtlarını alın.
keywords: Azure HDInsight, Hive, SSS, sorun giderme kılavuzu, sık sorulan sorular
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 9169bd9a63666238e9d6b97d86bf1e9e10312c1b
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076518"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Azure HDInsight 'ı kullanarak Apache Hive sorunlarını giderme

Apache ambarı 'nda Apache Hive yükleri ile çalışırken önde gelen sorular ve bunların çözümlerini öğrenin.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Nasıl yaparım? bir Hive meta veri deposu dışarı aktarıp başka bir kümeye içeri aktarsın mı?

### <a name="resolution-steps"></a>Çözüm adımları

1. Güvenli Kabuk (SSH) istemcisi kullanarak HDInsight kümesine bağlanın. Daha fazla bilgi için [ek okuma](#additional-reading-end).

2. Meta veri deposunu dışarı aktarmak istediğiniz HDInsight kümesinde aşağıdaki komutu çalıştırın:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Bu komut allatables. SQL adlı bir dosya oluşturur.

3. Alltables. SQL dosyasını yeni HDInsight kümesine kopyalayın ve ardından aşağıdaki komutu çalıştırın:

    ```apache
    hive -f alltables.sql
    ```

Çözüm adımlarında bulunan kod, yeni kümedeki veri yollarının Eski kümedeki veri yollarıyla aynı olduğunu varsayar. Veri yolları farklıysa, oluşturulan `alltables.sql` dosyayı tüm değişiklikleri yansıtacak şekilde el ile düzenleyebilirsiniz.

### <a name="additional-reading"></a>Ek okuma

- [SSH kullanarak bir HDInsight kümesine bağlanma](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Nasıl yaparım? bir kümede Hive günlükleri bulunamıyor mu?

### <a name="resolution-steps"></a>Çözüm adımları

1. SSH kullanarak HDInsight kümesine bağlanın. Daha fazla bilgi için **ek okuma**.

2. Hive istemci günlüklerini görüntülemek için aşağıdaki komutu kullanın:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Hive meta veri deposu günlüklerini görüntülemek için aşağıdaki komutu kullanın:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Hive sunucu günlüklerini görüntülemek için aşağıdaki komutu kullanın:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Ek okuma

- [SSH kullanarak bir HDInsight kümesine bağlanma](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Bir kümedeki belirli yapılandırmalara sahip Hive kabuğunu Nasıl yaparım? mi başlatın?

### <a name="resolution-steps"></a>Çözüm adımları

1. Hive kabuğunu başlattığınızda bir yapılandırma anahtar-değer çifti belirtin. Daha fazla bilgi için [ek okuma](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. Hive kabuğu 'ndaki tüm etkin konfigürasyonları listelemek için aşağıdaki komutu kullanın:

   ```apache
   hive> set;
   ```

   Örneğin, konsolunda hata ayıklama günlüğü etkin olarak Hive kabuğu 'nu başlatmak için aşağıdaki komutu kullanın:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Ek okuma

- [Hive yapılandırma özellikleri](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Küme kritik yolundaki Apache Tez DAG verilerini çözümlemek Nasıl yaparım??

### <a name="resolution-steps"></a>Çözüm adımları

1. Küme açısından kritik bir grafikte Apache Tez yönlendirilmiş bir Çevrimsiz grafiği (DAG) analiz etmek için SSH kullanarak HDInsight kümesine bağlanın. Daha fazla bilgi için [ek okuma](#additional-reading-end).

2. Komut isteminde aşağıdaki komutu çalıştırın:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Tez DAG 'yi çözümlemek için kullanılabilecek diğer Çözümleyicileri listelemek için aşağıdaki komutu kullanın:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   İlk bağımsız değişken olarak örnek bir program sağlamanız gerekir.

   Geçerli program adları şunları içerir:
    - **Containerreuseanalyzer**: Bir DAG 'de kapsayıcı yeniden kullanım ayrıntılarını yazdırma
    - **Kritikyol**: Bir DAG 'nin kritik yolunu bulma
    - **LocalityAnalyzer**: Bir DAG 'de yerleşim ayrıntılarını yazdırma
    - **Karıştırılmış Letimeanalyzer**: Bir DAG 'de karıştırma süresi ayrıntılarını çözümleme
    - **SkewAnalyzer**: Bir DAG 'de eğme ayrıntılarını çözümleme
    - **Yavaşlatma Nodeanalyzer**: Bir DAG 'de düğüm ayrıntılarını yazdırma
    - **Yavaştaskidentifier**: Bir DAG 'de yavaş görev ayrıntılarını yazdırma
    - **SlowestVertexAnalyzer**: Bir DAG 'da en yavaş köşe ayrıntılarını Yazdır
    - **SpillAnalyzer**: Bir DAG 'de taşma ayrıntılarını yazdırma
    - **TaskConcurrencyAnalyzer**: Görev eşzamanlılık ayrıntılarını bir DAG 'da yazdırma
    - **Vertexlevelkritikpathanalyzer**: Bir DAG 'de köşe düzeyinde kritik yolu bulma

### <a name="additional-reading"></a>Ek okuma

- [SSH kullanarak bir HDInsight kümesine bağlanma](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Tez DAG verilerini bir kümeden indirmek Nasıl yaparım? mi?

#### <a name="resolution-steps"></a>Çözüm adımları

Tez DAG verilerini toplamanın iki yolu vardır:

- Komut satırından:

    SSH kullanarak HDInsight kümesine bağlanın. Komut isteminde aşağıdaki komutu çalıştırın:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Ambarı tez görünümünü kullanın:

  1. Ambarı 'na gidin.
  2. Tez görünümüne gidin (sağ üst köşedeki kutucuklar simgesi altında).
  3. Görüntülemek istediğiniz DAG 'yi seçin.
  4. **Verileri indir**' i seçin.

### <a name="additional-reading-end"></a>Ek okuma

[SSH kullanarak bir HDInsight kümesine bağlanma](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

- Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

- [@AzureSupport](https://twitter.com/azuresupport) Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

- Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
