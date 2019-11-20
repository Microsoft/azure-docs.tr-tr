---
title: Kapsayıcı desteği
titleSuffix: Azure Cognitive Services
description: Azure CLı 'dan Azure Container Instance kaynağı oluşturmayı öğrenin.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 2080d283c6cb7466dcb4847a81d76a4c3109217a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012316"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Azure CLı 'dan Azure Container Instance kaynağı oluşturma

Aşağıdaki YAML, Azure Container Instance kaynağını tanımlar. İçeriği kopyalayıp adlandırılan `my-aci.yaml` yeni bir dosyaya yapıştırın ve açıklama değerlerini kendi değerlerinizle değiştirin. Geçerli YAML için [şablon biçimine][template-format] bakın. Kullanılabilir görüntü adları ve bunlara karşılık gelen depo için [kapsayıcı depoları ve görüntüleri][repositories-and-images] inceleyin.

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials:
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Tüm konumlar aynı CPU ve bellek kullanılabilirliğine sahip değildir. Konum ve işletim sistemi başına kapsayıcılar için kullanılabilir kaynakların listesinin [konumu ve kaynakları][location-to-resource] tablosuna bakın.

[`az container create`][azure-container-create] Komutu için oluşturduğumuz YAML dosyasını kullanacağız. Azure CLI 'dan, `az container create` `<resource-group>` öğesini kendi ile değiştirme komutunu yürütün. Ek olarak, bir YAML dağıtımı içindeki değerlerin güvenliğini sağlamak için [güvenli değerler][secure-values]bölümüne bakın.

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Komutun `Running...` çıktısı geçerliyse, çıkış bir süre sonra yeni oluşturulan aci kaynağını temsil eden bir JSON dizesine değişir. Kapsayıcı görüntüsü büyük olasılıkla bir süre için kullanılamayabilir, ancak kaynak artık dağıtılır.

> [!TIP]
> YAML 'nin konumla eşleşecek şekilde ayarlanması gerektiği için, genel önizleme Azure bilişsel hizmet tekliflerinin konumlarına yakın bir şekilde dikkat edin.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
