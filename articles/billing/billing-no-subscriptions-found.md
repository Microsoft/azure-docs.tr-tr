---
title: Abonelik bulunamadı hatası - Azure portalında oturum açma | Microsoft Docs
description: Azure portalında veya Azure hesap merkezinde oturum açıldığında Abonelik bulunamadı hatasının oluştuğu bir sorunun çözümünü sağlar.
services: ''
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 19a30a99b9df23358aafd88c55bde08c73a3be72
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121537"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Azure portalı veya Azure hesap merkezi için Abonelik bulunamadı oturum açma hatası

[Azure portalında](https://portal.azure.com/) veya [Azure Hesap Merkezi](https://account.windowsazure.com/Subscriptions)’nde oturum açmayı denediğinizde “Abonelik bulunamadı” hata iletisini alabilirsiniz. Bu makalede, bu sorun için bir çözüm sağlanmaktadır.

## <a name="symptom"></a>Belirti

[Azure portalında](https://portal.azure.com/) veya [Azure hesap merkezinde](https://account.windowsazure.com/Subscriptions) oturum açmayı denediğinizde şu hata iletisini alırsınız: “Abonelik bulunamadı”.

## <a name="cause"></a>Nedeni

Yanlış dizinde seçim yaptıysanız veya hesabınız yeterli izinlere sahip değilse bu sorun oluşur. 

## <a name="solution"></a>Çözüm

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Senaryo 1: [Azure portalında](https://portal.azure.com) hata iletisi alınır

Bu sorunu düzeltmek için:

* Sağ üst kısımda hesabınıza tıklayarak doğru Azure dizininin seçildiğinden emin olun.

  ![Azure portalının sağ üst köşesinde dizini seçin](./media/billing-no-subscriptions-found/directory-switch.png)
* Doğru Azure dizini seçili olduğu halde hata iletisini almaya devam ediyorsanız [hesabınıza Sahip rolünü atayın](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Senaryo 2: [Azure Hesap Merkezi](https://account.windowsazure.com/Subscriptions)’nde hata iletisi alınır

Kullandığınız hesabın Hesap Yöneticisi olup olmadığını denetleyin. Hesap Yöneticisinin kim olduğunu doğrulamak için şu adımları izleyin:

1. [Azure portalındaki Abonelikler görünümünde](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oturum açın.
1. Denetlemek istediğiniz aboneliği seçin ve **Ayarlar** bölümüne bakın.
1. **Özellikler**’i seçin. Aboneliğin hesap yöneticisi, **Hesap Yöneticisi** kutusunda görüntülenir.  

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).
