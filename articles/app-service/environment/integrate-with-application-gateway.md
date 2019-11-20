---
title: ILB App Service Ortamı Application Gateway ile tümleştirme-Azure
description: ILB App Service Ortamı bir uygulamayı Application Gateway ile tümleştirme hakkında izlenecek yol
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 090952a065b8c3b2b25ad737992b68b9bc9aa9ec
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069478"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>ILB App Service Ortamı Azure Application Gateway tümleştirin #

[App Service ortamı](./intro.md) , bir müşterinin Azure sanal ağının alt ağında Azure App Service bir dağıtımdır. Uygulama erişimi için ortak veya özel bir uç nokta ile dağıtılabilir. Özel bir uç nokta (yani, bir iç yük dengeleyici) ile App Service Ortamı dağıtımı ıLB App Service Ortamı olarak adlandırılır.  

Web uygulaması güvenlik duvarları, SQL 'leri, siteler arası komut dosyalarını engellemek için gelen Web trafiğini inceleyerek Web uygulamalarınızın güvenliğini sağlamaya yardımcı olur, kötü amaçlı yazılım yükleme & uygulama DDoS ve diğer saldırıları. Ayrıca, arka uç Web sunucularından gelen ve veri kaybı önleme (DLP) için yanıtları inceler. Azure Marketi 'nden bir WAF cihazı alabilir veya [azure Application Gateway][appgw]kullanabilirsiniz.

Azure Application Gateway, katman 7 Yük Dengeleme, SSL boşaltma ve Web uygulaması güvenlik duvarı (WAF) koruması sağlayan bir sanal gereç. Genel bir IP adresini dinleyebilir ve trafiği uygulama uç noktanıza yönlendirebilir. Aşağıdaki bilgiler, WAF tarafından yapılandırılmış bir uygulama ağ geçidinin ıLB App Service Ortamı bir uygulamayla nasıl tümleştirileceğini açıklar.  

ILB App Service Ortamı uygulama ağ geçidinin tümleştirilmesi uygulama düzeyindedir. Uygulama ağ geçidini ıLB App Service Ortamı ile yapılandırdığınızda, bunu ıLB App Service Ortamı özel uygulamalar için yapıyorsunuz. Bu teknik, tek bir ıLB App Service Ortamı güvenli çok kiracılı uygulamaların barındırılmasına izin verebilir.  

![Uygulama ağ geçidi bir ıLB App Service Ortamı uygulama işaret ediyor][1]

Bu bölümde şunları yapacaksınız:

* Azure Application Gateway oluşturun.
* Application Gateway, ıLB App Service Ortamı bir uygulamaya işaret etmek üzere yapılandırın.
* Uygulamanızı özel etki alanı adını uyacak şekilde yapılandırın.
* Uygulama ağ geçidinize işaret eden genel DNS ana bilgisayar adını düzenleyin.

## <a name="prerequisites"></a>Önkoşullar

Application Gateway ıLB App Service Ortamı ile tümleştirmek için şunlar gerekir:

* ILB App Service Ortamı.
* ILB App Service Ortamı çalışan bir uygulama.
* ILB App Service Ortamı uygulamanızdaki uygulamanızla birlikte kullanılacak internet yönlendirilebilir etki alanı adı.
* ILB App Service Ortamı kullandığı ıLB adresi. Bu bilgiler, **Ayarlar** > **IP adresleri**altındaki App Service ortamı portalında bulunur:

    ![ILB App Service Ortamı tarafından kullanılan IP adreslerinin örnek listesi][9]
    
* Daha sonra Application Gateway göstermek için kullanılan genel bir DNS adı. 

ILB App Service Ortamı oluşturma hakkında ayrıntılı bilgi için, bkz. [ılb App Service ortamı oluşturma ve kullanma][ilbase].

Bu makalede, App Service Ortamı dağıtıldığı aynı Azure sanal ağında Application Gateway istediğinizi varsayılmaktadır. Application Gateway oluşturmaya başlamadan önce, ağ geçidini barındırmak için kullanacağınız bir alt ağ seçin veya oluşturun. 

GatewaySubnet adlı bir alt ağ kullanmanız gerekir. Application Gateway GatewaySubnet 'e yerleştirirseniz, daha sonra bir sanal ağ geçidi oluşturabileceksiniz. 

Ayrıca, ağ geçidini ıLB App Service Ortamı tarafından kullanılan alt ağa koyamazsınız. Bu alt ağda olabilecek tek şey App Service Ortamı.

