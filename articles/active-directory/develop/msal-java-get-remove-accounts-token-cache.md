---
title: Belirteç önbelleğinden hesapları al & Kaldır (MSAL4j) | Mavisi
titleSuffix: Microsoft identity platform
description: Java için Microsoft kimlik doğrulama kitaplığı 'nı kullanarak, belirteç önbelleğinde hesapları görüntülemeyi ve kaldırmayı öğrenin.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: fc039e06c8c9d75608b60c2f48e86bc5503e5aec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96344870"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Java için MSAL kullanarak belirteç önbelleğinden hesap alma ve kaldırma

Java için MSAL, varsayılan olarak bellek içi belirteç önbelleği sağlar. Bellek içi belirteç önbelleği, uygulama örneğinin süresini sürer.

## <a name="see-which-accounts-are-in-the-cache"></a>Hangi hesapların önbellekte olduğunu görün

Aşağıdaki örnekte gösterildiği gibi çağırarak hangi hesapların önbellekte olduğunu kontrol edebilirsiniz `PublicClientApplication.getAccounts()` :

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Hesapları önbellekten kaldır

Bir hesabı önbellekten kaldırmak için, kaldırılması gereken hesabı bulun ve `PublicClientApplication.removeAccount()` Aşağıdaki örnekte gösterildiği gibi çağırın:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Daha fazla bilgi edinin

Java için MSAL kullanıyorsanız, [Java IÇIN msal Içindeki özel belirteç önbelleği serileştirme](msal-java-token-cache-serialization.md)hakkında bilgi edinin.
