---
title: HDInsight 'ta .NET SDK ile Apache Hadoop kümelerini yönetme-Azure
description: HDInsight .NET SDK kullanarak HDInsight 'ta Apache Hadoop kümeleri için yönetim görevleri gerçekleştirmeyi öğrenin.
ms.service: hdinsight
ms.custom: hdinsightactive, devx-track-csharp
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: e5f328d6be52732c30478eb49360efb13919a23e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872226"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>.NET SDK'sını kullanarak HDInsight'taki Apache Hadoop kümelerini yönetme

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[HDINSIGHT.NET SDK](/dotnet/api/overview/azure/hdinsight)kullanarak HDInsight kümelerini yönetmeyi öğrenin.

**Önkoşullar**

Bu makaleye başlamadan önce aşağıdakilere sahip olmanız ve aşağıdaki işlemleri yapmış olmanız gerekir:

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Azure HDInsight 'a bağlanma

Aşağıdaki NuGet paketlerine ihtiyacınız vardır:

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

Aşağıdaki kod örneği, Azure aboneliğinizdeki HDInsight kümelerini yönetebilmeniz için önce Azure 'a nasıl bağlanabilmeniz gerektiğini gösterir.

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
    }
}
```

Bu programı çalıştırdığınızda bir istem görürsünüz.  İstemi görmek istemiyorsanız, bkz. [etkileşimli olmayan kimlik doğrulaması .net HDInsight uygulamaları oluşturma](hdinsight-create-non-interactive-authentication-dotnet-applications.md).


## <a name="list-clusters"></a>Kümeleri Listele

Aşağıdaki kod parçacığında kümeler ve bazı özellikler listelenir:

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>Kümeleri Sil

Bir kümeyi zaman uyumlu veya zaman uyumsuz olarak silmek için aşağıdaki kod parçacığını kullanın: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Ölçek Kümeleri

Küme ölçekleme özelliği, kümeyi yeniden oluşturmaya gerek kalmadan Azure HDInsight 'ta çalışan bir küme tarafından kullanılan çalışan düğümlerinin sayısını değiştirmenize izin verir.

> [!NOTE]  
> Yalnızca HDInsight sürümü 3.1.3 veya üzeri olan kümeler desteklenir. Kümenizin sürümünden emin değilseniz Özellikler sayfasını kontrol edebilirsiniz.  Bkz. [liste ve kümeleri gösterme](hdinsight-administer-use-portal-linux.md#showClusters).

HDInsight tarafından desteklenen her küme türü için veri düğümlerinin sayısını değiştirmenin etkisi:

* Apache Hadoop
  
    Bekleyen veya çalışan bir işi etkilemeden, çalışan bir Hadoop kümesinde çalışan düğümlerinin sayısını sorunsuzca artırabilirsiniz. İşlem devam ederken yeni işler de gönderilebilir. Ölçeklendirme işlemindeki başarısızlık, kümenin her zaman işlevsel bir durumda bırakılması için düzgün şekilde işlenir.
  
    Bir Hadoop kümesi, veri düğümleri sayısını azaltarak aşağı ölçeklendirilirse, kümedeki hizmetlerin bazıları yeniden başlatılır. Bu, tüm çalışan ve bekleyen işlerin ölçeklendirme işleminin tamamlanmasından sonra başarısız olmasına neden olur. Ancak, işlem tamamlandıktan sonra işleri yeniden gönderebilirsiniz.
* Apache HBase
  
    Çalışırken HBase kümenize düğümleri sorunsuzca ekleyebilir veya kaldırabilirsiniz. Bölgesel sunucular, ölçeklendirme işleminin tamamlanması birkaç dakika içinde otomatik olarak dengelenir. Bununla birlikte, kümenin baş düğümüne ' da oturum açarak ve bir komut istemi penceresinden aşağıdaki komutları çalıştırarak bölgesel sunucuları el ile dengeleyebilirsiniz:
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    Veri düğümlerini, çalışırken fırtınası kümenize sorunsuzca ekleyebilir veya kaldırabilirsiniz. Ancak ölçeklendirme işleminin başarılı bir şekilde tamamlandıktan sonra topolojiyi yeniden dengelemeniz gerekir.
  
    Yeniden dengeleme iki şekilde sağlanabilir:
  
  * Fırtınası Web Kullanıcı arabirimi
  * Komut satırı arabirimi (CLı) aracı
    
    Daha fazla bilgi için lütfen [Apache Storm belgelerine](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) bakın.
    
    Fırtınası Web Kullanıcı arabirimi HDInsight kümesinde kullanılabilir:
    
    :::image type="content" source="./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png" alt-text="HDInsight fırtınası ölçeği yeniden dengeleme":::
    
    Aşağıda, bir örneğin, bir fırtınası topolojisini yeniden dengelemek için CLı komutunun nasıl kullanılacağı gösterilmektedir:
    

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Aşağıdaki kod parçacığı, bir kümenin zaman uyumlu veya zaman uyumsuz olarak nasıl yeniden boyutlandırılacağını göstermektedir:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Erişim verme/iptal etme

HDInsight kümeleri aşağıdaki HTTP Web hizmetlerine sahiptir (Bu hizmetlerin tümü, yeniden oluşan uç noktalara sahiptir):

* ODBC
* JDBC
* Apache ambarı
* Apache Oozie
* Apache Templet

Varsayılan olarak, bu hizmetler erişim için verilir. Erişimi iptal edebilir/atayabilirsiniz. İptal etmek için:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Vermek için:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]  
> Erişimi vererek/iptal ederek, küme Kullanıcı adı ve parolasını sıfırlayacaksınız.

Bu, Portal üzerinden de yapılabilir. Bkz. [HDInsight 'ta Apache Hadoop kümelerini Azure Portal kullanarak yönetme](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>HTTP Kullanıcı kimlik bilgilerini güncelleştirme

HTTP erişimini verme/iptal etme ile aynı yordamdır.  Kümeye HTTP erişimi verildiyse, önce onu iptal etmeniz gerekir.  Ardından, yeni HTTP Kullanıcı kimlik bilgileriyle erişime izin verin.

## <a name="find-the-default-storage-account"></a>Varsayılan depolama hesabını bulun

Aşağıdaki kod parçacığı, bir küme için varsayılan depolama hesabı adının ve varsayılan depolama hesabı anahtarının nasıl alınacağını gösterir.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>İşleri gönder

**MapReduce işlerini göndermek için**

Bkz. [HDInsight 'Ta MapReduce örneklerini çalıştırma](hadoop/apache-hadoop-run-samples-linux.md).

**Apache Hive işleri göndermek için** 

Bkz. [.NET SDK kullanarak Apache Hive sorguları çalıştırma](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Apache Sqoop işleri göndermek için**

Bkz. [HDInsight Ile Apache Sqoop kullanma](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Apache Oozie işleri göndermek için**

[HDInsight 'ta bir iş akışı tanımlamak ve çalıştırmak için bkz. Hadoop Ile Apache Oozie kullanma](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Azure Blob depolama alanına veri yükleme

Bkz. [HDInsight'a veri yükleme][hdinsight-upload-data].

## <a name="see-also"></a>Ayrıca Bkz.

* [HDInsight .NET SDK başvuru belgeleri](/dotnet/api/overview/azure/hdinsight)
* [HDInsight 'ta Apache Hadoop kümelerini Azure portal kullanarak yönetin](hdinsight-administer-use-portal-linux.md)
* [Bir komut satırı arabirimi kullanarak HDInsight 'ı yönetme][hdinsight-admin-cli]
* [HDInsight kümeleri oluşturma][hdinsight-provision]
* [HDInsight'a veri yükleme][hdinsight-upload-data]
* [Azure HDInsight 'ı kullanmaya başlama][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
