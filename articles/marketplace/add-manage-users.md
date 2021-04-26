---
title: Ticari Market için Kullanıcı ekleme ve yönetme-Azure Marketi
description: Ticari Market programındaki kullanıcıları Iş Ortağı Merkezi 'nde bir Microsoft ticari Market hesabına göre yönetmeyi öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: d5b9197bfd2526dd414406ebf1aca509d3b3fa91
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108410"
---
# <a name="add-and-manage-users-for-the-commercial-marketplace"></a>Ticari Market için Kullanıcı ekleme ve yönetme

**Uygun roller**

- Sahip
- Yönetici

Iş Ortağı Merkezi 'nin ( **Hesap ayarları** altında) **Kullanıcılar** bölümü, iş ortağı merkezi hesabınıza erişimi olan kullanıcıları, grupları ve Azure AD UYGULAMALARıNı yönetmek için Azure AD 'yi kullanmanıza olanak sağlar. Hesabınız, Kullanıcı eklemek veya düzenlemek istediğiniz [iş hesabı (Azure AD kiracısı)](company-work-accounts.md) için yönetici düzeyinde izinlere sahip olmalıdır. Farklı bir iş hesabındaki/Kiracıdaki kullanıcıları yönetmek için, oturumu kapatıp bu iş hesabı/kiracısında **yönetici** izinlerine sahip bir kullanıcı olarak yeniden oturum açmanız gerekir.

İş hesabınızla (Azure AD kiracısı) oturum açtıktan sonra, kullanıcı ekleyebilir ve yönetebilirsiniz.

## <a name="add-existing-users"></a>Mevcut kullanıcıları Ekle

İş Ortağı Merkezi hesabınıza, şirketinizin [iş hesabında (Azure AD kiracısı)](company-work-accounts.md)zaten mevcut olan kullanıcıları eklemek için:

1. **Kullanıcılara** ( **Hesap ayarları** altında) gidin ve **Kullanıcı Ekle**' yi seçin.
1. Görüntülenen listeden bir veya daha fazla kullanıcı seçin. Belirli kullanıcıları aramak için arama kutusunu kullanabilirsiniz. * Iş Ortağı Merkezi hesabınıza eklemek için birden fazla kullanıcı seçerseniz, bunlara aynı rol veya özel izinler kümesi atamanız gerekir. Farklı rollere/izinlere sahip birden fazla kullanıcı eklemek için, bu adımları her bir rol veya özel izinler kümesi için tekrarlayın.
1. Kullanıcıları seçmeyi tamamladığınızda **Seçili Ekle**' yi seçin.
1. Roller bölümünde, seçili kullanıcılar için **rolleri** veya özelleştirilmiş izinleri belirtin.
1. **Kaydet**’i seçin.

## <a name="create-new-users"></a>Yeni Kullanıcı Oluştur

Yeni Kullanıcı hesapları oluşturmak için [genel yönetici](/azure/active-directory/roles/permissions-reference) izinlerine sahip bir hesabınız olması gerekir.

1. **Kullanıcılara** ( **Hesap ayarları** altında) gidin, **Kullanıcı Ekle**' yi seçin ve ardından **Yeni Kullanıcı oluştur**' u seçin.
1. Her yeni kullanıcı için bir ad, son ad ve Kullanıcı adı girin.
1. Yeni kullanıcının kuruluşunuzun dizininde genel yönetici hesabına sahip olmasını istiyorsanız, **tüm dizin kaynakları üzerinde tam denetim ile bu kullanıcıyı Azure AD 'niz üzerinde genel yönetici yap** etiketli kutuyu işaretleyin. Bu, kullanıcıya şirketinizin Azure AD 'deki tüm yönetim özelliklerine tam erişim sağlayacak. Hesaba uygun rolü/izinleri vermediğiniz müddetçe, iş ortağı merkezi 'nde değil, kuruluşunuzun iş hesabına (Azure AD kiracısı) Kullanıcı ekleyebilir ve bunları yönetebilecektir.
1. **Bu kullanıcıya genel yönetici yapma** kutusunu işaretlenirse, gerekirse parolasını kurtarmak için *parola kurtarma e-postası* sağlamanız gerekir.
1. **Grup üyeliği** bölümünde, yeni kullanıcının ait olmasını istediğiniz grupları seçin.
1. **Roller** bölümünde, Kullanıcı için rol (ler) veya özelleştirilmiş izinleri belirtin.
1. **Kaydet**’i seçin.

