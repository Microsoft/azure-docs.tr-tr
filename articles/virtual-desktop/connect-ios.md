---
title: İOS 'tan Windows sanal masaüstüne bağlanma-Azure
description: İOS istemcisini kullanarak Windows sanal masaüstüne bağlanma.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: bfc7efa6e8ead3b53704e3c9bd189b18cb787618
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605842"
---
# <a name="connect-with-the-ios-client"></a>iOS istemcisiyle bağlanma

> Uygulama hedefi: iOS 8,0 veya üzeri. İPhone, iPad ve iPod Touch ile uyumludur.

>[!NOTE]
> İOS istemcisi şu anda hala önizlemededir.

Windows sanal masaüstü kaynaklarına, indirilebilir istemcimiz ile iOS cihazınızdan erişebilirsiniz. Bu kılavuz, iOS istemcisini nasıl ayarlayacağınızı bildirir.

## <a name="install-the-ios-beta-client"></a>İOS Beta istemcisini yükler
İOS Beta istemcisini yüklemek için:

1. [Apple Testuçuş](https://apps.apple.com/us/app/testflight/id899247664) uygulamasını iOS cihazınıza yükler.
2. İOS cihazınızda bir tarayıcı açın ve [aka.MS/rdiosbeta](https://aka.ms/rdiosbeta)adresine gidin.
3. **2. Adım: Beta 'A katılarak**, **Testi Başlat**' ı seçin.
4. Testuçuş uygulamasına yeniden yönlendirildiğinde **kabul et**' i seçin ve ardından **Install**' ı seçin.

## <a name="subscribe-to-a-feed"></a>Bir akışa abone olma

İOS cihazınıza erişebileceğiniz yönetilen kaynakların listesini almak için yöneticiniz tarafından sunulan akışa abone olun.

Bir akışa abone olmak için:

1. Bağlantı merkezi 'nde **+** ' a ve ardından **çalışma alanı Ekle**' ye dokunun.
2. Akış **URL 'si alanına AKıŞ** URL 'sini girin. Akış URL 'SI ya bir URL ya da bir e-posta adresi olabilir.
   - Bir URL kullanıyorsanız, yöneticinizin size verdiği bir URL 'yi kullanın. Genellikle, URL <https://rdweb.wvd.microsoft.com>.
   - E-posta kullanmak için e-posta adresinizi girin. Bu, yönetici sunucuyu bu şekilde yapılandırdıysa, istemciye e-posta adresinizle ilişkili bir URL aramasını söyler.
3. **İleri**' ye dokunun.
4. İstendiğinde kimlik bilgilerinizi sağlayın.
   - **Kullanıcı adı**için, Kullanıcı adına kaynaklara erişim izni verin.
   - **Parola**için Kullanıcı adıyla ilişkili parolayı verin.
   - Ayrıca, yöneticinizin kimlik doğrulamasını bu şekilde yapılandırdıysa ek etmenler sağlamanız istenebilir.
5. **Kaydet**' e dokunun.

Bundan sonra bağlantı merkezi, uzak kaynakları görüntülemelidir.

Akışa abone olduktan sonra akışın içeriği düzenli aralıklarla otomatik olarak güncelleştirilecek. Kaynaklar, yöneticiniz tarafından yapılan değişikliklere göre eklenebilir, değiştirilebilir veya kaldırılabilir.

## <a name="next-steps"></a>Sonraki adımlar

İOS Beta istemcisinin kullanımı hakkında daha fazla bilgi edinmek için [iOS istemci](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios) belgelerini kullanmaya başlama konusunu inceleyin.
