---
title: İş cihazınızı kuruluşunuzun ağına ekleyin-AD
description: İş cihazınızı kuruluşunuzun ağına nasıl katılacağınızı öğrenin.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: b122d70179f22c91028c8343b23d74cc616b1f76
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028538"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>İş cihazınızı kuruluşunuzun ağına ekleyin
Potansiyel olarak kısıtlanmış kaynaklara erişebilmek için, iş sahibi Windows 10 cihazınızı kuruluşunuzun ağına katın.

## <a name="what-happens-when-you-join-your-device"></a>Cihazınıza katılırsanız ne olur?
Windows 10 cihazınızı kuruluşunuzun ağına katlarken aşağıdaki eylemler gerçekleşecektir:

- Windows, cihazınızı kuruluşunuzun ağına kaydeder ve kişisel hesabınızı kullanarak kaynaklarınıza erişmenize olanak tanır. Cihazınız kaydedildikten sonra Windows, cihazınızı ağa birleştirir. böylece, kuruluşunuzun Kullanıcı adını ve parolasını kullanarak oturum açabilir ve Kısıtlanmış kaynaklara erişebilirsiniz.

- İsteğe bağlı olarak, kuruluşunuzun seçeneklerine bağlı olarak, [Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) veya [güvenlik bilgileri](user-help-security-info-overview.md)aracılığıyla iki adımlı doğrulama ayarlamanız istenebilir.

- İsteğe bağlı olarak, kuruluşunuzun seçeneklerine bağlı olarak, Microsoft Intune gibi mobil cihaz yönetimine otomatik olarak kaydolmuş olabilirsiniz. Microsoft Intune kaydolma hakkında daha fazla bilgi için bkz. [cihazınızı Intune 'A kaydetme](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Kurumsal hesabınızla otomatik oturum açmayı kullanarak oturum açma işlemine geçebilirsiniz.

## <a name="to-join-a-brand-new-windows-10-device"></a>Yeni bir Windows 10 cihazına katmak için
Cihazınız yepyeni ve henüz ayarlanmamışsa, cihazınızı ağa katmak için Windows Ilk çalıştırma deneyimi (OOBE) işlemini izleyebilirsiniz.

1. Yeni cihazınızı başlatın ve OOBE işlemine başlayın.

2. **Microsoft hesabıyla oturum açın** ekranında iş veya okul e-posta adresinizi yazın.

    ![E-posta adresi ile oturum açma ekranı](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. **Parola gir** ekranında parolanızı yazın.

    ![Parola ekranınızı girin](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Mobil cihazınızda, hesabınıza erişebilmek için cihazınızı onaylayın. 

    ![Mobil Bildirim ekranı](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Gizlilik ayarlarınızı ayarlama ve Windows Hello 'Yu ayarlama da dahil olmak üzere OOBE işlemini doldurun (gerekirse).

    Cihazınız artık kuruluşunuzun ağına katılmış.

## <a name="to-make-sure-youre-joined"></a>Katıldığınızdan emin olmak için
Ayarlarınıza bakarak katıldığınızdan emin olabilirsiniz.

1. **Ayarları**açın ve ardından **hesaplar**' ı seçin.

    ![Ayarlar ekranındaki hesaplar](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. **İşe veya okula erişim**' i seçin ve örneğin  ***\<Your_organization* Azure AD > bağlı**olduğunu belirten bir metin görtığınızdan emin olun.

    ![Bağlı contoso hesabıyla iş veya okul ekranına erişin](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Zaten yapılandırılmış bir Windows 10 cihazına katmak için
Cihazınızda bir süredir varsa ve bu cihaz zaten ayarlandıysa, cihazınızı ağa katmak için aşağıdaki adımları izleyebilirsiniz.

1. **Ayarları**açın ve ardından **hesaplar**' ı seçin.

2. **İşe veya okula erişim**' i seçin ve ardından **Bağlan**' ı seçin.

    ![İş veya okul ve bağlantı bağlantılarına erişin](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. **İş veya okul hesabı ayarla** ekranında, **Azure Active Directory Için bu cihazı Birleştir**' i seçin.

    ![İş veya okul hesabı ekranı ayarlama](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. **Oturum açalım ekranına geldiğinizde** , e-posta adresinizi yazın (örneğin, alain@contoso.com) ve ardından **İleri**' yi seçin.

    ![Bu ekranda oturum açalım](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. **Parola gir** ekranında parolanızı yazın ve **oturum aç**' ı seçin.

    ![Parolayı girin](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Mobil cihazınızda, hesabınıza erişebilmek için cihazınızı onaylayın. 

    ![Mobil Bildirim ekranı](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. Kuruluşunuzun ekranı olduğundan **emin olun** , doğru olduğundan emin olmak için bilgileri gözden geçirin ve ardından **Birleştir**' i seçin.

    ![Bunun kuruluşunuzun doğrulama ekranı olduğundan emin olun](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. **Tüm** ekranındayken **bitti**' ye tıklayın.

    ![Tüm ekranlıdır](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Katıldığınızdan emin olmak için
Ayarlarınıza bakarak katıldığınızdan emin olabilirsiniz.

1. **Ayarları**açın ve ardından **hesaplar**' ı seçin.

    ![Ayarlar ekranındaki hesaplar](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. **İşe veya okula erişim**' i seçin ve örneğin  ***\<Your_organization* Azure AD > bağlı**olduğunu belirten bir metin görtığınızdan emin olun.

    ![Bağlı contoso hesabıyla iş veya okul ekranına erişin](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Sonraki adımlar
Cihazınızı kuruluşunuzun ağına katdıktan sonra iş veya okul hesabı bilgilerinizi kullanarak tüm kaynaklarınıza erişebilmelisiniz.

- Kuruluşunuz telefon gibi kişisel cihazınızı kaydetmenizi istiyorsa, bkz. [Kişisel cihazınızı kuruluşunuzun ağına kaydetme](user-help-register-device-on-network.md).

- Kuruluşunuz Microsoft Intune kullanılarak yönetiliyorsa ve kayıt, oturum açma veya Intune ile ilgili diğer sorunlar hakkında sorularınız varsa, bkz. [Intune Kullanıcı Yardım içeriği](https://docs.microsoft.com/intune-user-help/use-managed-devices-to-get-work-done).