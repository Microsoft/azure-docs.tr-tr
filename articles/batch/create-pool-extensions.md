---
title: Toplu Iş havuzlarıyla uzantıları kullanma
description: Uzantılar, sağlama sonrası yapılandırmayı ve toplu işlem düğümlerinde kurulumunu kolaylaştıran küçük uygulamalardır.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 1bf9847af57347c143ee3d790d89988ba7cd48e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417943"
---
# <a name="use-extensions-with-batch-pools"></a>Toplu Iş havuzlarıyla uzantıları kullanma

Uzantılar, sağlama sonrası yapılandırmayı ve toplu işlem düğümlerinde kurulumunu kolaylaştıran küçük uygulamalardır. Azure Batch tarafından izin verilen uzantılarından herhangi birini seçebilir ve bunların sağlandığı şekilde işlem düğümlerine yüklenmesini sağlayabilirsiniz. Bundan sonra uzantı amaçlanan işlemini gerçekleştirebilir.

Kullandığınız uzantıların canlı durumunu denetleyebilir ve herhangi bir algılama, düzeltme ya da tanılama özelliği elde etmek üzere döndürdüğü bilgileri alabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Uzantılara sahip havuzlar [sanal makine yapılandırmasını](nodes-and-pools.md#virtual-machine-configuration)kullanmalıdır.
- CustomScript uzantı türü Azure Batch hizmeti için ayrılmıştır ve geçersiz kılınamaz.

### <a name="supported-extensions"></a>Desteklenen uzantılar

Aşağıdaki uzantılar Şu anda bir Batch havuzu oluşturulurken yüklenebilir. 

- Hem [Linux](../virtual-machines/extensions/key-vault-linux.md) hem de [Windows](../virtual-machines/extensions/key-vault-windows.md) için Azure Key Vault uzantısı
- Hem [Linux](../virtual-machines/extensions/oms-linux.md) hem de [Windows](../virtual-machines/extensions/oms-windows.md) Için Log Analytics ve izleme uzantısı
- Azure Güvenlik paketi

Bir destek isteği açarak ek Yayımcılar ve/veya uzantı türleri için destek isteğinde bulunabilir.

## <a name="create-a-pool-with-extensions"></a>Uzantılara sahip bir havuz oluşturma

Aşağıdaki örnek, Azure Key Vault uzantısını kullanan Linux düğümlerinin toplu Işlem havuzunu oluşturur.

REST API URI'si

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

İstek Gövdesi

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>Bir havuzdan uzantı verileri al

Aşağıdaki örnek, Azure Key Vault uzantısından verileri alır.

REST API URI'si

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

Yanıt gövdesi

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulama ve verileri havuz düğümlerine kopyalamanın](batch-applications-to-pool-nodes.md)çeşitli yolları hakkında bilgi edinin.
- [Düğümler ve havuzlarla](nodes-and-pools.md)çalışma hakkında daha fazla bilgi edinin.
