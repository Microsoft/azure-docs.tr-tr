---
title: Python için MSAL 'te hatalar ve özel durumlar günlüğe kaydediliyor
titleSuffix: Microsoft identity platform
description: Python için MSAL 'te hataların ve özel durumların nasıl günlüğe alınacağını öğrenin
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1d52b017f94785f5fb25a25f127ae52d96e97d8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578762"
---
# <a name="logging-in-msal-for-python"></a>Python için MSAL’de oturum açma

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>Python günlüğü için MSAL

Python için MSAL içinde oturum açma, [Python Standart Kitaplığı 'ndaki günlük modülünü](https://docs.python.org/3/library/logging.html)kullanır. MSAL günlüğünü aşağıdaki şekilde yapılandırabilir ( [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Tüm modüller için hata ayıklama günlüğünü etkinleştir

Varsayılan olarak, herhangi bir Python betiğinde günlüğe kaydetme kapalıdır. Betiğinizdeki **Tüm** Python modülleri için ayrıntılı günlük kaydını etkinleştirmek istiyorsanız, şu `logging.basicConfig` düzeyle kullanın `logging.DEBUG` :

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Bu işlem, günlük modülüne verilen tüm günlük iletilerini standart çıktıya yazdırır.

### <a name="configure-msal-logging-level"></a>MSAL günlük düzeyini yapılandırma

Python için MSAL günlük kaydı düzeyini, `logging.getLogger()` günlükçü adı ile metodunu kullanarak yapılandırabilirsiniz `"msal"` :

```python
import logging

logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="configure-msal-logging-with-azure-app-insights"></a>Azure App Insights ile MSAL günlüğünü yapılandırma

Python günlükleri, varsayılan olarak olan bir günlük işleyicisine verilir `StreamHandler` . MSAL günlüklerini bir Izleme anahtarı ile bir Application Insights göndermek için, `AzureLogHandler` kitaplığı tarafından sunulan öğesini kullanın `opencensus-ext-azure` .

Yüklemek için, `opencensus-ext-azure` `opencensus-ext-azure` PyPI 'den bağımlılıklara veya PIMI yüklemesine olan paketi ekleyin:

```console
pip install opencensus-ext-azure
```

Ardından, günlük sağlayıcının varsayılan işleyicisini, `"msal"` `AzureLogHandler` ortam değişkeninde bir izleme anahtarı kümesiyle bir örneğine değiştirin `APP_INSIGHTS_KEY` :

```python
import logging
import os

from opencensus.ext.azure.log_exporter import AzureLogHandler

APP_INSIGHTS_KEY = os.getenv('APP_INSIGHTS_KEY')

logging.getLogger("msal").addHandler(AzureLogHandler(connection_string='InstrumentationKey={0}'.format(APP_INSIGHTS_KEY))
```

### <a name="personal-and-organizational-data-in-python"></a>Python 'da kişisel ve Kurumsal veriler

Python için MSAL kişisel verileri veya kurumsal verileri günlüğe eklemez. Kişisel veya kuruluş verilerinin oturum açmasını açma veya kapatma özelliği yoktur.

İstediğiniz her şeyi günlüğe kaydetmek için standart Python günlüğünü kullanabilirsiniz, ancak hassas verileri güvenle ve yasal gereksinimleri izleyerek siz de sorumlusunuz.

Python 'da günlüğe kaydetme hakkında daha fazla bilgi için lütfen Python 'un  [günlüğe kaydetme: nasıl yapılır](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla kod örneği için [Microsoft Identity platform kod örneklerine](sample-v2-code.md)bakın.
