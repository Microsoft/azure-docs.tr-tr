---
title: Azure AD oturum açma sayfam Microsoft hesaplarını kabul ediyor | Microsoft Docs
description: Ekran mesajlaşması, oturum açma sırasında Kullanıcı adı aramasını yansıtır
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221ab7c50a84650f1b2adf3fdb2b284365795f42
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024277"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Azure Active Directory 'de Microsoft hesapları için oturum açma seçenekleri

Azure Active Directory (Azure AD) için Microsoft 365 oturum açma sayfası iş veya okul hesaplarını ve Microsoft hesaplarını destekler, ancak kullanıcının durumuna bağlı olarak bir veya iki ya da her ikisi birden olabilir. Örneğin, Azure AD oturum açma sayfası şunları destekler:

* Her iki hesap türünden oturum açma işlemlerini kabul eden uygulamalar
* Konukları kabul eden kuruluşlar

## <a name="identification"></a>İsi
Kuruluşunuzun kullandığı oturum açma sayfasının, Kullanıcı adı alanındaki ipucu metnine bakarak Microsoft hesaplarını destekleyip desteklemediğini söyleyebilirsiniz. İpucu metni "e-posta, telefon veya Skype" diyor ise, oturum açma sayfası Microsoft hesaplarını destekler.

![Hesap oturum açma sayfaları arasındaki fark](./media/signin-account-support/ui-prompt.png)

[Ek oturum açma seçenekleri yalnızca kişisel Microsoft hesaplarında çalışır](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , ancak iş veya okul hesabı kaynaklarında oturum açmak için kullanılamaz.

## <a name="next-steps"></a>Sonraki adımlar

[Oturum açma markanızı özelleştirin](../fundamentals/add-custom-domain.md)