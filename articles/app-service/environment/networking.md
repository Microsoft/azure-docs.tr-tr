---
title: App Service Ortamı ağı
description: App Service Ortamı ağ ayrıntıları
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0a1221a8de10fd18768a1a0f0ac08277dc2901d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735042"
---
# <a name="app-service-environment-networking"></a>App Service Ortamı ağı

> [!NOTE]
> Bu makale, App Service Ortamı v3 (Önizleme) ile ilgilidir
> 

App Service Ortamı (Ao), Web uygulamaları, API uygulamaları ve işlev uygulamaları barındıran Azure App Service tek bir kiracı dağıtımsıdır. Bir ASE yüklediğinizde, dağıtılmasını istediğiniz Azure sanal ağını (VNet) seçin. Tüm gelen ve giden trafik uygulaması belirttiğiniz VNet içinde olacaktır.  

ASEv3 iki alt ağ kullanır.  Gelen trafiği işleyen özel uç nokta için bir alt ağ kullanılır. Bu, önceden var olan bir alt ağ veya yeni bir tane olabilir.  Gelen alt ağ, Özel uç noktadan başka amaçlar için de kullanılabilir. Giden alt ağ yalnızca Ao 'dan giden trafik için kullanılabilir. AıN giden alt ağında başka hiçbir şey olamaz.

## <a name="addresses"></a>Adresler 
ATıCı, oluşturma sırasında aşağıdaki adreslere sahiptir:

| Adres türü | açıklama |
|--------------|-------------|
| Gelen adres | Gelen adres, ATıCı 'niz tarafından kullanılan özel uç nokta adresidir. |
| Giden alt ağ | Giden alt ağ da Ao alt ağıdır. Önizleme sırasında bu alt ağ yalnızca giden trafik için kullanılır. |
| Windows giden adresi | Bu Ao 'daki Windows uygulamaları, internet 'e giden çağrılar yaparken varsayılan olarak bu adresi kullanır. |
| Linux giden adresi | Bu Ao 'nun içindeki Linux uygulamaları, internet 'e giden çağrılar yaparken varsayılan olarak bu adresi kullanır. |

ASEv3, ASE portalının **IP adresleri** bölümünde ASE tarafından kullanılan adresler hakkında ayrıntılı bilgiler içerir.

![Ao adresler Kullanıcı arabirimi](./media/networking/networking-ip-addresses.png)

Ao tarafından kullanılan özel uç noktasını silerseniz Ao 'inizdeki uygulamalara ulaşıamazsınız.  

ATıCı, Ao tarafından kullanılan altyapıyı desteklemek için giden alt ağdaki adresleri kullanır. App Service planlarınızı Ao uygulamanızda ölçeklendirirseniz, daha fazla adres kullanacaksınız. Ao 'daki uygulamalar giden alt ağda özel adresler içermez. Bir uygulama tarafından giden alt ağdaki bir uygulama tarafından kullanılan adresler zamanla değişecektir.

## <a name="ports"></a>Bağlantı noktaları

ATıCı, 80 ve 443 bağlantı noktalarında uygulama trafiğini alır.  ATıCı için gelen veya giden bağlantı noktası gereksinimi yoktur. 

## <a name="extra-configurations"></a>Ek yapılandırma

ASEv2 aksine, ASEv3 ile ağ güvenlik grupları (NSG 'ler) ve yol tabloları (UDRs), kısıtlama olmadan uygun gördüğünüz gibi ayarlayabilirsiniz. ATıCı 'den bir NVA cihazına giden tüm trafiği tünele zorlamak istiyorsanız. WAF cihazlarını, AP 'nize tüm gelen trafiğin önüne koyabilirsiniz. 

## <a name="dns"></a>DNS

ASE 'deki uygulamalar, sanal ağınızın yapılandırıldığı DNS 'i kullanacaktır. DNS sunucunuzu AŞIRINIZI işaret etmek üzere yapılandırmak için [App Service ortamı kullanma](./using.md#dns-configuration) yönergelerini izleyin. Bazı uygulamaların sanal ağınızın yapılandırılmış olandan farklı bir DNS sunucusu kullanmasını istiyorsanız, uygulama ayarları WEBSITE_DNS_SERVER ve WEBSITE_DNS_ALT_SERVER ile uygulama başına el ile ayarlayabilirsiniz. WEBSITE_DNS_ALT_SERVER uygulama ayarı ikincil DNS sunucusunu yapılandırır. İkincil DNS sunucusu yalnızca birincil DNS sunucusundan yanıt olmadığında kullanılır. 

## <a name="preview-limitation"></a>Önizleme sınırlaması

ASEv3 ile kullanılamayan birkaç ağ özelliği vardır.  ASEv3 ' de kullanılamayan şeyler şunlardır:

• FTP • uzaktan hata ayıklama • dış yük dengeleyici dağıtımı • kapsayıcı dağıtımları için bir özel kapsayıcı kayıt defterine erişme özelliği • Global olarak eşlenmiş sanal ağlara çağrı yapma yeteneği • bir hizmet uç noktası veya özel uç nokta güvenliği depolama hesabı ile yedekleme/geri yükleme olanağı • hizmet uç noktası veya özel uç nokta güvenli Anahtar Kasası hesaplarında uygulama ayarlarına Anahtar Kasası başvuruları sağlamak • KCG 'yi bir hizmet uç noktasına veya özel uç nokta güvenliği depolama hesabına kullanabilme • ağ Izleyicisi veya giden trafik üzerinde NSG akışı kullanımı
    
    