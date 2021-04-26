---
title: Azure AD kuruluşu (kiracı) silme-Azure Active Directory | Microsoft Docs
description: Self Servis kuruluşları dahil olmak üzere bir Azure AD organizasyonunun (kiracı) silinme için nasıl hazırlanacağını açıklar.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2edc6fb98359c5360836bc369e5ae1928464df92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861039"
---
# <a name="delete-a-tenant-in-azure-active-directory"></a>Azure Active Directory kiracı silme

Bir Azure AD kuruluşu (kiracı) silindiğinde, kuruluşta bulunan tüm kaynaklar da silinir. Silmeden önce ilişkili kaynaklarını indirerek kuruluşunuzu hazırlayın. Yalnızca bir Azure Active Directory (Azure AD) genel Yöneticisi portaldan bir Azure AD organizasyonunu silebilir.

## <a name="prepare-the-organization"></a>Organizasyonu hazırlama

Birkaç denetim geçirene kadar Azure AD 'de bir kuruluşu silemezsiniz. Bu denetimler, bir Azure AD organizasyonunun silinmesi, Azure 'daki Microsoft 365 oturum açma veya kaynaklara erişme özelliği gibi Kullanıcı erişimini olumsuz şekilde etkiler. Örneğin, bir abonelikle ilişkili kuruluş yanlışlıkla silinirse, kullanıcılar söz konusu aboneliğin Azure kaynaklarına erişemez. Şu koşullar denetlenir:

* Azure AD kuruluşunda (kiracı), kuruluşun silineceği bir genel yönetici haricinde hiçbir Kullanıcı olamaz. Kuruluşun silinebilmesi için önce diğer tüm kullanıcıların silinmesi gerekir. Kullanıcılar Şirket içinden eşitlendiğinde, eşitlemenin önce kapatılması ve kullanıcıların Azure portal veya Azure PowerShell cmdlet 'leri kullanılarak bulut kuruluşunda silinmesi gerekir.
* Kuruluşta hiç uygulama yok. Kuruluşun silinebilmesi için önce herhangi bir uygulamanın kaldırılması gerekir.
* Kuruluşa bağlı çok faktörlü kimlik doğrulama sağlayıcısı yok.
* Kuruluşla ilişkili Microsoft Azure, Microsoft 365 veya Azure AD Premium gibi herhangi bir Microsoft Online hizmeti için abonelik olamaz. Örneğin, Azure 'da sizin için varsayılan bir Azure AD kuruluşu oluşturulduysa, Azure aboneliğiniz kimlik doğrulaması için bu kuruluşa hala dayandıysa bu kuruluşu silemezsiniz. Benzer şekilde, başka bir Kullanıcı abonelikle ilişkili olduğunda bir kuruluşu silemezsiniz.

## <a name="delete-the-organization"></a>Organizasyonu silme

