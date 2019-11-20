---
title: Azure bağlı makine Aracısı CLı arabirimi
description: Azure bağlı makine Aracısı CLı için başvuru belgeleri
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513204"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Azure bağlı makine Aracısı CLı arabirimi

`Azcmagent` (Azure bağlı makine Aracısı) aracı, Azure 'a ait olmayan bir makine bağlantısını yapılandırmak ve sorunlarını gidermek için kullanılır.

Aracı, Linux üzerinde `himdsd` adlı bir Daemon işlemidir ve Windows üzerinde `himds` adlı bir Windows hizmetidir.

Normal kullanımda, bu makine ile Azure arasında bağlantı kurmak için `azcmagent connect` kullanılır ve bu bağlantıyı artık istemediğinize karar verirseniz `azcmagent disconnect`. Diğer komutlar sorun giderme veya diğer özel durumlar içindir.

## <a name="options"></a>Seçenekler

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>AYRıCA BKZ.

* [azcmagent Connect](#azcmagent-connect) -bu makineyi Azure 'a bağlar
* [azcmagent Disconnect](#azcmagent-disconnect) -bu makinenin Azure bağlantısını keser
* [azcmagent reconnect](#azcmagent-reconnect) -bu makineyi Azure 'a yeniden bağlar
* [azcmagent Show](#azcmagent-show) -makine meta verilerini ve aracı durumunu alır. Bu öncelikle sorun giderme için yararlıdır.
* [azcmagent sürümü](#azcmagent-version) -karma Yönetim Aracısı sürümünü görüntüle

## <a name="azcmagent-connect"></a>azcmagent Connect

Bu makineyi Azure 'a bağlar

### <a name="synopsis"></a>Özeti

Azure 'da bu makineyi temsil eden bir kaynak oluşturur.

Bu, bu makineyi temsil eden Azure Resource Manager bir kaynak oluşturmak için belirtilen kimlik doğrulama seçeneklerini kullanır. Kaynak abonelikte ve kaynak grubunda bulunur ve makineyle ilgili veriler konum parametresi tarafından belirtilen Azure bölgesinde depolanır.
Varsayılan kaynak adı, geçersiz kılınmamışsa bu makinenin ana bilgisayar adıdır.

Bu makinenin sistem tarafından atanan kimliğine karşılık gelen bir sertifika, daha sonra yerel olarak indirilir ve saklanır. Bu adım, **Azure bağlı makine meta veri** hizmetini tamamlar ve konuk yapılandırma Aracısı Azure bulutuyla eşitlemeye başlar.

Kimlik doğrulama seçenekleri:

* Erişim belirteci `azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* Hizmet sorumlusu KIMLIĞI ve gizli `azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Cihaz oturum açma (etkileşimli) `azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>Sözdizimi

```none
azcmagent connect [flags]
```

### <a name="options"></a>Seçenekler

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>azcmagent bağlantısı kesme

Bu makinenin bağlantısını Azure ile keser

### <a name="synopsis"></a>Özeti

Azure 'da bu sunucuyu temsil eden kaynağı siler.

Bu komut, bu makineyi temsil eden Azure Resource Manager kaynağını kaldırmak için belirtilen kimlik doğrulama seçeneklerini kullanır. Bu noktadan sonra, **Azure bağlı makine Metadata Service** ve konuk yapılandırma aracısının bağlantısı kesilir. Bu komut, Hizmetleri durdurmaz veya kaldırmaz: bunu yapmak için paketi kaldırın.

Bu komut, "Azure bağlı makine ekleme" rolünden daha yüksek ayrıcalıklar gerektirir.

Bir makinenin bağlantısı kesildiğinde, Azure 'da yeni bir kaynak oluşturmak istiyorsanız `azcmagent reconnect` değil `azcmagent connect`kullanın.

Kimlik doğrulama seçenekleri:

* Erişim belirteci `azcmagent disconnect --access-token <>`
* Hizmet sorumlusu KIMLIĞI ve gizli `azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Etkileşimli cihaz oturum açma `azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>Sözdizimi

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>Seçenekler

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>azcmagent yeniden bağlantısı

Bu makineyi Azure 'a yeniden bağlar

### <a name="synopsis"></a>Özeti

Makineyi Azure 'a geçersiz kimlik bilgileriyle yeniden bağlayın.

Bir makinenin zaten Azure 'da bir kaynağı varsa ancak kimlik doğrulaması yapamadığında, bu komut kullanılarak yeniden bağlanabilir. Bu, bir makinenin sertifikasının süre sonu (en az 45 gün) için yeterince uzun süre kapatılmışsa mümkündür.

Bir makinenin `azcmagent disconnect`bağlantısı kesildiyse bunun yerine `azcmagent connect` kullanın.

Bu komut, bu makineyi temsil eden Azure Resource Manager kaynağına karşılık gelen yeni kimlik bilgilerini almak için belirtilen kimlik doğrulama seçeneklerini kullanır.

Bu komut, **Azure bağlı makine ekleme** rolünden daha yüksek ayrıcalıklar gerektirir.

Kimlik doğrulama seçenekleri

* Erişim belirteci `azcmagent reconnect --access-token <>`
* Hizmet sorumlusu KIMLIĞI ve gizli `azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Etkileşimli cihaz oturum açma `azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>Sözdizimi

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>Seçenekler

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>azcmagent Show

Makine meta verilerini ve aracı durumunu alır. Bu öncelikle sorun giderme için yararlıdır.

### <a name="synopsis"></a>Özeti

Makine meta verilerini ve aracı durumunu alır. Bu öncelikle sorun giderme için yararlıdır.


### <a name="syntax"></a>Sözdizimi

```
azcmagent show [flags]
```

### <a name="options"></a>Seçenekler

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>azcmagent sürümü

Karma Yönetim Aracısı sürümünü görüntüle

### <a name="synopsis"></a>Özeti

Karma Yönetim Aracısı sürümünü görüntüle

### <a name="syntax"></a>Sözdizimi

```none
azcmagent version [flags]
```

### <a name="options"></a>Seçenekler

```none
  -h, --help   help for version
```
