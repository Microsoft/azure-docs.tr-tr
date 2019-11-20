---
title: Web kancası şeması başvurusunu Azure Container Registry
description: Azure Container Registry için Web kancası isteği JSON yük başvurusu.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/05/2019
ms.author: danlep
ms.openlocfilehash: fcdee2be92f2a3052e2ebbfaab3a2f9cb96e0125
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311610"
---
# <a name="azure-container-registry-webhook-reference"></a>Web kancası başvurusunu Azure Container Registry

[Web kancalarını](container-registry-webhook.md) kapsayıcı kayıt defteriniz için, belirli eylemleri buna karşı gerçekleştirilen olaylar üreten şekilde yapılandırabilirsiniz. Örneğin, bir kapsayıcı görüntüsü veya helb grafiği bir kayıt defterine gönderildiğinde veya silindiğinde tetiklenen Web kancalarını etkinleştirin. Web kancası tetiklendiğinde, belirttiğiniz bir uç noktaya olay hakkında bilgi içeren bir HTTP veya HTTPS isteği yayınlar Azure Container Registry. Uç noktanız daha sonra Web kancasını işleyebilir ve uygun şekilde hareket edebilir.

Aşağıdaki bölümler desteklenen olaylar tarafından oluşturulan Web kancası isteklerinin şemasını ayrıntılandırır. Olay bölümleri, olay türü için yük şemasını, örnek bir istek yükünü ve Web kancasını tetikleyecek bir veya daha fazla örnek komutu içerir.

Azure Container Registry 'niz için Web kancalarını yapılandırma hakkında daha fazla bilgi için bkz. [Azure Container Registry Web kancaları kullanma](container-registry-webhook.md).

## <a name="webhook-requests"></a>Web kancası istekleri

### <a name="http-request"></a>HTTP isteği

Tetiklenen bir Web kancası, Web `POST` kancasını yapılandırdığınızda belirlediğiniz URL uç noktasına bir http isteği oluşturur.

### <a name="http-headers"></a>HTTP üstbilgileri

Web kancası istekleri `Content-Type` , `application/json` Web kancası için `Content-Type` özel bir üst bilgi belirtminizi içerir.

İstek için Web kancası için belirtmiş olabileceğiniz özel üstbilgilerin ötesinde başka üst bilgi eklenmez.

## <a name="push-event"></a>Anında iletme olayı

Bir kapsayıcı görüntüsü depoya gönderildiğinde Web kancası tetiklendi.

### <a name="push-event-payload"></a>Olay yükünü gönder

