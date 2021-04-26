---
title: Web istemcisi ile Windows sanal masaüstüne bağlanma-Azure
description: Web istemcisini kullanarak Windows sanal masaüstüne bağlanma.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 80a090abee45f9cb3ec6ee5406aad6abf1d73a59
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725017"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Web istemcisiyle Windows sanal masaüstüne bağlanma

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/connect-web-2019.md)bakın.

Web istemcisi, Windows sanal masaüstü kaynaklarınıza, uzun yükleme işlemi olmadan bir Web tarayıcısından erişmenizi sağlar.

>[!NOTE]
>Web istemcisinde Şu anda mobil işletim sistemi desteği yok.

## <a name="supported-operating-systems-and-browsers"></a>Desteklenen işletim sistemleri ve tarayıcılar

HTML5 özellikli herhangi bir tarayıcı çalışması gerektiği sürece, aşağıdaki işletim sistemlerini ve tarayıcıları resmi olarak destekliyoruz.

| Tarayıcı           | Desteklenen İşletim Sistemi                     | Notlar               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Sürüm 11 veya üzeri |
| Apple Safari      | Mac OS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Sürüm 55 veya üzeri |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Uzak kaynak akışına erişin

Bir tarayıcıda, üzerinde Windows sanal masaüstü web istemcisinin Azure Resource Manager tümleşik sürümüne gidin <https://rdweb.wvd.microsoft.com/arm/webclient> ve Kullanıcı hesabınızla oturum açın.

>[!NOTE]
>Azure Resource Manager tümleştirme olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, <https://rdweb.wvd.microsoft.com/webclient> bunun yerine kaynaklarınıza bağlanın.
>
> US Gov portalını kullanıyorsanız, kullanın <https://rdweb.wvd.azure.us/arm/webclient/index.html> .

>[!NOTE]
>Windows sanal masaüstü için kullanmak istediğinden farklı bir Azure Active Directory hesabıyla oturum açtıysanız, oturumunuzu açmanız veya özel bir tarayıcı penceresi kullanmanız gerekir.

Oturum açtıktan sonra, artık kaynakların bir listesini görmeniz gerekir. Kaynakları, **tüm kaynaklar** sekmesindeki normal bir uygulama gibi seçerek de başlatabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Web istemcisini kullanma hakkında daha fazla bilgi edinmek için [Web istemcisini kullanmaya başlama](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)konusunu inceleyin.
