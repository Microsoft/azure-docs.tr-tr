---
title: Azure Arc etkin Kubernetes kümesi uzantıları
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Arc etkin Kubernetes üzerinde uzantıların yaşam döngüsünü dağıtın ve yönetin
ms.openlocfilehash: 854d7418515d7927a3c0b4b8790ed4770af555ab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312629"
---
# <a name="kubernetes-cluster-extensions"></a>Kubernetes kümesi uzantıları

Kubernetes uzantıları özelliği, Azure Arc etkin Kubernetes kümelerinde aşağıdakileri sağlar:

* Küme uzantısının Azure Resource Manager tabanlı dağıtımı.
* Uzantı helb grafiklerinin yaşam döngüsü yönetimi.

Bu özelliğe kavramsal genel bakış, [küme uzantıları-Azure Arc etkin Kubernetes](conceptual-extensions.md) makalesinde bulunabilir.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Önkoşullar

- Azure CLı 'yı >= 2.16.0 sürümüne [yükler veya yükseltin](https://docs.microsoft.com/cli/azure/install-azure-cli) .
- `connectedk8s` (sürüm >= 1.1.0) ve `k8s-extension` (sürüm >= 0.2.0) Azure CLI uzantıları. Aşağıdaki komutları çalıştırarak bu Azure CLı uzantılarını yüklemelisiniz:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    ```
    
    `connectedk8s`Ve `k8s-extension` Uzantısı zaten yüklüyse, aşağıdaki komutu kullanarak bunları en son sürüme güncelleştirebilirsiniz:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    ```

