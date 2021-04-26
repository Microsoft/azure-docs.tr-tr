---
title: Azure statik Web Apps 'de özel etki alanı ayarlama
description: Özel bir etki alanını Azure statik Web Apps eşlemeyi öğrenin
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 578860883a108bba4b4bcd8cd04e8c08f484d474
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92173677"
---
# <a name="setup-a-custom-domain-in-azure-static-web-apps-preview"></a>Azure Static Web Apps Önizlemesinde özel etki alanı ayarlama

Varsayılan olarak, Azure statik Web Apps otomatik olarak oluşturulan bir etki alanı adı sağlar. Bu makalede bir Azure statik Web Apps uygulamasına özel bir etki alanı adı eşleme gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

- Satın alınan etki alanı adı
- Etki alanınız için DNS yapılandırma özelliklerine erişim

Etki alanı adlarını yapılandırırken, "A" kayıtları, kök etki alanlarını (örneğin, `example.com` ) BIR IP adresine eşlemek için kullanılır. DNS belirtimi bir etki alanının diğerine eşlenmeye izin vermediğinden, kök etki alanları doğrudan bir IP adresi ile eşlenmelidir.

## <a name="dns-configuration-options"></a>DNS yapılandırma seçenekleri

Bir uygulama için kullanılabilir birkaç farklı DNS yapılandırması türü vardır.

