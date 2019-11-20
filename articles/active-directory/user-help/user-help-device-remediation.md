---
title: "\"Buradan alamıyoruz\" hatasını gidermeye yönelik düzeltmeler-Azure AD"
description: Neden "Buradan alamazsınız" hata iletisini aldığınızı öğrenmek için olası düzeltmeleri bulun.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lizross
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d6385343ed26e5043e1491529fd2454caff4d04
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028569"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>"Buradan alamazsınız" hata iletisiyle ilgili olası düzeltmeler

Kuruluşunuzun iç Web uygulamalarına veya hizmetlerine erişirken, **burada**bir hata iletisi alabilirsiniz. Bu ileti, kuruluşunuzun cihazın kuruluşunuzun kaynaklarına erişmesini engelleyen bir ilke yerleştirmediği anlamına gelir. Bu sorunu gidermek için yardım masanızla iletişim kurmaya gerek duyabilirsiniz, ancak öncelikle deneyebileceğiniz birkaç şey vardır.

## <a name="make-sure-youre-using-a-supported-browser"></a>Desteklenen bir tarayıcı kullandığınızdan emin olun
Burada, kuruluşunuzun sitelerine desteklenmeyen bir tarayıcıdan erişmeye çalıştığınızın ne olduğunu bildiren bir iletiyle **karşılaşamıyoruz** , çalıştırmakta olduğunuz tarayıcıyı kontrol edin.

![Tarayıcı desteğiyle ilgili hata iletisi](media/user-help-device-remediation/browser-version.png)

Bu sorunu gidermek için işletim sisteminize bağlı olarak desteklenen bir tarayıcı yükleyip çalıştırmanız gerekir. Windows 10 kullanıyorsanız, desteklenen tarayıcılar Microsoft Edge, Internet Explorer ve Google Chrome 'u içerir. Farklı bir işletim sistemi kullanıyorsanız, [Desteklenen tarayıcıların](../conditional-access/technical-reference.md#supported-browsers)tüm listesini kontrol edebilirsiniz.

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Desteklenen bir işletim sistemi kullandığınızdan emin olun
İşletim sisteminin desteklenen bir sürümünü çalıştırdığınızdan emin olun; örneğin:

- **Windows Istemcisi.** Windows 7 veya üzeri.

- **Windows Server.** Windows Server 2008 R2 veya üzeri.

- **MacOS.** macOS X veya üzeri

- **Android ve iOS.** Android ve iOS mobil işletim sistemlerinin en son sürümü

Bu sorunu gidermek için desteklenen bir işletim sistemi yükleyip çalıştırmanız gerekir.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Cihazınızın ağınıza katılmış olduğundan emin olun
Burada, cihazınızın kuruluşunuzun erişim ilkesiyle uyumlu olmadığını söyleyen bir iletiyle ilgili bilgi **alamazsanız** , cihazınızı kuruluşunuzun ağına katıldığınızdan emin olun.

![Ağınızda olup olmadığınız ile ilgili hata iletisi](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Cihazınızın ağınıza katılıp katılmadığını denetlemek için
1. İş veya okul hesabınızı kullanarak Windows 'da oturum açın. Örneğin, alain@contoso.com.

2. Sanal özel ağ (VPN) veya DirectAccess aracılığıyla kuruluşunuzun ağına bağlanın.

3. Bağlandıktan sonra, cihazınızı kilitlemek için **Windows logosu tuşu + L** tuşlarına basın.

4. İş veya okul hesabınızı kullanarak cihazınızın kilidini açın ve ardından sorunlu uygulamaya veya hizmete erişmeyi yeniden deneyin.

    Buradan bir hata iletisi **alırsanız** , **daha fazla ayrıntı** bağlantısını seçin ve ardından yardım masanızla iletişime geçin.

### <a name="to-join-your-device-to-your-network"></a>Cihazınızı ağınıza katmak için
Cihazınız kuruluşunuzun ağına katılmamışsa, iki işlemlerden birini yapabilirsiniz:

- **İş cihazınıza katın.** Potansiyel olarak kısıtlanmış kaynaklara erişebilmek için, iş sahibi Windows 10 cihazınızı kuruluşunuzun ağına katın. Daha fazla bilgi ve adım adım yönergeler için bkz. [iş cihazınızı kuruluşunuzun ağına ekleme](user-help-join-device-on-network.md).

- **Kişisel cihazınızı iş için kaydedin.** Kuruluşunuzun ağına, genellikle telefon veya tablet kişisel cihazınızı kaydedin. Cihazınız kaydolduktan sonra kuruluşunuzun kısıtlanmış kaynaklarına erişebilir. Daha fazla bilgi ve adım adım yönergeler için bkz. [Kişisel cihazınızı kuruluşunuzun ağına kaydetme](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Sonraki adımlar
- [Uygulamaps portalı nedir?](active-directory-saas-access-panel-introduction.md)

- [Parolanızla değil telefonunuzla oturum açma](user-help-auth-app-sign-in.md)
