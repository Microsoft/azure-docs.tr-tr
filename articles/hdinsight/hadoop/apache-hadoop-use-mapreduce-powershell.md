---
title: Apache Hadoop-Azure HDInsight ile MapReduce ve PowerShell kullanma
description: HDInsight üzerinde Apache Hadoop MapReduce işlerini uzaktan çalıştırmak için PowerShell 'in nasıl kullanılacağını öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: 16c6c5e317591b70c3a1300453093fc715e213fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939684"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>PowerShell kullanarak MapReduce işlerini HDInsight üzerinde Apache Hadoop çalıştırma

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Bu belge, HDInsight kümesinde Hadoop 'ta MapReduce işini çalıştırmak için Azure PowerShell kullanılmasına bir örnek sağlar.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Hadoop kümesi. Bkz. [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md).

* PowerShell [az Module](/powershell/azure/) yüklendi.

## <a name="run-a-mapreduce-job"></a>MapReduce işi çalıştırma

Azure PowerShell HDInsight 'ta MapReduce işlerini uzaktan çalıştırmanıza olanak tanıyan *cmdlet 'ler* sağlar. Dahili olarak PowerShell, HDInsight kümesinde çalışan [Webhcat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (eski adı Templeton) için REST çağrıları yapar.

Aşağıdaki cmdlet 'ler, MapReduce işlerini uzak bir HDInsight kümesinde çalıştırırken kullanılır.

|Cmdlet | Açıklama |
|---|---|
|Connect-AzAccount|Azure aboneliğinize Azure PowerShell kimliğini doğrular.|
|New-AzHDInsightMapReduceJobDefinition|Belirtilen MapReduce bilgilerini kullanarak yeni bir *iş tanımı* oluşturur.|
|Start-AzHDInsightJob|İş tanımını HDInsight 'a gönderir ve işi başlatır. Bir *iş* nesnesi döndürülür.|
|Wait-AzHDInsightJob|İşin durumunu denetlemek için iş nesnesini kullanır. İş tamamlanana kadar bekler veya bekleme süresi aşana kadar bekler.|
|Get-AzHDInsightJobOutput|İşin çıkışını almak için kullanılır.|

Aşağıdaki adımlarda, HDInsight kümenizdeki bir işi çalıştırmak için bu cmdlet 'lerin nasıl kullanılacağı gösterilmektedir.

1. Bir düzenleyici kullanarak aşağıdaki kodu **mapreducejob.ps1** olarak kaydedin.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Yeni bir **Azure PowerShell** komut istemi açın. Dizinleri **mapreducejob.ps1** dosyanın konumuyla değiştirin ve betiği çalıştırmak için aşağıdaki komutu kullanın:

    ```azurepowershell
    .\mapreducejob.ps1
    ```

    Betiği çalıştırdığınızda, HDInsight kümesinin adı ve küme oturumu açmanız istenir. Ayrıca Azure aboneliğinizde kimlik doğrulaması yapmanız da istenebilir.

3. İş tamamlandığında, aşağıdaki metne benzer bir çıktı alırsınız:

    ```output
    Cluster         : CLUSTERNAME
    ExitCode        : 0
    Name            : wordcount
    PercentComplete : map 100% reduce 100%
    Query           :
    State           : Completed
    StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
    SubmissionTime  : 12/5/2014 8:34:09 PM
    JobId           : job_1415949758166_0071
    ```

    Bu çıktı, işin başarıyla tamamlandığını gösterir.

    > [!NOTE]  
    > **ExitCode** 0 dışında bir değer ise bkz. [sorun giderme](#troubleshooting).

    Bu örnek ayrıca indirilen dosyaları, içinden betiği çalıştırdığınız dizindeki bir **output.txt** dosyasına depolar.

### <a name="view-output"></a>Çıktıyı görüntüle

İş tarafından üretilen kelimeleri ve sayıları görmek için **output.txt** dosyasını bir metin düzenleyicisinde açın.

> [!NOTE]  
> MapReduce işinin çıkış dosyaları sabittir. Bu nedenle, bu örneği yeniden çalıştırırsanız, çıkış dosyasının adını değiştirmeniz gerekir.

## <a name="troubleshooting"></a>Sorun giderme

İş tamamlandığında hiçbir bilgi döndürülmezse, iş için hataları görüntüleyin. Bu işin hata bilgilerini görüntülemek için, **mapreducejob.ps1** dosyasının sonuna aşağıdaki komutu ekleyin. Ardından dosyayı kaydedin ve betiği yeniden çalıştırın.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Bu cmdlet, iş çalıştığı için STDERR 'e yazılan bilgileri döndürür.

## <a name="next-steps"></a>Sonraki adımlar

Gördüğünüz gibi Azure PowerShell, MapReduce işlerini HDInsight kümesinde çalıştırmanın kolay bir yolunu sağlar, iş durumunu izleyebilir ve çıktıyı alabilir. HDInsight 'ta Hadoop ile birlikte çalışmak için kullanabileceğiniz diğer yollar hakkında daha fazla bilgi için:

* [HDInsight Hadoop üzerinde MapReduce kullanma](hdinsight-use-mapreduce.md)
* [HDInsight üzerinde Apache Hadoop ile Apache Hive kullanma](hdinsight-use-hive.md)