---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4983c1e1e7f235fa7a5b748a0ce5b1c79176c849
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102510631"
---
`deploymentconfig.json`Belgedeki girişler [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration)parametrelerine eşlenir. Aşağıdaki tabloda, JSON belgesindeki varlıklar ve yöntemin parametreleri arasındaki eşleme açıklanmaktadır:

| JSON varlığı | Yöntem parametresi | Description |
| ----- | ----- | ----- |
| `computeType` | NA | Bilgi işlem hedefi. ACI için değer olmalıdır `ACI` . |
| `containerResourceRequirements` | NA | CPU ve bellek varlıklarının kapsayıcısı. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Ayrılacak CPU çekirdeklerinin sayısı. Olarak `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Bu Web hizmeti için ayrılacak bellek miktarı (GB cinsinden). Varsayılanını `0.5` |
| `location` | `location` | Bu Web hizmeti 'nin dağıtılacağı Azure bölgesi. Belirtilmemişse, çalışma alanı konumu kullanılacaktır. Kullanılabilir bölgeler hakkında daha fazla ayrıntı şurada bulunabilir: [acı bölgeleri](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Bu Web hizmeti için kimlik doğrulamanın etkinleştirilip etkinleştirilmeyeceğini belirtir. Varsayılan değer false şeklindedir |
| `sslEnabled` | `ssl_enabled` | Bu Web hizmeti için SSL etkinleştirilip etkinleştirilmeyeceğini belirtir. Varsayılan değer false şeklindedir. |
| `appInsightsEnabled` | `enable_app_insights` | Bu Web hizmeti için Appınsights etkinleştirilip etkinleştirilmeyeceğini belirtir. Varsayılan değer false şeklindedir |
| `sslCertificate` | `ssl_cert_pem_file` | SSL etkinse gereken sertifika dosyası |
| `sslKey` | `ssl_key_pem_file` | SSL etkinse gereken anahtar dosya |
| `cname` | `ssl_cname` | SSL etkinse için CNAME |
| `dnsNameLabel` | `dns_name_label` | Puanlama uç noktası için DNS ad etiketi. Belirtilmemişse, Puanlama uç noktası için benzersiz bir DNS ad etiketi oluşturulacaktır. |

Aşağıdaki JSON, CLı ile kullanılacak örnek bir dağıtım yapılandırması örneğidir:

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```