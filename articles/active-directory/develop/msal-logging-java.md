---
title: Java için MSAL 'te hataları ve özel durumları günlüğe kaydetme
titleSuffix: Microsoft identity platform
description: Java için MSAL 'te hataların ve özel durumların nasıl günlüğe alınacağını öğrenin
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
ms.openlocfilehash: d3fa13a6751b2d8acf1fc99aecbf174f1823fb0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954927"
---
# <a name="logging-in-msal-for-java"></a>Java için MSAL’de oturum açma

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-java-logging"></a>Java günlüğü için MSAL

Java için MSAL, DOLAYıSıYLA SLF4J ile uyumlu olduğu sürece, zaten uygulamanızla kullanmakta olduğunuz günlük kitaplığını kullanmanıza olanak sağlar. Java için MSAL, Java [. util. Logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Logback](http://logback.qos.ch/) ve [Log4J](https://logging.apache.org/log4j/2.x/)gibi çeşitli günlük çerçeveleri için basit bir façlade veya soyutlama olarak Java (dolayısıyla slf4j) [için basit günlük kaydı](http://www.slf4j.org/) kullanır. DOLAYıSıYLA SLF4J, kullanıcının istenen günlük çerçevesini dağıtım zamanında eklemesine izin verir.

Örneğin, uygulamanızdaki günlüğe kaydetme çerçevesi olarak Logback kullanmak için, uygulamanız için Maven Pod dosyasına Logback bağımlılığını ekleyin:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Sonra Logback yapılandırma dosyasını ekleyin:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

DOLAYıSıYLA SLF4J, dağıtım zamanında otomatik olarak Logback 'a bağlanır. MSAL Günlükler konsola yazılacak.

Diğer günlük çerçevelerine nasıl bağlayacağınız hakkında yönergeler için bkz. [dolayısıyla slf4j Manual](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Kişisel ve kuruluş bilgileri

Varsayılan olarak, MSAL Logging kişisel veya kurumsal verileri yakalamaz veya günlüğe eklemez. Aşağıdaki örnekte, kişisel veya kurumsal verilerin günlüğe kaydedilmesi varsayılan olarak kapalıdır:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

İstemci uygulama Oluşturucusu ' nu ayarlayarak kişisel ve kurumsal veri günlüğünü açın `logPii()` . Kişisel veya kurumsal veri günlüğü 'nü açarsanız, uygulamanızın yüksek oranda duyarlı verileri güvenli bir şekilde işlemek ve tüm düzenleme gereksinimleriyle uyumlu olması için sorumluluk olması gerekir.

Aşağıdaki örnekte, kişisel veya kurumsal verilerin günlüğe kaydedilmesi etkindir:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla kod örneği için [Microsoft Identity platform kod örneklerine](sample-v2-code.md)bakın.