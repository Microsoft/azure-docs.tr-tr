---
title: include dosyası
description: include dosyası
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5e605e4ad987db16b98649b32dc96fa1620b1564
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011986"
---
.NET için [Microsoft Azure uygulama kimlik doğrulaması](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) istemci kitaplığı (Önizleme), kodunuzun bir belirtecini alma ve yenileme sürecini basitleştirir. Uygulama kimlik doğrulaması istemci kitaplığı, kimlik doğrulamasını otomatik olarak yönetir. Kitaplık, yerel geliştirme sırasında kimlik doğrulaması yapmak için geliştiricinin kimlik bilgilerini kullanır. Geliştirici kimlik bilgilerini yerel geliştirme sırasında kullanmak daha güvenlidir çünkü Azure AD kimlik bilgileri oluşturmanız veya kimlik bilgilerini geliştiriciler arasında paylaşmanız gerekmez. Çözüm daha sonra Azure 'a dağıtıldığında, kitaplık otomatik olarak uygulama kimlik bilgilerini kullanarak geçiş yapar.

Uygulama kimlik doğrulama kitaplığını bir Azure depolama uygulamasında kullanmak için, [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)'den en son önizleme paketini ve [.NET için Azure Storage ortak Istemci kitaplığının](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) ve [Azure Blob depolama istemci Kitaplığı ' nın en son sürümünü yüklemek için .NET için](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Aşağıdaki **using** deyimlerini kodunuza ekleyin:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```