İş Ortağı Merkezi 'nde yeni bir kullanıcı oluşturmak, oturum açtığınız iş hesabındaki (Azure AD kiracısı) bu kullanıcı için bir hesap de oluşturur. Iş ortağı merkezindeki bir kullanıcının adında değişiklik yapmak, kuruluşunuzun iş hesabında (Azure AD kiracısı) aynı değişiklikleri yapar.

## <a name="invite-new-users-by-email"></a>Yeni kullanıcıları e-posta ile davet et

Şirket iş hesabınızın (Azure AD kiracısı) bir parçası olmayan kullanıcıları e-posta ile davet etmek için, [genel yönetici](/azure/active-directory/roles/permissions-reference) izinlerine sahip bir hesabınız olmalıdır.

1. **Kullanıcılara** ( **Hesap ayarları** altında) gidin, **Kullanıcı Ekle**' yi seçin ve ardından **kullanıcıları e-postayla davet et**' i seçin
1. Virgülle veya noktalı virgülle ayırarak bir veya daha fazla e-posta adresi (en fazla 10) girin.
1. **Roller** bölümünde, Kullanıcı için rol (ler) veya özelleştirilmiş izinleri belirtin.
1. **Kaydet**’i seçin.

Davet ettiğiniz kullanıcılar, Iş Ortağı Merkezi hesabınıza katılması için bir e-posta daveti alacak. İş hesabınızda (Azure AD kiracısı) yeni bir Konuk Kullanıcı hesabı oluşturulacaktır. Hesabınıza erişebilmek için her kullanıcının davetini kabul etmesi gerekir.

Bir daveti yeniden göndermeniz gerekiyorsa, *Kullanıcılar* sayfasını ziyaret edin, Kullanıcı listesinde daveti bulun, e-posta adresini (veya *daveti bekleyen* metni) seçin. Ardından sayfanın alt kısmındaki **daveti yeniden gönder**' i seçin.

Kuruluşunuzda şirket içi dizin hizmetini Azure AD ile eşitlemek için [Dizin tümleştirmesi](https://docs.microsoft.com/previous-versions/azure/azure-services/jj573653(v=azure.100)) kullanılıyorsa, Iş Ortağı Merkezi 'nde yeni kullanıcılar, gruplar veya Azure AD uygulamaları oluşturamazsınız. Siz (veya şirket içi dizininizde bulunan başka bir yöneticinin), Iş Ortağı Merkezi 'nde görebilmeniz ve ekleyebilmeniz için doğrudan şirket içi dizinde oluşturulması gerekir.

## <a name="remove-a-user"></a>Kullanıcıyı kaldırma

Bir kullanıcıyı iş hesabınızdan (Azure AD kiracısı) kaldırmak için **kullanıcılara** gidin ( **Hesap ayarları** altında), en sağdaki sütunda onay kutusunu kullanarak kaldırmak istediğiniz kullanıcıyı seçin ve ardından kullanılabilir eylemlerden **Kaldır** ' ı seçin. Seçili kullanıcıları kaldırmak istediğinizi onaylamanız için bir açılır pencere görüntülenir.

## <a name="change-a-user-password"></a>Kullanıcı parolasını değiştirme

Kullanıcılarınızın, parolasını değiştirmesi gerekiyorsa, Kullanıcı hesabı oluştururken *parola kurtarma e-postası* sağlanmışsa, bunları kendileri yapabilir. Ayrıca, aşağıdaki adımları izleyerek bir kullanıcının parolasını güncelleştirebilirsiniz. Şirket iş hesabınızdaki (Azure AD kiracısı) bir kullanıcının parolasını değiştirmek için, [genel yönetici](/azure/active-directory/roles/permissions-reference) izinlerine sahip bir hesapta oturum açmış olmanız gerekir. Bu işlem, kullanıcının Azure AD kiracınızdaki parolasını, Iş Ortağı Merkezi 'ne erişmek için kullandıkları parolayı değiştirecek şekilde değiştirir.

1. **Kullanıcılar** sayfasından ( **Hesap ayarları** altında), düzenlemek istediğiniz kullanıcı hesabının adını seçin.
1. Sayfanın alt kısmındaki **Parolayı Sıfırla** düğmesini seçin.
1. Geçici bir parola dahil olmak üzere, kullanıcının oturum açma bilgilerini göstermek için bir onay sayfası görüntülenir. Bu sayfadan ayrıldıktan sonra geçici parolaya erişemeyeceksiniz, bu bilgileri yazdırdığınızdan veya kopyalamanız ve kullanıcıya sunduğunuzdan emin olun.
