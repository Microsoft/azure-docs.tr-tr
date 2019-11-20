---
title: Zorlamalı tünel için App Service Ortamı Yapılandırma-Azure
description: App Service Ortamınızın giden trafiğe zorlamalı tünel uygulandığında çalışmasını etkinleştirme
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e80c0e4e57f8af067c17d0dcfefd26ce7ce8255f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069461"
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>App Service Ortamınızı zorlamalı tünel ile yapılandırma

App Service Ortamı (ASE), Azure App Service’in bir müşterinin Azure Sanal Ağındaki dağıtımıdır. Birçok müşteri, Azure sanal ağlarını VPN veya Azure ExpressRoute bağlantıları ile şirket içi ağlarının uzantıları olacak şekilde yapılandırır. İnternet’e bağlı trafiği bunun yerine VPN’nize veya sanal gerecinize yeniden yönlendirdiğinizde buna zorlamalı tünel denir. Sanal gereçler genellikle giden ağ trafiğini incelemek ve denetlemek için kullanılır. 

ATıCı, [App Service ortamı ağ mimarisi][network] belgesinde açıklanan çeşitli dış bağımlılıklara sahiptir. Normalde tüm ASE giden bağımlılık trafiği, ASE ile sağlanan VIP’den geçmelidir. Aşağıdaki bilgileri izlemeden ASE’ye giden veya ASE’den gelen trafik için yönlendirmeyi değiştirirseniz ASE’niz çalışmayı durdurur.

Bir Azure sanal ağında yönlendirme, en uzun ön ek eşleşmesi (LPM) temel alınarak yapılır. Aynı LPM eşleşmesine sahip birden fazla yol bulunuyorsa yol aşağıdaki sırayla ve kaynağına göre seçilir:

* Kullanıcı tanımlı yol (UDR)
* BGP yolu (ExpressRoute kullanıldığında)
* Sistem yolu

Bir sanal ağda yönlendirme hakkında daha fazla bilgi edinmek için [Kullanıcı tanımlı yolları ve IP iletmeyi][routes]okuyun. 

ASE giden trafiğinizi doğrudan İnternet yerine başka bir yere yönlendirmek isterseniz aşağıdaki seçenekleriniz vardır:

* ASE’nizin doğrudan İnternet erişimini sağlama
* ASE alt yağınızı BGP rotalarını yoksayacak şekilde yapılandırma
* ASE alt ağını, Azure SQL ve Azure Depolama’ya yönelik Hizmet Uç Noktalarını kullanacak şekilde yapılandırma
* ASE Azure SQL güvenlik duvarına kendi IP’lerinizi ekleme

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>App Service Ortamınızın doğrudan internet erişimine sahip olmasını sağlama

Azure sanal ağınız, ExpressRoute ile yapılandırılmış olsa bile ASE’nizin doğrudan İnternet'e gitmesini sağlamak için aşağıdakileri yapabilirsiniz:

* ExpressRoute’u 0.0.0.0/0 tanıtacak şekilde yapılandırın. Varsayılan olarak bu tüm şirket içi giden trafiği yönlendirir.
* 0\.0.0.0/0 adres öneki ve sonraki bir İnternet atlama türü ile UDR oluşturun ve bunu ASE alt ağına uygulayın.

Bu iki değişikliği yaparsanız, App Service Ortamı alt ağından çıkıp internet’i hedefleyen trafik, ExpressRoute bağlantısına zorlanmaz.

Ağ zaten şirket içi trafiği yönlendiriyorsa, ASE’nizi barındırmak ve ASE’yi dağıtmaya çalışmadan önce UDR’yi yapılandırmak için alt ağı oluşturmanız gerekir.  

