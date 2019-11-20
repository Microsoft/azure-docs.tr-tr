---
title: Bir gruba lisans atama-Azure Active Directory | Microsoft Docs
description: Azure Active Directory Grup Lisanslama aracılığıyla kullanıcılara lisans atama
services: active-directory
keywords: Azure AD lisanslama
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 497efda857dcd7de3079d702be00a094d221b779
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034852"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Azure Active Directory ' de grup üyeliğine göre kullanıcılara lisans atama

Bu makalede, bir kullanıcı grubuna ürün lisansları atama ve Azure Active Directory (Azure AD) ' de doğru lisanslandığının doğrulanması anlatılmaktadır.

Bu örnekte, kiracı **HR departmanı**adlı bir güvenlik grubu içerir. Bu grup, insan kaynakları bölümünün tüm üyelerini içerir (1.000 kullanıcının etrafında). Office 365 Kurumsal E3 lisanslarını tüm departmana atamak istiyorsunuz. Departman kullanmaya başlamaya hazırlanana kadar ürüne dahil olan Yammer Enterprise hizmeti geçici olarak devre dışı bırakılmalıdır. Ayrıca, Enterprise Mobility + Security lisanslarını aynı kullanıcı grubuna dağıtmak istersiniz.

> [!NOTE]
> Bazı Microsoft hizmetleri tüm konumlarda kullanılamaz. Bir lisansın bir kullanıcıya atanabilmesi için önce yönetici kullanıcı üzerinde kullanım konumu özelliğini belirtmelidir.
>
> Grup lisansı ataması için, kullanım konumu belirtilmemiş tüm kullanıcılar dizinin konumunu alır. Birden çok konumdaki kullanıcılarınız varsa, kullanım konumunu her zaman Azure AD 'de Kullanıcı oluşturma akışınızın bir parçası olarak ayarlamanızı öneririz (örn. AAD Connect yapılandırması aracılığıyla), lisans atamasının sonucunun her zaman doğru ve kullanıcıların almamasını sağlar izin verilmeyen konumlarda bulunan hizmetler.

## <a name="step-1-assign-the-required-licenses"></a>1\. Adım: gerekli lisansları atama

