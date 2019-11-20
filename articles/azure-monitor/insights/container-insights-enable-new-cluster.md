---
title: Yeni bir Azure Kubernetes hizmeti (AKS) kümesini izleme | Microsoft Docs
description: Kapsayıcılar için Azure Izleyici aboneliğine sahip yeni bir Azure Kubernetes hizmeti (AKS) kümesi için izlemeyi nasıl etkinleştireceğinizi öğrenin.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/25/2019
ms.openlocfilehash: 01553eb020c1493488e941705d9df8c8c946340a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73478705"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Yeni bir Azure Kubernetes hizmeti (AKS) kümesini izlemeyi etkinleştir

Bu makalede, aboneliğinizden dağıtmaya hazırlanmakta olduğunuz [Azure Kubernetes hizmetinde](https://docs.microsoft.com/azure/aks/) barındırılan yönetilen Kubernetes kümesini izlemek için kapsayıcılar Için Azure izleyici 'nin nasıl ayarlanacağı açıklanır.

Desteklenen yöntemlerden birini kullanarak bir AKS kümesinin izlenmesini etkinleştirebilirsiniz:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Azure CLı kullanarak etkinleştirme

Azure CLı ile oluşturulan yeni bir AKS kümesinin izlenmesini etkinleştirmek için, [aks kümesi oluşturma](../../aks/kubernetes-walkthrough.md#create-aks-cluster)bölümünde yer alarak hızlı başlangıç makalesindeki adımları izleyin.  

>[!NOTE]
>Azure CLı 'yı kullanmayı seçerseniz, önce CLı 'yi yerel olarak yüklemeniz ve kullanmanız gerekir. Azure CLı sürüm 2.0.74 veya üstünü çalıştırıyor olmanız gerekir. Sürümünüzü belirlemek için `az --version`çalıştırın. Azure CLı 'yi yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 'Yı yüklemek](https://docs.microsoft.com/cli/azure/install-azure-cli). Aks-Preview CLı uzantısı sürümü 0.4.12 veya üstünü yüklediyseniz, AKS Önizleme özellikleri Azure US Governmnet bulutu 'nda kullanılamadığından, önizleme uzantısını etkinleştirmek için yaptığınız tüm değişiklikleri kaldırın.

## <a name="enable-using-terraform"></a>Terrayform kullanarak etkinleştir

[Teresform kullanarak yeni bir AKS kümesi dağıtıyorsanız](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), mevcut bir tane belirtmeyi seçmediyseniz [Log Analytics bir çalışma alanı oluşturmak için](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) profilde gereken bağımsız değişkenleri belirtirsiniz. 

>[!NOTE]
>Terrayform kullanmayı seçerseniz, Teraform Azure RM Provider sürüm 1.17.0 veya üstünü çalıştırıyor olmanız gerekir.

Çalışma alanına kapsayıcılar için Azure Izleyici eklemek için bkz. [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) ve [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) 'i ekleyerek profili doldurun ve **oms_agent**belirtin. 

İzlemeyi etkinleştirdikten ve tüm yapılandırma görevleri başarıyla tamamlandıktan sonra, kümenizin performansını iki şekilde izleyebilirsiniz:

* Sol bölmedeki **sistem durumu** ' nu seçerek doğrudan aks kümesinde.
* Seçili küme için AKS kümesi sayfasında bulunan **kapsayıcı öngörülerini izle** kutucuğunu seçin. Azure Izleyici 'de, sol bölmede **sistem durumu**' nu seçin. 

  ![AKS 'teki kapsayıcılar için Azure Izleyicisini seçme seçenekleri](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

İzlemeyi etkinleştirdikten sonra, küme için sistem durumu ölçümlerini görüntüleyebilmeniz yaklaşık 15 dakika sürebilir. 

## <a name="verify-agent-and-solution-deployment"></a>Aracıyı ve çözüm dağıtımını doğrulama
Aracı sürümü *06072018* veya sonraki sürümlerde, hem aracının hem de çözümün başarıyla dağıtıldığını doğrulayabilirsiniz. Aracının önceki sürümleriyle, yalnızca aracı dağıtımını doğrulayabilirsiniz.

### <a name="agent-version-06072018-or-later"></a>Aracı sürüm 06072018 veya üzeri
Aracının başarıyla dağıtıldığını doğrulamak için aşağıdaki komutu çalıştırın. 

```
kubectl get ds omsagent --namespace=kube-system
```

Çıktının düzgün şekilde dağıtıldığını belirten aşağıdakine benzer olması gerekir:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Çözümün dağıtımını doğrulamak için şu komutu çalıştırın:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Çıktının düzgün şekilde dağıtıldığını belirten aşağıdakine benzer olması gerekir:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>06072018 'den önceki aracı sürümü

*06072018* ' den önce yayınlanan Log Analytics Agent sürümünün düzgün şekilde dağıtılmadığını doğrulamak için şu komutu çalıştırın:  

```
kubectl get ds omsagent --namespace=kube-system
```

Çıktının düzgün şekilde dağıtıldığını belirten aşağıdakine benzer olması gerekir:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>CLı ile yapılandırmayı görüntüleme
Çözüm etkin değil veya Not, Log Analytics çalışma alanı RESOURCEID nedir ve kümeyle ilgili Özet ayrıntılar gibi ayrıntıları almak için `aks show` komutunu kullanın.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Birkaç dakika sonra komut tamamlanır ve çözüm hakkında JSON biçimli bilgileri döndürür.  Komutun sonuçları izleme eklentisi profilini göstermelidir ve aşağıdaki örnek çıktıya benzer:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Sonraki adımlar

* Çözümü oluşturmaya çalışırken sorunlarla karşılaşırsanız, [sorun giderme kılavuzunu](container-insights-troubleshoot.md) gözden geçirin

* İzleme etkinken, AKS kümeniz ve üzerinde çalışan iş yüklerinizin sistem durumunu ve kaynak kullanımını toplayıp, kapsayıcılar için Azure Izleyicisini [nasıl kullanacağınızı](container-insights-analyze.md) öğrenin.
