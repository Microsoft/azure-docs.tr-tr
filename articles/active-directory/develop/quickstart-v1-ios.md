---
title: OAuth 2,0 kullanarak oturum açmak için Azure AD ile tümleştirilmiş bir iOS uygulaması oluşturun | Microsoft Docs
description: iOS uygulamanızdan kullanıcıların oturumunu açmayı ve Microsoft Graph API'sini çağırmayı öğrenin.
services: active-directory
documentationcenter: ios
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: brandwe
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b78461c9e585cbba4902799543dadb0f013e85
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149675"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app-v10"></a>Hızlı başlangıç: kullanıcılar oturum açın ve bir iOS uygulamasından (v 1.0) Microsoft Graph API 'sini çağırın

[Microsoft Identity platform](v2-overview.md) , Azure Active Directory (Azure AD) geliştirici platformunun bir evmidir. Bu platform geliştiricilerin tüm Microsoft kimlikleriyle oturum açan ve Microsoft Graph veya diğer Microsoft API'leri ya da geliştiricilerin derlemiş olduğu API'lere çağrı göndermek için gerekli belirteçleri alan uygulamalar derlemesini sağlar.

[Microsoft kimlik doğrulama kitaplığı (msal)](msal-overview.md) , geliştiricilerin güvenli Web API 'lerine erişmek için Microsoft Identity platform uç noktasından belirteç almasına olanak sağlar. Active Directory Authentication Library (ADAL), MSAL Microsoft Identity platform (v 2.0) uç noktasıyla tümleştirilebilen geliştiriciler için Azure AD (v 1.0) uç noktası ile tümleşir.

Yeni macOS ve iOS uygulamaları için Microsoft Identity platform (v 2.0) ve MSAL kullanarak belirteçleri edinmeniz ve güvenli Web API 'Lerine erişmeniz önerilir: [hızlı başlangıç: kullanıcıların oturumunu açın ve iOS veya macOS uygulamasından MICROSOFT Graph API 'sini çağırın](quickstart-v2-ios.md).
