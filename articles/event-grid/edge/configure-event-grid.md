---
title: Yapılandırma-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge Event Grid yapılandırma.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a9bac9a84e1ba034b011691ae82a1cb67eb71af0
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992437"
---
# <a name="event-grid-configuration"></a>Event Grid yapılandırması

Event Grid, ortam başına değiştirilebilen birçok yapılandırma sağlar. Aşağıdaki bölüm, kullanılabilir tüm seçeneklere ve bunların varsayılanlarına yönelik bir başvurudur.

## <a name="tls-configuration"></a>TLS yapılandırması

İstemci kimlik doğrulaması hakkında genel bilgi edinmek için bkz. [güvenlik ve kimlik doğrulaması](security-authentication.md). Kullanım örnekleri [Bu makalede](configure-api-protocol.md)bulunabilir.

| Özellik adı | Açıklama |
| ---------------- | ------------ |
|`inbound:serverAuth:tlsPolicy`| Event Grid modülünün TLS Ilkesi. Varsayılan değer yalnızca HTTPS 'dir.
|`inbound:serverAuth:serverCert:source`| Event Grid modülü tarafından TLS yapılandırması için kullanılan sunucu sertifikasının kaynağı. Varsayılan değer IoT Edge.

## <a name="incoming-client-authentication"></a>Gelen istemci kimlik doğrulaması

İstemci kimlik doğrulaması hakkında genel bilgi edinmek için bkz. [güvenlik ve kimlik doğrulaması](security-authentication.md). [Bu makalede](configure-client-auth.md)örnekleri bulabilirsiniz.

| Özellik adı | Açıklama |
| ---------------- | ------------ |
|`inbound:clientAuth:clientCert:enabled`| Sertifika tabanlı istemci kimlik doğrulamasını açmak/devre dışı bırakmak için. Varsayılan değer true 'dur.
|`inbound:clientAuth:clientCert:source`| İstemci sertifikalarının doğrulanması için kaynak. Varsayılan değer IoT Edge.
|`inbound:clientAuth:clientCert:allowUnknownCA`| Otomatik olarak imzalanan bir istemci sertifikasına izin verme ilkesi. Varsayılan değer true 'dur.
|`inbound:clientAuth:sasKeys:enabled`| SAS anahtar tabanlı istemci kimlik doğrulamasını açmak/devre dışı bırakmak için. Varsayılan değer kapalı.
|`inbound:clientAuth:sasKeys:key1`| Gelen istekleri doğrulamak için değerlerden biri.
|`inbound:clientAuth:sasKeys:key2`| Gelen istekleri doğrulamak için isteğe bağlı ikinci değer.

## <a name="outgoing-client-authentication"></a>Giden istemci kimlik doğrulaması
İstemci kimlik doğrulaması hakkında genel bilgi edinmek için bkz. [güvenlik ve kimlik doğrulaması](security-authentication.md). [Bu makalede](configure-identity-auth.md)örnekleri bulabilirsiniz.

| Özellik adı | Açıklama |
| ---------------- | ------------ |
|`outbound:clientAuth:clientCert:enabled`| Giden istekler için kimlik sertifikasını ekleme/kapatma. Varsayılan değer true 'dur.
|`outbound:clientAuth:clientCert:source`| Event Grid modülün giden sertifikasını alma kaynağı. Varsayılan değer IoT Edge.

## <a name="webhook-event-handlers"></a>Web kancası olay işleyicileri

İstemci kimlik doğrulaması hakkında genel bilgi edinmek için bkz. [güvenlik ve kimlik doğrulaması](security-authentication.md). [Bu makalede](configure-webhook-subscriber-auth.md)örnekleri bulabilirsiniz.

| Özellik adı | Açıklama |
| ---------------- | ------------ |
|`outbound:webhook:httpsOnly`| Yalnızca HTTPS abonelerine izin verilip verilmeyeceğini denetleyen ilke. Varsayılan değer true 'dur (yalnızca HTTPS).
|`outbound:webhook:skipServerCertValidation`| Abonenin sertifikasının doğrulanıp doğrulanmayacağını denetleyen bayrak. Varsayılan değer true 'dur.
|`outbound:webhook:allowUnknownCA`| Otomatik olarak imzalanan bir sertifikanın bir abone tarafından sunulup sunulmadığını denetleyen ilke. Varsayılan değer true 'dur. 

## <a name="delivery-and-retry"></a>Teslim ve yeniden deneme

Bu özellik hakkında genel bilgi edinmek için bkz. [teslim ve yeniden deneme](delivery-retry.md).

| Özellik adı | Açıklama |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | Bir olayı teslim etmeye yönelik deneme sayısı üst sınırı. Varsayılan değer 30 ' dur.
| `broker:defaultEventTimeToLiveInSeconds` | Saniyeler içinde, bir olayın teslim edilmeden önce bırakılma yaşam süresi (TTL). Varsayılan değer **7200** saniyedir

## <a name="output-batching"></a>Çıktıyı toplu işleme

Bu özellik hakkında genel bilgi edinmek için bkz. [teslim ve çıkış toplu işleme](delivery-output-batching.md).

| Özellik adı | Açıklama |
| ---------------- | ------------ |
| `api:deliveryPolicyLimits:maxBatchSizeInBytes` | `ApproxBatchSizeInBytes` düğmesi için izin verilen en büyük değer. Varsayılan değer `1_058_576`.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | `MaxEventsPerBatch` düğmesi için izin verilen en büyük değer. Varsayılan değer `50`.
| `broker:defaultMaxBatchSizeInBytes` | Yalnızca `MaxEventsPerBatch` belirtildiğinde en fazla teslim isteği boyutu. Varsayılan değer `1_058_576`.
| `broker:defaultMaxEventsPerBatch` | Yalnızca `MaxBatchSizeInBytes` belirtildiğinde bir toplu işe eklenecek en fazla olay sayısı. Varsayılan değer `10`.
