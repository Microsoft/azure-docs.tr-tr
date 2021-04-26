---
title: Azure AD Connect "yapılandırmayın" olarak kullanırken PTA 'ı devre dışı bırak "| Microsoft Docs
description: Bu makalede, "yapılandırmayın" özelliği Azure AD Connect PTA 'nin nasıl devre dışı bırakılacağı açıklanmaktadır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26112b1e799cbde3145e7137c686b4b336db4bab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919944"
---
# <a name="disable-pta-when-using-azure-ad-connect"></a>Azure AD Connect kullanırken PTA 'ı devre dışı bırak

Azure AD Connect ile geçişli kimlik doğrulaması kullanıyorsanız ve **"yapılandırmayın"** olarak ayarlandıysa, devre dışı bırakabilirsiniz. 

>[!NOTE]
>Daha önce hiç PHıNıZ varsa PTA 'nin devre dışı bırakılması, kiracının kiracıya geri dönüşüne neden olur.

PTA 'nin devre dışı bırakılması aşağıdaki cmdlet 'ler kullanılarak yapılabilir. 

## <a name="prerequisites"></a>Önkoşullar
Aşağıdaki Önkoşullar gereklidir:
- PTA aracısının yüklü olduğu herhangi bir Windows makinesi. 
- Aracı 1.5.1742.0 veya üzeri bir sürümde olmalıdır. 
- PTA 'yi devre dışı bırakmak üzere PowerShell cmdlet 'lerini çalıştırmak için bir Azure genel yönetici hesabı.

>[!NOTE]
> Aracınız daha eskiyse, bu işlemi gerçekleştirmek için gerekli cmdlet 'ler bulunmayabilir. Azure portalından yeni bir aracı, herhangi bir Windows makinesine yükleyebilir ve yönetici kimlik bilgileri sağlayabilirsiniz. (Aracıyı yüklemek, bulutta PTA durumunu etkilemez)

> [!IMPORTANT]
> Azure Kamu Bulutu kullanıyorsanız, ENVIRONMENTNAME parametresini aşağıdaki değerle geçirmeniz gerekir. 
>
>| Ortam Adı | Bulut |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>PTA 'yi devre dışı bırakmak için
PowerShell oturumunda, PTA 'yi devre dışı bırakmak için aşağıdakileri kullanın:
1. PS C:\Program Files\Microsoft Azure AD Connect kimlik doğrulama Aracısı> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` veya `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` veya `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Bir aracıya erişiminiz yoksa

Bir aracı makineniz yoksa, bir aracı yüklemek için aşağıdaki komutu kullanabilirsiniz.

1. Portal.azure.com adresinden en son auth aracısını indirin.
2. Özelliği yükler: `.\AADConnectAuthAgentSetup.exe` veya `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory Geçişli Kimlik Doğrulaması ile kullanıcı oturumu açma](how-to-connect-pta.md)
