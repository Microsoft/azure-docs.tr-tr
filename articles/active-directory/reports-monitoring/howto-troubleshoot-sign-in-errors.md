---
title: Oturum açma hataları raporları sorunlarını giderme | Microsoft Docs
description: Azure portal Azure Active Directory raporlarını kullanarak oturum açma hatalarıyla ilgili sorunları giderme hakkında bilgi edinin
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec5fe7f62e8537a7f687202d365eb37d43b48b78
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008052"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Nasıl yapılır: Azure Active Directory raporlarını kullanarak oturum açma hatalarında sorun giderme

Azure Active Directory (Azure AD) içinde [oturum açma işlemleri raporu](concept-sign-ins.md) , kuruluşunuzda bulunan uygulamalara erişimi yönetme konusunda sorularınızın yanıtlarını bulmanıza olanak sağlar; örneğin:

- Belirli bir kullanıcının oturum açma düzeni nedir?
- Bir hafta içerisinde kaç adet kullanıcı oturum açtı?
- Bu açılan oturumların durumu nedir?


Ayrıca, oturum açma işlemleri raporu, kuruluşunuzdaki kullanıcılar için oturum açma hatalarıyla ilgili sorunları gidermenize de yardımcı olabilir. Bu kılavuzda, oturum açma başarısızlığının oturum açma raporundan nasıl yalıtılacağı ve hatanın kök nedenini anlamak için nasıl kullanılacağı hakkında bilgi edineceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Gerekenler:

* Premium (P1/P2) lisansına sahip bir Azure AD kiracısı. Azure Active Directory sürümünüzü yükseltmek için bkz. [Azure Active Directory Premium kullanmaya](../fundamentals/active-directory-get-started-premium.md) başlama.
* Kiracı için **genel yönetici**, **Güvenlik Yöneticisi**, **güvenlik okuyucusu**veya **rapor okuyucu** rolünde olan bir kullanıcı. Ayrıca, herhangi bir Kullanıcı kendi oturum açma işlemleri için de erişebilir. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Oturum açma hatalarıyla ilgili sorun giderme raporu

1. [Azure Portal](https://portal.azure.com) gidin ve dizininizi seçin.
2. **Azure Active Directory** ' yi seçin ve **Izleme** bölümünde **oturum açma** işlemlerini seçin. 
3. Kullanıcı adı ya da nesne tanımlayıcısı, uygulama adı veya tarih ile hatayı daraltmak için, belirtilen filtreleri kullanın. Ayrıca, yalnızca başarısız oturum açma işlemlerini göstermek için **durum** açılan listesinden **hata** ' ı seçin. 

    ![Sonuçları filtreleme](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Araştırmak istediğiniz başarısız oturum açma kimliğini belirler. Başarısız oturum açma hakkında daha fazla bilgi içeren ek ayrıntılar penceresini açmak için bunu seçin. **Oturum açma hata kodunu** ve **hata nedenini**göz önünde edin. 

    ![Kayıt Seç](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Bu bilgileri, Ayrıntılar penceresindeki **sorun giderme ve destek** sekmesinde de bulabilirsiniz.

    ![Sorun giderme ve destek](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. Hata nedeni hatayı açıklar. Örneğin, yukarıdaki senaryoda hata nedeni **Geçersiz Kullanıcı adı veya parola ya da şirket içi Kullanıcı adı veya parola geçersiz**. Düzeltme, doğru Kullanıcı adı ve parolayla yeniden oturum açmanız yeterlidir.

7. Hata kodunu arayarak düzeltme fikirleri de dahil olmak üzere ek bilgiler alabilirsiniz. Bu örnekte, [oturum açma hata kodları başvurusunda](reference-sign-ins-error-codes.md) **50126** . 

8. Tüm diğerleri başarısız olursa veya önerilen eylem kursu gerçekleşse de sorun devam ederse, **sorun giderme ve destek** sekmesindeki adımları izleyerek [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md) . 

## <a name="next-steps"></a>Sonraki adımlar

* [Oturum açma işlemleri hata kodları başvurusu](reference-sign-ins-error-codes.md)
* [Oturum açma raporuna genel bakış](concept-sign-ins.md)
