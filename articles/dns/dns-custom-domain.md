---
title: Azure DNS Azure kaynaklarınızla tümleştirin
description: Azure kaynaklarınız için DNS sağlamak üzere Azure DNS kullanımı hakkında bilgi edinin.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 7/13/2019
ms.author: victorh
ms.openlocfilehash: 051aabed758f80208549cf64bf5d74b1fecfbe75
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854166"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Azure hizmeti için özel etki alanı ayarları sağlamak üzere Azure DNS kullanma

Azure DNS, özel etki alanlarını destekleyen veya tam etki alanı adı (FQDN) olan Azure kaynaklarınızın herhangi biri için özel bir etki alanı için DNS sağlar. Örneğin, bir Azure Web uygulamanız var ve kullanıcılarınızın buna bir FQDN olarak contoso.com ya da www\.contoso.com kullanarak erişmesini istiyorsunuz. Bu makalede, Azure hizmetinizi özel etki alanlarının kullanımı için Azure DNS yapılandırma adımları gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Özel etki alanınız için Azure DNS kullanabilmeniz için öncelikle etki alanınızı Azure DNS için temsilci olarak vermelisiniz. Ad sunucularınızı temsilci olarak yapılandırmayla ilgili yönergeler için [Azure DNS için bir etki alanı temsilcisini](./dns-delegate-domain-azure-dns.md) ziyaret edin. Etki alanınız Azure DNS bölgenize atandıktan sonra, gereken DNS kayıtlarını yapılandırabilirsiniz.

