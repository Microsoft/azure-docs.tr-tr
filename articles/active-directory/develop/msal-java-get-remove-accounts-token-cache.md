---
title: Java için MSAL kullanarak belirteç önbelleğinden hesap alma ve kaldırma (MSAL4j)
titleSuffix: Microsoft identity platform
description: Java için Microsoft kimlik doğrulama kitaplığı 'nı kullanarak, belirteç önbelleğinde hesapları görüntülemeyi ve kaldırmayı öğrenin.
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343abd87d3b5e8b82989b8b370cef61ec6d051df
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905517"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java-msal4j"></a>Java için MSAL kullanarak belirteç önbelleğinden hesap alma ve kaldırma (MSAL4j)

MSAL4J, varsayılan olarak bellek içi belirteç önbelleği sağlar. Bellek içi belirteç önbelleği, uygulama örneğinin süresini sürer.

## <a name="see-which-accounts-are-in-the-cache"></a>Hangi hesapların önbellekte olduğunu görün

Aşağıdaki örnekte gösterildiği gibi `PublicClientApplication.getAccounts()` çağırarak hangi hesapların önbellekte olduğunu kontrol edebilirsiniz:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Hesapları önbellekten kaldır

Bir hesabı önbellekten kaldırmak için, kaldırılması gereken hesabı bulun ve aşağıdaki örnekte gösterildiği gibi `PublicClientApplicatoin.removeAccount()` çağırın:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Daha fazla bilgi edinin

Java için MSAL kullanıyorsanız, [Java IÇIN msal Içindeki özel belirteç önbelleği serileştirme](msal-java-token-cache-serialization.md)hakkında bilgi edinin.
