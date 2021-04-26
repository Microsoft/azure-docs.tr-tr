---
title: Android için MSAL 'te hatalar ve özel durumlar günlüğe kaydediliyor.
titleSuffix: Microsoft identity platform
description: Android için MSAL 'te hataların ve özel durumların nasıl günlüğe alınacağını öğrenin.
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
ms.openlocfilehash: ce0929adbb2b0213cfd4ee61fe795a2d5f33c648
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954893"
---
# <a name="logging-in-msal-for-android"></a>Android için MSAL’de oturum açma

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="logging-in-msal-for-android-using-java"></a>Java kullanarak Android için MSAL 'de oturum açma

Günlüğe kaydetme geri araması oluşturarak uygulama oluşturma sırasında oturum açmayı açın. Geri çağırma bu parametreleri alır:

- `tag` , kitaplık tarafından geri çağırmaya geçirilen bir dizedir. Günlük girdisiyle ilişkilendirilir ve günlük iletilerini sıralamak için kullanılabilir.
- `logLevel` hangi günlük kayıt düzeyini istediğinize karar vermenize olanak sağlar. Desteklenen günlük düzeyleri şunlardır: `Error` , `Warning` , `Info` , ve `Verbose` .
- `message` , günlük girdisinin içeridir.
- `containsPII` kişisel verileri içeren iletilerin veya kurumsal verilerin günlüğe kaydedilip kaydedilmeyeceğini belirtir. Varsayılan olarak, uygulamanızın kişisel verileri günlüğe almamasını sağlamak için bu false olarak ayarlanır. `containsPII`İse `true` , bu yöntem iletileri iki kez alır: `containsPII` parametresi `false` ve `message` kişisel veriler olmadan bir kez, ve parametresi olarak ayarlanmış ikinci bir kez ve `containsPii` `true` ileti kişisel verileri içerebilir. Bazı durumlarda (ileti kişisel veriler içermiyorsa), ileti aynı olur.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Varsayılan olarak, MSAL günlükçüsü herhangi bir kişisel bilgi veya kuruluş tarafından tanımlanabilen bilgileri yakalamaz.
Kişisel olarak tanımlanabilen bilgilerin veya kurumsal olarak tanımlanabilen bilgilerin günlüğe kaydedilmesini etkinleştirmek için:

```java
Logger.getInstance().setEnablePII(true);
```

Kişisel verileri ve kuruluş verilerini günlüğe kaydetmeyi devre dışı bırakmak için:

```java
Logger.getInstance().setEnablePII(false);
```

Günlüğe kaydetme varsayılan olarak Logcat devre dışıdır. Şunları etkinleştirmek için:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla kod örneği için [Microsoft Identity platform kod örneklerine](sample-v2-code.md)bakın.