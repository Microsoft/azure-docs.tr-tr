---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a2a5935079a339e85713e9cbcd0f32c211cabbb5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729889"
---
`Logging`Ayarlar, Kapsayıcınız için ASP.NET Core günlük desteğini yönetir. ASP.NET Core bir uygulama için kullandığınız, Kapsayıcınız için aynı yapılandırma ayarlarını ve değerlerini kullanabilirsiniz. 

Aşağıdaki günlük oluşturma sağlayıcıları kapsayıcı tarafından desteklenir:

|Sağlayıcı|Amaç|
|--|--|
|[Konsol](/aspnet/core/fundamentals/logging/#console-provider)|ASP.NET Core `Console` günlük sağlayıcısı. Bu günlüğe kaydetme sağlayıcısı için ASP.NET Core yapılandırma ayarları ve varsayılan değerler desteklenir.|
|[Hata Ayıklama](/aspnet/core/fundamentals/logging/#debug-provider)|ASP.NET Core `Debug` günlük sağlayıcısı. Bu günlüğe kaydetme sağlayıcısı için ASP.NET Core yapılandırma ayarları ve varsayılan değerler desteklenir.|
|[Disk](#disk-logging)|JSON günlük sağlayıcısı. Bu günlüğe kaydetme sağlayıcısı günlük verilerini çıkış bağlamalarına yazar.|

Bu kapsayıcı komutu, günlük bilgilerini JSON biçiminde çıktı bağlamaya depolar:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Bu kapsayıcı komutu, kapsayıcı çalışırken, ön eki olan hata ayıklama bilgilerini gösterir `dbug` :

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Disk günlüğü

`Disk`Günlük sağlayıcısı aşağıdaki yapılandırma ayarlarını destekler:

| Name | Veri türü | Açıklama |
|------|-----------|-------------|
| `Format` | Dize | Günlük dosyaları için çıkış biçimi.<br/> **Note:** `json` Günlüğe kaydetme sağlayıcısını etkinleştirmek için bu değerin olarak ayarlanması gerekir. Bu değer, bir kapsayıcıyı örneklarken bir çıkış bağlaması belirtilmeden belirtilirse bir hata oluşur. |
| `MaxFileSize` | Tamsayı | Günlük dosyasının megabayt (MB) cinsinden en büyük boyutu. Geçerli günlük dosyasının boyutu bu değeri karşıladığında veya aştığında, günlük sağlayıcısı tarafından yeni bir günlük dosyası başlatılır. -1 belirtilirse, günlük dosyasının boyutu, varsa çıkış bağlaması için en fazla dosya boyutuyla sınırlıdır. Varsayılan değer 1’dir. |

ASP.NET Core günlüğü desteğini yapılandırma hakkında daha fazla bilgi için bkz. [Ayarlar dosya yapılandırması](/aspnet/core/fundamentals/logging/).