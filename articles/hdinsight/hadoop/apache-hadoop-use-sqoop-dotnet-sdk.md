---
title: .NET ve HDInsight kullanarak Apache Sqoop işleri çalıştırma-Azure
description: Apache Hadoop kümesi ile Azure SQL veritabanı arasında Apache Sqoop içeri aktarma ve dışarı aktarma çalıştırmak için HDInsight .NET SDK 'sını nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, hdiseo17may2017, devx-track-csharp
ms.date: 01/14/2020
ms.openlocfilehash: a06326f5b4cf34a06475c10e368c6dcbb49ca9e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928349"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>HDInsight 'ta Apache Hadoop için .NET SDK kullanarak Apache Sqoop işleri çalıştırma

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

HDInsight kümesi ile Azure SQL veritabanı veya SQL Server veritabanı arasında içeri ve dışarı aktarmak için HDInsight 'ta Apache Sqoop işleri çalıştırmak üzere Azure HDInsight .NET SDK 'sını nasıl kullanacağınızı öğrenin.

## <a name="prerequisites"></a>Önkoşullar

* [Test ortamını ayarlama](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) , [HDInsight 'Ta Hadoop Ile Apache Sqoop kullanın](./hdinsight-use-sqoop.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Sqoop ile benzerlik. Daha fazla bilgi için bkz. [Sqoop Kullanıcı Kılavuzu](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>.NET SDK ile HDInsight kümelerinde Sqoop kullanma

HDInsight .NET SDK 'Sı .NET istemci kitaplıkları sağlar. bu sayede, .NET 'ten HDInsight kümeleriyle çalışmak daha kolay olur. Bu bölümde, ' yi `hivesampletable` önkoşullardan oluşturduğunuz Azure SQL veritabanı tablosuna aktarmak için bir C# konsol uygulaması oluşturursunuz.

## <a name="set-up"></a>Kurulum

1. Visual Studio 'Yu başlatın ve bir C# konsol uygulaması oluşturun.

1. **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **Paket Yöneticisi konsolu** ' na gidin ve şu komutu çalıştırın:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Sqoop dışarı aktarma

Hive 'dan SQL Server.  Bu örnek, Hive `hivesampletable` TABLOSUNDAN `mobiledata` SQL veritabanındaki tabloya veri aktarır.

1. Program. cs dosyasında aşağıdaki kodu kullanın. Ve değerlerini ayarlamak için kodu düzenleyin `ExistingClusterName` `ExistingClusterPassword` .

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. Programı çalıştırmak için **F5** tuşunu seçin.

## <a name="sqoop-import"></a>Sqoop içeri aktarma

SQL Server 'den Azure depolama 'ya. Bu örnek, yukarıdaki dışa aktarmaya bağımlıdır.  Bu örnek, `mobiledata` SQL veritabanındaki tablodaki verileri `wasb:///tutorials/usesqoop/importeddata` kümenin varsayılan depolama hesabındaki dizine aktarır.

1. Blokta yukarıdaki kodu `//sqoop start //sqoop end` aşağıdaki kodla değiştirin:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Programı çalıştırmak için **F5** tuşunu seçin.

## <a name="limitations"></a>Sınırlamalar

Linux tabanlı HDInsight aşağıdaki sınırlamaları sunar:

* Toplu dışa aktarma: Microsoft SQL Server veya Azure SQL veritabanı 'na veri aktarmak için kullanılan Sqoop Bağlayıcısı Şu anda toplu eklemeleri desteklememektedir.

* Toplu işleme: anahtar kullanılarak `-batch` , Sqoop ekleme işlemlerini toplu olarak gerçekleştirmek yerine birden çok ekleme gerçekleştirir.

## <a name="next-steps"></a>Sonraki adımlar

Şimdi Sqoop 'yi nasıl kullanacağınızı öğrendiniz. Daha fazla bilgi edinmek için şu makalelere bakın:

* [HDInsight Ile Apache Oozie kullanma](../hdinsight-use-oozie-linux-mac.md): bir Oozie Iş akışında Sqoop eylemini kullanın.
* [HDInsight 'a veri yükleme](../hdinsight-upload-data.md): HDInsight 'A veya Azure Blob depolamaya veri yüklemek için diğer yöntemleri bulun.
