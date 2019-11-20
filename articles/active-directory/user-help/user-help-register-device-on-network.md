---
title: Kişisel cihazları bir kuruluşun ağına kaydetme-Azure AD
description: Kuruluşunuzun korunan kaynaklarına erişebilmek için kişisel cihazınızı kuruluşunuzun ağına nasıl kaydedeceğinizi öğrenin.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: lizross
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 879fa55df422e6039c6830e25e43637fc31b8037
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028481"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Kişisel cihazınızı kuruluşunuzun ağına kaydetme
Kuruluşunuzun ağına kişisel cihazınızı (genellikle telefon veya tablet) kaydedin. Cihazınız kaydolduktan sonra kuruluşunuzun kısıtlanmış kaynaklarına erişebilir.

>[!Note]
>Bu makalede tanıtım amacıyla bir Windows cihazı kullanılmaktadır, ancak iOS, Android veya macOS çalıştıran cihazları da kaydedebilirsiniz.

## <a name="what-happens-when-you-register-your-device"></a>Cihazınızı kaydettiğinizde ne olur?
Cihazınızı kuruluşunuzun ağına kaydederken aşağıdaki eylemler gerçekleşecektir:

- Windows cihazınızı kuruluşunuzun ağına kaydeder.

- İsteğe bağlı olarak, kuruluşunuzun seçeneklerine bağlı olarak, [Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) veya [güvenlik bilgileri](user-help-security-info-overview.md)aracılığıyla iki adımlı doğrulama ayarlamanız istenebilir.

- İsteğe bağlı olarak, kuruluşunuzun seçeneklerine bağlı olarak, Microsoft Intune gibi mobil cihaz yönetimine otomatik olarak kaydolmuş olabilirsiniz. Microsoft Intune kaydolma hakkında daha fazla bilgi için bkz. [cihazınızı Intune 'A kaydetme](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- İş veya okul hesabınızın Kullanıcı adı ve parolasını kullanarak oturum açma işlemine geçebilirsiniz.

## <a name="to-register-your-windows-device"></a>Windows cihazınızı kaydetmek için

Kişisel cihazınızı ağınıza kaydetmek için aşağıdaki adımları izleyin.

1. **Ayarları**açın ve ardından **hesaplar**' ı seçin.

    ![Ayarlar ekranındaki hesaplar](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. **İşe veya okula erişim**' i seçin ve ardından **erişim iş veya okul** ekranından **Bağlan** ' ı seçin.

    ![Bağlan seçeneği vurgulanmış şekilde iş veya okul ekranına erişin](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. **İş veya okul hesabı ekle** ekranında, iş veya okul hesabınız için e-posta adresinizi yazın ve ardından **İleri**' yi seçin. Örneğin, alain@contoso.com.

4. İş veya okul hesabınızda oturum açın ve **oturum aç**' ı seçin.

5. Kimlik doğrulama isteğinizi onaylama (iki adımlı doğrulama kullanıyorsanız) ve Windows Hello 'Yu ayarlama (gerekliyse) dahil olmak üzere kayıt işleminin geri kalanını tamamlayın.

## <a name="to-verify-that-youre-registered"></a>Kaydoldığınızı doğrulamak için
Ayarlarınıza bakarak kaydolduğunuzdan emin olabilirsiniz.

1. **Ayarları**açın ve ardından **hesaplar**' ı seçin.

    ![Ayarlar ekranındaki hesaplar](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. **İşe veya okula erişim**' i seçin ve iş veya okul hesabınızı görtığınızdan emin olun.

    ![Bağlı contoso hesabıyla iş veya okul ekranına erişin](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Sonraki adımlar
Kişisel cihazınızı kuruluşunuzun ağına kaydettikten sonra, kaynaklarınızın çoğuna erişebilmelisiniz.

- Kuruluşunuz iş cihazınıza katılmayı istiyorsa, bkz. [iş cihazınızı kuruluşunuzun ağına ekleme](user-help-join-device-on-network.md).



