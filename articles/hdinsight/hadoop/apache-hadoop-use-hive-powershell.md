---
title: HDInsight 'ta PowerShell ile Apache Hive kullanma-Azure
description: Azure HDInsight 'ta Apache Hadoop Apache Hive sorguları çalıştırmak için PowerShell 'i kullanma
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 89fa7976b922ba0e40e97b72de5d4eb9a02f0dfd
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736075"
---
# <a name="run-apache-hive-queries-using-powershell"></a>PowerShell kullanarak Apache Hive sorguları çalıştırma
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Bu belge, Azure Kaynak grubu modunda Azure PowerShell kullanarak Hive sorgularını HDInsight kümesinde bir Apache Hadoop çalıştırmak için bir örnek sağlar.

> [!NOTE]  
> Bu belge, örneklerde kullanılan HiveQL deyimlerinin ne olduğuna ilişkin ayrıntılı bir açıklama sağlamaz. Bu örnekte kullanılan HiveQL hakkında daha fazla bilgi için bkz. [HDInsight üzerinde Apache Hadoop ile Apache Hive kullanma](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* HDInsight kümesi sürüm 3,4 veya daha sonraki bir Linux tabanlı Apache Hadoop.

* Azure PowerShell olan bir istemci.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Hive sorgusu çalıştırma

Azure PowerShell, HDInsight üzerinde Hive sorgularını uzaktan çalıştırmanıza olanak tanıyan *cmdlet 'ler* sağlar. Dahili olarak, cmdlet 'ler HDInsight kümesinde [Webhcat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) 'e geri çağrı yapar.

Aşağıdaki cmdlet 'ler, uzak bir HDInsight kümesinde Hive sorguları çalıştırılırken kullanılır:

* `Connect-AzAccount`: Azure aboneliğinize Azure PowerShell kimliğini doğrular.
* `New-AzHDInsightHiveJobDefinition`: Belirtilen HiveQL deyimlerini kullanarak bir *iş tanımı* oluşturur.
* `Start-AzHDInsightJob`: İş tanımını HDInsight 'a gönderir ve işi başlatır. Bir *iş* nesnesi döndürülür.
* `Wait-AzHDInsightJob`: İşin durumunu denetlemek için iş nesnesini kullanır. İş tamamlanana kadar bekler veya bekleme süresi aşana kadar bekler.
* `Get-AzHDInsightJobOutput`: İşin çıkışını almak için kullanılır.
* `Invoke-AzHDInsightHiveJob`: HiveQL deyimlerini çalıştırmak için kullanılır. Bu cmdlet sorgu tamamlanmasını engeller, sonra sonuçları döndürür.
* `Use-AzHDInsightCluster`: `Invoke-AzHDInsightHiveJob` Komut için kullanılacak geçerli kümeyi ayarlar.

Aşağıdaki adımlarda, HDInsight kümenizdeki bir işi çalıştırmak için bu cmdlet 'lerin nasıl kullanılacağı gösterilmektedir:

1. Bir düzenleyici kullanarak aşağıdaki kodu olarak `hivejob.ps1`kaydedin.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Yeni bir **Azure PowerShell** komut istemi açın. Dizini `hivejob.ps1` dosyanın konumuyla değiştirin, ardından betiği çalıştırmak için aşağıdaki komutu kullanın:

        .\hivejob.ps1

    Betik çalıştırıldığında, küme adını ve HTTPS/Küme Yöneticisi hesabı kimlik bilgilerini girmeniz istenir. Ayrıca Azure aboneliğinizde oturum açmanız istenebilir.

3. İş tamamlandığında, aşağıdaki metne benzer bilgiler döndürür:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Daha önce belirtildiği gibi `Invoke-Hive` , bir sorgu çalıştırmak ve yanıt beklemek için kullanılabilir. Invoke-Hive nasıl çalıştığını görmek için aşağıdaki betiği kullanın:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    Çıktı aşağıdaki metin gibi görünür:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > Daha uzun HiveQL sorguları için Azure PowerShell **burada-dizeler** cmdlet 'Ini veya hiveql betik dosyalarını kullanabilirsiniz. Aşağıdaki kod parçacığında, bir hiveql `Invoke-Hive` betik dosyasını çalıştırmak için cmdlet 'inin nasıl kullanılacağı gösterilmektedir. HiveQL betik dosyasının wasb://'e yüklenmesi gerekir.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > **Burada dizeler**hakkında daha fazla bilgi için, bkz. <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">Windows PowerShell 'i burada kullanma-dizeler</a>.

## <a name="troubleshooting"></a>Sorun giderme

İş tamamlandığında hiçbir bilgi döndürülmezse, hata günlüklerini görüntüleyin. Bu işin hata bilgilerini görüntülemek için, `hivejob.ps1` dosyanın sonuna aşağıdakini ekleyin, kaydedin ve yeniden çalıştırın.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Bu cmdlet, iş işleme sırasında STDERR 'e yazılan bilgileri döndürür.

## <a name="summary"></a>Özet

Gördüğünüz gibi Azure PowerShell, bir HDInsight kümesinde Hive sorguları çalıştırmanın kolay bir yolunu sağlar, iş durumunu izleyebilir ve çıktıyı alabilir.

## <a name="next-steps"></a>Sonraki adımlar

HDInsight 'ta Hive hakkında genel bilgi için:

* [HDInsight üzerinde Apache Hadoop ile Apache Hive kullanma](hdinsight-use-hive.md)

HDInsight 'ta Hadoop ile birlikte çalışmak için kullanabileceğiniz diğer yollar hakkında daha fazla bilgi için:

* [HDInsight üzerinde Apache Hadoop Apache Pig kullanma](hdinsight-use-pig.md)
* [HDInsight üzerinde Apache Hadoop MapReduce kullanma](hdinsight-use-mapreduce.md)
