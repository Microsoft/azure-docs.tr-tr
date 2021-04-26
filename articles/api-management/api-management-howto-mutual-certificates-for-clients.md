---
title: API Management 'de istemci sertifikası kimlik doğrulaması kullanarak API 'Leri güvenli hale getirme
titleSuffix: Azure API Management
description: İstemci sertifikalarını kullanarak API 'lere erişimi güvenli hale getirme hakkında bilgi edinin. İlke ifadelerini, gelen sertifikaları doğrulamak için kullanabilirsiniz.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 553b4527796db3e5d0f430afd6c5e614626187e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988897"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API Management'ta istemci sertifikası kimlik doğrulamasını kullanarak API'lerin güvenliğini sağlama

API Management, istemci sertifikaları kullanarak API 'lere (yani API Management istemci) erişimi güvenli hale getirme yeteneği sağlar. İlke ifadelerini kullanarak, gelen sertifikayı doğrulayabilir ve istenen değerlere göre sertifika özelliklerini denetleyebilirsiniz.

İstemci sertifikaları kullanarak bir API 'nin arka uç hizmetine erişimin güvenliğini sağlama hakkında bilgi için (örneğin, arka uca API Management), bkz. [istemci sertifikası kimlik doğrulaması kullanarak arka uç hizmetlerini güvenli hale](./api-management-howto-mutual-certificates.md) getirme

> [!IMPORTANT]
> Geliştirici, temel, standart veya Premium katmanlarda HTTP/2 üzerinden istemci sertifikaları almak ve doğrulamak için aşağıda gösterildiği gibi "özel etki alanları" dikey penceresinde "istemci sertifikası anlaş" ayarını açmanız gerekir.

![İstemci sertifikası ile anlaş](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Tüketim katmanında istemci sertifikalarını almak ve doğrulamak için aşağıda gösterildiği gibi "özel etki alanları" dikey penceresinde "istemci sertifikası ıste" ayarını açmanız gerekir.

![İstemci sertifikası iste](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Veren ve Subject denetleniyor

Aşağıdaki ilkeler bir istemci sertifikasının vereni ve konusunu denetlemek için yapılandırılabilir:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Yerine sertifika iptal listesi kullanımını denetlemeyi devre dışı bırakmak için `context.Request.Certificate.VerifyNoRevocation()` `context.Request.Certificate.Verify()` .
> İstemci sertifikası kendinden imzalanmışsa, ve ' nin çalışması için API Management, kök (veya ara) CA sertifikalarının [yüklenmiş](api-management-howto-ca-certificates.md) olması gerekir `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` .

## <a name="checking-the-thumbprint"></a>Parmak izi denetleniyor

Aşağıdaki ilkeler bir istemci sertifikasının parmak izini denetlemek için yapılandırılabilir:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Yerine sertifika iptal listesi kullanımını denetlemeyi devre dışı bırakmak için `context.Request.Certificate.VerifyNoRevocation()` `context.Request.Certificate.Verify()` .
> İstemci sertifikası kendinden imzalanmışsa, ve ' nin çalışması için API Management, kök (veya ara) CA sertifikalarının [yüklenmiş](api-management-howto-ca-certificates.md) olması gerekir `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` .

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>API Management yüklenen sertifikalara yönelik parmak izi denetleniyor

Aşağıdaki örnek, API Management yüklenen sertifikalara karşı bir istemci sertifikasının parmak izini nasıl denet, gösterir:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Yerine sertifika iptal listesi kullanımını denetlemeyi devre dışı bırakmak için `context.Request.Certificate.VerifyNoRevocation()` `context.Request.Certificate.Verify()` .
> İstemci sertifikası kendinden imzalanmışsa, ve ' nin çalışması için API Management, kök (veya ara) CA sertifikalarının [yüklenmiş](api-management-howto-ca-certificates.md) olması gerekir `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` .

> [!TIP]
> Bu [makalede](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) açıklanan istemci sertifikası kilitlenmesi sorunu, tek bir şekilde kendi kendine bildirimde bulunabilir. Örneğin, istekler `403 Forbidden` dondurduktan sonra istek zaman aşımından sonra durum koduna neden `context.Request.Certificate` olur `null` . Bu sorun genellikle `POST` `PUT` yaklaşık 60 KB veya daha büyük içerik uzunluğuna sahip olan istekleri etkiler.
> Bu sorunun oluşmasını önlemek için, "özel etki alanları" dikey penceresindeki istenen ana bilgisayar adları için "istemci sertifikası anlaş" ayarını bu belgenin ilk görüntüsünde gösterildiği gibi etkinleştirin. Bu özellik, tüketim katmanında kullanılamaz.

## <a name="certificate-validation-in-self-hosted-gateway"></a>Şirket içinde barındırılan ağ geçidinde sertifika doğrulaması

[Şirket içinde barındırılan ağ geçidi](self-hosted-gateway-overview.md) görüntüsü varsayılan API Management, bir API Management örneğine yüklenen [CA kök sertifikalarını](api-management-howto-ca-certificates.md) kullanarak sunucu ve istemci sertifikalarının doğrulanmasını desteklemez. Şirket içinde barındırılan ağ geçidine özel bir sertifika sunan istemciler yavaş yanıt verebilir, çünkü sertifika iptal listesi (CRL) doğrulamasının ağ geçidinde zaman aşımına uğradığından uzun zaman alabilir. 

Ağ geçidini çalıştırırken geçici bir çözüm olarak, PKI IP adresini API Management örneği yerine localhost adresini (127.0.0.1) gösterecek şekilde yapılandırabilirsiniz. Bu, ağ geçidi istemci sertifikasını doğrulamaya çalıştığında CRL doğrulamanın hızlı bir şekilde başarısız olmasına neden olur. Ağ geçidini yapılandırmak için, kapsayıcıdaki dosyadaki localhost 'a çözülecek API Management örneğine bir DNS girişi ekleyin `/etc/hosts` . Ağ Geçidi dağıtımı sırasında bu girişi ekleyebilirsiniz:
 
* Docker dağıtımı için, `--add-host <hostname>:127.0.0.1` komutuna parametresini ekleyin `docker run` . Daha fazla bilgi için bkz. [kapsayıcı ana bilgisayar dosyasına giriş ekleme](https://docs.docker.com/engine/reference/commandline/run/#add-entries-to-container-hosts-file---add-host)
 
* Kubernetes dağıtımı için `hostAliases` yapılandırma dosyasına bir belirtim ekleyin `myGateway.yaml` . Daha fazla bilgi için bkz. [konak diğer adlarına sahip Pod/etc/konaklarına giriş ekleme](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/).




## <a name="next-steps"></a>Sonraki adımlar

-   [İstemci sertifikası kimlik doğrulaması kullanarak arka uç hizmetlerini güvenli hale getirme](./api-management-howto-mutual-certificates.md)
-   [Sertifikaları karşıya yükleme](./api-management-howto-mutual-certificates.md)
