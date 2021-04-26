---
title: Kapsayıcı örneğinde ortam değişkenlerini ayarlama
description: Azure Container Instances çalıştırdığınız kapsayıcılarda ortam değişkenlerini ayarlamayı öğrenin
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 9d95ee3d64460aa5e11f450c9e582cdc0de4f0ae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790806"
---
# <a name="set-environment-variables-in-container-instances"></a>Kapsayıcı örneklerinde ortam değişkenlerini ayarlama

Kapsayıcı örneklerinizde ortam değişkenleri ayarlamanız, kapsayıcı tarafından çalıştırılan uygulamanın veya betiğin dinamik yapılandırmasını sağlamanıza imkan tanır. Bu, `--env` için komut satırı bağımsız değişkenine benzerdir `docker run` . 

Bir kapsayıcıda ortam değişkenlerini ayarlamak için, bir kapsayıcı örneği oluşturduğunuzda bunları belirtin. Bu makalede, [Azure CLI](#azure-cli-example), [Azure PowerShell](#azure-powershell-example)ve [Azure Portal](#azure-portal-example)ile bir kapsayıcı başlattığınızda ortam değişkenlerini ayarlama örnekleri gösterilmektedir. 

Örneğin, Microsoft [aci-WORDCOUNT][aci-wordcount] kapsayıcı görüntüsünü çalıştırırsanız, aşağıdaki ortam değişkenlerini belirterek davranışını değiştirebilirsiniz:

*NumWords*: stdout 'a gönderilen sözcüklerin sayısı.

*MinLength*: sayılacak bir sözcükteki karakter sayısı alt sınırı. Daha yüksek bir sayı, "/" ve "The" gibi yaygın kelimeleri yoksayar.

Gizli dizileri ortam değişkenleri olarak geçirmeniz gerekiyorsa, Azure Container Instances hem Windows hem de Linux kapsayıcıları için [güvenli değerleri](#secure-values) destekler.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Azure CLı örneği

[Aci-WORDCOUNT][aci-wordcount] kapsayıcısının varsayılan çıktısını görmek için, bunu önce bu [az Container Create][az-container-create] komutuyla çalıştırın (ortam değişkeni belirtilmemiş):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Çıktıyı değiştirmek için, eklenen bağımsız değişkenle ikinci bir kapsayıcı başlatın `--environment-variables` , *NumWords* ve *minLength* değişkenlerinin değerlerini belirtin. (Bu örnek, bir bash kabuğunda veya Azure Cloud Shell CLı kullandığınızı varsayar. Windows komut Istemi 'ni kullanırsanız, gibi çift tırnak ile değişkenleri belirtin `--environment-variables "NumWords"="5" "MinLength"="8"` .)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Her iki kapsayıcının durumu *sonlandırıldığını* gösterir (durumu denetlemek için [az Container Show][az-container-show] kullanın), çıktıyı görmek için [az Container logs][az-container-logs] ile günlüklerini görüntüleyin.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Kapsayıcıların çıktısı, ortam değişkenlerini ayarlayarak ikinci kapsayıcının betik davranışını nasıl değiştirmiş olduğunu gösterir.

**mycontainer1**
```output
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

**mycontainer2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell örneği

PowerShell 'de ortam değişkenlerinin ayarlanması CLı 'ye benzerdir, ancak `-EnvironmentVariable` komut satırı bağımsız değişkenini kullanır.

İlk olarak, bu [New-AzContainerGroup][new-Azcontainergroup] komutuyla [aci-WORDCOUNT][aci-wordcount] kapsayıcısını varsayılan yapılandırmasında başlatın:

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Şimdi aşağıdaki [New-AzContainerGroup][new-Azcontainergroup] komutunu çalıştırın. Bu, bir dizi değişkeni doldurulduktan sonra *NumWords* ve *minLength* ortam değişkenlerini belirtir `envVars` :

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Her iki kapsayıcının durumu *sonlandırıldıktan* sonra (durumu denetlemek için [Get-AzContainerInstanceLog][azure-instance-log] kullanın), [Get-AzContainerInstanceLog][azure-instance-log] komutuyla günlüklerini çekin.

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Her kapsayıcının çıktısı, ortam değişkenlerini ayarlayarak kapsayıcı tarafından çalıştırılan betiği nasıl değiştirmiş olduğunu gösterir.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure portal örneği

Azure portal bir kapsayıcıyı başlattığınızda ortam değişkenlerini ayarlamak için, kapsayıcıyı oluştururken bunu **Gelişmiş** sayfada belirtin.

1. **Gelişmiş** sayfasında, **yeniden başlatma ilkesini** *hata durumunda* olarak ayarlayın
2. **Ortam değişkenleri**' nın altında, `NumWords` `5` ilk değişken için değerini girin ve `MinLength` `8` ikinci değişken için değerini girin. 
1. Kapsayıcıyı doğrulamak ve sonra dağıtmak için **gözden geçir + oluştur** ' u seçin.

![Ortam değişkeni etkinleştir düğme ve metin kutularını gösteren Portal sayfası][portal-env-vars-01]

Kapsayıcının günlüklerini görüntülemek için, **Ayarlar** ' ın altında **kapsayıcılar**' ı ve ardından **Günlükler**' i seçin. Önceki CLı ve PowerShell bölümlerinde gösterilen çıktıya benzer şekilde, komut dosyasının davranışının ortam değişkenleri tarafından nasıl değiştirildiğini görebilirsiniz. Her biri en az sekiz karakter uzunluğunda olan beş sözcük görüntülenir.

![Kapsayıcı günlüğü çıkışını gösteren Portal][portal-env-vars-02]

## <a name="secure-values"></a>Güvenli değerler

Güvenli değerlere sahip nesneler, uygulamanız için parola veya anahtarlar gibi hassas bilgileri tutmak üzere tasarlanmıştır. Ortam değişkenlerinin güvenli değerlerinin kullanılması, kapsayıcının görüntüsüne dahil etme özelliğinden daha güvenli ve daha esnektir. Başka bir seçenek [de Azure Container Instances bir gizli birim bağlama](container-instances-volume-secret.md)bölümünde açıklanan gizli birimleri kullanmaktır.

Güvenli değerlere sahip ortam değişkenleri, kapsayıcının özelliklerinde görünmez, ancak değerleri yalnızca kapsayıcının içinden erişilebilir. Örneğin, Azure portal veya Azure CLı 'de görüntülenen kapsayıcı özellikleri, değerini değil yalnızca güvenli bir değişkenin adını görüntüler.

`secureValue`Değişkenin türü için normal yerine özelliği belirterek güvenli bir ortam değişkeni ayarlayın `value` . Aşağıdaki YAML 'de tanımlanan iki değişken iki değişken türünü gösterir.

### <a name="yaml-deployment"></a>YAML dağıtımı

`secure-env.yaml`Aşağıdaki kod parçacığına sahip bir dosya oluşturun.

```yaml
apiVersion: 2019-12-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Bir kapsayıcı grubunu YAML ile dağıtmak için aşağıdaki komutu çalıştırın (kaynak grubu adını gerektiği şekilde ayarlayın):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Ortam değişkenlerini doğrulama

Kapsayıcının ortam değişkenlerini sorgulamak için [az Container Show][az-container-show] komutunu çalıştırın:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

JSON yanıtı hem güvensiz ortam değişkeninin anahtarını hem de değerini gösterir, ancak yalnızca güvenli ortam değişkeninin adı:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Çalışan bir kapsayıcıda bir komutun yürütülmesini sağlayan [az Container exec][az-container-exec] komutuyla, güvenli ortam değişkeninin ayarlandığını doğrulayabilirsiniz. Kapsayıcıda etkileşimli bir bash oturumu başlatmak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Kapsayıcı içinde etkileşimli bir kabuk açtıktan sonra `SECRET` değişkenin değerine erişebilirsiniz:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Sonraki adımlar

Birden çok kapsayıcı içeren büyük bir veri kümesini toplu olarak işleme gibi görev tabanlı senaryolar, çalışma zamanında özel ortam değişkenlerinden faydalanabilir. Görev tabanlı kapsayıcılar çalıştırma hakkında daha fazla bilgi için bkz. [yeniden başlatma ilkeleriyle Kapsayıcılı görevleri çalıştırma](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
