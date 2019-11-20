---
title: Azure AD rollerimi PıM-Azure Active Directory 'de etkinleştir | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içinde Azure AD rollerini nasıl etkinleştireceğinizi öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6007762c897337170dec69c3486302aa62723480
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756299"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Azure AD rollerimi PıM 'de etkinleştir

Azure Active Directory (Azure AD) Privileged Identity Management (PıM), kuruluşların Azure AD 'deki kaynaklara yönelik ayrıcalıklı erişimi ve Office 365 veya Microsoft Intune gibi diğer Microsoft çevrimiçi hizmetler yönetmesini basitleştirir.  

Bir yönetim rolü için uygun yaptıysanız, ayrıcalıklı eylemleri gerçekleştirmeniz gerektiğinde bu rolü etkinleştirebilmeniz anlamına gelir. Örneğin, bazen Office 365 özelliklerini yönetiyorsanız, bu rol diğer hizmetleri de etkilediği için kuruluşunuzun ayrıcalıklı rol yöneticileri kalıcı bir genel yönetici sunmayabilir. Bunun yerine, Exchange Online Yöneticisi gibi Azure AD rollerine uygun hale getirir. Ayrıcalıklarına ihtiyacınız olduğunda bu rolü etkinleştirmek isteyebilirsiniz ve daha sonra önceden belirlenmiş bir süre için yönetici denetimine sahip olursunuz.

Bu makale, Privileged Identity Management ' de Azure AD rolünü etkinleştirmesi gereken yöneticilere yöneliktir.

## <a name="activate-a-role"></a>Rol etkinleştirme

Bir Azure AD rolünü gerçekleştirmeniz gerektiğinde, Privileged Identity Management ' de **rollerim** gezinti seçeneğini kullanarak etkinleştirme isteğinde bulunabilir.

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.

1. **Azure AD Privileged Identity Management**açın. Privileged Identity Management kutucuğunu panonuza ekleme hakkında daha fazla bilgi için bkz. [Privileged Identity Management kullanmaya başlama](pim-getting-started.md).

1. **Azure AD rolleri**' ne tıklayın.

1. Uygun Azure AD rollerinizin listesini görmek için **rollerim** ' e tıklayın.

    ![Azure AD rolleri-uygun veya etkin roller listesini gösteren rollerim](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Etkinleştirmek istediğiniz bir rol bulun.

    ![Azure AD rolleri-Etkinleştir bağlantısını gösteren uygun rollerim listesi](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Rol etkinleştirme ayrıntıları bölmesini açmak için **Etkinleştir** ' e tıklayın.

1. Rolünüz Multi-Factor Authentication (MFA) gerektiriyorsa, **devam etmeden önce kimliğinizi doğrula**' ya tıklayın. Her oturum için yalnızca bir kez kimlik doğrulaması yapmanız gerekir.

    ![Rol etkinleştirmeden önce MFA ile kimlik bölmi doğrula](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. **Kimliğimi doğrula** ' ya tıklayın ve ek güvenlik doğrulaması sağlamak için yönergeleri izleyin.

    ![Sizinle nasıl iletişim kurasoran ek güvenlik doğrulama sayfası](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Etkinleştirme bölmesini açmak için **Etkinleştir** ' e tıklayın.

    ![Başlangıç zamanı, süre, Bilet ve neden belirtmek için etkinleştirme bölmesi](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Gerekirse, özel bir etkinleştirme başlangıç saati belirtin.

1. Etkinleştirme süresini belirtin.

1. **Etkinleştirme nedeni** kutusunda, etkinleştirme isteğinin nedenini girin. Bazı roller bir sorun bileti numarası vermenizi gerektirir.

    ![Etkinleştirme bölmesi özel bir başlangıç zamanı, süre, Bilet ve neden ile tamamlandı](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. **Etkinleştir**' e tıklayın.

    Rol onay gerektirmiyorsa, etkinleştirmenin durumunu gösteren bir **etkinleştirme durumu** bölmesi görüntülenir.

    ![Etkinleştirmenin üç aşamasını gösteren etkinleştirme durumu sayfası](./media/pim-how-to-activate-role/activation-status.png)

    Tüm aşamalar tamamlandıktan sonra, Azure portal oturumu kapatmak için **oturumu** kapat bağlantısına tıklayın. Portalda yeniden oturum açtığınızda, artık rolünü kullanabilirsiniz.

    Rol etkinleştirme için [onay gerektiriyorsa](./azure-ad-pim-approval-workflow.md) , tarayıcınızın sağ üst köşesinde, isteğin onay beklendiğini bildiren bir bildirim görüntülenir.

    ![Etkinleştirme isteği onay bildirimi bekliyor](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>İsteklerinizin durumunu görüntüleyin

Etkinleştirme için bekleyen isteklerinizin durumunu görüntüleyebilirsiniz.

1. Azure AD Privileged Identity Management açın.

1. **Azure AD rolleri**' ne tıklayın.

1. İsteklerinizin listesini görmek için **Isteklerim** ' e tıklayın.

    ![Azure AD rolleri-isteklerim listesi](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Rolü devre dışı bırak

Bir rol etkinleştirildikten sonra, zaman sınırına (uygun süre) ulaşıldığında otomatik olarak devre dışı bırakır.

Yönetici görevlerinizi erken tamamladıktan sonra Azure AD Privileged Identity Management bir rolü el ile de devre dışı bırakabilirsiniz.

1. Azure AD Privileged Identity Management açın.

1. **Azure AD rolleri**' ne tıklayın.

1. **Rollerim**' e tıklayın.

1. Etkin roller listenizi görmek için **etkin roller** ' e tıklayın.

1. Kullanarak yaptığınız rolü bulun ve **devre dışı bırak**' a tıklayın.

## <a name="cancel-a-pending-request"></a>Bekleyen bir isteği iptal etme

Onay gerektiren bir rolün etkinleştirilmesini gerektirmiyorsa, bekleyen bir isteği dilediğiniz zaman iptal edebilirsiniz.

1. Azure AD Privileged Identity Management açın.

1. **Azure AD rolleri**' ne tıklayın.

1. **Isteklerim**' e tıklayın.

1. İptal etmek istediğiniz rol için **iptal** düğmesine tıklayın.

    Iptal ' e tıkladığınızda istek iptal edilir. Rolü yeniden etkinleştirmek için, etkinleştirme için yeni bir istek göndermeniz gerekir.

   ![Iptal düğmesi vurgulanmış isteklerim listesi](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Sorun giderme

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Rol etkinleştirildikten sonra izinler verilmiyor

Privileged Identity Management bir rolü etkinleştirdiğinizde, etkinleştirme ayrıcalıklı rol gerektiren tüm portallara anında yaymayabilir. Bazı durumlarda değişiklik yayılsa bile portalda web önbelleği değişikliğin anında geçerlilik kazanmamasına yol açabilir. Etkinleştirme gecikirse, yapmanız gerekenler aşağıda verilmiştir.

1. Azure portalında oturumunuzu kapatın ve sonra yeniden oturum açın.

    Bir Azure AD rolünü etkinleştirdiğinizde, etkinleştirmesinin aşamalarını görürsünüz. Tüm aşamalar tamamlandıktan sonra bir **Oturumu kapat** bağlantısı görürsünüz. Oturumu kapatmak için bu bağlantıyı kullanabilirsiniz. Bu, etkinleştirme gecikmesi için çoğu durumu çöztirecek.

1. Privileged Identity Management, rolün üyesi olarak listelendiğinizi doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Kaynak rollerimi Privileged Identity Management etkinleştir](pim-resource-roles-activate-your-roles.md)
