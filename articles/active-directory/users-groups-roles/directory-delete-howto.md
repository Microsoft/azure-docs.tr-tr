---
title: Azure AD dizinini silme-Azure Active Directory | Microsoft Docs
description: Self Servis dizinleri dahil olmak üzere bir Azure AD dizininin silinme için nasıl hazırlanacağını açıklar
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a60ed44ddf057ef983f8f76f23fd784bc3efd5
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961825"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Azure Active Directory bir dizini silme

Bir Azure AD dizini silindiğinde, dizinde bulunan tüm kaynaklar da silinir. Silmeden önce ilişkili kaynaklarını indirerek kuruluşunuzu hazırlayın. Yalnızca bir Azure Active Directory (Azure AD) genel Yöneticisi portaldan bir Azure AD dizinini silebilir.

## <a name="prepare-the-directory"></a>Dizini hazırlama

Azure AD 'de birkaç denetim geçirene kadar bir dizini silemezsiniz. Bu denetimler, bir Azure AD dizinini silmenin, Office 365 ' de oturum açma veya Azure 'daki kaynaklara erişme gibi Kullanıcı erişimini olumsuz yönde etkilediği riskini azaltır. Örneğin, bir abonelikle ilişkilendirilen Dizin istenmeden silinirse, kullanıcılar söz konusu aboneliğin Azure kaynaklarına erişemez. Şu koşullar denetlenir:

* Dizinde silinecek bir genel yönetici dışında, dizinde hiç Kullanıcı olamaz. Dizin silinmeden önce diğer tüm kullanıcılar silinmelidir. Kullanıcılar Şirket içinden eşitlendiğinde, eşitlemenin önce kapatılması ve kullanıcıların Azure portal veya Azure PowerShell cmdlet 'leri kullanılarak bulut dizininde silinmesi gerekir.
* Dizinde uygulama bulunamaz. Dizinin silinebilmesi için önce herhangi bir uygulamanın kaldırılması gerekir.
* Dizine bağlı çok faktörlü kimlik doğrulama sağlayıcısı yok.
* Microsoft Çevrimiçi Hizmetlerine (dizinle ilişkili Azure AD Premium, Microsoft Azure veya Office 365 gibi) ilişkin hiçbir aboneliğin bulunmaması gerekir. Örneğin, sizin için Azure'da varsayılan bir dizin oluşturulduysa ve Azure aboneliğinizin kimlik doğrulaması için hâlâ bu dizini kullanıyor olması halinde bu dizini silemezsiniz. Benzer şekilde, başka bir kullanıcı dizinle bir aboneliği ilişkilendirdiyse o dizini silemezsiniz.

## <a name="delete-the-directory"></a>Dizini Sil

