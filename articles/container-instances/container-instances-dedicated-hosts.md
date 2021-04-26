---
title: Adanmış konakta dağıt
description: Azure Container Instances iş yükleriniz için doğru konak düzeyi yalıtımına ulaşmak üzere adanmış bir konak kullanın
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 72ad05eea9232e7a0d6ac24d1d0d22a971a7f6a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790842"
---
# <a name="deploy-on-dedicated-hosts"></a>Ayrılmış konaklara dağıtma

"Adanmış", güvenli bir şekilde çalışan kapsayıcılar için yalıtılmış ve ayrılmış bir işlem ortamı sağlayan bir Azure Container Instances (acı) SKU 'su. Özel iş yükü yalıtımının, kuruluşunuzun güvenlik ve uyumluluk gereksinimlerini karşılamasına yardımcı olması için, her bir kapsayıcı grubunda adanmış bir fiziksel sunucuya sahip olan adanmış SKU sonuçları kullanılması. 

Adanmış SKU, fiziksel sunucu perspektifinden iş yükü yalıtımı gerektiren kapsayıcı iş yükleri için uygundur.

## <a name="prerequisites"></a>Önkoşullar

> [!NOTE]
> Bazı geçerli sınırlamalar nedeniyle, tüm sınır artış istekleri onaylanmak garanti edilmez.

* Adanmış SKU 'yu kullanmak için abonelik varsayılan sınırı 0 ' dır. Bu SKU 'yu üretim kapsayıcısı dağıtımlarınız için kullanmak isterseniz, sınırı artırmak için bir [Azure destek isteği][azure-support] oluşturun.

## <a name="use-the-dedicated-sku"></a>Adanmış SKU 'yu kullanma

> [!IMPORTANT]
> Adanmış SKU kullanımı, yalnızca şu anda kullanıma sunulan en son API sürümünde (2019-12-01) kullanılabilir. Dağıtım şablonunuzda bu API sürümünü belirtin.
>

API sürüm 2019-12-01 ' den başlayarak, bir `sku` acı dağıtımı için gerekli olan dağıtım şablonunun kapsayıcı grubu özellikleri bölümünde bir özellik vardır. Şu anda, bu özelliği ACI için bir Azure Resource Manager dağıtım şablonunun parçası olarak kullanabilirsiniz. Öğreticide bir şablonla acı kaynaklarını dağıtma hakkında daha fazla bilgi edinin [: Kaynak Yöneticisi şablonu kullanarak çok kapsayıcılı bir grup dağıtın](./container-instances-multi-container-group.md). 

`sku`Özelliği aşağıdaki değerlerden birine sahip olabilir:
* `Standard` -Hiper yönetici düzeyinde güvenliği güvence altına almaya devam eden standart acı dağıtımı seçeneği 
* `Dedicated` -kapsayıcı grubu için adanmış fiziksel konaklarla iş yükü düzeyi yalıtımı için kullanılır

## <a name="modify-your-json-deployment-template"></a>JSON dağıtım şablonunuzu değiştirme

Dağıtım şablonunuzda, aşağıdaki özellikleri değiştirin veya ekleyin:
* Altında `resources` , `apiVersion` olarak ayarlayın `2019-12-01` .
* Kapsayıcı grubu özellikleri altında, `sku` değerine sahip bir özellik ekleyin `Dedicated` .

Ayrılmış SKU 'yu kullanan bir kapsayıcı grubu dağıtım şablonunun kaynaklar bölümü için örnek bir kod parçacığı aşağıda verilmiştir:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

Tek bir kapsayıcı örneği çalıştıran örnek bir kapsayıcı grubunu dağıtan bir şablon aşağıda verilmiştir:

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
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Kapsayıcı grubunuzu dağıtın

Masaüstünüzde dağıtım şablonu dosyasını oluşturup düzenlediyseniz, dosyayı içine sürükleyerek Cloud Shell dizininize yükleyebilirsiniz... 

[az group create][az-group-create] komutuyla bir kaynak grubu oluşturun.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[Az Deployment Group Create][az-deployment-group-create] komutuyla şablonu dağıtın.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Birkaç saniye içinde Azure’dan bir ilk yanıt almanız gerekir. Ayrılmış bir konakta başarılı bir dağıtım gerçekleşir.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
