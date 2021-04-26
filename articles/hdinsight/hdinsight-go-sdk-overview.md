---
title: Go için Azure HDInsight SDK
description: Go ve Apache Hadoop kümeleri için Azure HDInsight SDK 'Yı kullanmaya yönelik başvuru malzemeleri
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18, devx-track-azurecli
ms.date: 01/03/2020
ms.openlocfilehash: 09564bd149488d956586c8a0e349cd79eec358a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582755"
---
# <a name="hdinsight-sdk-for-go-preview"></a>Go için HDInsight SDK (Önizleme)

## <a name="overview"></a>Genel Bakış
Go için HDInsight SDK 'Sı, HDInsight kümelerinizi yönetmenize imkan tanıyan sınıflar ve işlevler sağlar. Oluşturma, silme, güncelleştirme, listeleme, yeniden boyutlandırma, betik eylemlerini yürütme, izleme, HDInsight kümelerinin özelliklerini al ve daha fazlasını içeren işlemleri içerir.

> [!NOTE]  
>Bu SDK için GoDoc başvuru malzemesine de [buradan ulaşabilirsiniz](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Bir [ `go get` araç](https://github.com/golang/go/wiki/GoGetTools).
* [Git](https://golang.org/dl/).

## <a name="sdk-installation"></a>SDK yüklemesi

GOPATH konumundan şunu çalıştırın `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Kimlik Doğrulaması

Önce SDK 'nın Azure aboneliğinizle doğrulanması gerekir.  Hizmet sorumlusu oluşturmak için aşağıdaki örneği izleyin ve kimlik doğrulaması için kullanın. Bu yapıldıktan sonra, `ClustersClient` yönetim işlemleri gerçekleştirmek için kullanılabilecek çok sayıda işlev (aşağıdaki bölümlerde özetlenen) içeren bir örneğine sahip olacaksınız.

> [!NOTE]  
> Aşağıdaki örnekte, gereksinimlerinize daha uygun olabilecek büyük olasılıkla kimlik doğrulamanın başka yolları vardır. Tüm işlevler burada özetlenmiştir: [Go için Azure SDK kimlik doğrulama işlevleri](/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Hizmet sorumlusu kullanarak kimlik doğrulama örneği

İlk olarak [Azure Cloud Shell](https://shell.azure.com/bash)oturum açın. Şu anda hizmet sorumlusu 'nın oluşturulmasını istediğiniz aboneliği kullandığınızı doğrulayın.

```azurecli-interactive
az account show
```

Abonelik bilgileriniz JSON olarak görüntülenir.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Doğru abonelikte oturum açmadıysanız, şunu çalıştırarak doğru olanı seçin: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> HDInsight kaynak sağlayıcısını başka bir işlev (örneğin, Azure portal aracılığıyla HDInsight kümesi oluşturma) ile henüz kaydolmadıysanız, kimlik doğrulamasından geçmeden önce bunu yapmanız gerekir. Bu, aşağıdaki komut çalıştırılarak [Azure Cloud Shell](https://shell.azure.com/bash) yapılabilir:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Sonra, hizmet sorumlusu için bir ad seçin ve aşağıdaki komutla oluşturun:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Hizmet sorumlusu bilgileri JSON olarak görüntülenir.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Aşağıdaki kod parçacığını kopyalayın ve `TENANT_ID` `CLIENT_ID` `CLIENT_SECRET` `SUBSCRIPTION_ID` hizmet sorumlusu oluşturmak için komutunu çalıştırdıktan sonra döndürülen JSON 'daki dizeleri kullanarak,, ve öğesini girin.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Küme yönetimi

> [!NOTE]  
> Bu bölümde, önceden kimlik doğrulamasından geçen ve bir örnek oluşturmuş olduğunuz ve `ClusterClient` adlı bir değişkende depoladığınız varsayılmaktadır `client` . Kimlik doğrulaması ve alma yönergeleri `ClusterClient` Yukarıdaki kimlik doğrulama bölümünde bulunabilir.

### <a name="create-a-cluster"></a>Küme oluşturma

Çağırarak yeni bir küme oluşturulabilir `client.Create()` . 

#### <a name="example"></a>Örnek

Bu örnekte, iki baş düğüm ve bir çalışan düğümü ile bir [Apache Spark](https://spark.apache.org/) kümesinin nasıl oluşturulacağı gösterilmektedir.

> [!NOTE]  
> İlk olarak, aşağıda açıklandığı gibi bir kaynak grubu ve depolama hesabı oluşturmanız gerekir. Bunları zaten oluşturduysanız, bu adımları atlayabilirsiniz.

##### <a name="creating-a-resource-group"></a>Kaynak grubu oluşturma

Çalıştıran [Azure Cloud Shell](https://shell.azure.com/bash) kullanarak bir kaynak grubu oluşturabilirsiniz

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Depolama hesabı oluşturma

Aşağıdakileri çalıştırarak [Azure Cloud Shell](https://shell.azure.com/bash) kullanarak bir depolama hesabı oluşturabilirsiniz:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Şimdi depolama hesabınızın anahtarını almak için aşağıdaki komutu çalıştırın (bir küme oluşturmak için bunu yapmanız gerekir):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
Aşağıdaki go kod parçacığı, iki baş düğüm ve bir çalışan düğümü olan bir Spark kümesi oluşturur. Açıklamalarda açıklandığı şekilde boş değişkenleri girin ve diğer parametreleri özel gereksinimlerinize uyacak şekilde değiştirin.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Küme ayrıntılarını al

Belirli bir kümenin özelliklerini almak için:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Örnek

`get`Kümenizi başarıyla oluşturduğdığınızı doğrulamak için ' i kullanabilirsiniz.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

Çıktının şöyle görünmesi gerekir:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Kümeleri Listele

#### <a name="list-clusters-under-the-subscription"></a>Abonelik altındaki kümeleri listeleyin

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Grupları kaynak grubuna göre Listele

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Hem hem de `List()` `ListByResourceGroup()` bir `ClusterListResultPage` struct döndürün. Sonraki sayfayı almak için çağırabilirsiniz `Next()` . Bu `ClusterListResultPage.NotDone()` `false` , aşağıdaki örnekte gösterildiği gibi dönüşene kadar tekrarlanabilir.

#### <a name="example"></a>Örnek

Aşağıdaki örnek, geçerli abonelik için tüm kümelerin özelliklerini yazdırır:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Küme silme

Bir kümeyi silmek için:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Küme etiketlerini güncelleştirme

Belirli bir kümenin etiketlerini şu şekilde güncelleştirebilirsiniz:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Örnek

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Kümeyi yeniden boyutlandır

Aşağıdakine benzer yeni bir boyut belirterek, belirli bir kümenin çalışan düğümü sayısını yeniden boyutlandırabilirsiniz:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Küme izleme

HDInsight Yönetim SDK 'Sı, Operations Management Suite (OMS) aracılığıyla kümelerinizde izlemeyi yönetmek için de kullanılabilir.

`ClusterClient`Yönetim işlemleri için kullanmak üzere oluşturduğunuz şekilde, `ExtensionClient` izleme işlemleri için kullanmak üzere bir oluşturmanız gerekir. Yukarıdaki kimlik doğrulama bölümünü tamamladıktan sonra, `ExtensionClient` şöyle bir şekilde oluşturabilirsiniz:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Aşağıdaki izleme örnekleri, zaten `ExtensionClient` çağrılan bir adı başlattığını `extClient` ve `Authorizer` yukarıda gösterildiği gibi ayarlamış olduğunu varsayar.

### <a name="enable-oms-monitoring"></a>OMS izlemeyi etkinleştir

> [!NOTE]  
> OMS Izlemesini etkinleştirmek için, mevcut bir Log Analytics çalışma alanına sahip olmanız gerekir. Henüz bir tane oluşturmadıysanız, bunu nasıl yapacağınızı öğrenebilirsiniz: [Azure portal Log Analytics çalışma alanı oluşturma](../azure-monitor/logs/quick-create-workspace.md).

Kümenizde OMS Izlemesini etkinleştirmek için:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>OMS izlemenin durumunu görüntüleme

Kümenizdeki OMS durumunu almak için:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>OMS izlemesini devre dışı bırak

Kümenizde OMS 'yi devre dışı bırakmak için:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Betik eylemleri

HDInsight, kümeyi özelleştirmek için özel betikler çağıran betik eylemleri adlı bir yapılandırma işlevi sağlar.

> [!NOTE]  
> Betik eylemlerinin kullanımı hakkında daha fazla bilgi burada bulunabilir: [betik eylemleri kullanarak Linux tabanlı HDInsight kümelerini özelleştirme](./hdinsight-hadoop-customize-cluster-linux.md)

### <a name="execute-script-actions"></a>Betik eylemlerini yürütme

Belirli bir kümede betik eylemlerini şu şekilde yürütebilirsiniz:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

' Betik eylemini Sil ' ve ' kalıcı betik eylemlerini Listele ' işlemleri için, `ScriptActionsClient` `ClusterClient` yönetim işlemleri için kullanmak üzere nasıl oluşturduğunuza benzer şekilde bir oluşturmanız gerekir. Yukarıdaki kimlik doğrulama bölümünü tamamladıktan sonra şöyle bir oluşturabilirsiniz `ScriptActionsClient` :

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Aşağıdaki betik eylemleri örnekleri, zaten bir çağrılmış olduğunu varsayar `ScriptActionsClient` `scriptActionsClient` ve `Authorizer` yukarıda gösterildiği gibi ayarlayın.

### <a name="delete-script-action"></a>Betik eylemini Sil

Belirli bir kümede belirtilen kalıcı betik eylemini silmek için:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Kalıcı betik eylemlerini listeleme

> [!NOTE]  
> Her ikisi de `ListByCluster()` bir `ScriptActionsListPage` struct döndürür. Sonraki sayfayı almak için çağırabilirsiniz `Next()` . Bu `ClusterListResultPage.NotDone()` `false` , aşağıdaki örnekte gösterildiği gibi dönüşene kadar tekrarlanabilir.

Belirtilen küme için tüm kalıcı betik eylemlerini listelemek için:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Örnek

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="list-all-scripts-execution-history"></a>Tüm betiklerin yürütme geçmişini Listele

Bu işlem için, `ScriptExecutionHistoryClient` `ClusterClient` yönetim işlemleri için kullanmak üzere nasıl oluşturduğunuza benzer şekilde, oluşturmanız gerekir. Yukarıdaki kimlik doğrulama bölümünü tamamladıktan sonra şöyle bir oluşturabilirsiniz `ScriptActionsClient` :

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Aşağıda, zaten bir `ScriptExecutionHistoryClient` çağrılan `scriptExecutionHistoryClient` ve ' ın `Authorizer` yukarıda gösterildiği gibi ayarlamış olduğunuz varsayılmaktadır.

Belirtilen küme için tüm betiklerin yürütme geçmişini listelemek için:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Örnek

Bu örnek, tüm geçmiş betik yürütmelerinin tüm ayrıntılarını yazdırır.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

[Godoc başvuru malzemesini](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight)bulun. GoDocs, SDK 'daki tüm işlevler için başvuru belgeleri sağlar.
