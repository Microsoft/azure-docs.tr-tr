---
title: Windows sanal masaüstü ortamı-Azure
description: Windows sanal masaüstü ortamının temel öğeleri.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 23bf9be8e3e5f1c52546faa9ed5171c140eba59a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676617"
---
# <a name="windows-virtual-desktop-environment"></a>Windows Sanal Masaüstü ortamı

Windows sanal masaüstü, kullanıcıların sanallaştırılmış masaüstlerine ve RemoteApps 'e kolay ve güvenli bir şekilde erişmesini sağlayan bir hizmettir. Bu konu, Windows sanal masaüstü ortamının genel yapısı hakkında biraz daha fazla bilgi sağlayacaktır.

## <a name="tenants"></a>Kira

Windows sanal masaüstü kiracısı, Windows sanal masaüstü ortamınızı yönetmeye yönelik birincil arabirimdir. Her Windows sanal masaüstü kiracının, ortamda oturum açan kullanıcıları içeren Azure Active Directory ilişkilendirilmesi gerekir. Windows sanal masaüstü kiracısında, kullanıcılarınızın iş yüklerini çalıştırmak için konak havuzları oluşturmaya başlayabilirsiniz.

## <a name="host-pools"></a>Konak havuzları

Konak havuzu, Windows sanal masaüstü aracısını çalıştırdığınızda oturum ana bilgisayarları olarak Windows sanal masaüstüne kaydeden Azure sanal makineleri koleksiyonudur. Bir konak havuzundaki tüm oturum ana bilgisayarları sanal makineleri, tutarlı bir kullanıcı deneyimi için aynı görüntüden kaynaklıdır.

Bir konak havuzu iki türden biri olabilir:

- Her oturum ana bilgisayarının bireysel kullanıcılara atandığı kişisel.
- Oturum ana bilgisayarlarının, ana bilgisayar havuzu içindeki bir uygulama grubuna yetkili olan herhangi bir kullanıcının bağlantısını kabul edebileceği havuza alınmış.

Yük Dengeleme davranışını değiştirmek için konak havuzunda ek özellikler ayarlayabilirsiniz, her bir oturum ana bilgisayarının kaç oturumu yapabilecekleri ve kullanıcının Windows Sanal Masaüstü oturumlarında oturum açdıkları ana bilgisayar havuzundaki oturum konakları için neler yapabileceğini seçebilirsiniz. Uygulama grupları aracılığıyla kullanıcılara yayınlanan kaynakları kontrol edersiniz.

## <a name="app-groups"></a>Uygulama grupları

Uygulama grubu, ana bilgisayar havuzundaki oturum ana bilgisayarlarına yüklenen uygulamaların mantıksal gruplandırmasıdır. Bir uygulama grubu iki türden biri olabilir:

- Kullanıcılar, bireysel olarak seçtiğiniz ve uygulama grubuna yayımladığınız RemoteApps 'e erişen RemoteApp
- Kullanıcılar tam masaüstüne erişen Masaüstü

Varsayılan olarak, bir masaüstü uygulama grubu ("Masaüstü uygulama grubu" adlı), her bir konak havuzu oluşturduğunuzda otomatik olarak oluşturulur. Bu uygulama grubunu dilediğiniz zaman kaldırabilirsiniz. Ancak, bir masaüstü uygulama grubu varken konak havuzunda başka bir masaüstü uygulama grubu oluşturamazsınız. RemoteApps 'i yayımlamak için bir RemoteApp uygulama grubu oluşturmanız gerekir. Farklı çalışan senaryolarına uyum sağlamak için birden çok RemoteApp uygulama grubu oluşturabilirsiniz. Farklı RemoteApp uygulama grupları, çakışan RemoteApps de içerebilir.

Kaynakları kullanıcılara yayımlamak için, bunları uygulama gruplarına atamanız gerekir. Uygulama gruplarına kullanıcı atarken şunları göz önünde bulundurun:

- Bir kullanıcı aynı konak havuzundaki bir masaüstü uygulama grubuna ve RemoteApp uygulama grubuna atanamaz.
- Bir kullanıcı aynı konak havuzu içindeki birden çok uygulama grubuna atanabilir ve akışları her iki uygulama grubunun birikmesi olacaktır.

## <a name="tenant-groups"></a>Kiracı grupları

Windows sanal masaüstü 'nde, Windows sanal masaüstü kiracısı, çoğu kurulum ve yapılandırmanın gerçekleştiği yerdir. Windows sanal masaüstü kiracısı konak havuzlarını, uygulama gruplarını ve uygulama grubu Kullanıcı atamalarını içerir. Ancak, özellikle bir bulut hizmeti sağlayıcısı (CSP) veya barındırma iş ortağıysanız, birden çok Windows sanal masaüstü kiracıyı aynı anda yönetmeniz gereken bazı durumlar olabilir. Bu durumlarda, müşterilerin Windows sanal masaüstü kiracılarının her birini yerleştirmek ve erişimi merkezi olarak yönetmek için özel bir Windows sanal masaüstü kiracı grubu kullanabilirsiniz. Ancak, yalnızca tek bir Windows sanal masaüstü kiracısı yönetiyorsanız, kiracı grubu kavramı uygulanmaz ve varsayılan kiracı grubunda bulunan kiracınızı çalıştırmaya ve yönetmeye devam edebilirsiniz.

## <a name="end-users"></a>Son kullanıcılar

Kullanıcıları uygulama gruplarına atadıktan sonra, Windows Sanal Masaüstü istemcilerinden herhangi birine sahip bir Windows sanal masaüstü dağıtımına bağlanabilirler.

## <a name="next-steps"></a>Sonraki adımlar

Temsilci erişimi hakkında daha fazla bilgi edinin ve [Windows sanal masaüstü 'Nde temsilci erişimi olan](delegated-access-virtual-desktop.md)kullanıcılara roller atama hakkında daha fazla bilgi edinin.

Windows sanal masaüstü kiracınızı ayarlamayı öğrenmek için bkz. [Windows sanal masaüstü 'nde kiracı oluşturma](tenant-setup-azure-active-directory.md).

Windows sanal masaüstüne nasıl bağlanacağınızı öğrenmek için aşağıdaki makalelerden birine bakın:

- [Windows 10 veya Windows 7 ' den bağlan](connect-windows-7-and-10.md)
- [Web tarayıcısından Bağlan](connect-web.md)
