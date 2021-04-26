---
title: include dosyası
description: dosya dahil etme
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95997965"
---
App Service’te uygulama kodunuzun içinden oluşturulan konsol günlüklerine erişmek için şu komutu [Cloud Shell](https://shell.azure.com)’de çalıştırarak tanılama günlüğüne kaydetmeyi açın:

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

`--level` için olası değerler: `Error`, `Warning`, `Info` ve `Verbose`. Her düzey kendisinden önceki düzeyi içerir. Örneğin: `Error` yalnızca hata iletilerini içerir, `Verbose` ise tüm iletileri içerir.

Tanılama günlüğüne kaydetme açıldıktan sonra günlük akışını görmek için şu komutu çalıştırın:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Konsol günlüklerini hemen görmüyorsanız, 30 saniye içinde yeniden kontrol edin.

> [!NOTE]
> Ayrıca, tarayıcıdan `https://<app-name>.scm.azurewebsites.net/api/logs/docker` adresine giderek günlük dosyalarını inceleyebilirsiniz.

Günlük akışını dilediğiniz zaman durdurmak için `Ctrl`+`C` yazın.
