---
title: Windows sanal masaüstü MALTı uygulama iliştirme SSS-Azure
description: Windows sanal masaüstü için MSIX uygulama iliştirme hakkında sık sorulan sorular.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 6057f4a76f274e34b036ea352a3691b34d24b3a1
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835371"
---
# <a name="msix-app-attach-faq"></a>MSIX uygulama iliştirme SSS

Bu makalede, Windows sanal masaüstü için MSIX uygulama iliştirme hakkında sık sorulan sorular yanıtlanmaktadır.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Maltıve MSIX uygulama iliştirme arasındaki fark nedir?

Msix, uygulamalar için bir paketleme biçimidir, ancak MSIX uygulama iliştirme, dağıtımınıza MSIX paketi sunan özelliktir.

## <a name="does-msix-app-attach-use-fslogix"></a>MSIX uygulaması iliştirme FSLogix kullanıyor mu?

MSIX uygulaması iliştirme FSLogix kullanmaz. Ancak, MALTıUYGULAMA iliştirme ve FSLogix, sorunsuz bir kullanıcı deneyimi sağlamak için birlikte çalışacak şekilde tasarlanmıştır.

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>MALTı uygulamayı Windows sanal masaüstü dışında kullanıma açabilir miyim?

Power MALTı uygulama iliştirmeye yönelik API 'Ler Windows 10 Enterprise için kullanılabilir. Bu API 'Ler, Windows sanal masaüstü dışında kullanılabilir. Ancak, MSIX uygulaması için Windows sanal masaüstü dışında ek bir yönetim düzlemi yoktur.

## <a name="how-do-i-get-an-msix-package"></a>Nasıl yaparım? MSIX paketi mi kullanıyorsunuz?

Yazılım satıcınız size bir MSIX paketi verecektir. Maltısız paketleri de MALTıYA dönüştürebilirsiniz. [Mevcut yükleyiclerinizi MALTıYA taşıma hakkında](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)daha fazla bilgi edinin.

## <a name="which-operating-systems-support-msix-app-attach"></a>Hangi işletim sistemleri MSIX uygulamasını destekliyor?

Windows 10 Enterprise ve Windows 10 Enterprise multi-session, sürüm 2004 veya üzeri.

## <a name="is-msix-app-attach-currently-generally-available"></a>MALTı uygulama iliştirme Şu anda genel olarak kullanılabilir mi?

MSIX uygulama iliştirme, Windows 10 Enterprise ve Windows 10 Enterprise multi-session, sürüm 2004 veya üzeri bir parçasıdır. Her iki işletim sistemi şu anda genel kullanıma sunulmuştur. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>MALTı uygulamayı Windows sanal masaüstü dışında kullanıma açabilir miyim?

MSIX ve MSIX uygulama iliştirme API 'Leri, Windows 10 Enterprise ve Windows 10 Enterprise multi-session, sürüm 2004 ve üzeri bir parçasıdır. Şu anda, Windows sanal masaüstü dışında MALTıK uygulama iliştirme için yönetim yazılımı sağlamayız.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>Aynı uygulamanın iki sürümünü aynı anda çalıştırabilir miyim?

Aynı MSIX uygulamasının aynı anda çalışacağı iki sürümü için, appxmanifest.xml dosyasında tanımlanan MSIX paket ailesi her uygulama için farklı olmalıdır.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>MSIX uygulama iliştirme kullanılırken otomatik güncelleştirmeyi devre dışı mıyım?

Evet. MSIX uygulaması iliştirme, MSIX uygulamaları için otomatik güncelleştirmeyi desteklemez.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>İzinler MALTı uygulama iliştirme ile nasıl çalışır?

MSIX uygulama iliştirme kullanan bir konak havuzundaki tüm sanal makinelerin (VM 'Ler), MSIX görüntülerinin depolandığı dosya paylaşımında okuma izinleri olmalıdır. Azure dosyaları da kullanıyorsa, bunlara hem rol tabanlı erişim denetimi (RBAC) hem de yeni teknoloji dosya sistemi (NTFS) izinleri verilmelidir.

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>HTTP veya HTTPs için MSIX uygulama iliştirmeyi kullanabilir miyim?

HTTP veya HTTPs üzerinden MSIX uygulama iliştirme kullanımı Şu anda desteklenmiyor.

## <a name="can-i-restage-the-same-msix-application"></a>Aynı MALTıLıK uygulamayı yeniden alabilir miyim?

Evet. Zaten restaged olan uygulamaları daha sonra hatalara neden olmaz.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>MSIX uygulaması iliştirme otomatik olarak imzalanan sertifikaları destekliyor mu?

Evet. Otomatik olarak imzalanan sertifikayı barındırmak için MSIX uygulama eklemenin kullanıldığı tüm oturum ana bilgisayar VM 'lerine otomatik olarak imzalanan sertifikayı yüklemeniz gerekir.

## <a name="what-applications-can-i-repackage-to-msix"></a>Hangi uygulamaları MALTıYA yeniden paketleyebilir?

Her uygulama, işletim sistemi, programlama dili ve çerçeveler için farklı özellikler kullanır. Uygulamanızı yeniden paketlemek için, [mevcut yükleyiclerinizi maltıya taşıma](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)bölümündeki yönergeleri izleyin. Bir uygulamayı, [bir masaüstü uygulamasını paketlemek üzere hazırlama](/windows/msix/desktop/desktop-to-uwp-prepare)sırasında yeniden paketlemek için ihtiyacınız olan işlerin bir listesini bulabilirsiniz. 

Bazı uygulamalar Application katmanlı olamaz, bu da bir MSIX dosyasına yeniden paketlenemeyeceği anlamına gelir. Yeniden paketlenemeyecek uygulamaların listesi aşağıdadır:

- Sürücüler 
- Active-X veya Silverlight
- VPN istemcileri
- Virüsten koruma programları

## <a name="next-steps"></a>Sonraki adımlar

MSIX uygulama iliştirme hakkında daha fazla bilgi edinmek istiyorsanız [genel bakış](what-is-app-attach.md) ve [Sözlükte](app-attach-glossary.md)göz atın. Aksi takdirde, [uygulama eklemeyi ayarlama](app-attach.md)ile çalışmaya başlayın.
