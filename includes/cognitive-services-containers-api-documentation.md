---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034422"
---
## <a name="validate-that-a-container-is-running"></a>Bir kapsayıcının çalıştığını doğrulama 

Kapsayıcının çalıştığını doğrulamak için birkaç yol vardır. *Dış IP* adresini ve söz konusu kapsayıcının bağlantı noktasını bulun ve en sevdiğiniz web tarayıcınızı açın. Kapsayıcının çalıştığını doğrulamak için aşağıdaki çeşitli istek URL 'Lerini kullanın. Aşağıda listelenen örnek URL 'ler aşağıda verilmiştir `http://localhost:5000`ancak belirli Kapsayıcınız farklılık gösterebilir. Kapsayıcının *dış IP* adresine ve açığa çıkarılan bağlantı noktasına güvendiğini aklınızda bulundurun.

| İstek URL'si | Amaç |
|--|--|
| `http://localhost:5000/` | Kapsayıcı bir giriş sayfası sağlar. |
| `http://localhost:5000/status` | Bir uç nokta sorgusuna neden olmadan kapsayıcının çalıştığını doğrulamak için bir HTTP GET ile istendi. Bu istek, Kubernetes [limize ve hazırlık araştırmaları](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)için kullanılabilir. |
| `http://localhost:5000/swagger` | Kapsayıcı, uç noktalar ve bir **deneyin** özelliği için tam bir belge kümesi sağlar. Bu özellikle, ayarlarınızı bir Web tabanlı HTML biçiminde girebilir ve herhangi bir kod yazmak zorunda kalmadan sorguyu oluşturabilirsiniz. Sorgu çağrıldıktan sonra, gereken HTTP üst bilgilerini ve gövde biçimini göstermek için örnek bir KıVRıMLı komut sağlanır. |

![Kapsayıcının ana sayfası](./media/cognitive-services-containers-api-documentation/container-webpage.png)