| İsterseniz | Ardından |
|--|--|
| Destek `www.example.com` veya `blog.example.net` | [CNAME kaydını eşleme](#map-a-cname-record) |
| Support `example.com` | [Kök etki alanı yapılandırma](#configure-a-root-domain) |
| Tüm alt etki alanlarını işaret edin `www.example.com` | [Joker karakterle eşleme](#map-a-wildcard-domain) |

## <a name="map-a-cname-record"></a>CNAME kaydını eşleme

Bir CNAME kaydı bir etki alanını diğerine eşler. CNAME kaydını, `www.example.com` `blog.example.com` ya da başka bir alt etki alanını Azure static Web Apps tarafından sunulan otomatik oluşturulan etki alanına eşlemek için kullanabilirsiniz.

1. [Azure Portal](https://portal.azure.com)'ı açın ve Azure hesabınızla oturum açın.

1. **Statik Web Apps** arama ve seçme

1. _Statik Web Apps_ sayfasında uygulamanızın adını seçin.

1. Menüdeki **özel etki alanları** ' na tıklayın.

1. **Ekle** düğmesine tıklayın

1. _Özel etki alanları_ penceresinde, **değer** alanına URL 'yi kopyalayın.

### <a name="configure-dns-provider"></a>DNS sağlayıcısını Yapılandır

1. Etki alanı sağlayıcınızın web sitesinde oturum açın.

2. DNS kayıtlarını yönetme sayfasını bulun. Her etki alanı sağlayıcısının kendi DNS kayıtları arabirimi vardır, bu nedenle sağlayıcının belgelerine başvurun. Sitede **Domain Name**, **DNS** veya **Name Server Management** etiketli alanları bulun.

3. Çoğunlukla, hesap bilgilerinizi görüntüleyip ardından **My domains** (Etki alanlarım) gibi bir bağlantı arayarak DNS kayıtları sayfasını bulabilirsiniz. Bu sayfaya gidin ve **bölge dosyasına**, **DNS kayıtlarına** veya **Gelişmiş yapılandırmaya** benzer adlı bir bağlantıyı arayın.

    DNS kayıtları sayfasının bir örneğini aşağıdaki ekran görüntüsünde görebilirsiniz:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Örnek DNS sağlayıcısı yapılandırması":::

4. Aşağıdaki değerlerle yeni bir **CNAME** kaydı oluşturun...

    | Ayar             | Değer                     |
    | ------------------- | ------------------------- |
    | Tür                | CNAME                     |
    | Ana bilgisayar                | www                       |
    | Değer               | Panodan yapıştırma |
    | TTL (varsa) | Varsayılan değer olarak bırak    |

5. Değişiklikleri DNS sağlayıcınızla kaydedin.

### <a name="validate-cname"></a>CNAME 'i doğrula

1. Azure portal _özel etki alanları_ penceresine geri dönün.

1. `www` _Özel etki alanını doğrulama_ bölümündeki bölüm dahil olmak üzere etki alanınızı girin.

1. **Doğrula** düğmesine tıklayın.

Özel etki alanı yapılandırıldığına göre, DNS sağlayıcısının dünyanın her yerindeki değişiklikleri yaymasını birkaç saat sürebilir. [Dnspropagation.net](https://dnspropagation.net)' a giderek yayılmanın durumunu kontrol edebilirsiniz. Dahil olmak üzere özel etki alanınızı girin `www` , açılan LISTEDEN CNAME ' i seçin ve **Başlat**' ı seçin.

DNS değişiklikleriniz doldurulduktan sonra, Web sitesi statik Web uygulamanızın otomatik olarak oluşturulan URL 'sini (örneğin, _Random-Name-123456789c.azurestaticapps.net_) döndürür.

## <a name="configure-a-root-domain"></a>Kök etki alanı yapılandırma

Kök etki alanları, etki alanınız, dahil olmak üzere tüm alt etki alanıdır `www` . Örneğin, için kök etki alanı `www.example.com` `example.com` . Bu, "TEPESINDE" etki alanı olarak da bilinir.

Önizleme sırasında kök etki alanı desteği mevcut olmasa da, statik bir Web uygulamasıyla kök etki alanı desteğini yapılandırma hakkında ayrıntılı bilgi için [Azure statik Web Apps 'da kök etki alanlarını yapılandırma](https://burkeholland.github.io/posts/static-app-root-domain) başlıklı blog gönderisine bakabilirsiniz.

## <a name="map-a-wildcard-domain"></a>Joker karakter etki alanını eşleme

Bazen başka bir etki alanına yönlendirmek için bir alt etki alanına gönderilen tüm trafiğin olmasını isteyebilirsiniz. Ortak bir örnek, tüm alt etki alanı trafiğini ile eşleştirmekte `www.example.com` . Bu şekilde,, yerine başka bir tür olsa da `w.example.com` `www.example.com` , istek öğesine gönderilir `www.example.com` .

### <a name="configure-dns-provider"></a>DNS sağlayıcısını Yapılandır

1. Etki alanı sağlayıcınızın web sitesinde oturum açın.

2. DNS kayıtlarını yönetme sayfasını bulun. Her etki alanı sağlayıcısının kendi DNS kayıtları arabirimi vardır, bu nedenle sağlayıcının belgelerine başvurun. Sitede **Domain Name**, **DNS** veya **Name Server Management** etiketli alanları bulun.

3. Çoğunlukla, hesap bilgilerinizi görüntüleyip ardından **My domains** (Etki alanlarım) gibi bir bağlantı arayarak DNS kayıtları sayfasını bulabilirsiniz. Bu sayfaya gidin ve **bölge dosyası**, **DNS kayıtları** veya **Gelişmiş yapılandırma** gibi adlı bir bağlantıyı arayın.

    DNS kayıtları sayfasının bir örneğini aşağıdaki ekran görüntüsünde görebilirsiniz:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Örnek DNS sağlayıcısı yapılandırması":::

4. Özel etki alanı adınızla değiştirerek aşağıdaki değerlerle yeni bir **CNAME** kaydı oluşturun `www.example.com` .

    | Ayar | Değer                  |
    | ------- | ---------------------- |
    | Tür    | CNAME                  |
    | Ana bilgisayar    | \*                     |
    | Değer   | www.example.com        |
    | TTL     | Varsayılan değer olarak bırak |

5. Değişiklikleri DNS sağlayıcınızla kaydedin.

Joker karakter etki alanı yapılandırıldığına göre, değişikliklerin dünya çapında yayılması birkaç saat sürebilir. [Dnspropagation.net](https://dnspropagation.net)' a giderek yayılmanın durumunu kontrol edebilirsiniz. Etki alanı özel etki alanınızı herhangi bir alt etki alanı (dışında `www` ) girin, açılan LISTEDEN CNAME ' i seçin ve **Başlat**' ı seçin.

DNS değişiklikleriniz doldurulduktan sonra, Web sitesi statik Web uygulamanız için yapılandırılmış özel etki alanınızı döndürür (örneğin, `www.example.com` ).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulama ayarlarını yapılandırma](application-settings.md)
