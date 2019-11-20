---
title: Application Gateway için Azure Izleyici ölçümleri
description: Uygulama ağ geçidinin performansını izlemek için ölçümleri nasıl kullanacağınızı öğrenin
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: f0937ee53e66cb1bf0c5d6b55a8dde045570e924
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309847"
---
# <a name="metrics-for-application-gateway"></a>Application Gateway ölçümleri

Application Gateway, Application Gateway ve arka uç örneklerinizin performansı için ölçümler olarak adlandırılan veri noktalarını [Azure izleyici](https://docs.microsoft.com/azure/azure-monitor/overview) 'ye yayımlar. Bu ölçümler, belirli bir zamanda uygulama ağ geçidinizin bazı yönlerini tanımlayan sıralı bir zaman serisi verileri kümesindeki sayısal değerlerdir. Application Gateway üzerinden akan istekler varsa, ölçümlerini ölçer ve 60 saniyelik aralıklarla gönderir. Application Gateway veya bir ölçüm için veri akışı olmayan bir istek yoksa, ölçüm raporlanmaz. Daha fazla bilgi için bkz. [Azure izleyici ölçümleri](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Application Gateway v2 SKU 'SU tarafından desteklenen ölçümler

### <a name="timing-metrics"></a>Zamanlama ölçümleri

İstek ve yanıtın zamanlaması ile ilgili aşağıdaki ölçümler kullanılabilir. Bu ölçümleri çözümleyerek, WAN, Application Gateway, Application Gateway ile arka uç arasındaki ağ veya uygulama performansı arasında uygulama üzerinde yavaşlama olup olmadığını belirleyebilirsiniz.

- **İstemci RTT**

  İstemcilerle Application Gateway arasında ortalama gidiş dönüş süresi. Bu ölçüm, bağlantı kurmak ve bildirimleri döndürmek için ne kadar sürdüğünü gösterir.

- **Application Gateway toplam süre**

  Bir isteğin işlenmesi için geçen ortalama süre ve gönderilecek yanıt. Bu, yanıt gönderme işlemi tamamlandığında Application Gateway bir HTTP isteğinin ilk baytını aldığında zaman aralığının ortalaması olarak hesaplanır. Bu, genellikle Application Gateway işleme süresini, istek ve Yanıt paketlerinin ağ üzerinden hareket etmekte olduğu süreyi ve arka uç sunucusunun yanıt vermesi gerektiğini unutmayın.

- **Arka uç bağlantı saati**

  Arka uç sunucusuyla bağlantı kurmaya harcanan süre. 

- **Arka uç ilk bayt yanıt süresi**

  Arka uç sunucusuna bağlantı kurma ve yanıt üst bilgisinin ilk baytını alma işlemi arasındaki zaman aralığı, arka uç sunucusunun işlem süresini yaklaştırmaya başladı

- **Arka uç son bayt yanıt süresi**

  Arka uç sunucusuna bağlantı kurma ve yanıt gövdesinin son baytını alma arasındaki zaman aralığı

### <a name="application-gateway-metrics"></a>Application Gateway ölçümleri

Application Gateway için aşağıdaki ölçümler kullanılabilir:

- **Alınan bayt**

   İstemcilerden Application Gateway tarafından alınan bayt sayısı

- **Gönderilen bayt**

   Application Gateway istemciye gönderilen bayt sayısı

- **İstemci TLS protokolü**

   Application Gateway ile bağlantı kurulan istemci tarafından başlatılan TLS ve TLS olmayan isteklerin sayısı. TLS protokol dağıtımını görüntülemek için Dimension TLS protokolüne göre filtreleyin.

- **Geçerli kapasite birimleri**

   Tüketilen Kapasite birimi sayısı. Kapasite birimleri, sabit maliyete ek olarak ücretlendirilen tüketim tabanlı maliyeti ölçer. Kapasite birimi işlem biriminin, kalıcı bağlantıların ve aktarım hızının üç belirleyici vardır. Her kapasite birimi en fazla şundan oluşur: 1 işlem birimi veya 2500 kalıcı bağlantı ya da 2,22 Mbps üretilen iş.

- **Geçerli işlem birimleri**

   Tüketilen işlemci kapasitesi sayısı. İşlem birimini etkileyen faktörler, TLS bağlantısı/sn, URL yeniden yazma hesaplamaları ve WAF kural işleme. 

- **Geçerli bağlantılar**

   Application Gateway ile kurulan geçerli bağlantı sayısı

- **Başarısız Istekler**

   Application Gateway tarafından sunulan başarısız istek sayısı. İstek sayısı, her/belirli bir arka uç havuzu için sayıyı göstermek üzere daha fazla filtrelenebilir-http ayar birleşimi.


- **Yanıt durumu**

   Application Gateway tarafından döndürülen HTTP yanıt durumu. Yanıt durum kodu dağıtımı, 5 xx, 3xx, 4xx ve 5xx kategorilerindeki yanıtları göstermek için daha fazla kategorilere ayrılabilir.

- **Aktarım hızı**

   Application Gateway saniyede hizmet verilen bayt sayısı

- **Toplam Istek sayısı**

   Application Gateway tarafından sunulan başarılı istek sayısı. İstek sayısı, her/belirli bir arka uç havuzu için sayıyı göstermek üzere daha fazla filtrelenebilir-http ayar birleşimi.

- **Web uygulaması güvenlik duvarı ile eşleşen kurallar**

- **Web uygulaması güvenlik duvarı tarafından tetiklenen kurallar**

### <a name="backend-metrics"></a>Arka uç ölçümleri

Application Gateway için aşağıdaki ölçümler kullanılabilir:

- **Arka uç yanıt durumu**

  Arka uçların döndürdüğü HTTP yanıt durum kodlarının sayısı. Bu, Application Gateway tarafından oluşturulan yanıt kodlarını içermez. Yanıt durum kodu dağıtımı, 5 xx, 3xx, 4xx ve 5xx kategorilerindeki yanıtları göstermek için daha fazla kategorilere ayrılabilir.

- **Sağlıklı konak sayısı**

  Sistem durumu araştırması tarafından sağlıklı olarak belirlenen arka uç sayısı. Belirli bir arka uç havuzundaki sağlıklı/sağlıksız Konakları göstermek için arka uç havuzu başına filtre uygulayabilirsiniz.

- **Sağlıksız konak sayısı**

  Durum araştırmasının sağlıksız olduğunu belirlenen arka uçların sayısı. Belirli bir arka uç havuzunda sağlıklı olmayan Konakları göstermek için arka uç havuzu başına filtre uygulayabilirsiniz.

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Application Gateway v1 SKU 'SU tarafından desteklenen ölçümler

### <a name="application-gateway-metrics"></a>Application Gateway ölçümleri

Application Gateway için aşağıdaki ölçümler kullanılabilir:

- **Geçerli bağlantılar**

  Application Gateway ile kurulan geçerli bağlantı sayısı

- **Başarısız Istekler**

  Application Gateway tarafından sunulan başarısız istek sayısı. İstek sayısı, her/belirli bir arka uç havuzu için sayıyı göstermek üzere daha fazla filtrelenebilir-http ayar birleşimi.

- **Yanıt durumu**

  Application Gateway tarafından döndürülen HTTP yanıt durumu. Yanıt durum kodu dağıtımı, 5 xx, 3xx, 4xx ve 5xx kategorilerindeki yanıtları göstermek için daha fazla kategorilere ayrılabilir.

- **Aktarım hızı**

  Application Gateway saniyede hizmet verilen bayt sayısı

- **Toplam Istek sayısı**

  Application Gateway tarafından sunulan başarılı istek sayısı. İstek sayısı, her/belirli bir arka uç havuzu için sayıyı göstermek üzere daha fazla filtrelenebilir-http ayar birleşimi.

- **Web uygulaması güvenlik duvarı ile eşleşen kurallar**

- **Web uygulaması güvenlik duvarı tarafından tetiklenen kurallar**

### <a name="backend-metrics"></a>Arka uç ölçümleri

Application Gateway için aşağıdaki ölçümler kullanılabilir:

- **Sağlıklı konak sayısı**

  Sistem durumu araştırması tarafından sağlıklı olarak belirlenen arka uç sayısı. Belirli bir arka uç havuzundaki sağlıklı/sağlıksız Konakları göstermek için arka uç havuzu başına filtre uygulayabilirsiniz.

- **Sağlıksız konak sayısı**

  Durum araştırmasının sağlıksız olduğunu belirlenen arka uçların sayısı. Belirli bir arka uç havuzunda sağlıklı olmayan Konakları göstermek için arka uç havuzu başına filtre uygulayabilirsiniz.

## <a name="metrics-visualization"></a>Ölçüm görselleştirme

Uygulama ağ geçidine gidin, **izleme** ' nin altında **ölçümleri**seçin. Kullanılabilir değerleri görüntülemek için **ÖLÇÜM** açılan listesini seçin.

Aşağıdaki görüntüde, son 30 dakika boyunca üç ölçüm görüntülenirken bir örnek görürsünüz:

[![](media/application-gateway-diagnostics/figure5.png "Ölçüm görünümü")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Geçerli ölçüm listesini görmek için bkz. [Azure izleyici Ile desteklenen ölçümler](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Ölçümlerde uyarı kuralları

Bir kaynağın ölçümlerine göre uyarı kuralları başlatabilirsiniz. Örneğin, bir uyarı, uygulama ağ geçidinin üretilen işi belirli bir dönem üzerinde, aşağıda veya bir eşiğin üzerinde ise bir Web kancasını veya bir yöneticiye e-posta gönderebilir.

Aşağıdaki örnek, aktarım hızı bir eşiğe ulaştığında bir yöneticiye e-posta gönderen bir uyarı kuralı oluşturma işleminde size yol gösterir:

1. **Kural Ekle** sayfasını açmak için **ölçüm uyarısı Ekle** ' yi seçin. Bu sayfaya ölçüm sayfasından da ulaşabilirsiniz.

   !["Ölçüm uyarısı Ekle" düğmesi][6]

2. **Kural Ekle** sayfasında, ad, koşul ve bildirim bölümlerini doldurun ve **Tamam**' ı seçin.

   * **Koşul** seçicide dört değerden birini seçin: **Büyüktür,** **büyüktür veya eşittir**, **küçüktür**ya da **küçüktür veya eşittir**.

   * **Süre** seçicide beş dakikadan altı saat arasında bir dönem seçin.

   * **E-posta sahipleri, katkıda bulunanlar ve okuyucular**' ı seçerseniz, e-posta söz konusu kaynağa erişimi olan kullanıcılara göre dinamik olabilir. Aksi takdirde, **ek yönetici e-postaları** kutusunda kullanıcıların virgülle ayrılmış bir listesini sağlayabilirsiniz.

   ![Kural sayfası ekle][7]

Eşiğe ihlal olursa, aşağıdaki görüntüde olana benzer bir e-posta gönderilir:

![İhlal eşiği için e-posta][8]

Bir ölçüm uyarısı oluşturduktan sonra uyarıların bir listesi görüntülenir. Tüm uyarı kurallarına genel bir bakış sağlar.

![Uyarıların ve kuralların listesi][9]

Uyarı bildirimleri hakkında daha fazla bilgi edinmek için bkz. [uyarı bildirimleri alma](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Web kancaları ve bunları uyarılarla nasıl kullanabileceğiniz hakkında daha fazla bilgi edinmek için [Azure ölçüm uyarısında Web kancası yapılandırma](../azure-monitor/platform/alerts-webhooks.md)makalesini ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure izleyici günlüklerini](../azure-monitor/insights/azure-networking-analytics.md)kullanarak sayaç ve olay günlüklerini görselleştirin.
* [Azure etkinlik günlüğünüzü Power BI blog gönderisi Ile görselleştirin](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
* [Power BI ve daha fazla blog gönderisine Azure etkinlik günlüklerini görüntüleyin ve çözümleyin](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) .

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
