---
title: include dosyası
description: Gizli istemci senaryosu giriş sayfaları için dosya ekleme (Daemon, Web uygulaması, Web API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2020
ms.author: jmprieur
ms.openlocfilehash: 42102f38959911388cefcc141d949e59f24a2c31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996024"
---
## <a name="add-a-client-secret-or-certificate"></a>İstemci parolası veya sertifikası ekleme

Tüm gizli istemci uygulamaları gibi, Kullanıcı etkileşimi olmadan kendisi olarak kimlik doğrulaması yapabilmesi için uygulamanın *kimlik bilgileri* olarak davranacak bir gizli dizi veya sertifika eklemeniz gerekir.

[Azure Portal](#add-client-credentials-by-using-the-azure-portal) kullanarak veya [PowerShell](#add-client-credentials-by-using-powershell)gibi bir komut satırı aracı kullanarak, istemci uygulamanızın kaydına kimlik bilgileri ekleyebilirsiniz.

### <a name="add-client-credentials-by-using-the-azure-portal"></a>Azure portal kullanarak istemci kimlik bilgilerini ekleme

Gizli istemci uygulamanızın uygulama kaydına kimlik bilgileri eklemek için hızlı başlangıç: eklemek istediğiniz kimlik bilgisi türü için [Microsoft Identity platformu ile uygulama kaydetme](../articles/active-directory/develop/quickstart-register-app.md) ' daki adımları izleyin:

* [İstemci parolası ekleme](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [Sertifika ekle](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>PowerShell kullanarak istemci kimlik bilgilerini ekleme

Alternatif olarak, PowerShell kullanarak uygulamanızı Microsoft Identity platformu ile kaydettiğinizde kimlik bilgilerini ekleyebilirsiniz.

GitHub 'daki [Active-Directory-dotnetcore-Daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) kod örneği, bir uygulama kaydedilirken uygulama gizli dizisi veya sertifikasının nasıl ekleneceğini gösterir:

- PowerShell ile bir **istemci gizli anahtarı** ekleme hakkında ayrıntılı bilgi için bkz. [appcreationscripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- PowerShell ile bir **sertifika** ekleme hakkında ayrıntılı bilgi için bkz. [appcreationscripts-withcert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
