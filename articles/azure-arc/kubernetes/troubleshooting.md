---
title: Yaygın Azure Arc etkin Kubernetes sorunlarını giderme
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Arc etkin Kubernetes kümeleriyle ilgili yaygın sorunları giderme.
keywords: Kubernetes, yay, Azure, kapsayıcılar
ms.openlocfilehash: 992ea75c48b2630032e1314610986fbc610eec7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025790"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Azure Arc etkin Kubernetes sorunlarını giderme

Bu belge bağlantı, izin ve aracılarla ilgili sorunlar için sorun giderme kılavuzu sağlar.

## <a name="general-troubleshooting"></a>Genel sorun giderme

### <a name="azure-cli"></a>Azure CLI’si

`az connectedk8s`Veya `az k8s-configuration` CLI komutlarını kullanmadan önce, Azure CLI 'Nin doğru Azure aboneliğine göre çalışacak şekilde ayarlandığından emin olun.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure Arc aracıları

Azure Arc etkin Kubernetes için tüm aracılar ad alanında Pod olarak dağıtılır `azure-arc` . Tüm yığınların çalışıyor olması ve sistem durumu denetimlerinin geçirilmesi gerekir.

İlk olarak, Azure Arc helb sürümünü doğrulayın:

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Helu sürümü bulunamazsa veya eksikse, [kümeyi tekrar Azure yaya bağlamayı](./quickstart-connect-cluster.md) deneyin.

Helu sürümü varsa `STATUS: deployed` , şu kullanarak aracıların durumunu denetleyin `kubectl` :

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Tüm Pod 'ler `STATUS` `Running` `3/3` `2/2` , sütununda veya altında olarak göstermelidir `READY` . Günlükleri getirin ve bir veya döndüren Pod 'leri `Error` tanıtın `CrashLoopBackOff` . Herhangi bir pod `Pending` durumunda kalırsa, küme düğümlerinde yeterli kaynak olmayabilir. [Kümenizin ölçeğini ölçeklendirmek](https://kubernetes.io/docs/tasks/administer-cluster/) , bu yığınların duruma geçmesine izin verebilir `Running` .

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Kubernetes kümelerini Azure yaya bağlama

Kümelerin Azure 'a bağlanması için hem Azure aboneliğine hem de `cluster-admin` hedef kümeye erişim gerekir. Kümeye ulaşamadıysanız veya izinleriniz yetersizse, kümeyi Azure yaya bağlamak başarısız olur.

### <a name="insufficient-cluster-permissions"></a>Küme izinleri yetersiz

Belirtilen kubeconfig dosyası, Azure Arc aracılarını yüklemek için yeterli izinlere sahip değilse, Azure CLı komutu bir hata döndürür.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

Kümeyi Azure yaya bağlayan kullanıcının `cluster-admin` kümede role atanmış olması gerekir.

### <a name="installation-timeouts"></a>Yükleme zaman aşımları

Bir Kubernetes kümesini Azure Arc etkin Kubernetes 'e bağlamak, kümede Azure Arc aracılarının yüklenmesini gerektirir. Küme yavaş bir internet bağlantısı üzerinden çalışıyorsa, aracıların kapsayıcı görüntüsü çekme işlemi Azure CLı zaman aşımlarından daha uzun sürebilir.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helb sorunu

`v3.3.0-rc.1`Helmversion, Held yüklemesi/yükseltmesi (CLI uzantısı tarafından kullanılır), [](https://github.com/helm/helm/pull/8527) `connectedk8s` Tüm kancaları aşağıdaki hata ile çalıştırmaya neden olan bir soruna sahiptir:

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Bu sorundan kurtulmak için aşağıdaki adımları izleyin:

1. Azure portal Azure Arc etkin Kubernetes kaynağını silin.
2. Makinenizde aşağıdaki komutları çalıştırın:
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. Release Candidate sürümü yerine makinenize Held 3 ' ün [kararlı bir sürümünü yükler](https://helm.sh/docs/intro/install/) .
4. `az connectedk8s connect`Kümeyi Azure yaya bağlamak için uygun değerlerle komutu çalıştırın.

## <a name="configuration-management"></a>Yapılandırma yönetimi

### <a name="general"></a>Genel
Yapılandırma kaynağıyla ilgili sorunları gidermeye yardımcı olmak için, belirtilen parametreyle az Commands komutunu çalıştırın `--debug` .

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>Yapılandırma oluşturma

Azure Arc etkin Kubernetes kaynağı () üzerinde yazma izinleri `Microsoft.Kubernetes/connectedClusters/Write` gereklidir ve bu kümede yapılandırma oluşturmak için yeterlidir.

### <a name="configuration-remains-pending"></a>Yapılandırma kalır `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>İzleme

Kapsayıcılar için Azure Izleyici, DaemonSet 'in ayrıcalıklı modda çalıştırılmasını gerektirir. İzleme için kurallı bir Kubernetes kümesini başarılı bir şekilde ayarlamak için aşağıdaki komutu çalıştırın:

```console
juju config kubernetes-worker allow-privileged=true
```