1. [**Azure AD Yönetim merkezinde**](https://aad.portal.azure.com) bir lisans yönetici hesabıyla oturum açın. Lisansları yönetmek için hesabın Lisans Yöneticisi, Kullanıcı Yöneticisi veya genel yönetici olması gerekir.

1. Kiracıdaki tüm lisanslanabilir ürünleri görebileceğiniz ve yönetebileceğiniz bir sayfa açmak için **lisanslar** ' ı seçin.

1. **Tüm ürünler**altında, ürün adlarını seçerek hem Office 365 Kurumsal E5 hem de Enterprise Mobility + Security E3 ' nı seçin. Atamayı başlatmak için sayfanın en üstünde bulunan **ata** ' yı seçin.

   ![Lisans atamak için ürünleri seçin](./media/licensing-groups-assign/all-products-assign.png)
  
1. **Lisans ata** sayfasında kullanıcılar ve gruplar ' **ı seçerek kullanıcılar** ve gruplar listesini açın.

1. Bir kullanıcı veya grup seçin ve sonra seçiminizi onaylamak için sayfanın alt kısmındaki **Seç** düğmesini kullanın.

1. **Lisans ata** sayfasında, daha önce seçtiğimiz iki ürüne dahil olan tüm hizmet planlarını görüntüleyen **atama seçenekleri**' ne tıklayın. **Yammer Enterprise** 'ı bulun ve ürün lisansından bu hizmeti devre dışı bırakmak için **devre** dışı bırakın. **Lisans seçeneklerinin**altındaki **Tamam** ' a tıklayarak onaylayın.

   ![lisanslar için hizmet planlarını seçin](./media/licensing-groups-assign/assignment-options.png)
  
1. Atamayı tamamladıktan sonra, **Lisans ata** sayfasında, sayfanın en altında bulunan **ata** ' ya tıklayın.

1. Sağ üst köşede, işlemin durumunu ve sonucunu gösteren bir bildirim görüntülenir. Gruba atama işlemi tamamlanamadığından (örneğin, gruptaki önceden var olan lisanslar nedeniyle), hatanın ayrıntılarını görüntülemek için bildirime tıklayın.

Bir gruba lisans atarken, Azure AD söz konusu grubun tüm mevcut üyelerini işler. Bu işlem, grubun boyutuyla değiştirerek biraz zaman alabilir. Sonraki adımda, işlemin tamamlandığını doğrulamak ve sorunları çözmek için daha fazla dikkat gerekip gerekmediğini belirleme açıklanmaktadır.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>2\. Adım: ilk atamanın tamamlandığını doğrulama

1. **Azure Active Directory** > **gruplarına**gidin. Lisansın atandığı grubu seçin.

1. Grup sayfasında, **lisanslar**' ı seçin. Bu, lisansların kullanıcılara tam olarak atandığını ve aramanız gereken herhangi bir hata olup olmadığını hızlı bir şekilde doğrulamanıza olanak sağlar. Aşağıdaki bilgiler kullanılabilir:

   - Gruba atanmış olan hizmet lisansları. Etkinleştirilen belirli hizmetleri göstermek ve değişiklik yapmak için bir giriş seçin.

   - Değişiklikler işlendiğinde veya tüm Kullanıcı üyeleri için işleme bitiyorsa kullanılabilir olan en son lisans değişikliklerinin durum güncelleştirmeleri.

   - Bir hata durumundaki Kullanıcı Lisans atamaları hakkında bilgiler.

   ![Lisanslama hataları ve lisans durumu](./media/licensing-groups-assign/assignment-errors.png)

1. **Azure Active Directory** > **kullanıcı ve grup** > *Grup adı* > **Denetim günlükleri**altında lisans işleme hakkında daha ayrıntılı bilgi görüntüleyin. Aşağıdaki etkinlikleri denetleyin:

   - Etkinlik: `Start applying group based license to users`. Bu, sistem gruptaki lisans ataması değişikliğini seçtiğinde günlüğe kaydedilir ve tüm Kullanıcı üyelerine uygulamayı başlatır. Yapılan değişiklik hakkında bilgi içerir.

   - Etkinlik: `Finish applying group based license to users`. Bu, sistem gruptaki tüm kullanıcıları işlemeyi tamamladığında günlüğe kaydedilir. Kaç kullanıcının başarıyla işlendiğini ve kaç kullanıcıya grup lisansı atanmadığını bir Özet içerir.

   Denetim günlüklerinin, grup tabanlı lisanslama tarafından yapılan değişiklikleri analiz etmek için nasıl kullanılabileceği hakkında daha fazla bilgi edinmek için [Bu bölümü okuyun](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) .

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>3\. Adım: lisans sorunlarını denetleyin ve çözümleyin

1. **Azure Active Directory** > **gruplarına**gidin ve Lisansların atandığı grubu bulun.
1. Grup sayfasında, **lisanslar**' ı seçin. Sayfanın üst kısmındaki bildirim, lisansların atanabileceği 10 Kullanıcı olduğunu gösterir. Bu grup için bir lisanslama hata durumunda tüm kullanıcıların listesini görmek için onu açın.
1. **Başarısız atamalar** sütunu, kullanıcılara her iki ürün lisansının atanmayacağını söyler. **Hata sütununun en üstteki nedeni** , hatanın nedenini içerir. Bu durumda, bu **Çakışan hizmet planlarımız**.

   ![atanmayan lisanslar](./media/licensing-groups-assign/failed-assignments.png)

1. Kullanıcının **lisanslar** sayfasını açmak için bir kullanıcı seçin. Bu sayfa, kullanıcıya şu anda atanmış olan tüm lisansları gösterir. Bu örnekte, kullanıcının **bilgi noktası kullanıcıları** grubundan devralınan Office 365 Kurumsal E1 lisansına sahip olması gerekir. Bu, sistemin **HR departmanı** grubundan uygulanmaya çalıştığı E3 lisansıyla çakışıyor. Sonuç olarak, bu gruptan lisanslardan hiçbiri kullanıcıya atanmaz.

   ![Bir kullanıcı için tüm lisans çakışmalarını görüntüleme](./media/licensing-groups-assign/user-license-view.png)

1. Bu çakışmayı çözümlemek için, kullanıcıyı **bilgi noktası kullanıcıları** grubundan kaldırın. Azure AD değişikliği tamamladıktan sonra, **HR departmanı** lisansları doğru şekilde atanır.

## <a name="next-steps"></a>Sonraki adımlar

Grupları kullanarak lisans atamaya yönelik özellik kümesi hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure Active Directory 'de grup tabanlı lisanslama nedir?](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)
- [Azure Active Directory'de grubun lisans sorunlarını tanımlama ve çözme](licensing-groups-resolve-problems.md)
- [Azure Active Directory'de tek tek lisanslı kullanıcıları grup tabanlı lisanslamaya geçirme](licensing-groups-migrate-users.md)
- [Azure Active Directory 'de grup tabanlı lisanslama kullanarak kullanıcıları ürün lisansları arasında geçirme](licensing-groups-change-licenses.md)
- [Azure Active Directory grup tabanlı lisanslamayla ilgili ek senaryolar](../active-directory-licensing-group-advanced.md)
- [Azure Active Directory 'de grup tabanlı lisanslama için PowerShell örnekleri](licensing-ps-examples.md)
