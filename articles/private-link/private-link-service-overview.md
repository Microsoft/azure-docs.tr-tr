---
title: Azure özel bağlantı hizmeti nedir?
description: Azure özel bağlantı hizmeti hakkında bilgi edinin.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 7983a80da8a5ca9d900e44515b5e078cc9d70d79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684195"
---
# <a name="what-is-azure-private-link-service"></a>Azure özel bağlantı hizmeti nedir?

Azure özel bağlantı hizmeti, Azure özel bağlantısı tarafından desteklenen kendi hizmetinize yapılan başvurudur. [Azure Standart Load Balancer](../load-balancer/load-balancer-overview.md) arkasında çalışan hizmetiniz, özel bağlantı erişimi için etkinleştirilebilir ve böylece hizmetinize ait tüketiciler kendi sanal ağlarına özel olarak erişebilir. Müşterileriniz, sanal ağı içinde özel bir uç nokta oluşturabilir ve bu hizmetle eşleşmekte olabilir. Bu makalede hizmet sağlayıcı tarafı ile ilgili kavramlar açıklanmaktadır. 

:::image type="content" source="./media/private-link-service-overview/consumer-provider-endpoint.png" alt-text="Özel bağlantı hizmeti iş akışı" border="true":::

*Şekil: Azure özel bağlantı hizmeti.*

## <a name="workflow"></a>İş akışı

![Özel bağlantı hizmeti iş akışı](media/private-link-service-overview/private-link-service-workflow.png)


*Şekil: Azure özel bağlantı hizmeti iş akışı.*

### <a name="create-your-private-link-service"></a>Özel bağlantı hizmetinizi oluşturma

- Uygulamanızı sanal ağınızda standart yük dengeleyici arkasında çalışacak şekilde yapılandırın. Uygulamanız zaten standart yük dengeleyici 'nin arkasında yapılandırılmışsa, bu adımı atlayabilirsiniz.   
- Yukarıdaki yük dengeleyiciye başvuran bir özel bağlantı hizmeti oluşturun. Yük dengeleyici seçim sürecinde, trafiği almak istediğiniz ön uç IP yapılandırmasını seçin. Özel bağlantı hizmeti için NAT IP adresleri için bir alt ağ seçin. Alt ağda en az sekiz NAT IP adresinin kullanılabilir olması önerilir. Tüm tüketici trafiği, bu özel IP adresi havuzundan hizmet sağlayıcısına geliyor gibi görünür. Özel bağlantı hizmeti için uygun özellikleri/ayarları seçin.    

    > [!NOTE]
    > Azure özel bağlantı hizmeti yalnızca Standart Load Balancer desteklenir. 
    
### <a name="share-your-service"></a>Hizmetinizi paylaşma

Özel bir bağlantı hizmeti oluşturduktan sonra Azure, hizmetiniz için sağladığınız adı temel alan "diğer ad" adlı bir genel benzersiz adlı bilinen ad oluşturur. Müşterilerinizin diğer adını veya Kaynak URI 'sini müşterilerinizle çevrimdışı olarak paylaşabilirsiniz. Tüketiciler, diğer adı veya Kaynak URI 'sini kullanarak bir özel bağlantı bağlantısı başlatabilir.
 
### <a name="manage-your-connection-requests"></a>Bağlantı isteklerinizi yönetme

Bir tüketici bir bağlantıyı başlattıktan sonra, hizmet sağlayıcı bağlantı isteğini kabul edebilir veya reddedebilir. Tüm bağlantı istekleri özel bağlantı hizmetindeki **privateendpointconnections** özelliği altında listelenecektir.
 
### <a name="delete-your-service"></a>Hizmetinizi silme

Özel bağlantı hizmeti artık kullanımda değilse, bunu silebilirsiniz. Ancak, hizmeti silmeden önce kendisiyle ilişkili özel uç nokta bağlantısı olmadığından emin olun. Tüm bağlantıları reddedebilir ve hizmeti silebilirsiniz.

## <a name="properties"></a>Özellikler

Özel bir bağlantı hizmeti aşağıdaki özellikleri belirtir: 

