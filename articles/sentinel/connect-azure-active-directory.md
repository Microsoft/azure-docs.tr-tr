---
title: Azure Active Directory verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Azure Active Directory verileri nasıl toplayacağınızı ve Azure AD oturum açma, denetim ve sağlama günlüklerinin Azure Sentinel 'e nasıl akışının nasıl yapılacağını öğrenin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: d2cfc2d592c24cf5d15a489ea4bde36ea3c2f863
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872380"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Azure Active Directory (Azure AD) verilerini Azure Sentinel 'e bağlama

Azure Sentinel 'in yerleşik bağlayıcısını kullanarak [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) verileri toplayıp Azure Sentinel 'e aktarabilirsiniz. Bağlayıcı, aşağıdaki günlük türlerini akışla kullanmanıza olanak sağlar:

- Kullanıcının kimlik doğrulama faktörü sağlayan [etkileşimli kullanıcı oturum açma](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) bilgileri Içeren [**oturum açma günlükleri**](../active-directory/reports-monitoring/concept-all-sign-ins.md).

    Azure AD Bağlayıcısı artık şu anda **Önizleme** aşamasında olan şu üç ek oturum açma günlüğü kategorisini içermektedir:
    
    - Kullanıcıdan herhangi bir etkileşim veya kimlik doğrulama faktörü olmadan bir kullanıcı adına istemci tarafından gerçekleştirilen oturum açma bilgileri içeren [**etkileşimli olmayan kullanıcı oturum açma günlükleri**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins).
    
    - Herhangi bir Kullanıcı içermeyen uygulamalar ve hizmet sorumluları tarafından gerçekleştirilen oturum açma bilgilerini içeren [**hizmet sorumlusu oturum açma günlükleri**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins). Bu oturum açma işlemleri sırasında, uygulama veya hizmet, kaynakların kimliğini doğrulamak veya kaynaklara erişmek için kendi adına bir kimlik bilgisi sağlar.
    
    - Azure tarafından yönetilen gizli dizileri olan Azure kaynakları tarafından gerçekleştirilen oturum açma bilgilerini içeren [**yönetilen kimlik oturum açma günlükleri**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins). Daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../active-directory/managed-identities-azure-resources/overview.md)

- Kullanıcı ve Grup Yönetimi, yönetilen uygulamalar ve Dizin etkinlikleriyle ilgili sistem etkinliğiyle ilgili bilgileri içeren [**Denetim günlükleri**](../active-directory/reports-monitoring/concept-audit-logs.md).

- Azure AD sağlama hizmeti tarafından sağlanan kullanıcılar, gruplar ve rollerle ilgili sistem etkinliği bilgilerini içeren günlükleri (Ayrıca **önizlemede**) [**sağlama**](../active-directory/reports-monitoring/concept-provisioning-logs.md) . 

> [!IMPORTANT]
> Yukarıda belirtildiği gibi, bazı kullanılabilir günlük türleri şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.
## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel 'de oturum açma günlüklerini almak için bir Azure Active Directory P1 veya P2 lisansı gerekir. Herhangi bir Azure AD lisansı (ücretsiz/O365/P1/P2) diğer günlük türlerini almak için yeterlidir. Azure Izleyici (Log Analytics) ve Azure Sentinel için ek gigabayt başına ücretler uygulanabilir.

- Kullanıcı çalışma alanında Azure Sentinel katılımcısı rolüne atanmalıdır.

- Kullanıcıların, günlüklerini akışını istediğiniz kiracıda genel yönetici veya güvenlik yöneticisi rollerine atanması gerekir.

- Bağlantı durumunu görebilmeniz için, Kullanıcı Azure AD Tanılama ayarları üzerinde okuma ve yazma izinlerine sahip olmalıdır. 

## <a name="connect-to-azure-active-directory"></a>Azure Active Directory Bağlan

1. Azure Sentinel 'de, gezinti menüsünden **veri bağlayıcıları** ' nı seçin.

1. Veri bağlayıcıları galerisinden **Azure Active Directory** ' yi seçin ve ardından **bağlayıcı sayfasını aç**' ı seçin.

1. Azure Sentinel 'e akışa almak istediğiniz günlük türlerinin yanındaki onay kutularını işaretleyin (yukarıya bakın) ve **Bağlan**' a tıklayın.

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler **günlüklerde**, **logmanagement** bölümünün altında aşağıdaki tablolarda görünür:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Azure AD günlüklerini sorgulamak için, sorgu penceresinin üst kısmında ilgili tablo adını girin.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede Azure Active Directory Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