> [!IMPORTANT]
> Bir UDR’de tanımlanan yollar, ExpressRoute yapılandırması tarafından tanıtılan herhangi bir yoldan öncelikli olacak kadar spesifik olmalıdır. Önceki örnekte geniş 0.0.0.0/0 adres aralığı kullanılır. Bu aralık, daha spesifik adres aralıkları kullanan yol tanıtımları tarafından yanlışlıkla geçersiz kılınabilir.
>
> App Service Ortamları, genel eşleme yolundan özel eşleme yoluna çapraz olarak tanıtılan ExpressRoute yapılandırmaları ile desteklenmez. Genel eşlemesi yapılandırılmış ExpressRoute yapılandırmaları, Microsoft’tan yol tanıtımları almaz. Reklamlar çok sayıda Microsoft Azure adres aralığı içerir. Adres aralıkları özel eşleme yolunda çapraz tanıtılırsa, App Service Ortamının alt ağından giden tüm ağ paketleri, bir müşterinin şirket içi ağ altyapısına yönlendirilir. Bu ağ akışı, App Service Ortamları ile varsayılan olarak desteklenmemektedir. Bu sorunun bir çözümü, genel eşleme yolundan özel eşleme yoluna yolların çapraz tanıtımını durdurmaktır. Başka bir çözüm ise App Service Ortamınızı bir zorlamalı tünel yapılandırmasında çalışacak şekilde etkinleştirmektir.

![Doğrudan İnternet erişimi][1]

## <a name="configure-your-ase-subnet-to-ignore-bgp-routes"></a>ASE alt yağınızı BGP rotalarını yoksayacak şekilde yapılandırma ## 

ASE alt yağınızı BGP rotalarını yoksayacak şekilde yapılandırabilirsiniz.  BGP yollarını yoksayacak şekilde yapılandırıldığında Ao, herhangi bir sorun olmadan bağımlılıklara erişebilir.  Ancak uygulamalarınızın şirket içi kaynaklara erişmesini sağlamak için UDR'ler oluşturmanız gerekir.

ASE alt yağınızı BGP rotalarını yoksayacak şekilde yapılandırmak için:

* Mevcut değilse bir UDR oluşturun ve ASE alt ağınıza atayın.
* Azure portalda ASE alt ağınıza atanmış olan rota tablosu arabirimini açın.  Yapılandırma'yı seçin.  BGP rota yayma özelliğini Devre dışı olarak ayarlayın.  Kaydet’e tıklayın. Bunu kapatma hakkındaki belgeler, [rota tablosu oluşturma][routetable] belgesi içinde yer alır.

Ao alt ağını tüm BGP yollarını yoksayacak şekilde yapılandırdıktan sonra, uygulamalarınız şirket içinde artık erişemez. Uygulamalarınızın Şirket içindeki kaynaklara erişmesini sağlamak için Age alt ağına atanan UDR 'yi düzenleyin ve şirket içi adres aralıklarınız için rotalar ekleyin. Sonraki atlama türünün Sanal ağ geçidi olarak ayarlanması gerekir. 


## <a name="configure-your-ase-with-service-endpoints"></a>Hizmet Uç Noktaları ile ASE’nizi yapılandırma ##

Azure SQL ve Azure Depolama’ya gidenler dışında, ASE’nizden çıkan tüm giden trafiği yönlendirmek için aşağıdaki adımları gerçekleştirin:

1. Bir rota tablosu oluşturun ve bunu ASE alt ağınıza atayın. [Yönetim adreslerini App Service ortamı][management]buradaki bölgenize göre eşleşen adresleri bulun. Sonraki İnternet atlaması olan adresler için rotalar oluşturun. Gelen yönetim trafiğinin App Service Ortamı gönderildiği adresten yanıt vermesi gerektiğinden bu yollar gereklidir.   

2. ASE alt ağınız ile Azure SQL ve Azure Depolama ile birlikte Hizmet Uç Noktalarını etkinleştirin.  Bu adım tamamlandıktan sonra zorlamalı tünel ile VNet’inizi yapılandırabilirsiniz.

