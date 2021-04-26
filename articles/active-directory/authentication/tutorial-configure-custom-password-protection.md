---
title: Özel Azure Active Directory parola koruma listelerini yapılandırma
description: Bu öğreticide, ortamınızdaki ortak kelimeleri kısıtlamak için Azure Active Directory için özel yasaklanmış parola koruma listelerini nasıl yapılandıracağınızı öğreneceksiniz.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89abdcf4c76c92c2ed967594527d323b2cd8be32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741193"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Öğretici: Azure Active Directory parola koruması için özel yasaklanmış parolaları yapılandırma

Kullanıcılar genellikle okul, spor ekibi veya çok büyük kişi gibi yaygın yerel sözcükleri kullanan parolalar oluşturur. Bu parolalar kolayca tahmin edilir ve sözlük tabanlı saldırılara karşı zayıftır. Kuruluşunuzda güçlü parolalar zorlamak için, Azure Active Directory (Azure AD) özel yasaklanmış parola listesi, değerlendirmek ve engellemek için özel dizeler eklemenizi sağlar. Özel yasaklanmış parola listesinde bir eşleşme varsa parola değiştirme isteği başarısız olur.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Özel yasaklanmış parolaları etkinleştir
> * Özel yasaklanmış parola listesine giriş ekleme
> * Parola değişikliklerini yasaklanmış parolayla sına

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* En az bir Azure AD Premium P1 veya deneme lisansı etkin çalışan bir Azure AD kiracısı.
    * Gerekirse, [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* *Genel yönetici* ayrıcalıklarına sahip bir hesap.
* Yönetici olmayan ve Kullanıcı tarafından bildiğiniz, *testuser* gibi bir parola. Bu öğreticide bu hesabı kullanarak bir parola değiştirme olayını test edersiniz.
    * Bir kullanıcı oluşturmanız gerekiyorsa bkz. [hızlı başlangıç: Azure Active Directory yeni kullanıcı ekleme](../fundamentals/add-users-azure-active-directory.md).
    * Parola değiştirme işlemini yasaklanmış bir parola kullanarak test etmek için, Azure AD kiracısı [self servis parola sıfırlama için yapılandırılmalıdır](tutorial-enable-sspr.md).

## <a name="what-are-banned-password-lists"></a>Yasaklanmış parola listeleri nelerdir?

Azure AD, genel yasaklanmış parola listesi içerir. Genel yasaklanmış parola listesinin içeriği herhangi bir dış veri kaynağını temel almaz. Bunun yerine, genel yasaklanmış parola listesi, Azure AD güvenlik telemetri ve analizinin devam eden sonuçlarına dayanır. Bir kullanıcı veya yönetici kimlik bilgilerini değiştirmeye veya sıfırlamaya çalıştığında, istenen parola yasaklanmış parolalar listesine göre denetlenir. Genel yasaklanmış parola listesinde bir eşleşme varsa parola değiştirme isteği başarısız olur. Bu varsayılan genel yasaklanmış parola listesini düzenleyemezsiniz.

Parolaların hangi parolalara izin verileceğini sağlamak için özel bir yasaklanmış parola listesi de tanımlayabilirsiniz. Özel yasaklanmış parola listesi, kuruluşunuzda güçlü parolalar zorlamak için genel yasaklanmış parola listesi ile birlikte kullanılır. Aşağıdaki örnekler gibi özel yasaklanmış parola listesine kuruluşa özgü terimler eklenebilir:

* Marka adları
* Ürün adları
* Şirket yönetim merkezleri gibi konumlar
* Şirkete özgü dahili şartlar
* Belirli bir şirkete anlamı olan kısaltmalar

Bir Kullanıcı, genel veya özel yasaklanmış parola listesindeki bir parolayı sıfırlama girişiminde bulunduğunda, aşağıdaki hata iletilerinden birini görürler:

* *Ne yazık ki parolanız, parolanızın kolayca tahmin edilebilir olmasını sağlayan bir sözcük, tümcecik veya model içerir. Lütfen farklı bir parola ile yeniden deneyin.*
* *Ne yazık ki, yöneticiniz tarafından engellenmiş sözcükler veya karakterler içerdiğinden bu parolayı kullanamazsınız. Lütfen farklı bir parola ile yeniden deneyin.*

Özel yasaklanmış parola listesi en fazla 1000 terim ile sınırlıdır. Büyük Parolaların listesini engellemek için tasarlanmamıştır. Özel yasaklanmış parola listesinin avantajlarından en üst düzeye çıkarmak için, [özel yasaklanmış parola listesi kavramlarını](concept-password-ban-bad.md#custom-banned-password-list) ve [parola değerlendirme algoritmasına genel bakış ' a](concept-password-ban-bad.md#how-are-passwords-evaluated)bakın.

## <a name="configure-custom-banned-passwords"></a>Özel yasaklanmış parolaları yapılandırma

Özel yasaklanmış parola listesini etkinleştirip bazı girdiler ekleyelim. Özel yasaklanmış parola listesine istediğiniz zaman ek girişler ekleyebilirsiniz.

Özel yasaklanmış parola listesini etkinleştirmek ve buna giriş eklemek için aşağıdaki adımları izleyin:

1. *Genel yönetici* izinlerine sahip bir hesap kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. **Azure Active Directory** bulun ve seçin ve ardından sol taraftaki menüden **güvenlik** ' i seçin.
1. **Yönet** menü üst bilgisi altında **kimlik doğrulama yöntemleri**' ni ve ardından **parola koruması**' nı seçin.
1. **Özel listeyi zorla** seçeneğini *Evet* olarak ayarlayın.
1. **Özel yasaklanmış parola listesine** dizeler ekleyin, her satır için bir dize. Aşağıdaki önemli noktalar ve sınırlamalar özel yasaklanmış parola listesi için geçerlidir:

    * Özel yasaklanmış parola listesi, en fazla 1000 terim içerebilir.
    * Özel yasaklanmış parola listesi, büyük/küçük harfe duyarsızdır.
    * Özel yasaklanmış parola listesi, "o" ve "0" ya da "a" ve "" gibi ortak karakter değişimini dikkate alır \@ .
    * En küçük dize uzunluğu dört karakterdir ve en fazla 16 karakter uzunluğunda olur.

    Aşağıdaki örnekte gösterildiği gibi, Ban için kendi özel parolalarınızı belirtin

    [![Azure Portal ](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png) kimlik doğrulama yöntemleri altında özel yasaklanmış parola listesini değiştirin](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. **Windows Server Active Directory parola korumasını etkinleştir** seçeneğini *Hayır* olarak bırakın.
1. Özel yasaklanmış parolaları ve girdilerinizi etkinleştirmek için **Kaydet**' i seçin.

Özel yasaklanmış parola listesindeki güncelleştirmelerin uygulanması birkaç saat sürebilir.

Karma bir ortamda [Azure AD parola korumasını şirket içi bir ortama da dağıtabilirsiniz](howto-password-ban-bad-on-premises-deploy.md). Hem bulut hem de şirket içi parola değiştirme istekleri için aynı genel ve özel yasaklanmış parola listeleri kullanılır.

## <a name="test-custom-banned-password-list"></a>Özel yasaklanmış parola listesini sına

Özel yasaklanmış parola listesini eylem bölümünde görmek için, parolayı önceki bölümde eklediğiniz bir varyasyon olarak değiştirmeyi deneyin. Azure AD, parola değişikliğini işlemeye çalıştığında, parola, özel yasaklanmış parola listesindeki bir girdiyle eşleştirilir. Daha sonra kullanıcıya bir hata görüntülenir.

> [!NOTE]
> Bir Kullanıcı Web tabanlı portalda parolasını sıfırlayabilmeniz için, Azure AD kiracısının [self servis parola sıfırlama için yapılandırılması](tutorial-enable-sspr.md)gerekir. Gerekirse, Kullanıcı daha sonra [SSPR https://aka.ms/ssprsetup için kayıt ](https://aka.ms/ssprsetup)yapabilir.

1. Konumundaki **uygulamalarım** sayfasına gidin [https://myapps.microsoft.com](https://myapps.microsoft.com) .
1. Sağ üst köşede adınızı seçin, sonra açılır menüden **profil** ' i seçin.

    ![Profil seçme](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. **Profil** sayfasında, **Parolayı Değiştir**' i seçin.
1. **Parolayı Değiştir** sayfasında, mevcut (eski) parolayı girin. Önceki bölümde tanımladığınız özel yasaklanmış parola listesinde yeni bir parola girin ve onaylayın, sonra **Gönder**' i seçin.
1. Aşağıdaki örnekte gösterildiği gibi, parolayı yönetici tarafından engellendiğini bildiren bir hata iletisi döndürülür:

    ![Özel yasaklanmış parola listesinin parçası olan bir parolayı kullanmaya çalıştığınızda görüntülenen hata iletisi](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticinin bir parçası olarak yapılandırdığınız özel yasaklanmış parola listesini artık kullanmak istemiyorsanız, aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Azure Active Directory** bulun ve seçin ve ardından sol taraftaki menüden **güvenlik** ' i seçin.
1. **Yönet** menü üst bilgisi altında **kimlik doğrulama yöntemleri**' ni ve ardından **parola koruması**' nı seçin.
1. **Özel liste Uygula** seçeneğini *Hayır* olarak ayarlayın.
1. Özel yasaklanmış parola yapılandırmasını güncelleştirmek için **Kaydet**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure AD için özel parola koruma listelerini etkinleştirmiş ve yapılandırmış olursunuz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Özel yasaklanmış parolaları etkinleştir
> * Özel yasaklanmış parola listesine giriş ekleme
> * Parola değişikliklerini yasaklanmış parolayla sına

> [!div class="nextstepaction"]
> [Risk tabanlı Azure AD Multi-Factor Authentication'ı etkinleştirme](./tutorial-enable-azure-mfa.md)