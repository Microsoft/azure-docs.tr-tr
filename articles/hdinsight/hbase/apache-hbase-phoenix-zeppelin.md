---
title: Apache Phoenix ile Azure HDInsight 'ta Apache temel sorguları çalıştırma
description: Apache Zeppelin kullanarak Apache temel sorgularını Phoenix ile çalıştırma hakkında bilgi edinin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392247"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache HBase üzerinde Apache Phoenix sorguları çalıştırmak için Apache Zeppelin kullanma

Apache Phoenix, HBase üzerinde oluşturulan açık kaynaklı, yüksek düzeyde paralel ilişkisel veritabanı katmanıdır. Phoenix, HBase üzerinde SQL gibi sorgular kullanmanıza olanak sağlar. Phoenix, SQL tabloları, dizinleri, görünümleri ve dizileri oluşturmanıza, silebilmeniz, bunları silmenizi sağlamak için, altında JDBC sürücülerini kullanır.  Ayrıca, satırları ayrı ayrı ve toplu olarak güncelleştirmek için Phoenix 'i de kullanabilirsiniz. Phoenix, sorguları derlemek için MapReduce kullanmak yerine bir NOSQL yerel derleme kullanır ve bu, HBase üzerinde düşük gecikmeli uygulamalar oluşturulmasını sağlar.

Apache Zeppelin, etkileşimli veri analizlerini ve SQL ve Scala gibi dilleri kullanarak veri odaklı, işbirlikçi belgeler oluşturmanızı sağlayan açık kaynaklı, Web tabanlı bir not Defterinizdir. Veri geliştiricilerinin veri uzmanları tarafından veri işleme için kod geliştirme, düzenleme, yürütme ve paylaşma & yardımcı olur. Bu, komut satırına başvurmadan veya küme ayrıntılarına gerek kalmadan sonuçları görselleştirmenize olanak tanır.

HDInsight kullanıcıları, Phoenix tablolarını sorgulamak için Apache Zeppelin kullanabilirler. Apache Zeppelin, HDInsight kümesiyle tümleşiktir ve bunu kullanmak için başka bir adım yoktur. JDBC yorumlayıcı ile Zeppelin Not defteri oluşturmanız ve Phoenix SQL sorgularınızı yazmaya başlamanız yeterlidir

## <a name="prerequisites"></a>Önkoşullar

HDInsight 'ta Apache HBase kümesi. Bkz. [Apache HBase ile çalışmaya başlama](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin notunun oluşturulması

1. @No__t-0 değerini aşağıdaki URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin` ' de kümenizin adıyla değiştirin. Sonra URL 'YI bir Web tarayıcısına girin. Küme oturum açma kullanıcı adınızı ve parolanızı girin.

1. Zeppelin sayfasında **Yeni dekont oluştur**' u seçin.

    ![HDInsight etkileşimli sorgu Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. **Yeni nota oluştur** iletişim kutusunda aşağıdaki değerleri yazın veya seçin:

    - Note adı: nota bir ad girin.
    - Varsayılan yorumlayıcı: açılan listeden **JDBC** ' ı seçin.

    Ardından, **Note oluştur**' u seçin.

1. Not defteri üstbilgisinin bağlı bir durum belirttiğinden emin olun. Sağ üst köşedeki yeşil noktayla gösterilir.

    ![Zeppelin Not defteri durumu](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Zeppelin Not defteri durumu")

1. Bir HBase tablosu oluşturun. Aşağıdaki komutu girin ve ardından **SHIFT + enter**tuşlarına basın:

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    İlk satırdaki **% JDBC (Phoenix)** deyimleri, Not DEFTERINE Phoenix JDBC yorumlayıcı kullanmasını söyler.

1. Oluşturulan tabloları görüntüleyin.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Tablodaki değerleri ekleyin.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. Tabloyu sorgulayın.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Bir kaydı silin.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Tabloyu bırakın.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Apache Phoenix artık Azure HDInsight 'ta Zeppelin 'i destekliyor](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Apache Phoenix dilbilgisi](https://phoenix.apache.org/language/index.html)