Tüm şirket içi trafiği yönlendirmek için önceden yapılandırılmış bir sanal ağ üzerinde ASE’nizi oluşturmak için, kaynak yöneticisi şablonunu kullanarak ASE’nizi oluşturmanız gerekir.  Önceden mevcut olan bir alt ağ içinde portal ile ASE oluşturulması mümkün değildir.  Şirket içi giden trafiği yönlendirmek için önceden yapılandırılmış bir VNet’e ASE’nizi dağıtırken, önceden mevcut olan bir alt ağ belirtmenize olanak sağlayan bir kaynak yöneticisi şablonu kullanarak ASE’nizi oluşturmanız gerekir. Bir şablon ile ATıCı dağıtma hakkında ayrıntılı bilgi için, [şablon kullanarak App Service ortamı oluşturma][template]makalesini okuyun.

Hizmet Uç Noktaları, çok kiracılı hizmetlere erişimi bir dizi Azure sanal ağı ve alt ağı ile kısıtlamanızı sağlar. [Sanal ağ hizmeti uç noktaları][serviceendpoints] belgelerindeki hizmet uç noktaları hakkında daha fazla bilgi edinebilirsiniz. 

Bir kaynakta Hizmet Uç Noktalarını etkinleştirdiğinizde, diğer tüm yönlendiricilerden daha yüksek öncelikle oluşturulmuş rotalar vardır. ASE’ye zorlamalı tünel uygulanmış şekilde Hizmet Uç Noktalarını kullanırsanız, Azure SQL ve Azure Depolama yönetimi trafiğine zorlamalı tünel uygulanmaz. Diğer ASE bağımlılık trafiğine zorlamalı tünel uygulanır ve kaybedilemez; aksi takdirde ASE düzgün çalışmaz.

Azure SQL örneği içeren bir alt ağda Hizmet Uç Noktaları etkinleştirilirse, o alt ağa veya alt ağdan bağlanan tüm Azure SQL örnekleri için Hizmet Uç Noktaları etkinleştirilmiş olmalıdır. Aynı alt ağdan birden fazla Azure SQL örneğine erişmek istiyorsanız, tek bir Azure SQL örneğinde Hizmet Uç Noktalarını etkinleştirebilir, başka bir örnekte etkinleştiremezsiniz.  Azure Depolama, Azure SQL ile aynı şekilde hareket etmez.  Azure Depolama ile Hizmet Uç Noktalarını etkinleştirdiğinizde, alt ağınızdan o kaynağa erişimi kilitlersiniz, ancak Hizmet Uç Noktaları etkinleştirilmiş olmasa da diğer Azure Depolama hesaplarından erişmeye devam edebilirsiniz.  

Bir ağ filtresi gereci ile zorlamalı tüneli yapılandırırsanız, ASE’nin Azure SQL ve Azure Depolama’ya ek olarak bağımlılıklar içerdiğini unutmayın. Trafik bu bağımlılıklara engellenirse, Ao, düzgün çalışmaz.

