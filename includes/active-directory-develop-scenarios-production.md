---
title: include dosyası
description: include dosyası
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 75650d7ff0ac647aeb6dace76c270680b1b89347
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954977"
---
## <a name="enable-logging"></a>Günlü kaydını etkinleştir

Hata ayıklama ve kimlik doğrulama sırasında hata giderme senaryolarında, Microsoft kimlik doğrulama kitaplığı yerleşik günlük desteği sağlar. Günlüğe kaydetme her bir kitaplık aşağıdaki makalelerde ele alınmıştır:

:::row:::
    :::column:::
        - [MSAL.NET’te oturum açma](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [Android için MSAL’de oturum açma](../articles/active-directory/develop/msal-logging-android.md)
        - [MSAL.js’de oturum açma](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [iOS/macOS için MSAL’de oturum açma](../articles/active-directory/develop/msal-logging-ios.md)
        - [Java için MSAL’de oturum açma](../articles/active-directory/develop/msal-logging-java.md)
        - [Python için MSAL’de oturum açma](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

Veri toplama için bazı öneriler aşağıda verilmiştir:

- Kullanıcılar sorun yaşadıklarında yardım isteyebilir. Günlükleri yakalamak ve geçici olarak depolamak en iyi uygulamadır. Kullanıcıların günlükleri karşıya yükleyebilecekleri bir konum belirtin. MSAL, kimlik doğrulamasıyla ilgili ayrıntılı bilgileri yakalamak için günlüğe kaydetme uzantıları sağlar.

- Telemetri varsa, kullanıcıların uygulamanızda oturum açma hakkında veri toplamak için MSAL aracılığıyla etkinleştirin.


## <a name="validate-your-integration"></a>Tümleştirmenizi doğrulama

[Microsoft Identity platform tümleştirme denetim listesini](../articles/active-directory/develop/identity-platform-integration-checklist.md)izleyerek tümleştirmenizi test edin.
