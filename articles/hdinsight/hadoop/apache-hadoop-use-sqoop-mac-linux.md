---
title: Apache Hadoop-Azure HDInsight ile Apache Sqoop
description: HDInsight ve Azure SQL veritabanı arasında Apache Hadoop içeri aktarmak ve dışarı aktarmak için Apache Sqoop 'yi nasıl kullanacağınızı öğrenin.
keywords: Hadoop Sqoop, Sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c839aeae77d7e75fb30d82c410c331d21f5868ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406033"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>HDInsight ve SQL veritabanı Apache Hadoop arasında veri içeri ve dışarı aktarmak için Apache Sqoop kullanın

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Azure HDInsight ve Azure SQL veritabanı veya Microsoft SQL Server veritabanında bir Apache Hadoop kümesi arasında içeri ve dışarı aktarmak için Apache Sqoop 'yi nasıl kullanacağınızı öğrenin. Bu belgedeki adımlar, doğrudan Hadoop kümesinin `sqoop` baş düğümüne öğesinden komutunu kullanır. Baş düğüme bağlanmak ve bu belgedeki komutları çalıştırmak için SSH 'yi kullanırsınız. Bu makalede, [HDInsight 'Ta Hadoop Ile Apache Sqoop kullanma](./hdinsight-use-sqoop.md)işlemi devam ediyor.

## <a name="prerequisites"></a>Önkoşullar

* [Test ortamını ayarlama](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) , [HDInsight 'Ta Hadoop Ile Apache Sqoop kullanın](./hdinsight-use-sqoop.md).

* Azure SQL veritabanını sorgulamak için bir istemci. [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) veya [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)kullanmayı düşünün.

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight 'A bağlanma (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Sqoop dışarı aktarma

Hive 'dan SQL Server.

1. HDInsight kümesine bağlanmak için SSH kullanın. Kümenizin `CLUSTERNAME` adıyla değiştirin, sonra şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. SQL Server `MYSQLSERVER` adıyla değiştirin. Sqoop 'nin SQL veritabanınızı görebildiğini doğrulamak için, Open SSH bağlantınızda aşağıdaki komutu girin. İstendiğinde SQL Server oturum açma parolasını girin. Bu komut, veritabanlarının bir listesini döndürür.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Öğesini `MYSQLSERVER` SQL Server adıyla ve `MYDATABASE` SQL veritabanınızın adıyla değiştirin. Hive `hivesampletable` tablosundan`mobiledata` SQL veritabanındaki tabloya veri aktarmak için, Open SSH bağlantınızda aşağıdaki komutu girin. İstendiğinde SQL Server oturum açma parolasını girin

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Verilerin verildiğini doğrulamak için, SQL istemcinizden aşağıdaki sorguları kullanarak aktarılmış verileri görüntüleyin:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Sqoop içeri aktarma

SQL Server 'den Azure depolama 'ya.

1. Öğesini `MYSQLSERVER` SQL Server adıyla ve `MYDATABASE` SQL veritabanınızın adıyla değiştirin. SQL veritabanındaki `mobiledata` `wasb:///tutorials/usesqoop/importeddata` tablodaki verileri HDInsight üzerindeki dizine aktarmak için Open SSH bağlantınızın aşağıdaki komutunu girin. İstendiğinde SQL Server oturum açma parolasını girin. Verilerdeki alanlar bir sekme karakteriyle ayrılır ve satırlar yeni satır karakteri ile sonlandırılır.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. İçeri aktarma işlemi tamamlandıktan sonra, yeni dizindeki verileri listelemek için Open SSH bağlantısına aşağıdaki komutu girin:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Sınırlamalar

* Toplu dışa aktarma-Linux tabanlı HDInsight Ile, Microsoft SQL Server veya Azure SQL veritabanı 'na veri aktarmak için kullanılan Sqoop Bağlayıcısı toplu eklemeleri desteklemez.

* Toplu işleme-Linux tabanlı HDInsight ile, eklemeleri gerçekleştirirken `-batch` anahtarı kullanırken, ekleme işlemlerini toplu hale getirmek yerine Sqoop birden çok ekleme yapar.

## <a name="important-considerations"></a>Önemli konular

* Hem HDInsight hem de SQL Server aynı Azure sanal ağında olmalıdır.

    Bir örnek için bkz. HDInsight 'ı Şirket [içi ağ belgenize bağlama](./../connect-on-premises-network.md) .

    Azure sanal ağıyla HDInsight kullanma hakkında daha fazla bilgi için bkz. HDInsight 'ı [Azure sanal ağ Ile genişletme](../hdinsight-plan-virtual-network-deployment.md) . Azure sanal ağ hakkında daha fazla bilgi için bkz. [sanal ağa genel bakış](../../virtual-network/virtual-networks-overview.md) belgesi.

* SQL Server, SQL kimlik doğrulamasına izin verecek şekilde yapılandırılmalıdır. Daha fazla bilgi için bkz. [kimlik doğrulama modu seçme](https://msdn.microsoft.com/ms144284.aspx) belgesi.

* SQL Server uzak bağlantıları kabul edecek şekilde yapılandırmanız gerekebilir. Daha fazla bilgi için [SQL Server veritabanı altyapısı belgesine bağlanma sorunlarını giderme](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

Şimdi Sqoop 'yi nasıl kullanacağınızı öğrendiniz. Daha fazla bilgi için bkz:

* [HDInsight Ile Apache Oozie kullanın](../hdinsight-use-oozie-linux-mac.md): Bir Oozie iş akışında Sqoop eylemini kullanın.
* [HDInsight kullanarak Uçuş gecikmesi verilerini analiz edin](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Uçuş gecikmesi verilerini çözümlemek için etkileşimli sorgu kullanın ve ardından verileri bir Azure SQL veritabanına aktarmak için Sqoop kullanın.
* [HDInsight 'a veri yükleme](../hdinsight-upload-data.md): HDInsight/Azure Blob depolamaya veri yüklemek için diğer yöntemleri bulun.
