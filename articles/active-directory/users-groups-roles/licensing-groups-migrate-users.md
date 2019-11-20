---
title: Grup lisanslama 'ye doğrudan lisanslı kullanıcılar ekleme-Azure AD | Microsoft Docs
description: Azure Active Directory kullanarak bireysel kullanıcı lisanlarından grup tabanlı lisansa geçiş yapma
services: active-directory
keywords: Azure AD lisanslama
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c06d81f2f3f6cee781889d05ae08a1fd125df52
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025674"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Lisanslama için kullanıcıları bireysel lisanslarla geçirme

Kuruluşlardaki kullanıcılara doğrudan atama aracılığıyla dağıtılmış mevcut lisanslarınız olabilir; diğer bir deyişle, bireysel kullanıcı lisansları atamak için PowerShell betikleri veya diğer araçları kullanma. Kuruluşunuzdaki lisansları yönetmek için grup tabanlı lisanslama kullanmaya başlamadan önce, bu geçiş planını kullanarak var olan çözümlerin grup tabanlı lisanslamayla sorunsuz bir şekilde değiştirilmesini sağlayabilirsiniz.

Göz önünde bulundurmanız gereken en önemli şey, grup tabanlı lisanslama 'e geçiş yapmak için kullanıcılara Şu anda atanmış lisansların geçici olarak kaybolmasına neden olacak bir durum yapmaktan kaçınmaktır. Lisansların kaldırılmasına neden olabilecek herhangi bir işlem, kullanıcıların hizmetlere ve verilerine erişimi kaybetme riskini ortadan kaldırmaya kaçınılmalıdır.

## <a name="recommended-migration-process"></a>Önerilen geçiş işlemi

1. Mevcut Otomasyon (örneğin, PowerShell), kullanıcılar için lisans atamasını ve kaldırmayı yönetme. Çalışır durumda bırakın.

1. Yeni bir lisans grubu oluşturun (veya hangi mevcut grupların kullanılacağını belirleyin) ve gerekli tüm kullanıcıların üye olarak eklendiğinden emin olun.

1. Gerekli lisansları bu gruplara atayın; Amacınız, mevcut Otomasyonunuzu (örneğin, PowerShell) bu kullanıcılara uygulamak için aynı lisanslama durumunu yansıtmaktır.

1. Lisansların bu gruplardaki tüm kullanıcılara uygulandığını doğrulayın. Bu uygulama, her grup üzerinde işleme durumu denetlenerek ve denetim günlükleri kontrol edilirken yapılabilir.

   - Bireysel kullanıcılara lisans ayrıntılarına bakarak göz atın. Aynı lisanslara "doğrudan" ve "Devralınanlar" gruplarından atandığını görürsünüz.

   - [Lisansların kullanıcılara nasıl atandığını doğrulamak](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses)Için bir PowerShell betiği çalıştırabilirsiniz.

   - Aynı ürün lisansı kullanıcıya hem doğrudan hem de bir grup aracılığıyla atandığında, Kullanıcı tarafından yalnızca bir lisans tüketilecektir. Bu nedenle, geçiş yapmak için ek lisans gerekmez.

1. Hata durumundaki kullanıcılar için her grubu denetleyerek hiçbir lisans atamasının başarısız olmadığını doğrulayın. Daha fazla bilgi için bkz. [bir grup için lisans sorunlarını tanımlama ve çözme](licensing-groups-resolve-problems.md).

Özgün doğrudan atamaları kaldırmayı düşünün. Bunu kademeli olarak yapmanızı ve önce kullanıcıların bir alt kümesinde oluşan sonuçları izlemenizi öneririz. Kullanıcılara doğrudan özgün atamaları bırakıyorsanız, ancak kullanıcılar kendi lisanslı gruplarını bırakırlar, bu, istediğiniz gibi olmayabilir.

## <a name="an-example"></a>Örnek

Kuruluşun 1.000 kullanıcısı vardır. Tüm kullanıcılar Office 365 Enterprise E3 lisansları gerektirir. Şu anda kuruluşun şirket içinde çalışan bir PowerShell betiğinin olması, kullanıcılar tarafından geldiği ve gittiğinden lisansları ekleme ve kaldırma. Bununla birlikte, kuruluşun Azure AD tarafından otomatik olarak yönetilebilmesi için, bu betiği grup tabanlı lisanslama ile değiştirmek ister.

Geçiş işleminin şu şekilde görünebilecekleri aşağıda verilmiştir:

1. Azure portal kullanarak, Office 365 E3 lisansını Azure AD 'deki **tüm kullanıcılar** grubuna atayın.

1. Lisans atamasının tüm kullanıcılar için tamamlandığını onaylayın. Grubun genel bakış sayfasına gidin, **lisanslar**' ı seçin ve **lisanslar** dikey penceresinin en üstündeki işleme durumunu denetleyin.

   - İşlemin tamamlandığını onaylamak için "tüm kullanıcılara en son lisans değişiklikleri uygulandı" öğesini arayın.

   - Lisansları başarıyla atanmamış olabilecek tüm kullanıcılar hakkında bir bildirim bulun. Bazı kullanıcılar için lisanslardan çalıştırıldık mi? Bazı kullanıcılar, Grup lisanslarını devralmasını önleyen çakışan lisans planlarına sahip mi?

1. Tüm kullanıcılara hem doğrudan hem de grup lisanslarının uygulandığını doğrulamak için göz atın. Bir kullanıcının profil sayfasına gidin, **lisanslar**' ı seçin ve lisansların durumunu inceleyin.

   - Bu, geçiş sırasında beklenen Kullanıcı durumudur:

      ![geçiş sırasında beklenen Kullanıcı durumu](./media/licensing-groups-migrate-users/expected-user-state.png)

     Bu, kullanıcının hem doğrudan hem de devralınan lisanslarına sahip olduğunu onaylar. Office 365 E3 atandığını görüyoruz.

   - Hangi hizmetlerin etkin olduğunu görmek için her bir lisansı seçin. Doğrudan ve grup lisanslarının Kullanıcı için tam olarak aynı hizmetleri etkinleştirdiğini doğrulamak için **atamalar**' ı seçin.

1. Hem doğrudan hem de grup lisanslarının eşdeğer olduğunu doğruladıktan sonra, doğrudan lisansları kullanıcılardan kaldırmaya başlayabilirsiniz. Bunu portalda bireysel kullanıcılar için kaldırarak test edebilir ve sonra toplu olarak kaldırılmaları için Otomasyon betikleri çalıştırabilirsiniz. Portalda doğrudan lisanslarla kaldırılan aynı kullanıcıya bir örnek aşağıda verilmiştir. Lisans durumunun değişmeden kaldığı, ancak artık doğrudan atamaları görmediğine dikkat edin.

   ![doğrudan lisansların kaldırıldığını onaylayın](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Sonraki adımlar

Grup lisans yönetimi için diğer senaryolar hakkında daha fazla bilgi edinin:

- [Azure Active Directory 'de grup tabanlı lisanslama nedir?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Azure Active Directory'de gruba lisans atama](licensing-groups-assign.md)
- [Azure Active Directory'de grubun lisans sorunlarını tanımlama ve çözme](licensing-groups-resolve-problems.md)
- [Kullanıcılar Azure Active Directory'de Grup tabanlı lisanslama kullanarak ürün lisansları arasında geçirme](licensing-groups-change-licenses.md)
- [Azure Active Directory grup tabanlı lisanslamayla ilgili ek senaryolar](licensing-group-advanced.md)
- [Azure Active Directory'de Grup tabanlı lisanslama için PowerShell örnekleri](licensing-ps-examples.md)