![Hizmet uç noktaları ile zorlamalı tünel][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>ASE Azure SQL güvenlik duvarına kendi IP’lerinizi ekleme ##

Azure Depolama’ya gidenler dışında, ASE’nizden çıkan tüm giden trafiğe tünel uygulamak için aşağıdaki adımları gerçekleştirin:

1. Bir rota tablosu oluşturun ve bunu ASE alt ağınıza atayın. [Yönetim adreslerini App Service ortamı][management]buradaki bölgenize göre eşleşen adresleri bulun. Sonraki İnternet atlaması olan adresler için rotalar oluşturun. Gelen yönetim trafiğinin App Service Ortamı gönderildiği adresten yanıt vermesi gerektiğinden bu yollar gereklidir. 

2. ASE alt ağınız ile Azure Depolama ile birlikte Hizmet Uç Noktalarını etkinleştirme

3. App Service Ortamınızdan İnternet’e giden tüm trafik için kullanılacak adresleri alın. Şirket içi trafiği yönlendiriyorsanız bu adresler NAT veya ağ geçidi IP’lerinizdir. App Service Ortamı giden trafiğini bir NVA üzerinden yönlendirmek istiyorsanız, çıkış adresi NVA’nın genel IP’sidir.

4. _Mevcut bir App Service Ortamı çıkış adreslerini ayarlamak için:_ Resources.Azure.com adresine gidin ve abonelik/\<abonelik kimliği >/ResourceGroups/\<Ao kaynak grubu >/Providers/Microsoft.Web/hostingenvironments/menüsüne gidin\<atıcı adı > ' na gidin. Bundan sonra App Service Ortamınızı tanımlayan JSON dosyasını görebilirsiniz. Üst kısımda **read/write** ifadesinin gösterildiğinden emin olun. **Düzenle**’yi seçin. Ekranı en alta kadar kaydırın. **null** olan **userWhitelistedIpRanges** değerini aşağıdakine benzer bir değerle değiştirin. Çıkış adres aralığı olarak ayarlamak istediğiniz adresleri kullanın. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   Üst kısımdaki **PUT** öğesini seçin. Bu seçenek, App Service Ortamınızda bir ölçeklendirme işlemi başlatır ve güvenlik duvarını ayarlar.

_Çıkış adresleri ile atıcı 'nizi oluşturmak için_: [Şablonla App Service ortamı oluşturma][template] ve uygun şablonu çekme bölümündeki yönergeleri izleyin.  "Özellikler" bloğunda değil, azuredeploy.json dosyasındaki "kaynaklar" bölümünü düzenleyin ve **userWhitelistedIpRanges** için değerlerinizi içeren bir satır ekleyin.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/hostingEnvironments",
        "name": "[parameters('aseName')]",
        "kind": "ASEV2",
        "location": "[parameters('aseLocation')]",
        "properties": {
          "name": "[parameters('aseName')]",
          "location": "[parameters('aseLocation')]",
          "ipSslAddressCount": 0,
          "internalLoadBalancingMode": "[parameters('internalLoadBalancingMode')]",
          "dnsSuffix" : "[parameters('dnsSuffix')]",
          "virtualNetwork": {
            "Id": "[parameters('existingVnetResourceId')]",
            "Subnet": "[parameters('subnetName')]"
          },
        "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]
        }
      }
    ]

Bu değişiklikler, doğrudan ASE’de Azure Depolama’ya trafiği gönderir ve ASE VIP’si dışında ek adreslerden Azure SQL’e erişime izin verir.

   ![SQL beyaz listesi ile zorlamalı tünel][3]

## <a name="preventing-issues"></a>Sorunları önleme ##

ASE ile bağımlılıkları arasındaki iletişim koparsa ASE uygun olmayan duruma geçer.  Çok uzun süre uygun olmayan durumda kalırsa ASE askıya alınır. ASE’yi askıya alma işlemini kaldırmak için ASE portalınızdaki yönergeleri izleyin.

Yalnızca iletişimi koparmanın yanı sıra, çok fazla gecikmeye neden olarak da ASE’nizi olumsuz etkileyebilirsiniz. ASE’niz şirket içi ağınızdan çok uzaktaysa çok fazla gecikme oluşabilir.  Şirket içi ağa ulaşmak için okyanus veya kıta aşırı bir yere gitmek uzakta olmaya örnek verilebilir. İntranet tıkanması veya giden bant genişliği kısıtlamaları nedeniyle de gecikme süresi söz konusu olabilir.


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/asedependencies.png
[2]: ./media/forced-tunnel-support/forcedtunnelserviceendpoint.png
[3]: ./media/forced-tunnel-support/forcedtunnelexceptstorage.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[routetable]: ../../virtual-network/manage-route-table.md#create-a-route-table
