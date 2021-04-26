---
title: Komut satırı seçeneklerini Azure Depolama Gezgini | Microsoft Docs
description: Azure Depolama Gezgini başlangıç komut satırı seçeneklerinin belgeleri
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 0d588d85852f798542c5d52658e8dae3b9e57949
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746405"
---
# <a name="azure-storage-explorer-command-line-options"></a>Azure Depolama Gezgini komut satırı seçenekleri

Microsoft Azure Depolama Gezgini, uygulama başlatılırken eklenebilecek bir dizi komut satırı seçeneği vardır. Komut satırı seçeneklerinin çoğu hata ayıklama veya sorun giderme amaçlıdır.

## <a name="command-line-options"></a>Komut satırı seçenekleri
Seçenek  | Açıklama
:------- | :-----------
`--debug`/`--prod`  | Uygulamayı hata ayıklama veya üretim modunda başlatın. Hata ayıklama modunda, yerel ek verileri uygulamanın yerel depolama alanında depolanır ve şifrelenmez. Gizli özellikler, seçili kaynak düğümleri için Özellikler panelinde görüntülenir. Günlük ayrıntı düzeyi, Depolama Gezgini iç kurulum mantığını görüntüleyen hata ayıklama iletilerini yazdıracak şekilde ayarlanacak. `--prod` varsayılan değerdir.
`--lang`  | Uygulamayı belirli bir dille başlatın. Örneğin, `--lang="zh-Hans"`.
`--disable-gpu` | Uygulamayı GPU hızlandırma olmadan başlatın.
`--auto-open-dev-tools` | Tarayıcı penceresinin gösterdiği anda uygulamanın geliştirici araçları penceresini açmasına izin verin. Bu seçenek, tarayıcı penceresinin başlangıç kodundaki bir satırdaki kesme noktasına gitmek istediğinizde faydalıdır.
`--verbosity` | Depolama Gezgini günlüğün ayrıntı düzeyini ayarlayın. Desteklenen ayrıntı düzeyleri,,,, `debug` `verbose` ve içerir `info` `warn` `error` `silent` . Örneğin, `--verbosity=verbose`. Üretim modunda çalışırken varsayılan ayrıntı düzeyi olur `info` . Hata ayıklama modunda çalışırken, günlük ayrıntı düzeyi her zaman olur `debug` .
`--log-dir` | Günlük dosyalarını kaydetmek için dizini ayarlayın. Örneğin, `--log-dir=path_to_a_directory`.

Özel komut satırı seçenekleriyle Depolama Gezgini başlatma örneği

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> Bu komut satırı seçenekleri, Yeni Depolama Gezgini sürümlerinde değişebilir.

## <a name="when-to-use-command-line-options"></a>Komut satırı seçeneklerinin ne zaman kullanılacağı

Depolama Gezgini özelleştirmek için bazı komut satırı seçenekleri kullanılabilir. Gibi ilgili Kullanıcı ayarlarına sahip olan bu seçenekler için `--lang` . Komut satırı seçeneğini kullanmak yerine Kullanıcı ayarlarını kullanmanızı öneririz. 

Diğer komut satırı seçenekleri hata ayıklama ve sorun giderme için yararlı olabilir. Depolama Gezgini bir sorunla karşılaşırsanız, sorunu hata ayıklama modunda çalıştırmak, araştırmaya yönelik daha ayrıntılı bilgi almanıza yardımcı olabilir.