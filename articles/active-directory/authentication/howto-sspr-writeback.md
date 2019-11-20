---
title: Azure AD SSPR için parola geri yazmayı Yapılandırma-Azure Active Directory
description: Azure AD 'yi ve Azure AD Connect kullanarak bir şirket içi dizine parolaları geri yazma
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e12fe38ba69f6ac8f27130e01baff0c358aa409
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021770"
---
# <a name="how-to-configure-password-writeback"></a>Nasıl yapılır: parola geri yazmayı yapılandırma

Aşağıdaki adımlarda, [hızlı](../hybrid/how-to-connect-install-express.md) veya [özel](../hybrid/how-to-connect-install-custom.md) ayarları kullanarak ortamınızda Azure AD Connect zaten yapılandırmış olduğunuz varsayılır.

1. Parola geri yazma özelliğini yapılandırmak ve etkinleştirmek için Azure AD Connect sunucunuzda oturum açın ve **Azure AD Connect** yapılandırma sihirbazını başlatın.
2. **Hoş Geldiniz** sayfasında, **Yapılandır**’ı seçin.
3. **Ek görevler** sayfasında **Eşitleme seçeneklerini özelleştirme**'yi ve ardından **İleri**'yi seçin.
4. **Azure AD'ye bağlan** sayfasına bir genel yöneticinin kimlik bilgilerini girin ve **İleri**'yi seçin.
5. **Dizinleri bağla** ve **Etki Alanı/OU** filtreleme sayfalarında **İleri**'yi seçin.
6. **İsteğe bağlı özellikler** sayfasında **Parola geri yazma** özelliğinin yanındaki kutuyu işaretleyin ve **İleri**'yi seçin.
   ![Azure AD Connect parola geri yazmayı etkinleştirme][Writeback]
7. **Yapılandırma için hazır** sayfasında **Yapılandır**'ı seçin ve işlemin tamamlanmasını bekleyin.
8. Yapılandırma tamamlandığında **Çıkış**'ı seçin.

Parola geri yazma ile ilgili yaygın sorun giderme görevlerinde, sorun giderme makalesindeki [parola geri yazma sorunlarını giderme](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) bölümüne bakın.

