---
title: Windows sanal masaüstü ortamı-Azure
description: Konak havuzları ve uygulama grupları gibi bir Windows sanal masaüstü ortamının temel öğeleri hakkında bilgi edinin.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 2c598be641ee09773e4d56ebc485e6a2fbe49191
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446885"
---
# <a name="windows-virtual-desktop-environment"></a>Windows Sanal Masaüstü ortamı

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/environment-setup-2019.md)bakın.

Windows sanal masaüstü, kullanıcıların sanallaştırılmış masaüstlerine ve RemoteApps 'e kolay ve güvenli bir şekilde erişmesini sağlayan bir hizmettir. Bu konu, Windows sanal masaüstü ortamının genel yapısı hakkında biraz daha fazla bilgi sağlayacaktır.

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

- Bir kullanıcı aynı konak havuzundaki bir masaüstü uygulama grubuna ve RemoteApp uygulama grubuna atanabilir. Ancak, kullanıcılar her oturum için yalnızca bir uygulama grubu türü başlatabilir. Kullanıcılar tek bir oturumda aynı anda her iki tür uygulama grubunu da başlatamaz.
- Bir kullanıcı aynı konak havuzu içindeki birden çok uygulama grubuna atanabilir ve akışları her iki uygulama grubunun birikmesi olacaktır.

## <a name="workspaces"></a>Çalışma Alanları

Çalışma alanı, Windows sanal Masaüstündeki uygulama gruplarının mantıksal gruplandırmasıdır. Her Windows sanal masaüstü uygulaması grubu, kullanıcıların bu kullanıcılara yayınlanan uzak uygulamaları ve masaüstlerini görmesini sağlamak için bir çalışma alanı ile ilişkilendirilmesi gerekir.

## <a name="end-users"></a>Son kullanıcılar

Kullanıcıları uygulama gruplarına atadıktan sonra, Windows Sanal Masaüstü istemcilerinden herhangi birine sahip bir Windows sanal masaüstü dağıtımına bağlanabilirler.

## <a name="next-steps"></a>Sonraki adımlar

Temsilci erişimi hakkında daha fazla bilgi edinin ve [Windows sanal masaüstü 'Nde temsilci erişimi olan](delegated-access-virtual-desktop.md)kullanıcılara roller atama hakkında daha fazla bilgi edinin.

Windows sanal masaüstü konak havuzunuzu ayarlamayı öğrenmek için bkz. [Azure Portal bir konak havuzu oluşturma](create-host-pools-azure-marketplace.md).

Windows sanal masaüstüne nasıl bağlanacağınızı öğrenmek için aşağıdaki makalelerden birine bakın:

- [Windows 10 veya Windows 7 ile bağlanma](connect-windows-7-10.md)
- [Bir Web tarayıcısı ile bağlanma](connect-web.md)
- [Android istemcisiyle bağlanma](connect-android.md)
- [macOS istemcisiyle bağlanma](connect-macos.md)
- [iOS istemcisiyle bağlanma](connect-ios.md)