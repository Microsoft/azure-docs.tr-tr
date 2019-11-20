---
title: Azure AD ile parola geri yazmayı etkinleştirme
description: Bu öğreticide bulutta başlatılan parola değişikliklerini Azure AD Connect'in bir parçası olan şirket içi AD hizmetine almak için parola geri yazma özelliğini etkinleştireceksiniz.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1efb67df6c31a3b03fdc45fffc0564fb09e39faf
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853034"
---
# <a name="tutorial-enabling-password-writeback"></a>Öğretici: Parola geri yazma özelliğini etkinleştirme

Bu öğreticide hibrit ortamınız için parola geri yazma özelliğini etkinleştireceksiniz. Parola geri yazma özelliği, Azure Active Directory (Azure AD) üzerinde yapılan parola değişikliklerini şirket içi Active Directory Domain Services (AD DS) ortamınızla eşitlemek için kullanılır. Parola geri yazma özelliği, Azure AD'de yapılan parola değişikliklerini var olan şirket içi dizinine göndermek için kullanılan güvenli bir mekanizma olarak Azure AD Connect ile birlikte etkinleştirilir. Parola geri yazma özelliği hakkında ayrıntılı bilgi için bkz. [Parola geri yazma nedir?](concept-sspr-writeback.md)

> [!div class="checklist"]
> * Azure AD Connect'te parola geri yazma seçeneğini etkinleştirme
> * Self servis parola sıfırlama (SSPR) için parola geri yazma seçeneğini etkinleştirme

## <a name="prerequisites"></a>Önkoşullar

* En az deneme sürümü lisansı bulunan çalışan bir Azure AD kiracısına erişim.
* Azure AD kiracınızda Genel Yönetici ayrıcalıklarına sahip olan bir hesap.
* [Azure AD Connect](../hybrid/how-to-connect-install-express.md)'in güncel sürümünü çalıştıran yapılandırılmış bir sunucu.
* Önceki self servis parola sıfırlama (SSPR) öğreticilerinin tamamlanması.

## <a name="enable-password-writeback-option-in-azure-ad-connect"></a>Azure AD Connect'te parola geri yazma seçeneğini etkinleştirme

Parola geri yazma özelliğinin önce Azure AD Connect'i yüklediğiniz sunucuda etkinleştirilmesi gerekir.

1. Parola geri yazma özelliğini yapılandırmak ve etkinleştirmek için Azure AD Connect sunucunuzda oturum açın ve **Azure AD Connect** yapılandırma sihirbazını başlatın.
2. **Hoş Geldiniz** sayfasında, **Yapılandır**’ı seçin.
3. **Ek görevler** sayfasında **Eşitleme seçeneklerini özelleştirme**'yi ve ardından **İleri**'yi seçin.
4. **Azure AD'ye bağlan** sayfasına bir genel yöneticinin kimlik bilgilerini girin ve **İleri**'yi seçin.
5. **Dizinleri bağla** ve **Etki Alanı/OU** filtreleme sayfalarında **İleri**'yi seçin.
6. **İsteğe bağlı özellikler** sayfasında **Parola geri yazma** özelliğinin yanındaki kutuyu işaretleyin ve **İleri**'yi seçin.
7. **Yapılandırma için hazır** sayfasında **Yapılandır**'ı seçin ve işlemin tamamlanmasını bekleyin.
8. Yapılandırma tamamlandığında **Çıkış**'ı seçin.

## <a name="enable-password-writeback-option-in-sspr"></a>SSPR'de parola geri yazma seçeneğini etkinleştirme

Parola geri yazma özelliğini Azure AD Connect'te etkinleştirme, sürecin yarısıdır. Parola geri yazma özelliğini SSPR'de etkinleştirme döngüyü tamamlar ve parolalarını değiştirmek veya sıfırlamak isteyen kullanıcıları yeni parolalarını şirket içi ortamda da kullanma imkanı tanır.

1. Genel Yönetici hesabını kullanarak [Azure portal](https://portal.azure.com) oturumu açın.
2. **Azure Active Directory**'ye göz adın, **Parola Sıfırlama**'ya tıklayın ve ardından **Şirket içi tümleştirme**'yi seçin.
3. **Parolalar şirket içi dizininize geri yazılsın mı?** ayarını **Evet** olarak değiştirin.
4. **Kullanıcıların parolalarını sıfırlamadan hesapların kilidini açmasına izin verilsin mi?** ayarını **Evet** olarak değiştirin.
5. **Kaydet**'e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide self servis parola sıfırlama için parola geri yazma özelliğini etkinleştirdiniz. Azure portal penceresini açık bırakın ve bir sonraki öğreticiye geçerek çözümü pilot gruba dağıtmadan önce self servis parola sıfırlama özelliğiyle ilgili ek ayarları yapılandırın.

> [!div class="nextstepaction"]
> [Oturum açma sırasında risk değerlendirmesi yapma](tutorial-risk-based-sspr-mfa.md)
