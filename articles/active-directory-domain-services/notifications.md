---
title: Azure AD Domain Services için e-posta bildirimleri | Microsoft Docs '
description: Azure Active Directory Domain Services yönetilen bir etki alanındaki sorunlar hakkında sizi uyarmak için e-posta bildirimlerini nasıl yapılandıracağınızı öğrenin
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 09/12/2019
ms.author: iainfou
ms.openlocfilehash: 8261723f145c7b8ba01e27108e7a309f9f483d2c
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993145"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services sorunları için e-posta bildirimlerini yapılandırın

Azure Active Directory Domain Services (Azure AD DS) yönetilen etki alanının sistem durumu, Azure platformu tarafından izlenir. Azure portal sistem durumu sayfasında, yönetilen etki alanı için tüm uyarılar gösterilir. Sorunların zamanında yanıt verdiğinden emin olmak için e-posta bildirimleri, Azure AD DS yönetilen etki alanında algılanan her zamanki gibi sistem durumu uyarılarını raporlamak üzere yapılandırılabilir.

Bu makalede, Azure AD DS yönetilen bir etki alanı için e-posta bildirim alıcılarını nasıl yapılandıracağınız gösterilir.

## <a name="email-notification-overview"></a>E-posta bildirimine genel bakış

Azure AD DS yönetilen bir etki alanıyla ilgili sorunları uyarmak için e-posta bildirimleri yapılandırabilirsiniz. Bu e-posta bildirimleri, uyarının bulunduğu Azure AD DS yönetilen etki alanını belirtir ve ayrıca, algılama zamanını ve Azure portal sistem durumu sayfasına bir bağlantı verir. Daha sonra sorunları çözmek için, belirtilen sorun giderme önerisi ' ni izleyebilirsiniz.

Aşağıdaki örnek e-posta bildirimi, Azure AD DS yönetilen etki alanında kritik bir uyarı veya uyarının oluşturulduğunu gösterir:

![Örnek e-posta bildirimi](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> İletideki bağlantılara tıklamadan önce her zaman e-postanın doğrulanmış bir Microsoft göndericiden geldiğinden emin olun. E-posta bildirimleri her zaman `azure-noreply@microsoft.com` adresten gelir.

### <a name="why-would-i-receive-email-notifications"></a>Neden e-posta bildirimleri alıyorum?

Azure AD DS, yönetilen etki alanı hakkında önemli güncelleştirmeler için e-posta bildirimleri gönderir. Bu bildirimler yalnızca hizmeti etkileyen acil sorunlar içindir ve hemen ele alınmalıdır. Her e-posta bildirimi, Azure AD DS yönetilen etki alanındaki bir uyarı tarafından tetiklenir. Uyarılar Azure portal de görüntülenir ve [Azure AD DS sistem durumu sayfasında][check-health]görüntülenebilir.

Azure AD DS tanıtım, güncelleştirme veya satış amaçları için e-posta göndermez.

### <a name="when-will-i-receive-email-notifications"></a>Ne zaman e-posta bildirimleri alabilirim?

Azure AD DS yönetilen bir etki alanında [Yeni bir uyarı][troubleshoot-alerts] bulunduğunda anında bildirim gönderilir. Uyarı çözümlenmemişse, her dört günde bir anımsatıcı olarak ek e-posta bildirimleri gönderilir.

### <a name="who-should-receive-the-email-notifications"></a>E-posta bildirimlerini kimler alacak?

Azure AD DS e-posta alıcılarının listesi, yönetilen etki alanında yönetebilen ve değişiklik yapabilecek kişilerden oluşmalıdır. Bu e-posta listesi, herhangi bir uyarı ve sorun için "ilk Yanıtlayıcılar" olarak düşünülmelidir.

E-posta bildirimleri için beş adede kadar ek e-posta alıcısı ekleyebilirsiniz. E-posta bildirimleri için Beş taneden fazla alıcı istiyorsanız, bir dağıtım listesi oluşturun ve bunu bunun yerine bildirim listesine ekleyin.

Ayrıca, Azure AD dizini 'nin tüm *genel yöneticilerinin* ve *AAD DC yöneticileri* grubunun her üyesinin e-posta bildirimleri almasını seçebilirsiniz. Azure AD DS, genel yöneticilerin ve AAD DC yöneticilerinin listesi dahil olmak üzere yalnızca 100 e-posta adresine bildirim gönderir.

## <a name="configure-email-notifications"></a>E-posta bildirimlerini yapılandırma

Var olan e-posta bildirim alıcılarını gözden geçirmek veya ek alıcılar eklemek için aşağıdaki adımları izleyin:

1. Azure portal, araması yapın ve **Azure AD Domain Services**seçin.
1. *Contoso.com*gibi Azure AD DS yönetilen etki alanınızı seçin.
1. Azure AD DS kaynak penceresinin sol tarafında **bildirim ayarları**' nı seçin. E-posta bildirimleri için mevcut alıcılar gösterilir.
1. E-posta alıcısı eklemek için, ek alıcılar tablosuna e-posta adresini girin.
1. İşiniz bittiğinde, üstteki gezinmede **Kaydet** ' i seçin.

> [!WARNING]
> Bildirim ayarlarını değiştirdiğinizde, tüm Azure AD DS yönetilen etki alanı için bildirim ayarları yalnızca kendi başınıza değil güncelleştirilir.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Uyarı için bir e-posta bildirimi aldım, ancak Azure portal oturum açtıklarında hiçbir uyarı yoktu. Ne oldu?

Bir uyarı çözümlenirse, uyarı Azure portal temizlenir. En olası neden, e-posta bildirimleri alan başka birisinin Azure AD DS yönetilen etki alanındaki uyarıyı çözdüğü veya Azure platformu tarafından yeniden çözümlenmesidir.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Bildirim ayarlarını neden düzenleyemiyorum?

Azure portal bildirim ayarları sayfasına erişemezseniz Azure AD DS yönetilen etki alanını düzenleme izniniz yoktur. Azure AD DS kaynağını düzenleme veya alıcı listesinden kaldırılma izinleri almak için genel bir yöneticiye başvurmanız gerekir.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>E-posta adresimi sağladığım halde e-posta bildirimleri almıyorum. Neden?

Bildirim için e-postalarınızın istenmeyen posta veya istenmeyen klasörünüzü denetleyin ve göndericiye `azure-noreply@microsoft.com`izin vermeyi unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

Raporlanacak bazı sorunları giderme hakkında daha fazla bilgi için bkz. [Azure AD DS yönetilen etki alanındaki uyarıları çözümleme][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
