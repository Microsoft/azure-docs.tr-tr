---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 10/06/2019
ms.author: larryfr
ms.openlocfilehash: b3c1ad48da0ef28c73e3af5d1e30414534f4e94d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800185"
---
`deploymentconfig.json` belgesindeki girdiler [Aciwebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py)parametrelerine eşlenir. Aşağıdaki tabloda, JSON belgesindeki varlıklar ve yöntemin parametreleri arasındaki eşleme açıklanmaktadır:

| JSON varlığı | Yöntem parametresi | Açıklama |
| ----- | ----- | ----- |
| `computeType` | NA | Bilgi işlem hedefi. ACI için değer `ACI`olmalıdır. |
| `containerResourceRequirements` | NA | CPU ve bellek varlıklarının kapsayıcısı. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Ayrılacak CPU çekirdeklerinin sayısı. Varsayılanlar, `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Bu Web hizmeti için ayrılacak bellek miktarı (GB cinsinden). Varsayılan, `0.5` |
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