1. Kuruluşunuzun genel yöneticisi olan bir hesapla [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın.

2. **Azure Active Directory** seçin.

3. Silmek istediğiniz kuruluşa geçiş yapın.
  
   ![Silmeden önce kuruluşu Onayla](./media/directory-delete-howto/delete-directory-command.png)

4. **Kiracıyı Sil**' i seçin.
  
   ![organizasyonu silmek için komutu seçin](./media/directory-delete-howto/delete-directory-list.png)

5. Kuruluşunuz bir veya daha fazla denetim geçirmezse, nasıl geçiyoruz hakkında daha fazla bilgi için bir bağlantı sunulur. Tüm denetimleri geçirdikten sonra, işlemi gerçekleştirmek için **Sil** ' i seçin.

## <a name="if-you-cant-delete-the-organization"></a>Organizasyonu silemiyorum

Azure AD kuruluşunuzu yapılandırdığınızda, kuruluşunuz için Azure AD Premium P2, Microsoft 365 İş standart veya Enterprise Mobility + Security E5 gibi lisans tabanlı abonelikleri de etkinleştirmiş olabilirsiniz. Yanlışlıkla veri kaybını önlemek için, abonelikler tamamen silinene kadar bir kuruluşu silemezsiniz. Aboneliklerin, kuruluşun silinmesine izin vermek için, **sağlaması kaldırılmış** durumda olmalıdır. Bir **zaman aşımına uğradı** veya **iptal edildi** bir abonelik **devre dışı** durumuna geçer ve son aşama **sağlanmakta** olan durumdur.

Deneme Microsoft 365 aboneliğinin süresi dolarsa (ücretli Iş ortağı/CSP, Kurumsal Anlaşma veya toplu lisanslama dahil değil) bekleneceği için aşağıdaki tabloya bakın. Microsoft 365 veri saklama ve abonelik yaşam döngüsü hakkında daha fazla bilgi için bkz. My [Data ve Access for Business aboneliğMicrosoft 365 im sona erdiğinde ne olur?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Abonelik durumu | Veriler | Veri erişimi
----- | ----- | -----
Etkin (deneme için 30 gün) | Tüm veriler erişilebilir | Kullanıcıların Microsoft 365 dosyalara veya uygulamalarına normal erişimi vardır<br>Yöneticilerin Microsoft 365 yönetim merkezine ve kaynaklarına normal erişimi vardır 
Zaman aşımına uğradı (30 gün) | Tüm veriler erişilebilir| Kullanıcıların Microsoft 365 dosyalara veya uygulamalarına normal erişimi vardır<br>Yöneticilerin Microsoft 365 yönetim merkezine ve kaynaklarına normal erişimi vardır
Devre dışı (30 gün) | Yalnızca yönetici tarafından erişilebilen veriler | Kullanıcılar Microsoft 365 dosyalara veya uygulamalara erişemez<br>Yöneticiler Microsoft 365 yönetim merkezine erişebilir, ancak kullanıcılara lisans atayamaz veya kullanıcıları güncelleştirebilir
Sağlaması kaldırıldı (devre dışı bırakılsa 30 gün) | Silinen veriler (başka bir hizmet kullanımda değilse otomatik olarak silinir) | Kullanıcılar Microsoft 365 dosyalara veya uygulamalara erişemez<br>Yöneticiler Microsoft 365 yönetim merkezine erişerek diğer abonelikleri satın alabilir ve yönetebilir

## <a name="delete-a-subscription"></a>Abonelik silme

Microsoft 365 Yönetim merkezini kullanarak, üç gün içinde silinmek üzere, **sağlaması kaldırılmış** duruma bir abonelik koyabilirsiniz.

1. [Microsoft 365 Yönetim merkezinde](https://admin.microsoft.com) kuruluşunuzda genel yönetici olan bir hesapla oturum açın. İlk varsayılan etki alanı contoso.onmicrosoft.com olan "contoso" organizasyonunu silmeye çalışıyorsanız, gibi bir UPN ile oturum açın admin@contoso.onmicrosoft.com .

2. Yeni **yönetici merkezini dene** özelliğinin etkinleştirildiğinden emin olarak yeni Microsoft 365 Yönetim merkezini önizleyin.

   ![Yeni M365 Yönetim Merkezi deneyimini önizleyin](./media/directory-delete-howto/preview-toggle.png)

3. Yeni Yönetim Merkezi etkinleştirildikten sonra, silmeden önce bir aboneliği iptal etmeniz gerekir. **Faturalandırma** ' i seçin ve **Ürünler & Hizmetleri**' ni seçin ve ardından iptal etmek Istediğiniz aboneliğin **aboneliğini iptal et** ' i seçin. Geri bildirim sayfasına yönlendirilirsiniz.

   ![İptal etmek için bir abonelik seçin](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Geri bildirim formunu doldurun ve aboneliği iptal etmek için **aboneliği Iptal et** ' i seçin.

   ![Abonelik önizlemede iptal komutu](./media/directory-delete-howto/cancel-command.png)

5. Artık aboneliği silebilirsiniz. Silmek istediğiniz abonelik için **Sil** ' i seçin. **Ürünleri & Hizmetleri** sayfasında bulamazsanız, **abonelik durumunun** **Tümü** olarak ayarlanmış olduğundan emin olun.

   ![Aboneliği silmek için bağlantıyı Sil](./media/directory-delete-howto/delete-command.png)

6. Aboneliği silmek için **aboneliği Sil** ' i seçin ve hüküm ve koşulları kabul edin. Tüm veriler, üç gün içinde kalıcı olarak silinir. Fikrinizi değiştirirseniz, üç günlük dönemde [aboneliği yeniden etkinleştirebilirsiniz](/office365/admin/subscriptions-and-billing/reactivate-your-subscription) .
  
   ![hüküm ve koşulları dikkatle okuyun](./media/directory-delete-howto/delete-terms.png)

7. Artık abonelik durumu değişti ve abonelik silinmek üzere işaretlendi. Abonelik, **sağlaması kaldırılmış** durum 72 saat daha sonra girer.

8. Kuruluşunuzda bir aboneliği sildikten ve 72 saat geçtikten sonra, Azure AD Yönetim merkezine yeniden oturum açabilir ve gerekli bir eylem olmaması ve kuruluşunuzun silinmesini engelleyen bir abonelik olmaması gerekir. Azure AD kuruluşunuzu başarıyla silebilmelisiniz.
  
   ![silme ekranında abonelik denetimini geçir](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Silmeyi engelleyen bir deneme aboneliğine sahibim

Microsoft Power BI, Rights Management Hizmetleri, Microsoft Power Apps veya Dynamics 365 gibi [self servis kaydolma ürünleri](/office365/admin/misc/self-service-sign-up) vardır. bireysel kullanıcılar, Azure AD kuruluşunuzda kimlik doğrulaması için de Konuk kullanıcı oluşturan Microsoft 365 aracılığıyla kaydolabilir. Bu self servis ürünleri, ürünler kuruluştan tamamen silinene kadar, veri kaybını önlemek için Dizin silmelerini engeller. Yalnızca Azure AD yöneticisi tarafından, kullanıcının tek tek kaydolup kaydolmadığını veya ürünün atandığını de silinebilirler.

Nasıl atandıklarından bağımsız olarak iki tür self servis kaydolma ürünü vardır: 

* Kuruluş düzeyinde atama: bir Azure AD yöneticisi, ürünü kuruluşun tamamına atar ve bir Kullanıcı, bireysel olarak lisanslansalar bile bu kuruluştan yararlanarak hizmeti bu kuruluş düzeyi atama ile etkin bir şekilde kullanabilir.
* Kullanıcı düzeyi atama: Self Servis kaydolma sırasında bireysel bir Kullanıcı, ürünü yönetici olmadan kendilerine atar. Kuruluş, bir yönetici tarafından yönetildikten sonra (bkz. [yönetilmeyen bir kuruluşun yöneticisini](domains-admin-takeover.md)inceleyin), yönetici, ürünü self servis kaydolma olmadan doğrudan kullanıcılara atayabilir.  

Self Servis kaydolma ürününün silinmesine başladığınızda, eylem verileri kalıcı olarak siler ve hizmete tüm Kullanıcı erişimini kaldırır. Teklifin tek tek veya kuruluş düzeyinde atandığı herhangi bir kullanıcının, oturum açması veya mevcut verilere erişmesi engellenir. [Microsoft Power BI panoları](/power-bi/service-export-to-pbix) veya [Rights Management Services ilke yapılandırması](/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)gibi self servis kaydolma ürünüyle veri kaybını engellemek isterseniz, verilerin yedeklenip başka bir yerde kaydedildiğinden emin olun.

Mevcut olan Self Servis kaydolma ürünleri ve hizmetleri hakkında daha fazla bilgi için bkz. [kullanılabilir self servis programları](/office365/admin/misc/self-service-sign-up#available-self-service-programs).

Deneme Microsoft 365 aboneliğinin süresi dolarsa (ücretli Iş ortağı/CSP, Kurumsal Anlaşma veya toplu lisanslama dahil değil) bekleneceği için aşağıdaki tabloya bakın. Microsoft 365 veri saklama ve abonelik yaşam döngüsü hakkında daha fazla bilgi için bkz. My [Data ve Access for Business aboneliğMicrosoft 365 im sona erdiğinde ne olur?](/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires).

Ürün durumu | Veriler | Veri erişimi
------------- | ---- | --------------
Etkin (deneme için 30 gün) | Tüm veriler erişilebilir | Kullanıcıların Self Servis kaydolma ürününe, dosyalarına veya uygulamalarına normal erişimi vardır<br>Yöneticilerin Microsoft 365 yönetim merkezine ve kaynaklarına normal erişimi vardır
Silindi | Silinen veriler | Kullanıcılar self servis kaydolma ürününe, dosyalarına veya uygulamalarına erişemez<br>Yöneticiler Microsoft 365 yönetim merkezine erişerek diğer abonelikleri satın alabilir ve yönetebilir

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Azure portal bir self servis kaydolma ürününü nasıl silebilirim?

Azure AD portalında hemen silinmek üzere Microsoft Power BI veya Azure Rights Management hizmetleri gibi bir self servis kaydolma ürününü bir **silme** durumuna getirebilirsiniz.

1. Kuruluşunuzda genel yönetici olan bir hesapla [Azure AD Yönetim merkezinde](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) oturum açın. İlk varsayılan etki alanı contoso.onmicrosoft.com olan "contoso" organizasyonunu silmeye çalışıyorsanız, gibi bir UPN ile oturum açın admin@contoso.onmicrosoft.com .

2. **Lisanslar**' ı seçin ve ardından **self servis kaydolma ürünleri**' ni seçin. Tüm self servis kaydolma ürünlerini, bilgisayar tabanlı aboneliklerden ayrı olarak görebilirsiniz. Kalıcı olarak silmek istediğiniz ürünü seçin. Microsoft Power BI 'de bir örnek aşağıda verilmiştir:

    !["Lisanslar-self servis kaydolma ürünleri" sayfasını gösteren ekran görüntüsü.](./media/directory-delete-howto/licenses-page.png)

3. Ürünü silmek için **Sil** ' i seçin ve verilerin hemen silindiği ve geri alınamayacak koşulları kabul edin. Bu silme eylemi, tüm kullanıcıları kaldırır ve ürüne kuruluşun erişimini kaldırır. Silme işlemiyle ilerlemek için Evet ' e tıklayın.  

    !["Self servis kaydolma ürününü silme" penceresi açık olan "lisanslar-self servis kaydolma ürünleri" sayfasını gösteren ekran görüntüsü.](./media/directory-delete-howto/delete-product.png)

4. **Evet**' i seçtiğinizde self servis ürününün silinmesi başlatılır. Devam eden silme işlemini size söyleyen bir bildirim vardır.  

    !["Devam eden silme" bildiriminin gösterildiği "lisanslar-self servis kaydolma ürünleri" sayfasını gösteren ekran görüntüsü.](./media/directory-delete-howto/progress-message.png)

5. Şimdi self servis kaydolma ürün durumu **silindi** olarak değiştirilmiştir. Sayfayı yenilediğinizde, ürün **self servis kaydolma ürünleri** sayfasından kaldırılmalıdır.  

    ![Sağ taraftaki "self servis kaydolma ürünü silindi" bölmesine sahip "lisanslar-self servis kaydolma ürünleri" sayfasını gösteren ekran görüntüsü.](./media/directory-delete-howto/product-deleted.png)

6. Tüm ürünleri sildikten sonra Azure AD Yönetim merkezine yeniden oturum açabilir ve gerekli bir eylem olmaması ve kuruluşunuzun silinmesini engelleyen bir ürün olmaması gerekir. Azure AD kuruluşunuzu başarıyla silebilmelisiniz.

    ![Kullanıcı adı yanlış yazılmış veya bulunamadı](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Sonraki adımlar

[Azure Active Directory belgeleri](../index.yml)
