---
title: Apache Phoenix ile Azure HDInsight 'ta Apache temel sorguları çalıştırma
description: Apache Zeppelin kullanarak Apache temel sorgularını Phoenix ile çalıştırma hakkında bilgi edinin.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 10/14/2019
ms.openlocfilehash: ff963e661a2b258c1eb452ed63f41f4e7d84c6a0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867789"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache HBase üzerinde Apache Phoenix sorguları çalıştırmak için Apache Zeppelin kullanma

Apache Phoenix, HBase üzerinde oluşturulan açık kaynaklı, yüksek düzeyde paralel ilişkisel veritabanı katmanıdır. Phoenix, HBase üzerinde SQL gibi sorgular kullanmanıza olanak sağlar. Phoenix, SQL tabloları, dizinleri, görünümleri ve dizileri oluşturmanıza, silebilmeniz, bunları silmenizi sağlamak için, altında JDBC sürücülerini kullanır.  Ayrıca, satırları ayrı ayrı ve toplu olarak güncelleştirmek için Phoenix 'i de kullanabilirsiniz. Phoenix, sorguları derlemek için MapReduce kullanmak yerine bir NOSQL yerel derleme kullanır ve bu, HBase üzerinde düşük gecikmeli uygulamalar oluşturulmasını sağlar.

Apache Zeppelin, etkileşimli veri analizlerini ve SQL ve Scala gibi dilleri kullanarak veri odaklı, işbirlikçi belgeler oluşturmanızı sağlayan açık kaynaklı, Web tabanlı bir not Defterinizdir. Veri geliştiricilerinin veri uzmanları tarafından veri işleme için kod geliştirme, düzenleme, yürütme ve paylaşma & yardımcı olur. Bu, komut satırına başvurmadan veya küme ayrıntılarına gerek kalmadan sonuçları görselleştirmenize olanak tanır.

HDInsight kullanıcıları, Phoenix tablolarını sorgulamak için Apache Zeppelin kullanabilirler. Apache Zeppelin, HDInsight kümesiyle tümleşiktir ve bunu kullanmak için başka bir adım yoktur. JDBC yorumlayıcı ile Zeppelin Not defteri oluşturmanız ve Phoenix SQL sorgularınızı yazmaya başlamanız yeterlidir

## <a name="prerequisites"></a>Önkoşullar

HDInsight 'ta Apache HBase kümesi. Bkz. [Apache HBase ile çalışmaya başlama](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin notunun oluşturulması

1. `CLUSTERNAME`AŞAĞıDAKI URL 'deki kümenizin adıyla değiştirin `https://CLUSTERNAME.azurehdinsight.net/zeppelin` . Sonra URL 'YI bir Web tarayıcısına girin. Küme oturum açma kullanıcı adınızı ve parolanızı girin.

1. Zeppelin sayfasında **Yeni dekont oluştur**' u seçin.

   :::image type="content" source="./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png" alt-text="HDInsight etkileşimli sorgu Zeppelin" border="true":::

1. **Yeni nota oluştur** iletişim kutusunda aşağıdaki değerleri yazın veya seçin:

   - Note adı: nota bir ad girin.
   - Varsayılan yorumlayıcı: açılan listeden **JDBC** ' ı seçin.

   Ardından, **Note oluştur**' u seçin.

1. Not defteri üstbilgisinin bağlı bir durum belirttiğinden emin olun. Sağ üst köşedeki yeşil noktayla gösterilir.

   :::image type="content" source="./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png" alt-text="Zeppelin Not defteri durumu" border="true":::

1. Bir HBase tablosu oluşturun. Aşağıdaki komutu girin ve ardından **SHIFT + enter** tuşlarına basın:

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

- [Apache Phoenix artık Azure HDInsight 'ta Zeppelin 'i destekliyor](/archive/blogs/ashish/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight)
- [Apache Phoenix dilbilgisi](https://phoenix.apache.org/language/index.html)