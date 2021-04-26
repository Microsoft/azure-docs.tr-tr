---
title: Azure Otomasyonu hesabı yönetilen kimliğinizi devre dışı bırakma (Önizleme)
description: Bu makalede, bir Azure Otomasyonu hesabının yönetilen kimliğini devre dışı bırakma ve kaldırma işlemleri açıklanmaktadır.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: e17e1afda50d9a0263067a77bf26435f53b4f237
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519281"
---
# <a name="disable-your-azure-automation-account-managed-identity-preview"></a>Azure Otomasyonu hesabı yönetilen kimliğinizi devre dışı bırakma (Önizleme)

Azure Otomasyonu 'nda sistem tarafından atanan bir kimliği devre dışı bırakmak için iki yol vardır. Bu görevi Azure portal veya bir Azure Resource Manager (ARM) şablonu kullanarak tamamlayabilirsiniz.

## <a name="disable-managed-identity-in-the-azure-portal"></a>Azure portal yönetilen kimliği devre dışı bırakma

Yönetilen kimliğin ilk olarak nasıl ayarlanmadığına bakılmaksızın Azure portal yönetilen kimliği devre dışı bırakabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Otomasyon hesabınıza gidin ve **Hesap ayarları** altında **kimlik** ' i seçin.

1. Sistem tarafından **atanan** seçeneğini **kapalı** olarak ayarlayın ve **Kaydet**' e basın. Onaylamanız istendiğinde **Evet**' e basın.

Yönetilen kimlik kaldırılır ve artık hedef kaynağa erişemez.

## <a name="disable-using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak devre dışı bırak

Otomasyon hesabınız için yönetilen kimliği bir Azure Resource Manager şablonu kullanarak oluşturduysanız, bu şablonu yeniden kullanarak ve ayarlarını değiştirerek yönetilen kimliği devre dışı bırakabilirsiniz. Aşağıdaki örnekte gösterildiği gibi, Identity nesnesinin Child özelliğinin türünü **none** olarak ayarlayın ve ardından şablonu yeniden çalıştırın.

```json
"identity": { 
   "type": "None" 
} 
```

Bu yöntemi kullanarak sistem tarafından atanan bir kimliğin kaldırılması aynı zamanda Azure AD 'den de silinir. Atanan uygulama kaynağı silindiğinde, sistem tarafından atanan kimlikler de Azure AD 'den otomatik olarak kaldırılır.

## <a name="next-steps"></a>Sonraki adımlar

- Azure Otomasyonu 'nda yönetilen kimliği etkinleştirme hakkında daha fazla bilgi için bkz. [Otomasyon için yönetilen kimliği etkinleştirme ve kullanma (Önizleme)](enable-managed-identity-for-automation.md).

- Otomasyon hesabı güvenliğine genel bakış için bkz. [Otomasyon hesabı kimlik doğrulamasına genel bakış](automation-security-overview.md).
