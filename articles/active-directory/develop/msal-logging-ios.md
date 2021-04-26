---
title: İOS/macOS için MSAL 'te hatalar ve özel durumlar günlüğe kaydediliyor
titleSuffix: Microsoft identity platform
description: İOS/macOS için MSAL 'te hataların ve özel durumların nasıl günlüğe alınacağını öğrenin
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
ms.openlocfilehash: ee3837b75d586238e7ca6ac85434cc56f592929d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98763576"
---
# <a name="logging-in-msal-for-iosmacos"></a>iOS/macOS için MSAL’de oturum açma

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>İOS ve macOS günlüğü için MSAL-ObjC

MSAL günlüğünü yakalamak ve kendi uygulamanızın günlüğüne eklemek için bir geri çağırma ayarlayın. Geri arama imzası şöyle görünür:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Örnek:

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>Kişisel veriler

Varsayılan olarak, MSAL kişisel verileri yakalamaz veya günlüğe kaydetmez. Kitaplık, uygulama geliştiricilerinin Msalgünlükçü sınıfındaki bir özellik aracılığıyla bunu açmasına olanak sağlar. `pii.Enabled`Uygulama açılarak, yüksek düzeyde hassas verileri güvenli bir şekilde işleme ve yasal gereksinimleri takip eden bir sorumluluğu alır.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Günlüğe kaydetme düzeyleri

İOS ve macOS için MSAL kullanarak günlüğe kaydetme düzeyini ayarlamak için aşağıdaki değerlerden birini kullanın:

|Level  |Description |
|---------|---------|
| `MSALLogLevelNothing`| Tüm günlüğe kaydetmeyi devre dışı bırak |
| `MSALLogLevelError` | Varsayılan düzey, yalnızca hata oluştuğunda bilgileri yazdırır |
| `MSALLogLevelWarning` | Uyarılar |
| `MSALLogLevelInfo` |  Kitaplık giriş noktaları, parametreler ve çeşitli Anahtarlık işlemleri |
|`MSALLogLevelVerbose`     |  API izleme |

Örnek:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Günlük iletisi biçimi

MSAL günlük iletilerinin ileti kısmı şu biçimdedir `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Örnek:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Bağıntı kimlikleri ve zaman damgaları sağlamak, sorunları izlemek için yararlıdır. Zaman damgası ve bağıntı KIMLIĞI bilgileri günlük iletisinde bulunabilir. Bu dosyaları almak için tek güvenilir yer MSAL Logging iletilerinden alınır.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>İOS ve macOS günlüğü için MSAL-Swift

MSAL günlüğünü yakalamak ve kendi uygulamanızın günlüğüne eklemek için bir geri çağırma ayarlayın. Geri arama için imza (hedef-C olarak gösterilir) şuna benzer:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Örnek:

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>Kişisel veriler

Varsayılan olarak, MSAL kişisel verileri yakalamaz veya günlüğe kaydetmez. Kitaplık, uygulama geliştiricilerinin Msalgünlükçü sınıfındaki bir özellik aracılığıyla bunu açmasına olanak sağlar. `pii.Enabled`Uygulama açılarak, yüksek düzeyde hassas verileri güvenli bir şekilde işleme ve yasal gereksinimleri takip eden bir sorumluluğu alır.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Günlüğe kaydetme düzeyleri

İOS ve macOS için MSAL kullanarak günlüğe kaydetme düzeyini ayarlamak için aşağıdaki değerlerden birini kullanın:

|Level  |Description |
|---------|---------|
| `MSALLogLevelNothing`| Tüm günlüğe kaydetmeyi devre dışı bırak |
| `MSALLogLevelError` | Varsayılan düzey, yalnızca hata oluştuğunda bilgileri yazdırır |
| `MSALLogLevelWarning` | Uyarılar |
| `MSALLogLevelInfo` |  Kitaplık giriş noktaları, parametreler ve çeşitli Anahtarlık işlemleri |
|`MSALLogLevelVerbose`     |  API izleme |

Örnek:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Günlük iletisi biçimi

MSAL günlük iletilerinin ileti kısmı şu biçimdedir `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Örnek:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Bağıntı kimlikleri ve zaman damgaları sağlamak, sorunları izlemek için yararlıdır. Zaman damgası ve bağıntı KIMLIĞI bilgileri günlük iletisinde bulunabilir. Bu dosyaları almak için tek güvenilir yer MSAL Logging iletilerinden alınır.

---

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla kod örneği için [Microsoft Identity platform kod örneklerine](sample-v2-code.md)bakın.