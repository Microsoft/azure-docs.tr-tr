---
title: Veri erişimi sağlamak için güvenliği Yapılandırma-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights ortamınızda güvenlik, izinleri yapılandırma ve veri erişimi ilkelerini yönetme hakkında bilgi edinin.
ms.service: time-series-insights
services: time-series-insights
author: shipra1mishra
ms.author: shmishr
manager: dviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: 84b973dfa016b069b18fda47a4336fe952f73b3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780867"
---
# <a name="grant-data-access-to-an-environment"></a>Bir ortama veri erişimi verme

Bu makalede iki tür Azure Time Series Insights erişim ilkesi açıklanmaktadır.

## <a name="sign-in-to-azure-time-series-insights"></a>Azure Time Series Insights oturum açın

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Arama kutusuna girerek Azure Time Series Insights ortamınızı bulun `Time Series Insights environments` .  `Time Series Insights environments`Arama sonuçlarında öğesini seçin.
1. Listeden Azure Time Series Insights ortamınızı seçin.

## <a name="grant-data-access"></a>Veri erişim izni verme

Bir Kullanıcı sorumlusu için veri erişimi sağlamak üzere bu adımları izleyin.

1. **Veri erişim ilkeleri**' ni seçin ve **+ Ekle**' yi seçin.

    [![Veri erişim Ilkesi seçme ve ekleme](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. **Kullanıcı Seç ' i** seçin. Eklemek istediğiniz kullanıcıyı bulmak için Kullanıcı adını veya e-posta adresini arayın. Seçimi onaylamak için **Seç** ' i seçin.

    [![Eklenecek bir kullanıcı seçin](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. **Rol Seç ' i** seçin. Kullanıcı için uygun erişim rolünü seçin:

    * Kullanıcının başvuru verilerini değiştirmesine ve kaydedilmiş sorguları ve perspektifleri ortamın diğer kullanıcılarıyla paylaşmasına izin vermek istiyorsanız **katılımcı** ' ı seçin.

    * Aksi takdirde, kullanıcının ortamdaki verileri sorgulayave kişisel, paylaşılmayan sorguları ortamda kaydetmesine izin vermek için **okuyucu** ' ı seçin.

   Rol seçimini onaylamak için **Tamam ' ı** seçin.

    [![Seçili rolü Onayla](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. **Kullanıcı rolü Seç** sayfasında **Tamam ' ı** seçin.

    [![Kullanıcı rolü Seç sayfasında Tamam ' ı seçin.](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. **Veri erişim ilkeleri** sayfasının, her kullanıcı için kullanıcıları ve rolleri listelediğinden emin olun.

    [![Doğru Kullanıcı ve rolleri doğrulama](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Başka bir Azure AD kiracısından konuk erişimi sağlama

`Guest`Rol bir yönetim rolü değil. Bir kiracıdan diğerine davet edilen bir hesap için kullanılan bir terimdir. Konuk hesabı kiracının dizinine davet edildikten sonra, aynı erişim denetimine diğer tüm hesap gibi uygulanabilirler. Access Control (ıAM) dikey penceresini kullanarak Azure Time Series Insights ortamına yönetim erişimi verebilirsiniz. Ya da veri erişim Ilkeleri dikey penceresi aracılığıyla ortamdaki verilere erişim izni verebilirsiniz. Azure Active Directory (Azure AD) kiracı konuk erişimi hakkında daha fazla bilgi için [Azure Portal Add Azure ACTIVE DIRECTORY B2B işbirliği kullanıcıları](../active-directory/external-identities/add-users-administrator.md)' nı okuyun.

Başka bir kiracıdan bir Azure AD kullanıcısına Azure Time Series Insights ortamına konuk erişimi sağlamak için aşağıdaki adımları izleyin.

1. Azure portal gidin,  **Azure Active Directory**' e tıklayın, **genel bakış** sekmesinde aşağı kaydırın ve ardından **Konuk Kullanıcı**' yı seçin.

    [![Veri erişimi Ilkeleri ' ni seçin ve ardından + davet et](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Davet etmek istediğiniz kullanıcının e-posta adresini girin. Bu e-posta adresinin Azure AD ile ilişkilendirilmesi gerekir. İsteğe bağlı olarak davete kişisel bir ileti ekleyebilirsiniz.

    [![Seçili kullanıcıyı bulmak için e-posta adresini girin](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Ekranda görünen onay balonu olup olmadığına bakın. Konuk kullanıcının eklendiğini onaylamak için **Bildirimler** ' e de tıklayabilirsiniz.

    [![Onay kabarcığun görünmesini ara](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Yeni oluşturulan Konuk kullanıcıyı eklemek için Time Series Insights ortamınıza geri dönün. Verilere erişim **Izni ver** altında açıklandığı gibi **veri erişim ilkeleri** ' ne tıklayın. **Kullanıcı**' yı seçin. Eklemek istediğiniz kullanıcıyı bulmak için davet ettiğiniz Konuk kullanıcının e-posta adresini arayın. Sonra, seçimi onaylamak için öğesini **seçin** .

    [![Kullanıcıyı seçin ve seçimi onaylayın](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. **Rol Seç ' i** seçin. Konuk Kullanıcı için uygun erişim rolünü seçin:

    * Kullanıcının başvuru verilerini değiştirmesine ve kaydedilmiş sorguları ve perspektifleri ortamın diğer kullanıcılarıyla paylaşmasına izin vermek istiyorsanız **katılımcı** ' ı seçin.

    * Aksi takdirde, kullanıcının ortamdaki verileri sorgulayave kişisel, paylaşılmayan sorguları ortamda kaydetmesine izin vermek için **okuyucu** ' ı seçin.

   Rol seçimini onaylamak için **Tamam ' ı** seçin.

    [![Rol seçimini onaylayın](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. **Kullanıcı rolü Seç** sayfasında **Tamam ' ı** seçin.

1. **Veri erişim ilkeleri** sayfasının Konuk kullanıcıyı ve her Konuk Kullanıcı için rolleri listelediğinden emin olun.

    [![Kullanıcıların ve rollerin doğru atandığını doğrulama](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Artık Konuk Kullanıcı, yukarıda belirtilen e-posta adresinde bir davet e-postası alacak. Konuk Kullanıcı, kabul edip etmediklerini doğrulamak ve Azure bulutuna bağlanmak için **kullanmaya başlayın** ' ı seçer.

    [![Konuk kabul etmek için kullanmaya başlayın seçimini seçer](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. **Başlarken**' i seçtikten sonra, Konuk kullanıcıya yönetici organizasyonu ile ilişkili bir izinler kutusu sunulur. **Kabul et**' i seçerek izin verildiklerinde oturum açılır.

    [![Konuk, izinleri gözden geçirir ve kabul eder](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. Yönetici, [ortam URL](time-series-insights-parameterized-urls.md) 'sini konularıyla paylaşır.

1. Konuk Kullanıcı, davet etmek için kullandığınız e-posta adresine kaydolduktan sonra daveti kabul ettiğinde, Azure portal yönlendirilir.

1. Konuk artık yönetici tarafından belirtilen ortam URL 'sini kullanarak paylaşılan ortama erişebilir. Anında erişim için bu URL 'YI Web tarayıcısına girebilirler.

1. Yöneticinin kiracısı, zaman serisi Gezgini 'nin sağ üst köşesindeki profil simgesi seçildikten sonra Konuk kullanıcıya görüntülenecektir.

    [![İnsights.azure.com üzerinde seçimi avatar](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)

    Konuk kullanıcı yönetici kiracısını seçtikten sonra, paylaşılan Azure Time Series Insights ortamını seçme olanağına sahip olur.

    Artık, **Adım 5**' te size sağladıkları rolle ilişkili tüm yetenekler vardır.

    [![Konuk Kullanıcı açılan kutudan Azure kiracınızı seçer](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

* Azure Active Directory uygulama kaydı adımları için [kimlik doğrulaması ve yetkilendirmeyi](time-series-insights-authentication-and-authorization.md) okuyun.

* [Azure Time Series Insights Gezgini ' nde ortamınızı](./concepts-ux-panels.md)görüntüleyin.