## <a name="configuration-steps"></a>Yapılandırma adımları ##

1. Azure Portal **Yeni** > ağApplicationGateway > ' e gidin.

2. **Temel bilgiler** alanında:

   a. **Ad**için Application Gateway adını girin.

   b. **Katman**Için **WAF**' yi seçin.

   c. **Abonelik**için App Service ortamı sanal ağın kullandığı aboneliği seçin.

   d. **Kaynak grubu**için kaynak grubunu oluşturun veya seçin.

   e. **Konum**için App Service ortamı sanal ağın konumunu seçin.

   ![Yeni Application Gateway oluşturma temelleri][2]

3. **Ayarlar** alanında:

   a. **Sanal ağ**için App Service ortamı sanal ağı seçin.

   b. **Alt ağ**için Application Gateway dağıtılması gereken alt ağı seçin. VPN ağ geçitlerinin oluşturulmasını önleyecağından GatewaySubnet kullanmayın.

   c. **IP adresi türü**için **genel**' i seçin.

   d. **Genel IP adresi**için BIR genel IP adresi seçin. Bir tane yoksa, şimdi bir tane oluşturun.

   e. **Protokol**için **http** veya **https**' yi seçin. HTTPS için yapılandırıyorsanız, bir PFX sertifikası sağlamanız gerekir.

   f. **Web uygulaması güvenlik duvarı**için güvenlik duvarını etkinleştirebilir ve ayrıca uygun gördüğünüz şekilde **algılama** veya **önleme** için ayarlayabilirsiniz.

   ![Yeni Application Gateway oluşturma ayarları][3]
    
4. **Özet** bölümünde ayarları gözden geçirin ve **Tamam**' ı seçin. Application Gateway, kurulumun tamamlanması 30 dakikadan uzun sürebilir.  

5. Application Gateway kurulumu tamamladıktan sonra, Application Gateway portala gidin. **Arka uç havuzu**seçin. ILB App Service Ortamı için ıLB adresini ekleyin.

   ![Arka uç havuzunu yapılandırma][4]

6. Arka uç havuzunuzu yapılandırma işlemi tamamlandıktan sonra, **sistem durumu araştırmaları**' nı seçin. Uygulamanız için kullanmak istediğiniz etki alanı adı için bir sistem durumu araştırması oluşturun. 

   ![Sistem durumu araştırmalarını yapılandırma][5]
    
7. Sistem durumu araştırmalarını yapılandırma işlemi tamamlandıktan sonra **http ayarları**' nı seçin. Var olan ayarları düzenleyin, **özel araştırma kullan**' ı seçin ve yapılandırdığınız araştırmayı seçin.

   ![HTTP ayarlarını yapılandırma][6]
    
8. Application Gateway **genel bakış** bölümüne gidin ve Application Gateway KULLANDıĞı genel IP adresini kopyalayın. Bu IP adresini, uygulama etki alanı adınız için bir kayıt olarak ayarlayın veya bir CNAME kaydında Bu adresin DNS adını kullanın. Genel IP adresini seçmek ve Application Gateway **genel** IP adresinin kullanıcı arabiriminden kopyalamak daha kolay bir hale gelir. 

   ![Application Gateway Portalı][7]

9. Uygulamanızın özel etki alanı adını ıLB App Service Ortamı ayarlayın. Portalda uygulamanıza gidin ve **Ayarlar**altında **özel etki alanları**' nı seçin.

   ![Uygulamada özel etki alanı adı ayarla][8]

Web uygulamalarınız için özel etki alanı adları ayarlama makalesindeki Web uygulamanız için özel etki alanı [][custom-domain]adları ayarlama hakkında bilgi vardır. Ancak, ıLB App Service Ortamı bir uygulama için etki alanı adında herhangi bir doğrulama yoktur. Uygulama uç noktalarını yöneten DNS sahibi olduğunuzdan istediğiniz her şeyi dilediğiniz gibi yerleştirebilirsiniz. Bu durumda eklediğiniz özel etki alanı adının DNS 'niz içinde olması gerekmez, ancak yine de uygulamanız ile yapılandırılması gerekir. 

Kurulum tamamlandıktan ve DNS değişikliklerinizin yaymaya yönelik kısa bir süre için izin verdikten sonra, oluşturduğunuz özel etki alanı adını kullanarak uygulamanıza erişebilirsiniz. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
