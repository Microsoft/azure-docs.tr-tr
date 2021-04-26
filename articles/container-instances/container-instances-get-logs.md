---
title: Olayları & kapsayıcı örneği günlüklerini al
description: Kapsayıcı sorunlarını gidermeye yardımcı olmak için Azure Container Instances kapsayıcı günlüklerini ve olaylarını almayı öğrenin
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: f5eb8c878164846ed2f1daf1cb7e5014e0c62c55
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764054"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Azure Container Instances kapsayıcı günlüklerini ve olaylarını alma

Azure Container Instances hatalı bir kapsayıcınız varsa, günlüklerini [az Container logs][az-container-logs]ile görüntüleyerek başlatın ve [az Container Attach][az-container-attach]ile standart çıkış ve standart hata akışını yapın. Ayrıca, Azure portal kapsayıcı örnekleri için günlükleri ve olayları görüntüleyebilir veya [Azure izleyici günlüklerine](container-instances-log-analytics.md)kapsayıcı grupları için günlük ve olay verileri gönderebilirsiniz.

## <a name="view-logs"></a>Günlükleri görüntüleme

Uygulama kodunuzdaki günlükleri bir kapsayıcı içinde görüntülemek için [az Container logs][az-container-logs] komutunu kullanabilirsiniz.

Aşağıda, komut satırı geçersiz kılma kullanarak geçersiz bir URL sağlamadıktan sonra [bir kapsayıcı örneğindeki komut satırını ayarlama](container-instances-start-command.md#azure-cli-example)içindeki örnek görev tabanlı kapsayıcının günlük çıktısı verilmiştir:

```azurecli
az container logs --resource-group myResourceGroup --name mycontainer
```

```output
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>Çıkış akışları ekleme

[Az Container Attach][az-container-attach] komutu, kapsayıcı başlatma sırasında tanılama bilgileri sağlar. Kapsayıcı başlatıldıktan sonra STDOUT ve STDERR 'i yerel konsolunuza akışlar.

Örneğin, [bir kapsayıcı örneğindeki komut satırını ayarla](container-instances-start-command.md#azure-cli-example)' da görev tabanlı kapsayıcının çıktısı, işlemek için büyük bir metin dosyasının geçerli bir URL 'si sağlamadıktan sonra:

```azurecli
az container attach --resource-group myResourceGroup --name mycontainer
```

```output
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Tanılama olaylarını al

Kapsayıcınız başarıyla dağıtılamazsa Azure Container Instances kaynak sağlayıcısı tarafından sunulan tanılama bilgilerini gözden geçirin. Kapsayıcının olaylarını görüntülemek için [az Container Show][az-container-show] komutunu çalıştırın:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Çıktı, kapsayıcının temel özelliklerini, dağıtım olayları ile birlikte (burada kesilmiş olarak gösterilir) içerir:

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>Sonraki adımlar
Azure Container Instances için [yaygın kapsayıcı ve dağıtım sorunlarını giderme](container-instances-troubleshooting.md) hakkında bilgi edinin.

[Azure izleyici günlüklerine](container-instances-log-analytics.md)kapsayıcı grupları için günlük ve olay verilerini gönderme hakkında bilgi edinin.

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