|Özellik |Açıklama  |
|---------|---------|
|Sağlama durumu (provisioningState)  |Özel bağlantı hizmeti için geçerli sağlama durumunu listeleyen salt okunurdur bir özellik. İlgili sağlama durumları şunlardır: "silme; Başaramadı Baarı Güncelleştiriliyor ". Sağlama durumu "başarılı" olduğunda, özel bağlantı hizmetinizi başarıyla sağlamış olursunuz.        |
|Diğer ad (diğer ad)     | Diğer ad, hizmetiniz için genel olarak benzersiz bir salt okuma dizesidir. Hizmetiniz için müşteri verilerini maskelemenize yardımcı olur ve aynı zamanda hizmetiniz için kolay paylaşılan bir ad oluşturur. Özel bir bağlantı hizmeti oluşturduğunuzda, Azure hizmetinizin sizinle paylaşabileceğiniz diğer adını oluşturur. Müşterileriniz, hizmetinize bir bağlantı istemek için bu diğer adı kullanabilir.          |
|Görünürlük (görünürlük)     | Görünürlük, özel bağlantı hizmetiniz için pozlama ayarlarını denetleyen özelliktir. Hizmet sağlayıcıları, Azure rol tabanlı erişim denetimi (Azure RBAC) izinleri, kısıtlı bir abonelik kümesi veya tüm Azure abonelikleri ile hizmetleri ile olan Aboneliklerle ilgili pozlamayı tercih edebilir.          |
|Otomatik onay (otomatik onay)    |   Otomatik onay, özel bağlantı hizmetine otomatik erişimi denetler. Otomatik onay listesinde belirtilen abonelikler, bu aboneliklerdeki özel uç noktalardan bir bağlantı istendiğinde otomatik olarak onaylanır.          |
|Load Balancer ön uç IP yapılandırması (Loadbalancerfrontendıpconfigurations)    |    Özel bağlantı hizmeti bir Standart Load Balancer ön uç IP adresine bağlıdır. Hizmete yönelik tüm trafik, SLB 'nın ön ucunda iletişime geçecektir. Bu trafiği, uygulamalarınızın çalıştığı uygun arka uç havuzlarına yönlendirmek için SLB kurallarını yapılandırabilirsiniz. Yük dengeleyici ön uç IP yapılandırması NAT IP yapılandırmasından farklı.      |
|NAT IP yapılandırması (ipConfigurations)    |    Bu özellik, özel bağlantı hizmeti için NAT (ağ adresi çevirisi) IP yapılandırmasını ifade eder. NAT IP 'si, bir hizmet sağlayıcısının sanal ağındaki herhangi bir alt ağdan seçilebilir. Özel bağlantı hizmeti, özel bağlantı trafiği üzerinde hedef tarafı NAT kullanır. Bu, kaynak (tüketici tarafı) ve hedef (hizmet sağlayıcı) adres alanı arasında bir IP çakışması olmamasını sağlar. Hedef tarafta (hizmet sağlayıcı tarafı), NAT IP adresi, hizmetiniz tarafından gönderilen tüm paketler için hizmetinize ve hedef IP 'niz tarafından alınan tüm paketlerin kaynak IP 'si olarak görünür.       |
|Özel uç nokta bağlantıları (privateEndpointConnections)     |  Bu özellik, özel bağlantı hizmetine bağlanan özel uç noktaları listeler. Birden çok özel uç nokta aynı özel bağlantı hizmetine bağlanabilir ve hizmet sağlayıcısı tek özel uç noktaların durumunu denetleyebilir.        |
|TCP proxy v2 (EnableProxyProtocol)     |  Bu özellik, hizmet sağlayıcısının hizmet tüketicisi hakkındaki bağlantı bilgilerini almak için TCP proxy v2 kullanmasına olanak sağlar. Hizmet sağlayıcısı, alıcı yapılandırmalarını, proxy protokolü v2 üst bilgisini ayrıştırabilecek şekilde ayarlamaktan sorumludur.        |
|||


### <a name="details"></a>Ayrıntılar

- Özel bağlantı hizmetine, herhangi bir genel bölgedeki onaylanan özel uç noktalardan erişilebilir. Özel uç noktaya, bölgesel olarak eşlenmiş VNET 'ler, genel olarak eşlenmiş sanal ağlar ve özel VPN veya ExpressRoute bağlantıları kullanan şirket içi sanal ağ üzerinden erişilebilir. 
 
- Özel bir bağlantı hizmeti oluştururken, kaynağın yaşam döngüsü için bir ağ arabirimi oluşturulur. Bu arabirim müşteri tarafından yönetilemez.
 
- Özel bağlantı hizmetinin, sanal ağla aynı bölgede ve Standart Load Balancer dağıtılması gerekir.  
 