1. Kuruluşunuzun genel yöneticisi olan bir hesapla [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın.

2. **Azure Active Directory**'yi seçin.

3. Silmek istediğiniz dizine geçiş yapın.
  
   ![Silmeden önce kuruluşu Onayla](./media/directory-delete-howto/delete-directory-command.png)

4. **Dizini Sil**' i seçin.
  
   ![organizasyonu silmek için komutu seçin](./media/directory-delete-howto/delete-directory-list.png)

5. Dizininiz bir veya daha fazla denetim geçirmezse, nasıl geçiyoruz hakkında daha fazla bilgi için bir bağlantı sunulur. Tüm denetimleri geçirdikten sonra, işlemi gerçekleştirmek için **Sil** ' i seçin.

## <a name="if-you-cant-delete-the-directory"></a>Dizini silemiyorum

Azure AD dizininizi yapılandırdığınızda, kuruluşunuz için Azure AD Premium P2, Office 365 Business Premium veya Enterprise Mobility + Security E5 gibi lisans tabanlı abonelikleri de etkinleştirmiş olabilirsiniz. Yanlışlıkla veri kaybını önlemek için, abonelikler tamamen silinene kadar bir dizini silemezsiniz. Abonelik, Dizin silmeye izin vermek için **sağlanması kaldırılmış** bir durumda olmalıdır. Bir **zaman aşımına uğradı** veya **iptal edildi** bir abonelik **devre dışı** durumuna geçer ve son aşama **sağlanmakta** olan durumdur.

Deneme sürümü Office 365 aboneliğinin süresi dolarsa (ücretli Iş ortağı/CSP, Kurumsal Anlaşma veya toplu lisanslama dahil değildir), aşağıdaki tabloya bakın. Office 365 veri saklama ve abonelik yaşam döngüsü hakkında daha fazla bilgi için bkz. [office 365 for Business aboneliğim sona erdiğinde verilere ne olur?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3) 

Abonelik durumu | Veriler | Verilere erişim
----- | ----- | -----
Etkin (deneme için 30 gün) | Tüm veriler erişilebilir | Kullanıcıların Office 365 dosyalarına veya uygulamalarına normal erişimi vardır<br>Yöneticilerin Microsoft 365 yönetim merkezine ve kaynaklarına normal erişimi vardır 
Zaman aşımına uğradı (30 gün) | Tüm veriler erişilebilir| Kullanıcıların Office 365 dosyalarına veya uygulamalarına normal erişimi vardır<br>Yöneticilerin Microsoft 365 yönetim merkezine ve kaynaklarına normal erişimi vardır
Devre dışı (30 gün) | Yalnızca yönetici tarafından erişilebilen veriler | Kullanıcılar Office 365 dosyalarına veya uygulamalarına erişemez<br>Yöneticiler Microsoft 365 yönetim merkezine erişebilir, ancak kullanıcılara lisans atayamaz veya kullanıcıları güncelleştirebilir
Sağlaması kaldırıldı (devre dışı bırakılsa 30 gün) | Silinen veriler (başka bir hizmet kullanımda değilse otomatik olarak silinir) | Kullanıcılar Office 365 dosyalarına veya uygulamalarına erişemez<br>Yöneticiler Microsoft 365 yönetim merkezine erişerek diğer abonelikleri satın alabilir ve yönetebilir

## <a name="delete-a-subscription"></a>Abonelik silme

Microsoft 365 Yönetim merkezini kullanarak, üç gün içinde silinmek üzere, **sağlaması kaldırılmış** duruma bir abonelik koyabilirsiniz.

1. [Microsoft 365 Yönetim merkezinde](https://admin.microsoft.com) kuruluşunuzda genel yönetici olan bir hesapla oturum açın. İlk varsayılan etki alanı contoso.onmicrosoft.com olan "contoso" dizinini silmeye çalışıyorsanız, admin@contoso.onmicrosoft.comgibi bir UPN ile oturum açın.

2. Yeni **yönetici merkezini dene** özelliğinin etkinleştirildiğinden emin olarak yeni Microsoft 365 Yönetim merkezini önizleyin.

   ![Yeni M365 Yönetim Merkezi deneyimini önizleyin](./media/directory-delete-howto/preview-toggle.png)

3. Yeni Yönetim Merkezi etkinleştirildikten sonra, silmeden önce bir aboneliği iptal etmeniz gerekir. **Faturalandırma** ' i seçin ve **Ürünler & Hizmetleri**' ni seçin ve ardından iptal etmek Istediğiniz aboneliğin **aboneliğini iptal et** ' i seçin. Geri bildirim sayfasına yönlendirilirsiniz.

   ![İptal etmek için bir abonelik seçin](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Geri bildirim formunu doldurun ve aboneliği iptal etmek için **aboneliği Iptal et** ' i seçin.

   ![Abonelik önizlemede iptal komutu](./media/directory-delete-howto/cancel-command.png)

5. Artık aboneliği silebilirsiniz. Silmek istediğiniz abonelik için **Sil** ' i seçin. **Ürünleri & Hizmetleri** sayfasında bulamazsanız, **abonelik durumunun** **Tümü**olarak ayarlanmış olduğundan emin olun.

   ![Aboneliği silmek için bağlantıyı Sil](./media/directory-delete-howto/delete-command.png)

6. Aboneliği silmek için **aboneliği Sil** ' i seçin ve hüküm ve koşulları kabul edin. Tüm veriler, üç gün içinde kalıcı olarak silinir. Fikrinizi değiştirirseniz, üç günlük dönemde [aboneliği yeniden etkinleştirebilirsiniz](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) .
  
   ![hüküm ve koşulları dikkatle okuyun](./media/directory-delete-howto/delete-terms.png)

7. Artık abonelik durumu değişti ve abonelik silinmek üzere işaretlendi. Abonelik, **sağlaması kaldırılmış** durum 72 saat daha sonra girer.

8. Dizininizde bir aboneliği sildikten ve 72 saat geçtikten sonra, Azure AD Yönetim merkezine yeniden oturum açabilir ve gerekli bir eylem olmaması ve Dizin silinmesini engelleyen bir abonelik olmaması gerekir. Azure AD dizininizi başarıyla silebilmelisiniz.
  
   ![silme ekranında abonelik denetimini geçir](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Silmeyi engelleyen bir deneme aboneliğine sahibim

Microsoft Power BI, Rights Management Hizmetleri, Microsoft Power Apps veya Dynamics 365 gibi [self servis kaydolma ürünleri](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) vardır, bireysel kullanıcılar Office 365 aracılığıyla kaydolabilir ve bu da Azure AD dizininizde kimlik doğrulaması için bir Konuk kullanıcı oluşturur. Bu self servis ürünleri, veri kaybını önlemek için Dizin silmeleri, dizinden tamamen silinene kadar engeller. Yalnızca Azure AD yöneticisi tarafından, kullanıcının tek tek kaydolup kaydolmadığını veya ürünün atandığını de silinebilirler.

Nasıl atandıklarından bağımsız olarak iki tür self servis kaydolma ürünü vardır: 

* Kuruluş düzeyinde atama: bir Azure AD yöneticisi, ürünü kuruluşun tamamına atar ve bir Kullanıcı, bireysel olarak lisanslansalar bile bu kuruluştan yararlanarak hizmeti bu kuruluş düzeyi atama ile etkin bir şekilde kullanabilir.
* Kullanıcı düzeyi atama: Self Servis kaydolma sırasında bireysel bir Kullanıcı, ürünü yönetici olmadan kendilerine atar. Kuruluş, bir yönetici tarafından yönetildikten sonra (bkz. [yönetilmeyen bir dizinin yöneticisini](domains-admin-takeover.md)inceleyin), yönetici, ürünü self servis kaydolma olmadan doğrudan kullanıcılara atayabilir.  

Self Servis kaydolma ürününün silinmesine başladığınızda, eylem verileri kalıcı olarak siler ve hizmete tüm Kullanıcı erişimini kaldırır. Teklifin tek tek veya kuruluş düzeyinde atandığı herhangi bir kullanıcının, oturum açması veya mevcut verilere erişmesi engellenir. [Microsoft Power BI panoları](https://docs.microsoft.com/power-bi/service-export-to-pbix) veya [Rights Management Services ilke yapılandırması](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)gibi self servis kaydolma ürünüyle veri kaybını engellemek isterseniz, verilerin yedeklenip başka bir yerde kaydedildiğinden emin olun.

Mevcut olan Self Servis kaydolma ürünleri ve hizmetleri hakkında daha fazla bilgi için bkz. [kullanılabilir self servis programları](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Deneme sürümü Office 365 aboneliğinin süresi dolarsa (ücretli Iş ortağı/CSP, Kurumsal Anlaşma veya toplu lisanslama dahil değildir), aşağıdaki tabloya bakın. Office 365 veri saklama ve abonelik yaşam döngüsü hakkında daha fazla bilgi için bkz. [office 365 for Business aboneliğim sona erdiğinde verilere ne olur?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)

Ürün durumu | Veriler | Verilere erişim
------------- | ---- | --------------
Etkin (deneme için 30 gün) | Tüm veriler erişilebilir | Kullanıcıların Self Servis kaydolma ürününe, dosyalarına veya uygulamalarına normal erişimi vardır<br>Yöneticilerin Microsoft 365 yönetim merkezine ve kaynaklarına normal erişimi vardır
Silme | Silinen veriler | Kullanıcılar self servis kaydolma ürününe, dosyalarına veya uygulamalarına erişemez<br>Yöneticiler Microsoft 365 yönetim merkezine erişerek diğer abonelikleri satın alabilir ve yönetebilir

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Azure portal bir self servis kaydolma ürününü nasıl silebilirim?

Azure AD portalında hemen silinmek üzere Microsoft Power BI veya Azure Rights Management hizmetleri gibi bir self servis kaydolma ürününü bir **silme** durumuna getirebilirsiniz.

1. Kuruluşunuzda genel yönetici olan bir hesapla [Azure AD Yönetim merkezinde](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) oturum açın. İlk varsayılan etki alanı contoso.onmicrosoft.com olan "contoso" dizinini silmeye çalışıyorsanız, admin@contoso.onmicrosoft.comgibi bir UPN ile oturum açın.

2. **Lisanslar**' ı seçin ve ardından **self servis kaydolma ürünleri**' ni seçin. Tüm self servis kaydolma ürünlerini, bilgisayar tabanlı aboneliklerden ayrı olarak görebilirsiniz. Kalıcı olarak silmek istediğiniz ürünü seçin. Microsoft Power BI 'de bir örnek aşağıda verilmiştir:

    ![Kullanıcı adı yanlış yazılmış veya bulunamadı](./media/directory-delete-howto/licenses-page.png)

3. Ürünü silmek için **Sil** ' i seçin ve verilerin hemen silindiği ve geri alınamayacak koşulları kabul edin. Bu silme eylemi, tüm kullanıcıları kaldırır ve ürüne kuruluşun erişimini kaldırır. Silme işlemiyle ilerlemek için Evet ' e tıklayın.  

    ![Kullanıcı adı yanlış yazılmış veya bulunamadı](./media/directory-delete-howto/delete-product.png)

4. **Evet**' i seçtiğinizde self servis ürününün silinmesi başlatılır. Devam eden silme işlemini size söyleyen bir bildirim vardır.  

    ![Kullanıcı adı yanlış yazılmış veya bulunamadı](./media/directory-delete-howto/progress-message.png)

5. Şimdi self servis kaydolma ürün durumu **silindi**olarak değiştirilmiştir. Sayfayı yenilediğinizde, ürün **self servis kaydolma ürünleri** sayfasından kaldırılmalıdır.  

    ![Kullanıcı adı yanlış yazılmış veya bulunamadı](./media/directory-delete-howto/product-deleted.png)

6. Tüm ürünleri sildikten sonra Azure AD Yönetim merkezine yeniden oturum açabilirsiniz ve gerekli bir eylem olmaması ve Dizin silinmesini engelleyen bir ürün olmaması gerekir. Azure AD dizininizi başarıyla silebilmelisiniz.

    ![Kullanıcı adı yanlış yazılmış veya bulunamadı](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Sonraki adımlar

[Azure Active Directory belgeleri](https://docs.microsoft.com/azure/active-directory/)
