---
title: İnit kapsayıcılarını Çalıştır
description: Uygulama kapsayıcıları çalıştırılmadan önce bir kapsayıcı grubundaki kurulum görevlerini gerçekleştirmek için Azure Container Instances ' de init kapsayıcıları çalıştırın.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 9ccaf1a67d6ca3bcff422acb591b528cc72a9608
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763946"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Bir kapsayıcı grubundaki kurulum görevleri için bir init kapsayıcısı çalıştırın

Azure Container Instances bir kapsayıcı grubundaki *init kapsayıcıları* destekler. Başlangıç kapsayıcıları, uygulama kapsayıcısı veya kapsayıcılar başlamadan önce tamamlandığında çalışır. [Kubernetes init kapsayıcılarına](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)benzer şekilde, uygulama kapsayıcılarınız için hesap ayarlama, kurulum betikleri çalıştırma veya veritabanlarını yapılandırma gibi başlatma mantığını gerçekleştirmek için bir veya daha fazla başlangıç kapsayıcısı kullanın.

Bu makalede, bir Azure Resource Manager şablonunun, bir başlangıç kapsayıcısı olan bir kapsayıcı grubunu yapılandırmak için nasıl kullanılacağı gösterilmektedir.

## <a name="things-to-know"></a>Bilinmesi gerekenler

* **API sürümü** -başlangıç kapsayıcılarını dağıtmak için en az Azure Container Instances apı sürümü 2019-12-01 gerekir. `initContainers`Bir [YAML dosyası](container-instances-multi-container-yaml.md) veya [Kaynak Yöneticisi şablonundaki](container-instances-multi-container-group.md)bir özelliği kullanarak dağıtın.
* **Yürütme sırası** -Init kapsayıcıları şablonda belirtilen sırada ve diğer kapsayıcılardan önce yürütülür. Varsayılan olarak, kapsayıcı grubu başına en fazla 59 init kapsayıcı belirtebilirsiniz. En az bir init olmayan kapsayıcının grupta olması gerekir.
* **Konak ortamı** -Init kapsayıcıları, gruptaki diğer kapsayıcılarla aynı donanımda çalışır.
* **Kaynaklar** -başlangıç kapsayıcıları için kaynak belirtmezsiniz. Bunlar, kapsayıcı grubu için kullanılabilir olan CPU 'Lar ve bellek gibi toplam kaynak kaynaklarına verilir. Bir başlangıç kapsayıcısı çalışırken, grupta başka kapsayıcı çalıştırılmaz.
* **Desteklenen özellikler** -Init kapsayıcıları birimler, gizlilikler ve yönetilen kimlikler gibi Grup özelliklerini kullanabilir. Ancak, kapsayıcı grubu için yapılandırılmışsa bağlantı noktalarını veya IP adresini kullanamaz. 
* **Ilkeyi yeniden Başlat** -gruptaki bir sonraki kapsayıcı başlamadan önce her bir init kapsayıcısının başarıyla çıkış olması gerekir. Bir başlatma kapsayıcısı başarıyla çıkmazsa, yeniden başlatma eylemi, grup için yapılandırılan [yeniden başlatma ilkesine](container-instances-restart-policy.md) bağlıdır:

    |Gruptaki ilke  |İnit 'deki ilke  |
    |---------|---------|
    |Her zaman     |OnFailure         |
    |OnFailure     |OnFailure         |
    |Asla     |Asla         |
* **Ücretler** -kapsayıcı grubu, bir init kapsayıcısının ilk dağıtımından ücretler doğurur.

## <a name="resource-manager-template-example"></a>Kaynak Yöneticisi şablonu örneği

Aşağıdaki JSON öğesini adlı yeni bir dosyaya kopyalayarak başlayın `azuredeploy.json` . Şablon, bir başlangıç kapsayıcısı ve iki uygulama kapsayıcısı içeren bir kapsayıcı grubu ayarlar:

* *İnit1* kapsayıcısı, Docker Hub 'ından [Busi Box](https://hub.docker.com/_/busybox) görüntüsünü çalıştırır. 60 saniye boyunca uyku moduna geçer ve bir [Emptydir birimindeki](container-instances-volume-emptydir.md)dosyaya bir komut satırı dizesi yazar.
* Her iki uygulama kapsayıcısı da Microsoft `aci-wordcount` kapsayıcı görüntüsünü çalıştırır:
    * *Hamlet* kapsayıcısı, varsayılan yapılandırmasında WORDCOUNT uygulamasını çalıştırır ve bu da Shakespeare 'In Play *hamyalet*'teki sözcük sıklıklarını sayar.
    * *Juliet* uygulama kapsayıcısı, Shakespeare 'In *Romeo ve Juliet*' de bunun yerine WORDCOUNT uygulamasını çalıştırmak için emptdir biriminden komut satırı dizesini okur.

Görüntüyü kullanarak daha fazla bilgi ve örnek için `aci-wordcount` bkz. [kapsayıcı örneklerinde ortam değişkenlerini ayarlama](container-instances-environment-variables.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "busybox",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>Şablonu dağıtma

[az group create][az-group-create] komutuyla bir kaynak grubu oluşturun.

```azurecli
az group create --name myResourceGroup --location eastus
```

[Az Deployment Group Create][az-deployment-group-create] komutuyla şablonu dağıtın.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

İnit kapsayıcısına sahip bir grupta, başlangıç kapsayıcısının veya kapsayıcılarının tamamlanmasıyla ilgili süre nedeniyle dağıtım süresi artmıştır.


## <a name="view-container-logs"></a>Kapsayıcı günlüklerini görüntüleme

İnit kapsayıcısının başarıyla çalıştığını doğrulamak için, [az Container logs][az-container-logs] komutunu kullanarak uygulama kapsayıcılarının günlük çıktısını görüntüleyin. `--container-name`Bağımsız değişkeni, günlüklerin alınacağı kapsayıcıyı belirtir. Bu örnekte, farklı komut çıkışını gösteren *Hamlet* ve *Juliet* kapsayıcıları için günlükleri çekin:

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

Çıkış:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

Çıkış:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Sonraki adımlar

Init kapsayıcıları, uygulama kapsayıcılarınız için kurulum ve başlatma görevlerini gerçekleştirmenize yardımcı olur. Görev tabanlı kapsayıcılar çalıştırma hakkında daha fazla bilgi için bkz. [yeniden başlatma ilkeleriyle Kapsayıcılı görevleri çalıştırma](container-instances-restart-policy.md).

Azure Container Instances, uygulama kapsayıcılarının davranışını değiştirmek için başka seçenekler sağlar. Örnekler arasında şunlar yer almaktadır:

* [Kapsayıcı örneklerinde ortam değişkenlerini ayarlama](container-instances-environment-variables.md)
* [Varsayılan komut satırı işlemini geçersiz kılmak için bir kapsayıcı örneğindeki komut satırını ayarlayın](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-container-logs]: /cli/azure/container#az_container_logs