[Azure işlev uygulamaları](#azure-function-app), [genel IP adresleri](#public-ip-address), [App Service (Web Apps)](#app-service-web-apps), [BLOB depolama](#blob-storage)ve [Azure CDN](#azure-cdn)için bir gösterim veya özel etki alanı yapılandırabilirsiniz.

## <a name="azure-function-app"></a>Azure İşlev Uygulaması

Azure işlev uygulamaları için özel bir etki alanı yapılandırmak üzere, bir CNAME kaydının ve işlev uygulamasının kendisi üzerinde yapılandırma olarak oluşturulması gerekir.
 
**İşlev uygulaması** gidin ve işlev uygulamanızı seçin. **Platform özellikleri** ' ne ve **ağ** altında **özel etki alanları**' na tıklayın.

![işlev uygulaması dikey penceresi](./media/dns-custom-domain/functionapp.png)

**Özel etki alanları** dikey penceresindeki geçerli URL 'yi aklınızda, bu adres oluşturulan DNS kaydı için diğer ad olarak kullanılır.

![Özel etki alanı dikey penceresi](./media/dns-custom-domain/functionshostname.png)

DNS bölgenize gidin ve **+ kayıt kümesi**' ne tıklayın. **Kayıt kümesi Ekle** dikey penceresinde aşağıdaki bilgileri doldurun ve oluşturmak için **Tamam** ' ı tıklatın.

|Özellik  |Value  |Açıklama  |
|---------|---------|---------|
|Ad     | myfunctionapp        | Bu değer, etki alanı adı etiketiyle birlikte özel etki alanı adı için FQDN 'dir.        |
|Type     | CNAME        | CNAME kaydı kullanmak bir diğer ad kullanıyor.        |
|TTL     | 1\.        | 1 saat için 1 kullanılır        |
|TTL birimi     | Saat        | Saatler zaman ölçümü olarak kullanılır         |
|Alias     | adatumfunction.azurewebsites.net        | Diğer adı oluşturmakta olduğunuz DNS adı, bu örnekte, işlev uygulaması için varsayılan olarak sağlanmış olan adatumfunction.azurewebsites.net DNS adıdır.        |

İşlev uygulamanıza geri gidin, **platform özellikleri**' ne tıklayın ve **ağ** ' ın altında **özel etki alanları**' na tıklayın, ardından **özel ana bilgisayar adları** ' na tıklayarak **+ konak**

**Konak adı Ekle** dikey penceresinde, **hostname** metin alanına CNAME kaydını girin ve **Doğrula**' ya tıklayın. Kayıt bulunursa, **ana bilgisayar adı Ekle** düğmesi görüntülenir. Diğer adı eklemek için **konak adı Ekle** ' ye tıklayın.

![işlev uygulamaları konak adı Ekle dikey penceresi](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Genel IP adresi

Application Gateway, Load Balancer, bulut hizmeti, Kaynak Yöneticisi VM 'Ler ve klasik VM 'Ler gibi genel bir IP adresi kaynağı kullanan hizmetler için özel bir etki alanı yapılandırmak için bir kayıt kullanılır.

**Ağ** > **genel IP adresi**' ne gidin, genel IP kaynağını seçin ve **yapılandırma**' ya tıklayın. Gösterilen IP adresini önemli bir şekilde ekleyin.

![Genel IP dikey penceresi](./media/dns-custom-domain/publicip.png)

DNS bölgenize gidin ve **+ kayıt kümesi**' ne tıklayın. **Kayıt kümesi Ekle** dikey penceresinde aşağıdaki bilgileri doldurun ve oluşturmak için **Tamam** ' ı tıklatın.


|Özellik  |Değer  |Açıklama  |
|---------|---------|---------|
|Ad     | MyWebServer        | Bu değer, etki alanı adı etiketiyle birlikte özel etki alanı adı için FQDN 'dir.        |
|Type     | A        | Kaynak bir IP adresi olduğu için bir kayıt kullanın.        |
|TTL     | 1\.        | 1 saat için 1 kullanılır        |
|TTL birimi     | Saat        | Saatler zaman ölçümü olarak kullanılır         |
|IP Adresi     | `<your ip address>`       | Genel IP adresi.|

![Bir kayıt oluştur](./media/dns-custom-domain/arecord.png)

Kayıt oluşturulduktan sonra, kaydın çözümlendiğini doğrulamak `nslookup` için öğesini çalıştırın.

![Genel IP DNS araması](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (Web Apps)

Aşağıdaki adımlarda, bir App Service Web uygulaması için özel bir etki alanı yapılandırma işlemleri yapılır.

**App Service** gidin ve özel bir etki alanı adı yapılandırdığınız kaynağı seçin ve **özel etki alanları**' na tıklayın.

**Özel etki alanları** dikey penceresindeki geçerli URL 'yi aklınızda, bu adres oluşturulan DNS kaydı için diğer ad olarak kullanılır.

![Özel etki alanları dikey penceresi](./media/dns-custom-domain/url.png)

DNS bölgenize gidin ve **+ kayıt kümesi**' ne tıklayın. **Kayıt kümesi Ekle** dikey penceresinde aşağıdaki bilgileri doldurun ve oluşturmak için **Tamam** ' ı tıklatın.


|Özellik  |Value  |Açıklama  |
|---------|---------|---------|
|Ad     | MyWebServer        | Bu değer, etki alanı adı etiketiyle birlikte özel etki alanı adı için FQDN 'dir.        |
|Type     | CNAME        | CNAME kaydı kullanmak bir diğer ad kullanıyor. Kaynak bir IP adresi kullansaydı bir kayıt kullanılır.        |
|TTL     | 1\.        | 1 saat için 1 kullanılır        |
|TTL birimi     | Saat        | Saatler zaman ölçümü olarak kullanılır         |
|Alias     | webserver.azurewebsites.net        | Diğer adı oluşturmakta olduğunuz DNS adı, bu örnekte varsayılan olarak Web uygulamasına sunulan webserver.azurewebsites.net DNS adıdır.        |


![CNAME kaydı oluşturma](./media/dns-custom-domain/createcnamerecord.png)

Özel etki alanı adı için yapılandırılmış olan App Service 'e geri gidin. **Özel etki alanları**' na ve ardından **konak adları**' na tıklayın Oluşturduğunuz CNAME kaydını eklemek için **+ konak adı Ekle**' ye tıklayın.

![Şekil 1](./media/dns-custom-domain/figure1.png)

İşlem tamamlandıktan sonra, ad çözümlemenin çalıştığını doğrulamak için **nslookup** ' ı çalıştırın.

![Şekil 1](./media/dns-custom-domain/finalnslookup.png)

Özel bir etki alanını App Service eşleme hakkında daha fazla bilgi edinmek için, [mevcut bir özel DNS adını Azure Web Apps eşleme](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json)adresini ziyaret edin.

Etkin bir DNS adını nasıl geçirebileceğinizi öğrenmek için bkz. [Azure App Service için etkin BIR DNS adı geçirme](../app-service/manage-custom-dns-migrate-domain.md).

Özel bir etki alanı satın almanız gerekiyorsa, App Service etki alanları hakkında daha fazla bilgi edinmek için [Azure Web Apps için özel etki alanı adı satın alma](../app-service/manage-custom-dns-buy-domain.md) sayfasını ziyaret edin.

## <a name="blob-storage"></a>Blob depolama

Aşağıdaki adımlar, asverify metodunu kullanarak bir BLOB depolama hesabı için bir CNAME kaydı yapılandırmanıza yardımcı olacak. Bu yöntem, kapalı kalma süresi olmamasını sağlar.

**Depolama** > **depolama hesapları**' na gidin, depolama hesabınızı seçin ve **özel etki alanı**' na tıklayın. 2\. adım altında FQDN 'yi oluşturma, bu değer ilk CNAME kaydını oluşturmak için kullanılır

![BLOB depolama özel etki alanı](./media/dns-custom-domain/blobcustomdomain.png)

DNS bölgenize gidin ve **+ kayıt kümesi**' ne tıklayın. **Kayıt kümesi Ekle** dikey penceresinde aşağıdaki bilgileri doldurun ve oluşturmak için **Tamam** ' ı tıklatın.


|Özellik  |Value  |Açıklama  |
|---------|---------|---------|
|Ad     | asverify. mystorageaccount        | Bu değer, etki alanı adı etiketiyle birlikte özel etki alanı adı için FQDN 'dir.        |
|Type     | CNAME        | CNAME kaydı kullanmak bir diğer ad kullanıyor.        |
|TTL     | 1\.        | 1 saat için 1 kullanılır        |
|TTL birimi     | Saat        | Saatler zaman ölçümü olarak kullanılır         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Diğer adı oluşturmakta olduğunuz DNS adı, bu örnekte varsayılan olarak depolama hesabına sağlanmış olan asverify.adatumfunctiona9ed.blob.core.windows.net DNS adıdır.        |

Depolama**depolama hesapları**' **na tıklayarak** > depolama hesabınıza geri gidin, depolama hesabınızı seçin ve **özel etki alanı**' na tıklayın. Metin kutusunda asverify öneki olmadan oluşturduğunuz diğer adı yazın, * * dolaylı CNAME doğrulaması kullan ' ı işaretleyin ve **Kaydet**' e tıklayın. Bu adım tamamlandıktan sonra DNS bölgenize dönün ve asverify öneki olmadan bir CNAME kaydı oluşturun.  Bu noktadan sonra, CNAME kaydını cdnverify önekiyle silmeniz güvenlidir.

![BLOB depolama özel etki alanı](./media/dns-custom-domain/indirectvalidate.png)

Çalıştırarak DNS çözümlemesini doğrulama`nslookup`

Özel bir etki alanını BLOB depolama uç noktası ile eşleme hakkında daha fazla bilgi edinmek için [bkz. blob depolama uç noktanız için özel etki alanı adı yapılandırma](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

Aşağıdaki adımlar, cdnverify metodunu kullanarak bir CDN uç noktası için CNAME kaydı yapılandırmaya kılavuzluk eden bir yöntemdir. Bu yöntem, kapalı kalma süresi olmamasını sağlar.

**Ağ** > **CDN profilleri**' ne gidin, CDN profilinizi seçin.

Çalıştığınız uç noktayı seçin ve **+ özel etki alanı**' na tıklayın. Bu değer olarak, CNAME kaydının işaret ettiği kayıt olan **bitiş noktası ana bilgisayar adını** unutmayın.

![CDN özel etki alanı](./media/dns-custom-domain/endpointcustomdomain.png)

DNS bölgenize gidin ve **+ kayıt kümesi**' ne tıklayın. **Kayıt kümesi Ekle** dikey penceresinde aşağıdaki bilgileri doldurun ve oluşturmak için **Tamam** ' ı tıklatın.

|Özellik  |Değer  |Açıklama  |
|---------|---------|---------|
|Ad     | cdnverify. mycdnendpoint        | Bu değer, etki alanı adı etiketiyle birlikte özel etki alanı adı için FQDN 'dir.        |
|Type     | CNAME        | CNAME kaydı kullanmak bir diğer ad kullanıyor.        |
|TTL     | 1\.        | 1 saat için 1 kullanılır        |
|TTL birimi     | Saat        | Saatler zaman ölçümü olarak kullanılır         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Diğer adı oluşturmakta olduğunuz DNS adı, bu örnekte varsayılan olarak depolama hesabına sağlanmış olan cdnverify.adatumcdnendpoint.azureedge.net DNS adıdır.        |

**Ağ** > **CDN profilleri**' ne tıklayarak CDN uç noktanıza geri gidin ve CDN profilinizi seçin. **+ Özel etki alanı** ' na tıklayın ve cdnverify ÖNEKI olmadan CNAME kaydı diğer adınızı girip **Ekle**' ye tıklayın.

Bu adım tamamlandıktan sonra DNS bölgenize dönün ve cdnverify öneki olmadan bir CNAME kaydı oluşturun.  Bu noktadan sonra, CNAME kaydını cdnverify önekiyle silmeniz güvenlidir. CDN hakkında daha fazla bilgi ve ara kayıt adımı olmadan özel bir etki alanını yapılandırma hakkında daha fazla bilgi için [harita Azure CDN içeriğini özel bir etki alanına](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json)ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

[Azure 'da barındırılan hizmetler için ters DNS yapılandırmayı](dns-reverse-dns-for-azure-services.md)öğrenin.
