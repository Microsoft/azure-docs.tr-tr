---
author: mattchenderson
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: mahender
ms.openlocfilehash: 3b282a99bb7f6f107717d9c265a46d285d03b849
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649091"
---
Azure Active Directory (Azure AD) tarafından yönetilen bir kimlik, uygulamanızın Azure Key Vault gibi diğer Azure AD korumalı kaynaklara kolayca erişmesini sağlar. Kimlik, Azure platformu tarafından yönetilir ve herhangi bir gizli dizi sağlamanızı veya döndürmenizi gerektirmez. Azure AD 'de Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../articles/active-directory/managed-identities-azure-resources/overview.md).

Uygulamanıza iki tür kimlik verilebilir:

- **Sistem tarafından atanan bir kimlik** uygulamanıza bağlanır ve uygulamanız silinirse silinir. Uygulamanın yalnızca bir sistem tarafından atanmış kimliği olabilir.
- **Kullanıcı tarafından atanan bir kimlik** , uygulamanıza atanabilecek tek başına bir Azure kaynağıdır. Bir uygulamada birden çok kullanıcı tarafından atanan kimlik olabilir.