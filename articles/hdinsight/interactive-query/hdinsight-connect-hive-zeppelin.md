---
title: "Hızlı başlangıç: Apache Zeppelin ile Azure HDInsight 'ta Apache Hive"
description: Bu hızlı başlangıçta, Apache Hive sorguları çalıştırmak için Apache Zeppelin kullanmayı öğreneceksiniz.
keywords: HDInsight, Hadoop, Hive, etkileşimli sorgu, LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: 36d9e9b34deb4bc6cd5f599cfe2d09a12f680730
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494298"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Hızlı başlangıç: Apache Zeppelin ile Azure HDInsight 'ta Apache Hive sorguları yürütme

Bu hızlı başlangıçta, Azure HDInsight 'ta [Apache Hive](https://hive.apache.org/) sorguları çalıştırmak Için Apache Zeppelin kullanmayı öğreneceksiniz. HDInsight etkileşimli sorgu kümeleri, etkileşimli Hive sorguları çalıştırmak için kullanabileceğiniz [Apache Zeppelin](https://zeppelin.apache.org/) not defterlerini içerir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Etkileşimli sorgu kümesi An HDInsight. HDInsight kümesi oluşturmak için bkz. [küme oluşturma](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) .  **Etkileşimli sorgu** kümesi türünü seçtiğinizden emin olun.

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin notunun oluşturulması

1. `CLUSTERNAME` aşağıdaki URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`kümenizin adıyla değiştirin. Sonra URL 'YI bir Web tarayıcısına girin.

2. Küme oturum açma kullanıcı adınızı ve parolanızı girin. Zeppelin sayfasından yeni bir not oluşturabilir veya var olan notları açabilirsiniz. **Hivesample** bazı örnek Hive sorguları içerir.  

    ![HDInsight etkileşimli sorgu Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. **Yeni dekont oluştur**' u seçin.

4. **Yeni nota oluştur** iletişim kutusunda aşağıdaki değerleri yazın veya seçin:

    - Note adı: nota bir ad girin.
    - Varsayılan yorumlayıcı: açılan listeden **JDBC** ' ı seçin.

5. **Dekont oluştur**' u seçin.

6. Kod bölümüne aşağıdaki Hive sorgusunu girin ve ardından **SHIFT + enter**tuşlarına basın:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![HDInsight etkileşimli sorgu Zeppelin çalıştırmaları sorgusu](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    İlk satırdaki **% JDBC (Hive)** deyimleri, Not DEFTERINE Hive JDBC yorumlayıcı kullanmasını söyler.

    Sorgu **hivesampletable**adlı bir Hive tablosu döndürmelidir.

    **Hivesampletable**'a karşı çalıştırabileceğiniz Iki ek Hive sorgusu aşağıda verilmiştir:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Geleneksel Hive ile karşılaştırıldığında sorgu sonuçlarının geri alınması daha hızlı olur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıcı tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile, verileriniz Azure Storage’da depolanır, böylece kullanılmadığında bir kümeyi güvenle silebilirsiniz. Ayrıca, kullanılmıyorken dahi HDInsight kümesi için sizden ücret kesilir. Küme ücretleri depolama ücretlerinin birkaç katı olduğundan, kullanılmadığında kümelerin silinmesi mantıklı olandır.

Bir kümeyi silmek için bkz. [tarayıcınızı, PowerShell 'i veya Azure CLI 'yı kullanarak HDInsight kümesini silme](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure HDInsight 'ta Apache Hive sorguları çalıştırmak için Apache Zeppelin kullanmayı öğrendiniz. Hive sorguları hakkında daha fazla bilgi edinmek için, sonraki makalede Visual Studio ile sorguların nasıl yürütüleceği gösterilmektedir.

> [!div class="nextstepaction"]
> [Visual Studio Data Lake araçlarını kullanarak Azure HDInsight 'a bağlanma ve Apache Hive sorguları çalıştırma](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
