---
title: Android 'den Windows sanal masaüstüne bağlanma-Azure
description: Android istemcisini kullanarak Windows sanal masaüstüne bağlanma.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: helohr
ms.openlocfilehash: 41b0c1ced9e66bd58d73683865b2c40afc16c5d3
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605862"
---
# <a name="connect-with-the-android-client"></a>Android istemcisiyle bağlanma

> İçin geçerlidir: Android 4,1 ve üzeri, Kmeos 53 ve üzeri ile Kmebooks.

>[!NOTE]
> Android istemcisinden Windows sanal masaüstü kaynaklarına erişme özelliği şu anda önizleme aşamasındadır.

Windows sanal masaüstü kaynaklarına, indirilebilir istemcimizi kullanarak Android cihazınızdan erişebilirsiniz. Google Play Store destekleyen Kmebook cihazlarında Android istemcisini de kullanabilirsiniz. Bu kılavuz, Android istemcisini nasıl ayarlayacağınızı bildirir.

## <a name="install-the-android-client"></a>Android istemcisini yükler

Kullanmaya başlamak için, istemcisini Android cihazınıza [indirin](https://play.google.com/store/apps/details?id=com.microsoft.rdc.android) ve yükleyin.

## <a name="subscribe-to-a-feed"></a>Bir akışa abone olma

Android cihazınıza erişebileceğiniz yönetilen kaynakların listesini almak için yöneticiniz tarafından sunulan akışa abone olun.

Bir akışa abone olmak için:

1. Bağlantı merkezi 'nde **+** ' a ve ardından **uzak kaynak akışı**' na dokunun.
2. Akış **URL 'si alanına AKıŞ** URL 'sini girin. Akış URL 'SI ya bir URL ya da bir e-posta adresi olabilir.
   - Bir URL kullanıyorsanız, yöneticinizin size verdiği bir URL 'yi kullanın, genellikle <https://rdweb.wvd.microsoft.com>.
   - E-posta kullanmak için e-posta adresinizi girin. Yöneticiniz sunucuyu bu şekilde yapılandırdıysa istemci, e-posta adresinizle ilişkili bir URL arar.
3. **İleri**' ye dokunun.
4. İstendiğinde kimlik bilgilerinizi sağlayın.
   - **Kullanıcı adı**için, Kullanıcı adına kaynaklara erişim izni verin.
   - **Parola**için Kullanıcı adıyla ilişkili parolayı verin.
   - Ayrıca, yöneticinizin kimlik doğrulamasını bu şekilde yapılandırdıysa ek etmenler sağlamanız istenebilir.

Abone olduktan sonra bağlantı merkezi, uzak kaynakları görüntülemelidir.

Akışa abone olduktan sonra akışın içeriği düzenli aralıklarla otomatik olarak güncelleştirilecek. Kaynaklar, yöneticiniz tarafından yapılan değişikliklere göre eklenebilir, değiştirilebilir veya kaldırılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Android istemcisini kullanma hakkında daha fazla bilgi edinmek için [Android istemcisini kullanmaya başlama](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-android)konusunu inceleyin.