- Tek bir özel bağlantı hizmetine, farklı VNET 'ler, abonelikler ve/veya Active Directory kiracılarına ait birden çok özel uç noktasından erişilebilir. Bağlantı, bağlantı iş akışıyla oluşturulur. 
 
- Birden çok özel bağlantı hizmeti, farklı ön uç IP yapılandırması kullanılarak aynı Standart Load Balancer oluşturulabilir. Standart Load Balancer ve abonelik başına oluşturabileceğiniz özel bağlantı Hizmetleri sayısı için sınırlar vardır. Ayrıntılar için bkz. [Azure Limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
 
- Özel bağlantı hizmeti ile bağlantılı birden fazla NAT IP yapılandırması olabilir. Birden fazla NAT IP yapılandırması seçilmesi hizmet sağlayıcılarının ölçeklendirilmesine yardımcı olabilir. Günümüzde, hizmet sağlayıcıları özel bağlantı hizmeti başına en fazla sekiz NAT IP adresi atayabilir. Her NAT IP adresi ile TCP bağlantılarınız için daha fazla bağlantı noktası atayabilir ve böylece ölçeği değiştirebilirsiniz. Bir özel bağlantı hizmetine birden çok NAT IP adresi ekledikten sonra NAT IP adreslerini silemezsiniz. Bu, NAT IP adreslerini silerken etkin bağlantıların etkilenmemesini sağlamak için yapılır.


## <a name="alias"></a>Diğer ad

**Diğer ad** hizmetiniz için genel olarak benzersiz bir addır. Hizmetiniz için müşteri verilerini maskelemenize yardımcı olur ve aynı zamanda hizmetiniz için kolay paylaşılan bir ad oluşturur. Özel bir bağlantı hizmeti oluşturduğunuzda, Azure hizmetiniz için müşterilerinizle paylaşabileceğiniz bir diğer ad oluşturur. Müşterileriniz, hizmetinize bir bağlantı istemek için bu diğer adı kullanabilir.

Diğer ad üç bölümden oluşur: *ön ek*. *GUID*. *Son ek*

- Ön ek, hizmet adıdır. Size ait ön ekinizi seçebilirsiniz. "Diğer ad" oluşturulduktan sonra, bunu değiştiremezsiniz, bu nedenle öneğinizi uygun şekilde seçin.  
- GUID, platform tarafından sağlanacak. Bu, adın genel olarak benzersiz olmasına yardımcı olur. 
- Son ek Azure: *Region*. Azure. privatelinkservice tarafından eklendi 

Tüm diğer ad:  *ön ek*. {GUıD}. *Region*. Azure. privatelinkservice  

## <a name="control-service-exposure"></a>Hizmet pozlamasını denetleme

Özel bağlantı hizmeti, "görünürlük" ayarı aracılığıyla hizmetinizin görünürlüğünü denetlemeye yönelik seçenekler sağlar. Hizmeti, sahip olduğunuz farklı sanal ağlardan tüketim için özel hale getirebilirsiniz (yalnızca Azure RBAC izinleri), güvendiğiniz sınırlı bir abonelik kümesiyle pozlamayı kısıtlayabilir veya tüm Azure aboneliklerinin özel bağlantı hizmetinde bağlantı istemesi için ortak hale getirebilirsiniz. Görünürlük ayarlarınızda, bir tüketicinin hizmetinize bağlanıp bağlanamacağına karar verirsiniz. 

## <a name="control-service-access"></a>Denetim hizmeti erişimi

Özel bağlantı hizmetinize pozlama (görünürlük ayarı denetimli) olan tüketiciler, sanal ağlarında özel bir uç nokta oluşturabilir ve özel bağlantı hizmetinize bağlantı isteğinde bulunabilir. Özel uç nokta bağlantısı özel bağlantı hizmeti nesnesi üzerinde "beklemede" durumunda oluşturulur. Hizmet sağlayıcısı, bağlantı isteğine göre davranmasından sorumludur. Bağlantıyı onaylayabilir, bağlantıyı reddedebilir veya bağlantıyı silebilirsiniz. Yalnızca onaylanan bağlantılar özel bağlantı hizmetine trafik gönderebilir.

Bağlantıları onaylama eylemi, özel bağlantı hizmetindeki otomatik onaylama özelliği kullanılarak otomatikleştirilebilir. Otomatik onay, hizmet sağlayıcılarının, hizmetine otomatik erişim için bir abonelik kümesini önceden onaylamasını sağlar. Müşterilerin otomatik onay listesine eklemesi için aboneliklerini, hizmet sağlayıcılarının çevrimdışı olarak paylaşması gerekecektir. Otomatik onay, görünürlük dizisinin bir alt kümesidir. Görünürlük, pozlama ayarlarını denetler, ancak otomatik onay, hizmetinizin onay ayarlarını denetler. Bir müşteri otomatik onay listesindeki bir abonelikten bağlantı isterse bağlantı otomatik olarak onaylanır ve bağlantı oluşturulur. Hizmet sağlayıcılarının isteği artık el ile onaylaması gerekmez. Öte yandan, bir müşteri otomatik onaylama dizisinde değil görünürlük dizisindeki bir abonelikten bağlantı isterse, istek hizmet sağlayıcısına ulaşır, ancak hizmet sağlayıcının bağlantıları el ile onaylaması gerekir.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>TCP proxy v2 kullanarak bağlantı bilgilerini alma

Özel bağlantı hizmeti kullanılırken, Özel uç noktadan gelen paketlerin kaynak IP adresi, hizmet sağlayıcısı tarafında, sağlayıcının sanal ağından ayrılan NAT IP 'si kullanılarak ağ adresi çevirisi (NAT) olur. Bu nedenle, uygulamalar hizmet tüketicilerinin gerçek kaynak IP adresi yerine ayrılmış NAT IP adresini alır. Uygulamanızın Tüketici tarafında gerçek kaynak IP adresi gerekiyorsa, hizmetinize proxy protokolünü etkinleştirebilir ve proxy protokolü başlığından bilgileri alabilirsiniz. Kaynak IP adresine ek olarak, proxy protokol üstbilgisi de özel uç noktanın LinkId 'sini taşır. Kaynak IP adresi ve LinkId birleşimi, hizmet sağlayıcılarının tüketicilerini benzersiz bir şekilde belirlemesine yardımcı olabilir. Proxy protokolü hakkında daha fazla bilgi için [buraya](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt)gidin. 

Bu bilgiler, özel bir tür uzunluğu-değer (TLV) vektörü kullanılarak aşağıdaki şekilde kodlanır:

Özel TLV ayrıntıları:

|Alan |Uzunluk (sekizli)  |Açıklama  |
|---------|---------|----------|
|Tür  |1        |PP2_TYPE_AZURE (0xEE)|
|Uzunluk  |2      |Değer uzunluğu|
|Değer  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |Özel uç noktanın LINKıD 'sini temsil eden UINT32 (4 bayt). Little endian biçiminde kodlandı.|

 > [!NOTE]
 > Hizmet sağlayıcı, standart yük dengeleyicinin arkasındaki hizmetin, proxy protokol üst bilgisini özel bağlantı hizmetinde etkin olduğunda [belirtime](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) göre, proxy protokol üstbilgisini ayrıştırmak üzere yapılandırıldığından emin olmak için sorumludur. Özel bağlantı hizmetinde ara protokol ayarı etkinse istek başarısız olur, ancak hizmet sağlayıcının hizmeti üstbilgiyi ayrıştırmak üzere yapılandırılmamıştır. Benzer şekilde, bu ayar özel bağlantı hizmetinde etkinleştirilmediğinde hizmet sağlayıcının hizmeti bir proxy protokol üstbilgisi bekliyorsanız istek başarısız olur. Proxy protokolü ayarı etkinleştirildikten sonra, üst bilgide istemci bilgisi olmasa bile, proxy protokol üstbilgisi konaktan arka uç sanal makinelere HTTP/TCP sistem durumu araştırmalarına dahil edilir. 

## <a name="limitations"></a>Sınırlamalar

Özel bağlantı hizmeti kullanılırken aşağıdakiler bilinen sınırlamalar aşağıda verilmiştir:
- Yalnızca Standart Load Balancer destekleniyor 
- Yalnızca IPv4 trafiğini destekler
- Yalnızca TCP ve UDP trafiğini destekler

## <a name="next-steps"></a>Sonraki adımlar
- [Azure PowerShell kullanarak özel bir bağlantı hizmeti oluşturma](create-private-link-service-powershell.md)
- [Azure CLı kullanarak özel bağlantı hizmeti oluşturma](create-private-link-service-cli.md)