|Öğe|Type|Açıklama|
|-------------|----------|-----------|
|`id`|Dize|Web kancası olayının KIMLIĞI.|
|`timestamp`|Datetime|Web kancası olayının tetiklendiği zaman.|
|`action`|Dize|Web kancası olayını tetikleyen eylem.|
|[hedef](#target)|Karmaşık tür|Web kancası olayını tetikleyen etkinliğin hedefi.|
|[isteyen](#request)|Karmaşık tür|Web kancası olayını oluşturan istek.|

### <a name="target"></a>hedef

|Öğe|Type|Açıklama|
|------------------|----------|-----------|
|`mediaType`|Dize|Başvurulan nesnenin MIME türü.|
|`size`|Int32|İçeriğin bayt sayısı. Length alanıyla aynı.|
|`digest`|Dize|Kayıt defteri v2 HTTP API belirtiminde tanımlanan şekilde içeriğin özeti.|
|`length`|Int32|İçeriğin bayt sayısı. Boyut alanıyla aynı.|
|`repository`|Dize|Depo adı.|
|`tag`|Dize|Resim etiketi adı.|

### <a name="request"></a>isteyen

|Öğe|Type|Açıklama|
|------------------|----------|-----------|
|`id`|Dize|Olayı başlatan isteğin KIMLIĞI.|
|`host`|Dize|Gelen isteklerde HTTP ana bilgisayar üst bilgisi tarafından belirtilen şekilde, kayıt defteri örneğinin dışarıdan erişilebilen ana bilgisayar adı.|
|`method`|Dize|Olayı oluşturan istek yöntemi.|
|`useragent`|Dize|İsteğin Kullanıcı Aracısı üst bilgisi.|

### <a name="payload-example-image-push-event"></a>Yük örneği: görüntü gönderme olayı

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Görüntü **gönderme** olayı Web kancasını tetikleyen örnek [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) komutu:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Grafik gönderme olayı

Bir Helu grafiği depoya gönderildiğinde Web kancası tetiklendi.

### <a name="chart-push-event-payload"></a>Grafik gönderim olayı yükü

|Öğe|Type|Açıklama|
|-------------|----------|-----------|
|`id`|Dize|Web kancası olayının KIMLIĞI.|
|`timestamp`|Datetime|Web kancası olayının tetiklendiği zaman.|
|`action`|Dize|Web kancası olayını tetikleyen eylem.|
|[hedef](#helm_target)|Karmaşık tür|Web kancası olayını tetikleyen etkinliğin hedefi.|

### <a name="helm_target"></a>hedef

|Öğe|Type|Açıklama|
|------------------|----------|-----------|
|`mediaType`|Dize|Başvurulan nesnenin MIME türü.|
|`size`|Int32|İçeriğin bayt sayısı.|
|`digest`|Dize|Kayıt defteri v2 HTTP API belirtiminde tanımlanan şekilde içeriğin özeti.|
|`repository`|Dize|Depo adı.|
|`tag`|Dize|Grafik etiketi adı.|
|`name`|Dize|Grafik adı.|
|`version`|Dize|Grafik sürümü.|

### <a name="payload-example-chart-push-event"></a>Yük örneği: grafik gönderme olayı

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

**Chart_push** olay Web kancasını tetikleyen örnek [Azure CLI](/cli/azure/acr) komutu:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Etkinliği sil

Bir görüntü deposu veya bildirim silindiğinde Web kancası tetiklendi. Bir etiket silindiğinde tetiklenmez.

### <a name="delete-event-payload"></a>Olay yükünü Sil

|Öğe|Type|Açıklama|
|-------------|----------|-----------|
|`id`|Dize|Web kancası olayının KIMLIĞI.|
|`timestamp`|Datetime|Web kancası olayının tetiklendiği zaman.|
|`action`|Dize|Web kancası olayını tetikleyen eylem.|
|[hedef](#delete_target)|Karmaşık tür|Web kancası olayını tetikleyen etkinliğin hedefi.|
|[isteyen](#delete_request)|Karmaşık tür|Web kancası olayını oluşturan istek.|

### <a name="delete_target"></a>hedef

|Öğe|Type|Açıklama|
|------------------|----------|-----------|
|`mediaType`|Dize|Başvurulan nesnenin MIME türü.|
|`digest`|Dize|Kayıt defteri v2 HTTP API belirtiminde tanımlanan şekilde içeriğin özeti.|
|`repository`|Dize|Depo adı.|

### <a name="delete_request"></a>isteyen

|Öğe|Type|Açıklama|
|------------------|----------|-----------|
|`id`|Dize|Olayı başlatan isteğin KIMLIĞI.|
|`host`|Dize|Gelen isteklerde HTTP ana bilgisayar üst bilgisi tarafından belirtilen şekilde, kayıt defteri örneğinin dışarıdan erişilebilen ana bilgisayar adı.|
|`method`|Dize|Olayı oluşturan istek yöntemi.|
|`useragent`|Dize|İsteğin Kullanıcı Aracısı üst bilgisi.|

### <a name="payload-example-image-delete-event"></a>Yük örneği: görüntü silme olayı

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Olay **silme** Web kancasını tetikleyen örnek [Azure CLI](/cli/azure/acr) komutları:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Grafik silme olayı

Bir helb grafiği veya deposu silindiğinde Web kancası tetiklendi. 

### <a name="chart-delete-event-payload"></a>Grafik olay yükünü silme

|Öğe|Type|Açıklama|
|-------------|----------|-----------|
|`id`|Dize|Web kancası olayının KIMLIĞI.|
|`timestamp`|Datetime|Web kancası olayının tetiklendiği zaman.|
|`action`|Dize|Web kancası olayını tetikleyen eylem.|
|[hedef](#chart_delete_target)|Karmaşık tür|Web kancası olayını tetikleyen etkinliğin hedefi.|

### <a name="chart_delete_target"></a>hedef

|Öğe|Type|Açıklama|
|------------------|----------|-----------|
|`mediaType`|Dize|Başvurulan nesnenin MIME türü.|
|`size`|Int32|İçeriğin bayt sayısı.|
|`digest`|Dize|Kayıt defteri v2 HTTP API belirtiminde tanımlanan şekilde içeriğin özeti.|
|`repository`|Dize|Depo adı.|
|`tag`|Dize|Grafik etiketi adı.|
|`name`|Dize|Grafik adı.|
|`version`|Dize|Grafik sürümü.|

### <a name="payload-example-chart-delete-event"></a>Yük örneği: grafik silme olayı

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

**Chart_delete** olay Web kancasını tetikleyen örnek [Azure CLI](/cli/azure/acr) komutu:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure Container Registry Web kancalarını kullanma](container-registry-webhook.md)