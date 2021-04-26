---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/09/2021
ms.author: alkohli
ms.openlocfilehash: 1298673c475a0308f1db27641aae93837e6b5df3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305571"
---
Aşağıdaki adımların, cihazınızdan cihaza erişmek için kullanılabilir olduğundan emin olun.

İstemcinizin yerel Azure Resource Manager bağlanabildiğini doğrulayın. 

1. Kimlik doğrulamak için yerel cihaz API 'Lerini çağırın:

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. `EdgeArmUser`Azure Resource Manager üzerinden bağlanmak için Kullanıcı adını ve parolayı belirtin. Parolayı geri çağırmıyorsanız, [Azure Resource Manager parolasını sıfırlayın](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md) ve bu parolayı kullanarak oturum açın.