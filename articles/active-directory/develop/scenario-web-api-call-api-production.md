---
title: Web API 'Leri çağıran Web API 'sini üretime taşıma | Mavisi
titleSuffix: Microsoft identity platform
description: Web API 'Lerini üretime çağıran bir Web API 'sini nasıl taşıyacağınızı öğrenin.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0ab5baef925b7c8589dd7852b6ff8058d67ba745
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675881"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Web API 'Lerini çağıran bir Web API 'SI: üretime taşı

Web API 'Lerini çağırmak için bir belirteç aldıktan sonra, uygulamanızı üretime taşırken göz önünde bulundurmanız gereken bazı şeyler aşağıda verilmiştir.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Sonraki adımlar

Web API 'inizden Web API 'Lerini çağırma hakkında temel bilgileri öğrenmiş olduğunuza göre, Web API 'Lerini çağıran korumalı bir Web API 'SI oluşturmak için kullanılan kodu açıklayan aşağıdaki öğreticiyle ilgileniyor olabilirsiniz.

| Örnek | Platform | Description |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-WebApi-öğretici-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) Bölüm 1 | ASP.NET Core Web API, Masaüstü (WPF) | ASP.NET Core Web API çağrısı, Microsoft Identity platformunu kullanarak bir WPF uygulamasından çağırdığınız Microsoft Graph. |
