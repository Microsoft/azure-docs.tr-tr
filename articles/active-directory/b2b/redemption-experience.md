---
title: B2B işbirliği ile davet yeniden satın alma-Azure Active Directory | Microsoft Docs
description: Son kullanıcılar için, gizlilik koşullarına yönelik anlaşma dahil olmak üzere Azure AD B2B işbirliği daveti kullanım deneyimini açıklar.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d32818f9e96e931f9e8c3c13554752327c5c456
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622623"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B işbirliği daveti kullanım

Bu makalede, konuk kullanıcıların kaynaklarınızda ve iletişim kurdukları onay sürecinde erişebileceği yollar açıklanmaktadır. Konuğa bir davet e-postası gönderirseniz, davet, konuğun uygulamanıza veya portala erişim sağlamak için yararlanana bir bağlantı içerir. Davet e-postası, konuklarınızın kaynaklarınıza erişebileceği yollarla yalnızca biridir. Alternatif olarak, dizininize Konuk ekleyebilir ve paylaşmak istediğiniz portalın ya da uygulamanın doğrudan bağlantısını sağlayabilirsiniz. Konukların, kullandıkları yöntemden bağımsız olarak, bir ilk kez onay süreci aracılığıyla yapılır. Bu işlem, konukların gizlilik koşullarını kabul etmesini ve ayarlamış olduğunuz tüm [kullanım koşullarını](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) kabul etmesini sağlar.

