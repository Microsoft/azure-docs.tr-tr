---
title: "Hızlı başlangıç: Apache Zeppelin ile Azure HDInsight 'ta Apache Hive"
description: Bu hızlı başlangıçta, Apache Hive sorguları çalıştırmak için Apache Zeppelin kullanmayı öğreneceksiniz.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 75f758054f867c58dd4180fe5e728e66785e3b11
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869778"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Hızlı başlangıç: Apache Zeppelin ile Azure HDInsight 'ta Apache Hive sorguları yürütme

Bu hızlı başlangıçta, Azure HDInsight 'ta [Apache Hive](https://hive.apache.org/) sorguları çalıştırmak Için Apache Zeppelin kullanmayı öğreneceksiniz. HDInsight etkileşimli sorgu kümeleri, etkileşimli Hive sorguları çalıştırmak için kullanabileceğiniz [Apache Zeppelin](https://zeppelin.apache.org/) not defterlerini içerir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Etkileşimli sorgu kümesi An HDInsight. HDInsight kümesi oluşturmak için bkz. [küme oluşturma](../hadoop/apache-hadoop-linux-tutorial-get-started.md) .  **Etkileşimli sorgu** kümesi türünü seçtiğinizden emin olun.

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin notunun oluşturulması

1. `CLUSTERNAME`AŞAĞıDAKI URL 'deki kümenizin adıyla değiştirin `https://CLUSTERNAME.azurehdinsight.net/zeppelin` . Sonra URL 'YI bir Web tarayıcısına girin.

2. Küme oturum açma kullanıcı adınızı ve parolanızı girin. Zeppelin sayfasından yeni bir not oluşturabilir veya var olan notları açabilirsiniz. **Hivesample** bazı örnek Hive sorguları içerir.  

    :::image type="content" source="./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png" alt-text="HDInsight etkileşimli sorgu Zeppelin" border="true":::

3. **Yeni dekont oluştur**' u seçin.

4. **Yeni nota oluştur** iletişim kutusunda aşağıdaki değerleri yazın veya seçin:

    - Note adı: nota bir ad girin.
    - Varsayılan yorumlayıcı: açılan listeden **JDBC** ' ı seçin.

5. **Dekont oluştur**' u seçin.

6. Kod bölümüne aşağıdaki Hive sorgusunu girin ve ardından **SHIFT + enter** tuşlarına basın:

    ```hive
    %jdbc(hive)
    show tables
    ```

    :::image type="content" source="./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png" alt-text="HDInsight etkileşimli sorgu Zeppelin çalıştırmaları sorgusu" border="true":::

    İlk satırdaki **% JDBC (Hive)** deyimleri, Not DEFTERINE Hive JDBC yorumlayıcı kullanmasını söyler.

    Sorgu **hivesampletable** adlı bir Hive tablosu döndürmelidir.

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

### <a name="additional-examples"></a>Ek örnekler

1. Tablo oluşturun. Zeppelin not defterinde aşağıdaki kodu yürütün:

    ```hql
    %jdbc(hive)
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE;
    ```

1. Yeni tabloya veri yükleyin. Zeppelin not defterinde aşağıdaki kodu yürütün:

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Tek bir kayıt ekleyin. Zeppelin not defterinde aşağıdaki kodu yürütün:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Ek sözdizimi için [Hive dilini el ile](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) inceleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıcı tamamladıktan sonra kümeyi silmek isteyebilirsiniz. HDInsight ile Verileriniz Azure Storage 'da depolanır, bu sayede bir kümeyi kullanımda olmadığında güvenle silebilirsiniz. Ayrıca, kullanımda olmasa bile bir HDInsight kümesi için de ücretlendirilirsiniz. Kümenin ücretleri depolama ücretinden çok daha fazla olduğundan, kullanımda olmadıkları zaman kümeleri silmek ekonomik bir anlam sağlar.

Bir kümeyi silmek için bkz. [tarayıcınızı, PowerShell 'i veya Azure CLI 'yı kullanarak HDInsight kümesini silme](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure HDInsight 'ta Apache Hive sorguları çalıştırmak için Apache Zeppelin kullanmayı öğrendiniz. Hive sorguları hakkında daha fazla bilgi edinmek için, sonraki makalede Visual Studio ile sorguların nasıl yürütüleceği gösterilmektedir.

> [!div class="nextstepaction"]
> [Visual Studio Data Lake araçlarını kullanarak Azure HDInsight 'a bağlanma ve Apache Hive sorguları çalıştırma](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
