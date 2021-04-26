---
title: MacOS-Azure ' dan Windows sanal masaüstüne (klasik) bağlanma
description: MacOS istemcisini kullanarak Windows sanal masaüstüne (klasik) bağlanma.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 4014de262490259ed2e61eb17c80b40679e62773
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445219"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-macos-client"></a>MacOS istemcisiyle Windows sanal masaüstüne (klasik) bağlanma

> Uygulama hedefi: macOS 10,12 veya üzeri

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../connect-macos.md)bakın.

Windows sanal masaüstü kaynaklarına, macOS cihazlarınızdan indirilebilir istemcimizi kullanarak erişebilirsiniz. Bu kılavuz, istemcisini nasıl ayarlayacağınızı bildirir.

## <a name="install-the-client"></a>İstemciyi yükleme

Başlamak için, istemciyi macOS cihazınıza [indirin](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) ve yükleyin.

## <a name="subscribe-to-a-feed"></a>Bir akışa abone olma

Size, macOS cihazınızda kullanabileceğiniz yönetilen kaynakların listesini almak için yöneticinize verdiği akışa abone olun.

Bir akışa abone olmak için:

1. Hizmete bağlanmak ve kaynaklarınızı almak için ana sayfada **çalışma alanı Ekle** ' yi seçin.
2. Akış URL 'sini girin. Bu bir URL veya e-posta adresi olabilir:
   - Bir URL kullanıyorsanız, yöneticinizin size verdiği bir URL 'yi kullanın. Normalde, URL olur <https://rdweb.wvd.microsoft.com> .
   - E-posta kullanmak için e-posta adresinizi girin. Bu, yönetici sunucuyu bu şekilde yapılandırdıysa, istemciye e-posta adresinizle ilişkili bir URL aramasını söyler.
3. **Add (Ekle)** seçeneğini belirleyin.
4. İstendiğinde kullanıcı hesabınızla oturum açın.

Oturum açtıktan sonra, kullanılabilir kaynakların bir listesini görmeniz gerekir.

Bir akışa abone olduktan sonra, akışın içeriği düzenli aralıklarla otomatik olarak güncelleşmeyecektir. Kaynaklar, yöneticiniz tarafından yapılan değişikliklere göre eklenebilir, değiştirilebilir veya kaldırılabilir.

## <a name="next-steps"></a>Sonraki adımlar

MacOS istemcisi hakkında daha fazla bilgi edinmek için [MacOS istemcisini kullanmaya başlama](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) belgesine göz atın.