Dizininize bir Konuk kullanıcı eklediğinizde, Konuk Kullanıcı hesabının başlangıçta **Pendingkabulünü**olarak ayarlanan bir izin durumu (PowerShell 'de görüntülenebilir) vardır. Bu ayar, Konuk davetinizi kabul edene ve gizlilik ilkenize ve kullanım koşullarınıza karşı kabul edilene kadar kalır. Bundan sonra, onay durumu **kabul edildi**olarak değişir ve izin sayfaları artık konuğa sunulmaz.

## <a name="redemption-through-the-invitation-email"></a>Davet e-postası üzerinden ödeme

[Azure Portal kullanarak](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)dizininize bir Konuk kullanıcı eklediğinizde, işlemde konuğa bir davet e-postası gönderilir. Ayrıca, [PowerShell kullanırken](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) , dizininize Konuk kullanıcıları eklemek için davet e-postaları gönderilmesini de tercih edebilirsiniz. İşte e-postadaki bağlantıyı kullandıklarında konuğun deneyiminden oluşan bir açıklama.

1. Konuk, **Microsoft davetlerden**gönderilen bir [davet e-postası](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) alır.
2. Konuk, e-postada **Başlarken** ' i seçer.
3. Konukta bir Azure AD hesabına, bir Microsoft hesabına (MSA) veya bir Federasyon kuruluşunda e-posta hesabına sahip değilse, bu, bir MSA oluşturması istenir ( [bir kerelik geçiş kodu](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) özelliği etkin DEĞILSE, MSA gerektirmez).
4. Konuk, aşağıda açıklanan [onay deneyimine](#consent-experience-for-the-guest) göre yapılır.

## <a name="redemption-through-a-direct-link"></a>Doğrudan bir bağlantı üzerinden ödeme

Davet e-postasına alternatif olarak, bir konuğa uygulamanız veya portalınızın doğrudan bağlantısını sağlayabilirsiniz. Önce [Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) veya [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)aracılığıyla konuk kullanıcıyı dizininize eklemeniz gerekir. Ardından doğrudan oturum açma bağlantıları dahil olmak üzere [kullanıcılara uygulama dağıtmak için özelleştirilebilen bir yol](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)kullanabilirsiniz. Bir konuk davet e-postası yerine doğrudan bağlantı kullandığında, yine de ilk kez onay deneyiminde gezinir.

> [!IMPORTANT]
> Doğrudan bağlantı, kiracıya özgü olmalıdır. Diğer bir deyişle, bir kiracı KIMLIĞI veya doğrulanmış etki alanı içermesi gerekir, bu sayede Konuk, paylaşılan uygulamanın bulunduğu kiracınızda kimlik doğrulaması yapılabilir. Kimlik doğrulaması için kendi https://myapps.microsoft.com giriş kiracısına yönlendirireceği için, benzer ortak bir URL, konuk için çalışmaz. Kiracı bağlamıyla doğrudan bağlantı örnekleri aşağıda verilmiştir:
 > - Uygulamalar erişim paneli: https://myapps.microsoft.com/?tenantid=&lt; Kiracı kimliği&gt; 
 > - Doğrulanan etki alanı için uygulamalar erişim paneli: https://myapps.microsoft.com/&lt; doğrulanan etki alanı&gt;
 > - Azure Portal: https://portal.azure.com/&lt; Kiracı kimliği&gt;
 > - Tek uygulama: bkz. [doğrudan oturum açma bağlantısı](../manage-apps/end-user-experiences.md#direct-sign-on-links) kullanma

Davet e-postasında doğrudan bağlantı üzerinden önerilen bazı durumlar vardır. Bu özel durumlar kuruluşunuz için önemliyse, davet e-postasını hala gönderen yöntemleri kullanarak kullanıcıları davet etmenizi öneririz:
 - Kullanıcının Federasyon kuruluşunda bir Azure AD hesabı, MSA veya e-posta hesabı yoktur. Tek seferlik geçiş kodu özelliğini kullanmıyorsanız, bir MSA oluşturma adımlarında gezinmek için konuğun davet e-postasını sağlaması gerekir.
 - Bazen davet eden Kullanıcı nesnesinin bir e-posta adresi olmayabilir (örneğin, bir Outlook ilgili kişisi nesnesi). Bu durumda, Kullanıcı davet e-postasında kullanım URL 'sini tıklamalıdır.
 - Kullanıcı, davet edilen e-posta adresinin diğer adıyla oturum açabilir. (Diğer ad, bir e-posta hesabıyla ilişkili ek bir e-posta adresidir.) Bu durumda, Kullanıcı davet e-postasında kullanım URL 'sini tıklamalıdır.

## <a name="consent-experience-for-the-guest"></a>Konuk için onay deneyimi

Bir konuk, bir iş ortağı kuruluşundaki kaynaklara ilk kez erişmek üzere oturum açtığında, bunlar aşağıdaki sayfalara kılavuzluk ederler. 

1. Konuk, davet eden kuruluşun gizlilik bildirimini açıklayan **Izinleri gözden geçirme** sayfasını inceler. Bir kullanıcının devam etmesi için bilgilerin kullanımını davet eden kuruluşun gizlilik ilkelerine uygun olarak **kabul etmesi** gerekir.

   ![İzinleri gözden geçir sayfasını gösteren ekran görüntüsü](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Kiracı Yöneticisi olarak kuruluşunuzun gizlilik bildirimine nasıl bağlayabileceğiniz hakkında bilgi için bkz [. nasıl yapılır: Kuruluşunuzun gizlilik bilgilerini Azure Active Directory](https://aka.ms/adprivacystatement)ekleyin.

2. Kullanım koşulları yapılandırılırsa, Konuk açılıp kullanım koşullarını inceler ve **kabul et**' i seçer. 

   ![Yeni kullanım koşullarını gösteren ekran görüntüsü](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > Yapılandırabilirsiniz:**kuruluş ilişkilerini** >  [](../governance/active-directory-tou.md) yönetmekullanımkoşullarıiçinkullanımkoşulları > ' nı yapılandırabilirsiniz.

3. Aksi belirtilmediği takdirde, Konuk, konuk tarafından erişebilen uygulamaları listeleyen uygulamalar erişim paneline yönlendirilir.

   ![Uygulamalar erişim panelini gösteren ekran görüntüsü](media/redemption-experience/myapps.png) 

Dizininizde, Konuk **davetinin kabul edilebilir** değeri **Evet**olarak değişir. Bir MSA oluşturulduysa, Konuk **kaynağı** **Microsoft hesabını**gösterir. Konuk Kullanıcı hesabı özellikleri hakkında daha fazla bilgi için bkz. [Azure AD B2B işbirliği kullanıcısının özellikleri](user-properties.md). 

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
- [Azure portalında Azure Active Directory B2B işbirliği kullanıcıları ekleme](add-users-administrator.md)
- [Bilgi çalışanları, Azure Active Directory B2B işbirliği kullanıcılarını nasıl ekler?](add-users-information-worker.md)
- [PowerShell kullanarak Azure Active Directory B2B işbirliği kullanıcıları ekleme](customize-invitation-api.md#powershell)
- [Bir kuruluştan Konuk Kullanıcı olarak ayrılın](leave-the-organization.md)
