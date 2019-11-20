---
title: Azure 'da bir SMTP başlık denetimi için ters arama bölgelerini yapılandırma
titlesuffix: Azure Virtual Network
description: Azure 'da bir SMTP başlık denetimi için geriye doğru arama bölgelerinin nasıl yapılandırılacağını açıklar.
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 084fdb7f850f3819738a982127fa98efab114197
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059032"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Bir SMTP başlık denetimi için ters arama bölgelerini yapılandırma

Bu makalede, Azure DNS ' de ters bir bölgenin nasıl kullanılacağı ve SMTP başlık denetimi için ters DNS (PTR) kaydı oluşturma açıklanır.

## <a name="symptom"></a>Belirti

Microsoft Azure bir SMTP sunucusu barındırdığınızda, uzak posta sunucularından bir ileti gönderdiğinizde veya alırken aşağıdaki hata iletisini alabilirsiniz:

**554: PTR kaydı yok**

## <a name="solution"></a>Çözüm

Azure 'daki bir sanal IP adresi için, ters kayıtlar özel etki alanı bölgelerinde değil, Microsoft 'un sahip olduğu etki alanı bölgelerinde oluşturulur.

Microsoft 'a ait bölgelerde PTR kayıtlarını yapılandırmak için, Publicıpaddress kaynağında-Smarfqdn özelliğini kullanın. Daha fazla bilgi için bkz. [Azure 'da barındırılan hizmetler için ters DNS yapılandırma](../dns/dns-reverse-dns-for-azure-services.md).

PTR kayıtlarını yapılandırırken, IP adresinin ve ters FQDN 'nin aboneliğe ait olduğundan emin olun. Aboneliğe ait olmayan bir ters FQDN ayarlamaya çalışırsanız aşağıdaki hata iletisini alırsınız:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) Smarfqdn, abonelik kapsamındaki herhangi bir genel IP kaynağının FQDN 'siyle eşleşir;
    2) Smarfqdn, abonelik kapsamındaki herhangi bir genel IP kaynağının FQDN 'sine (CName kayıtları zinciri aracılığıyla) çözümleniyor;
    3) Abonelik altında statik bir genel IP kaynağının IP adresine (CName ve A kayıt zinciri aracılığıyla) çözümlenmektedir.

SMTP başlığını varsayılan ters FQDN 'umuza uyacak şekilde el ile değiştirirseniz, uzak posta sunucusu, SMTP başlık konağının etki alanı için MX kaydıyla eşleşmesini beklediği için yine de başarısız olabilir.
