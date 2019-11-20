---
title: Azure AD Connect yüklemesi Sihirbazı 'nı yeniden çalıştırın | Microsoft Docs
description: Yükleme sihirbazının ikinci kez çalıştırdığınızda nasıl çalıştığını açıklar.
keywords: Azure AD Connect Yükleme Sihirbazı ikinci kez çalıştırdığınızda bakım ayarlarını yapılandırmanızı sağlar
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5440c54b01f62b3ad61b355f4c622a31910a65c1
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71290928"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect eşitleme: Yükleme sihirbazını ikinci kez çalıştırma
Azure AD Connect Yükleme Sihirbazı 'nı ilk kez çalıştırdığınızda yüklemenizi nasıl yapılandıracağınızı adım adım gösterir. Yükleme sihirbazını yeniden çalıştırırsanız, bakım seçenekleri sunulur.

>[!IMPORTANT]
>Bir eşitleme sürerken Yükleme Sihirbazı 'nı çalıştıracağınızı unutmayın.  Sihirbazı başlatmadan önce lütfen bir eşitlemenin çalışmadığını doğrulayın.

Yükleme sihirbazını **Azure AD Connect**adlı başlangıç menüsünde bulabilirsiniz.

![Başlat menüsü](./media/how-to-connect-installation-wizard/startmenu.png)

Yükleme sihirbazını başlattığınızda, şu seçeneklere sahip bir sayfa görürsünüz:

![Ek görev listesi içeren sayfa](./media/how-to-connect-installation-wizard/additionaltasks.png)

Azure AD Connect ile ADFS yüklediyseniz, daha da fazla seçeneğiniz vardır. ADFS için sahip olduğunuz ek seçenekler, [ADFS yönetiminde](how-to-connect-fed-management.md#manage-ad-fs)belgelenmiştir.

Görevlerden birini seçin ve devam etmek için **İleri** 'yi tıklatın.

> [!IMPORTANT]
> Yükleme Sihirbazı açıkken, eşitleme altyapıdaki tüm işlemler askıya alınır. Yapılandırma değişikliklerinizi tamamladıktan hemen sonra yükleme sihirbazını kapattığınızdan emin olun.
>
>

## <a name="view-current-configuration"></a>Geçerli yapılandırmayı görüntüleme
Bu seçenek, şu anda yapılandırılmış seçeneklerinizin hızlı bir görünümünü sağlar.

![Tüm seçeneklerin listesini ve bunların durumunu içeren sayfa](./media/how-to-connect-installation-wizard/viewconfig.png)

Geri dönmek için **önceki** seçeneğine tıklayın. **Çıkış**' ı seçerseniz, Yükleme Sihirbazı 'nı kapatın.

## <a name="customize-synchronization-options"></a>Eşitleme seçeneklerini özelleştirme
Bu seçenek, eşitleme yapılandırmasında değişiklik yapmak için kullanılır. Özel yapılandırma yükleme yolundan bir seçenek alt kümesi görürsünüz. Başlangıçta Express yükleme kullandıysanız bile bu seçeneği görürsünüz.

* [Daha fazla dizin ekleyin](how-to-connect-install-custom.md#connect-your-directories). Bir dizini kaldırmak için bkz. [bağlayıcıyı silme](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Etki alanı ve OU filtresini değiştirin](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Grup filtrelemeyi kaldırın.
* [İsteğe bağlı özellikleri değiştirin](how-to-connect-install-custom.md#optional-features).

İlk yüklemedeki diğer seçenekler değiştirilemez ve kullanılabilir değildir. Bu seçenekler şunlardır:

* UserPrincipalName ve Sourcetutturucu için kullanılacak özniteliğini değiştirin.
* Farklı ormandaki nesneler için katılım yöntemini değiştirin.
* Grup tabanlı filtrelemeyi etkinleştirin.

## <a name="refresh-directory-schema"></a>Dizin şemasını yenile
Bu seçenek, şemayı şirket içi AD DS ormanlarınızın birinde değiştirdiyseniz kullanılır. Örneğin, Exchange 'i yüklemiş veya cihaz nesneleriyle bir Windows Server 2012 şemasına yükseltmişseniz olabilirsiniz. Bu durumda, şemayı AD DS ' dan yeniden okumak ve önbelleğini güncellemek için Azure AD Connect istemeniz gerekir. Bu eylem eşitleme kurallarını da yeniden oluşturur. Exchange şemasını eklerseniz, örnek olarak, Exchange için eşitleme kuralları yapılandırmaya eklenir.

Bu seçeneği belirlediğinizde, yapılandırmanızda bulunan tüm dizinler listelenir. Varsayılan ayarı koruyabilir ve tüm ormanları yenileyebilir veya bazılarının seçimini kaldırabilirsiniz.

![Ortamdaki tüm dizinlerin listesini içeren sayfa](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Hazırlama modunu yapılandırma
Bu seçenek, sunucusunda hazırlama modunu etkinleştirmenizi ve devre dışı bırakmanızı sağlar. Hazırlama modu ve bunların nasıl kullanıldığı hakkında daha fazla bilgi, [işlemler](how-to-connect-sync-staging-server.md)içinde bulunabilir.

Bu seçenek, hazırlama özelliğinin etkin mi yoksa devre dışı mı olduğunu gösterir:  
![Hazırlama modunun geçerli durumunu da gösteren seçenek](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Durumu değiştirmek için bu seçeneği belirleyin ve onay kutusunu seçin veya seçimini kaldırın.  
![Hazırlama modunun geçerli durumunu da gösteren seçenek](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Kullanıcı oturum açma değiştirme
Bu seçenek, Parola karması eşitleme, geçişli kimlik doğrulama veya Federasyon için Kullanıcı oturum açma yöntemini değiştirmenize izin verir. **Yapılandırma yok**olarak değiştiremezsiniz.

Bu seçenek hakkında daha fazla bilgi için bkz. [Kullanıcı oturumu açma](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Sonraki adımlar
* [Bildirim temelli sağlamayı anlamak](concept-azure-ad-connect-sync-declarative-provisioning.md)Azure AD Connect eşitleme tarafından kullanılan yapılandırma modeli hakkında daha fazla bilgi edinin.

**Genel Bakış konuları**

* [Azure AD Connect eşitlemesi: Eşitlemeyi anlama ve özelleştirme](how-to-connect-sync-whatis.md)
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md)
