---
title: include dosyası
description: include dosyası
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ffd9e54c0f39b4256dbc83a336328797a8b53c45
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608402"
---
## <a name="register-extensions"></a>Uzantılarını kaydetme

Bağlamaları hariç olmak üzere HTTP ve Zamanlayıcı Tetikleyicileri, İşlevler çalışma zamanı sürüm 2.x uzantı paketleri uygulanır. Sürüm 2.x Azure işlevleri çalışma zamanı, sahip, işlevlerde kullanılan bağlama türleri için uzantıları açıkça kaydedilecek. Bu HTTP bağlamaları ve uzantılar gerektirmeyen Zamanlayıcı Tetikleyicileri özel durumlardır.

Bağlama uzantıları'nı tek başına yüklemeyi seçebilirsiniz veya bir uzantı paketi başvurusu host.json proje dosyasına ekleyebilirsiniz. Uzantı paketleri birden çok bağlama türleri kullanırken paket uyumluluk sorunlarına sahip olma olasılığını kaldırır. Bağlama uzantılarını kaydetme için önerilen yaklaşımdır. Uzantı paketleri de .NET Core yükleme gereksinimini kaldırır 2.x SDK. 

### <a name="extension-bundles"></a>Uzantı paketleri

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Daha fazla bilgi için bkz. [kaydetme Azure işlevleri bağlama uzantıları](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Bağlamaları functions.json dosyaya eklemeden önce uzantı paketleri için host.json eklemeniz gerekir.

### <a name="register-individual-extensions"></a>Tek tek uzantılarını kaydetme

Bir paket halinde olmayan uzantıları yüklemeniz gerekiyorsa, özel bağlamalar için ayrı bir uzantı paketleri el ile kaydedebilirsiniz. 

> [!NOTE]
> El ile uzantıları kullanarak kaydetmek için `func extensions install`, .NET Core olmalıdır 2.x SDK'sı yüklü.

Güncelleştirdikten sonra *function.json* dosyasını proje klasöründe aşağıdaki komutu çalıştırın, işlevinizi gereken tüm bağlamaları içerecek şekilde.

```bash
func extensions install
```

Komut okur *function.json* ihtiyacınız paketler görmek için bir dosya yükler ve uzantıları projesi oluşturur. Geçerli sürümde yeni bağlamalar ekler, ancak var olan bağlamaları güncelleştirmez. Kullanım `--force` yenilerini yüklerken mevcut bağlamaları en son sürüme güncelleştirmek için seçeneği.