- Mevcut bir Azure Arc, Kubernetes bağlı kümesini etkinleştirdi.
    - Henüz bir kümeye bağlanmadıysanız [hızlı](quickstart-connect-cluster.md)başlangıçlarımızı kullanın.
    - [Aracılarınızı sürüme yükseltin](agent-upgrade.md#manually-upgrade-agents) >= 1.1.0.

## <a name="currently-available-extensions"></a>Şu anda kullanılabilir uzantılar

| Dahili numara | Açıklama |
| --------- | ----------- |
| [Azure İzleyici](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) | Kubernetes kümesine dağıtılan iş yüklerinin performansına ilişkin görünürlük sağlar. Denetleyiciler, düğümler ve kapsayıcılardan bellek ve CPU kullanımı ölçümlerini toplar. |
| [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json) | Kubernetes kümesinden denetim günlüğü verileri gibi güvenlikle ilgili bilgileri toplar. Toplanan verileri temel alan öneriler ve tehdit uyarıları sağlar. |

## <a name="usage-of-cluster-extensions"></a>Küme uzantılarının kullanımı

### <a name="create-extensions-instance"></a>Uzantı örneği oluştur

`k8s-extension create`Zorunlu parametreler için değerler geçirerek, ile yeni bir uzantı örneği oluşturun. Aşağıdaki komut, Azure Arc etkin Kubernetes kümesinde kapsayıcılar uzantısı örneği için bir Azure Izleyicisi oluşturur:

```azurecli
az k8s-extension create --name azuremonitor-containers  --extension-type Microsoft.AzureMonitor.Containers --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Çıktıların**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": null,
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

> [!NOTE]
> * Hizmet, 48 saatten uzun bir süreyle hassas bilgileri alamıyor. Azure Arc etkin Kubernetes aracılarında 48 saatten fazla ağ bağlantısı yoksa ve kümede bir uzantı oluşturulup oluşturulmayacağını belirleyemezseniz, uzantı `Failed` durumuna geçer. Durum ' dan bir kez `Failed` , `k8s-extension create` Yeni bir uzantı Azure kaynağı oluşturmak için yeniden çalıştırmanız gerekir.
> * * Kapsayıcılar için Azure Izleyici, tek bir uzantıdır (küme başına yalnızca bir tane gereklidir). Uzantılar aracılığıyla aynı yüklemeden önce kapsayıcılar için Azure Izleyici 'nin (Uzantılar olmadan) önceki Helm grafik yüklemelerini temizlemeniz gerekir. [Çalıştırmadan `az k8s-extension create` önce Held grafiğini silme ](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-optout-hybrid)yönergelerini izleyin.

**Gerekli parametreler**

| Parametre adı | Açıklama |
|----------------|------------|
| `--name` | Uzantı örneğinin adı |
| `--extension-type` | Kümeye yüklemek istediğiniz uzantının türü. Örneğin: Microsoft. AzureMonitor. containers, Microsoft. azuredefender. Kubernetes | 
| `--scope` | Uzantı için yükleme kapsamı- `cluster` veya `namespace` |
| `--cluster-name` | Uzantı örneğinin oluşturulması gereken Azure Arc etkin Kubernetes kaynağı adı |
| `--resource-group` | Azure Arc etkin Kubernetes kaynağını içeren kaynak grubu |
| `--cluster-type` | Uzantı örneğinin oluşturulması gereken küme türü. Yalnızca geçerli `connectedClusters` olan Azure Arc etkin Kubernetes 'e karşılık gelen bir değer kabul edilir |

**İsteğe bağlı parametreler**

| Parametre adı | Açıklama |
|--------------|------------|
| `--auto-upgrade-minor-version` | Uzantının ikincil sürümünün otomatik olarak yükseltildiğini belirten Boolean özelliği. Varsayılan: `true`.  Bu parametre true olarak ayarlanırsa, `version` Sürüm dinamik olarak güncelleştirildiğinden parametre ayarlayamazsınız. Olarak ayarlanırsa `false` , uzantı, yama sürümleri için bile otomatik olarak yükseltilmeyecektir. |
| `--version` | Yüklenecek uzantının sürümü (uzantı örneğini sabitlemek için belirli bir sürüm). Otomatik yükseltme-alt-sürümü olarak ayarlandıysa, sağlanmamalıdır `true` . |
| `--configuration-settings` | İşlevselliğini denetlemek için uzantıya geçirilebilecek ayarlar. Parametre adından sonra alana ayrılmış çiftler olarak geçirilirler `key=value` . Komutta Bu parametre kullanılırsa, `--configuration-settings-file` aynı komutta kullanılamaz. |
| `--configuration-settings-file` | Yapılandırma ayarlarını uzantıya geçirmek için kullanılacak anahtar değer çiftlerine sahip JSON dosyasının yolu. Komutta Bu parametre kullanılırsa, `--configuration-settings` aynı komutta kullanılamaz. |
| `--configuration-protected-settings` | Bu ayarlar `GET` , API çağrıları veya komutları kullanılarak alınabilir değildir `az k8s-extension show` ve bu nedenle hassas ayarları geçirmek için kullanılır. Parametre adından sonra alana ayrılmış çiftler olarak geçirilirler `key=value` . Komutta Bu parametre kullanılırsa, `--configuration-protected-settings-file` aynı komutta kullanılamaz. |
| `--configuration-protected-settings-file` | Anahtara duyarlı ayarları geçirmek için kullanılacak anahtar değer çiftlerine sahip JSON dosyasının yolu. Komutta Bu parametre kullanılırsa, `--configuration-protected-settings` aynı komutta kullanılamaz. |
| `--release-namespace` | Bu parametre, sürümün oluşturulacağı ad alanını belirtir. Bu parametre yalnızca `scope` parametresi olarak ayarlandıysa geçerlidir `cluster` . |
| `--release-train` |  Uzantı yazarları,, vb. gibi farklı sürüm sunumlarında sürümler `Stable` yayımlayabilir `Preview` . Bu parametre açıkça ayarlanmamışsa, `Stable` Varsayılan olarak kullanılır. Bu parametre `autoUpgradeMinorVersion` , parametresi olarak ayarlandığında kullanılamaz `false` . |
| `--target-namespace` | Bu parametre, sürümünün oluşturulacağı ad alanını gösterir. Bu uzantı örneği için oluşturulan sistem hesabının izni, bu ad alanı ile kısıtlanıyor. Bu parametre yalnızca `scope` parametresi olarak ayarlandıysa geçerlidir `namespace` . |

### <a name="show-details-of-an-extension-instance"></a>Uzantı örneğinin ayrıntılarını göster

Şu anda yüklü olan bir uzantı örneğinin ayrıntılarını `k8s-extension show` , zorunlu parametreler için değerler geçirerek görüntüleyin:

```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Çıktıların**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": "2021-04-02T12:13:49.636+00:00",
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>Kümede yüklü olan tüm uzantıları listeleme

Bir kümede yüklü olan tüm uzantıları `k8s-extension list` , zorunlu parametreler için değerler geçirerek listeleyin.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Çıktıların**

```json
[
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-15T02:26:03.5519523+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "Microsoft.AzureMonitor.Containers",
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/myExtInstanceName",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-15T02:48:45.6469664+00:00",
    "lastStatusTime": null,
    "name": "myExtInstanceName",
    "releaseTrain": "Stable",
    "resourceGroup": "myRG",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName1"
      }
    },
    "statuses": [],
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  },
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-02T00:41:16.8005159+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "microsoft.azuredefender.kubernetes",
    "id": "/subscriptions/0e849346-4343-582b-95a3-e40e6a648ae1/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/defender",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-02T00:41:16.8005162+00:00",
    "lastStatusTime": null,
    "name": "microsoft.azuredefender.kubernetes",
    "releaseTrain": "Stable",
    "resourceGroup": "myRg",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName2"
      }
    },
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  }
]
```

### <a name="delete-extension-instance"></a>Uzantı örneğini Sil

`k8s-extension delete`Zorunlu parametreler için değerler geçirerek bir kümedeki uzantı örneğini silin.

```azurecli
az k8s-extension delete --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

>[!NOTE]
> Bu uzantıyı temsil eden Azure kaynağı hemen silinir. Bu uzantıyla ilişkili kümedeki helk sürümü yalnızca, Kubernetes kümesinde çalışan aracıların ağ bağlantısı olduğunda ve istenen durumu getirmek için Azure hizmetlerine tekrar ulaştıklarında silinir.


## <a name="next-steps"></a>Sonraki adımlar

Şu anda Azure Arc etkin Kubernetes için kullanılabilen küme uzantıları hakkında daha fazla bilgi edinin:
> [!div class="nextstepaction"]
> [Azure izleyici](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) 
>  [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)