> [!WARNING]
> Parola geri yazma işlemi, [Azure Access Control hizmeti (ACS) 7 kasım 2018 tarihinde devre dışı bırakıldığında,](../develop/active-directory-acs-migration.md)Azure AD Connect sürümlerini kullanan müşteriler için çalışmayı durdurur. Azure AD Connect sürümleri, bu işlev için ACS 'ye bağlı olduğundan, 1.0.8641.0 ve üzeri sürümler artık parola geri yazmaya izin vermez.
>
> Bir hizmette kesintiye uğramamak için, önceki bir Azure AD Connect sürümünden daha yeni bir sürüme yükseltme yapın Azure AD Connect şu makaleye bakın [: önceki bir sürümden en son sürüme yükseltme](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="licensing-requirements-for-password-writeback"></a>Parola geri yazma için lisans gereksinimleri

**Şirket içi geri yazma Ile self servis parola sıfırlama/değiştirme/kilit açma, Azure AD 'nin Premium bir özelliğidir**. Lisanslama hakkında daha fazla bilgi için [Azure Active Directory fiyatlandırma sitesine](https://azure.microsoft.com/pricing/details/active-directory/)bakın.

Parola geri yazma özelliğini kullanmak için kiracınızda aşağıdaki lisanslardan birine atanmış olmanız gerekir:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 veya a3
* Enterprise Mobility + Security E5 veya a5
* Microsoft 365 E3 veya a3
* Microsoft 365 E5 veya a5
* Microsoft 365 F1
* Microsoft 365 İş

> [!WARNING]
> Tek başına Office 365 lisans planları, *"Şirket içi geri yazma Ile self servis parola sıfırlama/değiştirme/kilit açma" desteği vermez* ve bu işlevin çalışması için önceki planlardan birine sahip olmanızı gerektirir.
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Active Directory izinleri ve şirket içi parola karmaşıklığı ilkeleri 

SSPR kapsamında olmak istiyorsanız, Azure AD Connect yardımcı programında belirtilen hesabın aşağıdaki öğeler ayarlanmış olması gerekir:

* **Parola sıfırlama** 
* **Parola değiştirme** 
* `lockoutTime` **yazma izinleri**
* `pwdLastSet` **yazma izinleri**
* Her birinde **genişletilmiş haklar** :
   * Bu ormandaki *her etki alanının* kök nesnesi
   * SSPR için kapsamda olmasını istediğiniz kullanıcı kuruluş birimleri (OU)

Açıklanan hesabın başvurduğu hesaba emin değilseniz, Azure Active Directory Connect yapılandırma kullanıcı arabirimini açın ve **geçerli yapılandırmayı görüntüle** seçeneğini belirleyin. İzin eklemeniz gereken hesap, **eşitlenmiş dizinler**altında listelenmiştir.

Bu izinleri ayarlarsanız, her orman için MA hizmet hesabı, bu ormandaki Kullanıcı hesapları adına parolaları yönetebilir. 

> [!IMPORTANT]
> Bu izinleri atamayı düşünüyorsanız, geri yazma doğru şekilde yapılandırılmış gibi görünse de, kullanıcılar şirket içi parolalarını buluttan yönetmeye çalıştıklarında hatalar ile karşılaşacaktır.
>

> [!NOTE]
> Bu izinlerin dizininizdeki tüm nesnelere çoğaltılması bir saat veya daha fazla sürebilir.
>

Parola geri yazma işleminin gerçekleşmesi için uygun izinleri ayarlamak için aşağıdaki adımları izleyin:

1. Active Directory Kullanıcıları ve bilgisayarları, uygun etki alanı yönetim izinlerine sahip bir hesapla açın.
2. **Görünüm** menüsünde **Gelişmiş Özellikler** ' in açık olduğundan emin olun.
3. Sol bölmede, etki alanının kökünü temsil eden nesneye sağ tıklayın ve **özellikler** > **güvenlik** > **Gelişmiş**' i seçin.
4. **İzinler** sekmesinde **Ekle**' yi seçin.
5. İzinlerin uygulandığı hesabı seçin (Azure AD Connect kurulumundan).
6. **Uygulanacak** öğe açılır listesinde, alt **Kullanıcı nesneleri**' ni seçin.
7. **İzinler**altında, aşağıdaki seçenekler için kutuları seçin:
    * **Parola değiştirme**
    * **Parola sıfırlama**
8. **Özellikler**altında, aşağıdaki seçenekler için kutuları seçin:
    * **Yazma lockoutTime**
    * **PwdLastSet yazma**
9. Değişiklikleri uygulamak ve açık iletişim kutularından çıkmak için **Uygula/Tamam** ' ı seçin.

Yetkilinin kaynağı şirket içinde olduğundan, parola karmaşıklığı ilkeleri aynı bağlı veri kaynağından geçerlidir. "Minimum parola yaşı" için mevcut grup ilkelerini değiştirdiğinizden emin olun. Grup İlkesi 1 olarak ayarlanmamalıdır, bu, bir parolanın güncelleştirilebilmesi için en az bir gün daha eski olması gerektiği anlamına gelir. 0 olarak ayarlandığından emin olmanız gerekir. Bu ayarlar, **Windows ayarları > güvenlik ayarları > hesap ilkeleri > bilgisayar yapılandırması > ilkeleri**altındaki `gpmc.msc` bulunabilir. Değişikliğin geçerli olduğundan emin olmak için `gpupdate /force` çalıştırın. 

## <a name="next-steps"></a>Sonraki adımlar

[Parola geri yazma nedir?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Azure AD Connect parola geri yazmayı etkinleştirme"
