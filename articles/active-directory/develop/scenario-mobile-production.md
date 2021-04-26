---
title: Mobil uygulama için Web API 'Lerini üretim için hazırlama | Mavisi
titleSuffix: Microsoft identity platform
description: Web API 'Lerini çağıran bir mobil uygulama oluşturmayı öğrenin. (Uygulamaları üretime hazırlayın.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 08243fd06de289941d8e6a9197ccb349614af056
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675966"
---
# <a name="prepare-mobile-apps-for-production"></a>Mobil uygulamaları üretime hazırlama

Bu makalede, üretim ortamına taşımadan önce mobil uygulamanızın kalitesini ve güvenilirliğini geliştirme hakkında ayrıntılı bilgi sağlanır.

## <a name="handle-errors"></a>Hataları işleme

Bir mobil uygulamayı üretime hazırlarken çeşitli hata koşulları oluşabilir. İşletireceğiz ana durumlar sessiz arızalardır ve etkileşime geri dönüşlerdir. Göz önünde bulundurmanız gereken diğer koşullar arasında ağ durumları, hizmet kesintileri, yönetici onayı gereksinimleri ve diğer senaryoya özgü durumlar dahil değildir.

Her Microsoft kimlik doğrulama kitaplığı (MSAL) türü için, hata koşullarının nasıl işleneceğini açıklayan örnek kodu ve wiki içeriğini bulabilirsiniz:

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Sonraki adımlar

Ek örnekler denemek için bkz. [Masaüstü ve mobil ortak istemci uygulamaları](sample-v2-code.md#desktop-and-mobile-public-client-apps).