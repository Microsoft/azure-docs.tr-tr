---
title: Azure 'da Application Gateway sorunlarını giderme – ıLB Ao | Microsoft Docs
description: Azure 'da App Service Ortamı bir Iç Load Balancer kullanarak bir uygulama ağ geçidinde sorun gidermeyi öğrenin
services: vpn-gateway
documentationCenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: 9c3216af283ebd9d84a5469d4d50d18c19f67534
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121946"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Arka uç sunucu sertifikası, bir uygulama ağ geçidi için bir App Service Ortamı ile Iç Load Balancer kullanılarak güvenilir bir şekilde listelenmiyor

Bu makale aşağıdaki sorunu giderir: Azure 'da uçtan uca SSL kullandığınızda bir Iç Load Balancer (ıLB) ile birlikte, arka uçta bir App Service Ortamı (ACE) kullanarak bir uygulama ağ geçidi oluşturduğunuzda bir sertifika daha fazla listelenmez.

## <a name="symptoms"></a>Belirtiler

Arka uçta Ao ile ıLB kullanarak bir uygulama ağ geçidi oluşturduğunuzda, arka uç sunucusu sağlıksız hale gelebilir. Bu sorun, uygulama ağ geçidinin kimlik doğrulama sertifikası arka uç sunucusundaki yapılandırılmış sertifikayla eşleşmezse oluşur. Örnek olarak aşağıdaki senaryoya bakın:

**Application Gateway yapılandırması:**

- **Oluşturulurken** Çok siteli
- **Bağ** 443
- **Ana bilgisayar adı:** test.appgwtestase.com
- **SSL sertifikası:** CN = test. appgwtestao. com
- **Arka uç havuzu:** IP adresi veya FQDN
- **IP adresi:** : 10.1.5.11
- **HTTP ayarları:** HTTPS
- **Bağlantı noktası:** : 443
- **Özel Araştırma:** Ana bilgisayar adı – test.appgwtestase.com
- **Kimlik doğrulama sertifikası:** . cer of test.appgwtestase.com
- **Arka uç sistem durumu:** Sağlıksız – arka uç sunucu sertifikası Application Gateway ile birlikte listelenmez.

**ATıCı yapılandırması:**

- **ILB IP 'SI:** 10.1.5.11
- **Etki alanı adı:** appgwtestase.com
- **App Service:** test.appgwtestase.com
- **SSL bağlama:** SNI SSL – CN = test. appgwtestao. com

Uygulama ağ geçidine eriştiğinizde, arka uç sunucusu sağlıksız olduğu için aşağıdaki hata iletisini alırsınız:

**502 – Web sunucusu bir ağ geçidi veya proxy sunucusu görevi gören geçersiz bir yanıt aldı.**

## <a name="solution"></a>Çözüm

Bir HTTPS Web sitesine erişmek için bir ana bilgisayar adı kullanmazsanız, SNı devre dışı bırakmak için arka uç sunucusu varsayılan Web sitesinde yapılandırılan sertifikayı döndürür. ILB AI için varsayılan sertifika ıLB sertifikasından gelir. ILB için yapılandırılmış bir sertifika yoksa, sertifika Ao uygulama sertifikasından gelir.

ILB 'ye erişmek için tam etki alanı adı (FQDN) kullandığınızda, arka uç sunucusu HTTP ayarlarında karşıya yüklenen doğru sertifikayı döndürür. Böyle bir durum söz konusu değilse, aşağıdaki seçenekleri göz önünde bulundurun:

- ILB 'nin IP adresini işaret etmek için uygulama ağ geçidinin arka uç havuzunda FQDN 'yi kullanın. Bu seçenek yalnızca özel bir DNS bölgeniz veya yapılandırılmış özel bir DNS varsa geçerlidir. Aksi takdirde, genel DNS için bir "A" kaydı oluşturmanız gerekir.

- HTTP ayarlarındaki ıLB 'deki veya varsayılan sertifikadaki (ıLB sertifikası) karşıya yüklenen sertifikayı kullanın. Uygulama ağ geçidi, araştırma için ıLB 'nin IP 'ye eriştiğinde sertifikayı alır.

- ILB ve arka uç sunucusunda bir joker sertifika kullanın, böylece tüm Web siteleri için sertifika ortak olur. Ancak bu çözüm, Web sitelerinin her biri farklı ana bilgisayar adları gerektirse de, yalnızca alt etki alanında mümkündür.

- ILB 'nin IP adresini kullanıyorsanız uygulama ağ geçidi için **App Service kullan** seçeneğini temizleyin.

Ek yükü azaltmak için, araştırma yolunu çalışır hale getirmek üzere HTTP ayarlarına ıLB sertifikasını yükleyebilirsiniz. (Bu adım yalnızca beyaz listeye yöneliktir. SSL iletişimi için kullanılmaz.) ILB sertifikasını HTTPS üzerindeki tarayıcınızdan IP adresiyle ve ardından bir Base-64 kodlu CER biçiminde dışarı aktararak ve sertifikayı ilgili HTTP ayarları üzerine yükleyerek alabilirsiniz.

## <a name="need-help-contact-support"></a>Yardım mı gerekiyor? Desteğe başvurun

Yine de yardıma ihtiyacınız varsa sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
