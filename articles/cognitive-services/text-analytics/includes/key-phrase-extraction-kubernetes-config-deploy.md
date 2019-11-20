---
title: Kubernetes config ve Deploy adımları Anahtar İfade Ayıklama
titleSuffix: Azure Cognitive Services
description: Kubernetes config ve Deploy adımları Anahtar İfade Ayıklama
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/19/2019
ms.author: dapine
ms.openlocfilehash: 5ec535fe2ce23a2ead1163e870aae97fd104ef09
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130063"
---
### <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>Anahtar İfade Ayıklama kapsayıcısını bir AKS kümesine dağıtma

1. Azure CLı 'yi açın ve Azure 'da oturum açın.

    ```azurecli
    az login
    ```

1. AKS kümesinde oturum açın. `your-cluster-name` Ve`your-resource-group` değerlerini uygun değerlerle değiştirin.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Bu komut çalıştıktan sonra şuna benzer bir ileti bildirir:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Azure hesabınızda kullanabileceğiniz birden çok aboneliğiniz varsa ve `az aks get-credentials` komut bir hatayla döndürülürse, yaygın bir sorun, yanlış aboneliği kullanıyor olmanız olabilir. Azure CLı oturumunuzun bağlamını, kaynaklarını oluşturduğunuz aboneliğin aynısını kullanacak şekilde ayarlayın ve yeniden deneyin.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. İstediğiniz metin düzenleyicisini açın. Bu örnek Visual Studio Code kullanır.

    ```azurecli
    code .
    ```

1. Metin Düzenleyicisi içinde *keyphrase. YAML*adlı yeni bir dosya oluşturun ve içine aşağıdaki YAML 'yi yapıştırın. `billing/value` Ve`apikey/value` bilgilerinizi kendi bilgileriniz ile değiştirdiğinizden emin olun.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
            ports:
            - containerPort: 5000
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. Dosyayı kaydedin ve metin düzenleyicisini kapatın.
1. `apply` *Keyphrase. YAML* dosyası ile Kubernetes komutunu hedefi olarak çalıştırın:

    ```console
    kuberctl apply -f keyphrase.yaml
    ```

    Komut, dağıtım yapılandırmasını başarılı bir şekilde uyguladıktan sonra aşağıdaki çıktıya benzer bir ileti görünür:

    ```console
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Pod 'ın dağıtıldığını doğrulayın:

    ```console
    kubectl get pods
    ```

    Pod 'un çalışma durumunun çıkışı:

    ```console
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Hizmetin kullanılabilir olduğunu doğrulayın ve IP adresini alın.

    ```console
    kubectl get services
    ```

    Pod 'da *keyphrase* hizmetinin çalışma durumunun çıkışı:

    ```console
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
