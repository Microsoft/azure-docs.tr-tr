---
title: Application Gateway için Azure Izleyici ölçümleri
description: Uygulama ağ geçidinin performansını izlemek için ölçümleri nasıl kullanacağınızı öğrenin
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: article
ms.date: 04/19/2021
ms.author: azhussai
ms.openlocfilehash: 615db7e8d53e397755ae318d171dab1eab9ec6c6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727817"
---
# <a name="metrics-for-application-gateway"></a>Application Gateway ölçümleri

Application Gateway, Application Gateway ve arka uç örneklerinizin performansı için ölçümler olarak adlandırılan veri noktalarını [Azure izleyici](../azure-monitor/overview.md) 'ye yayımlar. Bu ölçümler, belirli bir zamanda uygulama ağ geçidinizin bazı yönlerini tanımlayan sıralı bir zaman serisi verileri kümesindeki sayısal değerlerdir. Application Gateway üzerinden akan istekler varsa, ölçümlerini ölçer ve 60 saniyelik aralıklarla gönderir. Application Gateway veya bir ölçüm için veri akışı olmayan bir istek yoksa, ölçüm raporlanmaz. Daha fazla bilgi için bkz. [Azure izleyici ölçümleri](../azure-monitor/essentials/data-platform-metrics.md).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Application Gateway v2 SKU 'SU tarafından desteklenen ölçümler

### <a name="timing-metrics"></a>Zamanlama ölçümleri

Application Gateway, istek ve Yanıtla ilgili, hepsi milisaniye cinsinden ölçülen çeşitli yerleşik zamanlama ölçümleri sağlar. 

:::image type="content" source="./media/application-gateway-metrics/application-gateway-metrics.png" alt-text="[Application Gateway için zamanlama ölçümlerinin diyagramı" border="false":::

> [!NOTE]
>
> Application Gateway birden fazla dinleyici varsa, anlamlı çıkarımı almak için farklı gecikme ölçümlerini karşılaştırırken her zaman *dinleyici* boyutuna göre filtreleyin.

- **Arka uç bağlantı saati**

  Arka uç uygulamasıyla bağlantı kurmaya harcanan süre. 

  Bu, yeni bağlantılar kurmak için arka uç sunucusunun TCP yığınının yanı sıra ağ gecikmesini da içerir. TLS durumunda ayrıca, el sıkışma için harcanan süreyi de içerir. 

- **Arka uç ilk bayt yanıt süresi**

  Arka uç sunucusuna bağlantı kurma ve Yanıt üstbilgisinin ilk baytını alma arasındaki zaman aralığı. 

  Bu, arka uca *bağlantı süresinin* toplamı, Application Gateway ' den arka uca ulaşmak için geçen süre (sunucunun içerik oluşturmak için geçen süre, büyük olasılıkla veritabanı sorguları) ve arka uçta Application Gateway ulaşmak için yanıtın ilk baytından geçen süre kadar yaklaşık bir süredir.

- **Arka uç son bayt yanıt süresi**

  Arka uç sunucusuna bağlantı kurma ve yanıt gövdesinin son baytını alma arasındaki zaman aralığı. 

  Bu, *Arka uç ilk bayt yanıt süresi* ve veri aktarımı süresinin (bu sayı, istenen nesnelerin boyutuna ve sunucu ağının gecikme süresine göre büyük ölçüde farklılık gösterebilir) toplamıdır.

- **Application Gateway toplam süre**

  İstek alma, işleme ve gönderme yanıtı için geçen ortalama süre. 

  Bu, Application Gateway HTTP isteğinin ilk baytını istemciye Son Yanıt baytı gönderilirken geçen zaman olan aralıktır. Bu, Application Gateway tarafından alınan işlem süresini, *arka uç son bayt yanıt süresini*, tüm yanıtı ve *istemci RTT*'yi göndermek için Application Gateway tarafından alınan süreyi içerir.

- **İstemci RTT**

  İstemcilerle Application Gateway arasında ortalama gidiş dönüş süresi.



Bu ölçümler, gözlemlenen yavaşlamanın istemci ağı, Application Gateway performans, arka uç ağı ve arka uç sunucusu TCP yığını doygunluğu, arka uç uygulama performansı veya büyük dosya boyutu nedeniyle olup olmadığını belirlemede kullanılabilir.

Örneğin, *arka uç ilk bayt yanıt süresi* eğilimi, ancak *arka uç bağlantı zamanı* eğilimi kararlı ise, uygulama ağ geçidinin arka uç gecikmesi ve bağlantı kurma süresi kararlı olduğunu ve ani bir şekilde arka uç uygulamasının yanıt süresi boyunca bir artış meydana geldiğini çıkarmış olabilir. Öte yandan, arka uç *ilk bayt yanıt süresi* içindeki ani artış, *arka uç bağlantı zamanında* karşılık gelen bir ani ilişkili ise, Application Gateway ile arka uç sunucusu veya arka uç sunucusu TCP yığını arasındaki ağın doymuş olduğu anlaşılamıyor. 

*Arka uç son bayt yanıt* süresi içinde bir ani artış fark ederseniz, ancak *arka uç ilk bayt yanıt süresi* kararlı olduğunda, bu durumda ani bir dosya istenmekte olduğundan emin olabilir.

Benzer şekilde, *uygulama ağ geçidinin toplam süresi* bir ani, ancak *arka uç son bayt yanıt süresi* kararlı ise, Application Gateway bir performans sorunu ya da istemci ile Application Gateway arasında ağ üzerinde tıkanıklık olabilir. Ayrıca, *ISTEMCI RTT* de karşılık gelen bir ani ağa sahipse, istemci ile Application Gateway arasındaki ağ nedeniyle azalma olduğunu gösterir.

### <a name="application-gateway-metrics"></a>Application Gateway ölçümleri

Application Gateway için aşağıdaki ölçümler kullanılabilir:

- **Alınan bayt**

   İstemcilerden Application Gateway tarafından alınan bayt sayısı

- **Gönderilen bayt**

   Application Gateway istemciye gönderilen bayt sayısı

- **İstemci TLS protokolü**

   Application Gateway ile bağlantı kurulan istemci tarafından başlatılan TLS ve TLS olmayan isteklerin sayısı. TLS protokol dağıtımını görüntülemek için Dimension TLS protokolüne göre filtreleyin.

- **Geçerli kapasite birimleri**

   Trafiğin yükünü dengelemek için tüketilen kapasite birimi sayısı. Kapasite birimi işlem biriminin, kalıcı bağlantıların ve aktarım hızının üç belirleyici vardır. Her kapasite birimi en çok: 1 işlem birimi veya 2500 kalıcı bağlantı ya da 2,22 Mbps aktarım hızı ' ten oluşur.

- **Geçerli işlem birimleri**

   Tüketilen işlemci kapasitesi sayısı. İşlem birimini etkileyen faktörler, TLS bağlantısı/sn, URL Yeniden yazma hesaplamaları ve WAF kural işlemedir. 

- **Geçerli bağlantılar**

   İstemcilerden Application Gateway etkin olan eşzamanlı bağlantıların toplam sayısı
   
- **Tahmini Faturalanan Kapasite birimleri**

  v2 SKU’su ile, fiyatlandırma modeli tüketime göre belirlenir. Kapasite birimleri, sabit maliyete ek olarak ücretlendirilen tüketim tabanlı maliyeti ölçer. *Tahmini faturalandırılan Kapasite birimleri* , faturalandırmayı tahmin eden Kapasite birimi sayısını belirtir. Bu, *Geçerli kapasite birimleri* (trafiğin yükünü dengelemek için gereken kapasite birimleri) ve *Sabit faturalanabilir kapasite birimleri* (sağlanan minimum kapasite birimleri) değerlerinden daha büyük olanıdır.

- **Başarısız Istekler**

  Application Gateway 5 xx sunucu hata kodlarıyla birlikte sunulan istek sayısı. Bu, Application Gateway oluşturulan 5xx kodlarını ve arka uçta oluşturulan 5xx kodlarını içerir. İstek sayısı, her/belirli bir arka uç havuzu için sayıyı göstermek üzere daha fazla filtrelenebilir-http ayar birleşimi.
   
- **Sabit Faturalanabilir Kapasite Birimleri**

  Application Gateway yapılandırmasındaki *Minimum ölçek birimi* ayarı (bir örnek 10 kapasite birimine eşittir) için sağlanan kapasite birimi sayısı alt sınırı.
   
 - **Saniyedeki yeni bağlantı sayısı**

   İstemcilerden Application Gateway ve Application Gateway arka uç üyelerine kurulan saniye başına yeni TCP bağlantısı sayısı.


- **Yanıt durumu**

   Application Gateway tarafından döndürülen HTTP yanıt durumu. Yanıt durum kodu dağıtımı, 5 xx, 3xx, 4xx ve 5xx kategorilerindeki yanıtları göstermek için daha fazla kategorilere ayrılabilir.

- **İşleme hızı**

   Application Gateway saniyede hizmet verilen bayt sayısı

- **Toplam Istek sayısı**

   Application Gateway tarafından sunulan başarılı istek sayısı. İstek sayısı, her/belirli bir arka uç havuzu için sayıyı göstermek üzere daha fazla filtrelenebilir-http ayar birleşimi.

### <a name="backend-metrics"></a>Arka uç ölçümleri

Application Gateway için aşağıdaki ölçümler kullanılabilir:

- **Arka uç yanıt durumu**

  Arka uçların döndürdüğü HTTP yanıt durum kodlarının sayısı. Bu, Application Gateway tarafından oluşturulan yanıt kodlarını içermez. Yanıt durum kodu dağıtımı, 5 xx, 3xx, 4xx ve 5xx kategorilerindeki yanıtları göstermek için daha fazla kategorilere ayrılabilir.

- **İyi durumda olan konak sayısı**

  Sistem durumu araştırması tarafından sağlıklı olarak belirlenen arka uç sayısı. Belirli bir arka uç havuzundaki sağlıklı ana bilgisayar sayısını göstermek için arka uç havuzu başına filtre uygulayabilirsiniz.

- **İyi durumda olmayan konak sayısı**

  Durum araştırmasının sağlıksız olduğunu belirlenen arka uçların sayısı. Belirli bir arka uç havuzundaki sağlıksız ana bilgisayar sayısını göstermek için arka uç havuzu başına filtre uygulayabilirsiniz.
  
- **Sağlıklı ana bilgisayar başına dakika başına istek**

  Bir arka uç havuzundaki her sağlıklı üye tarafından bir dakika içinde alınan isteklerin ortalama sayısı. *Backendpool HttpSettings* boyutunu kullanarak arka uç havuzunu belirtmeniz gerekir.  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Application Gateway v1 SKU 'SU tarafından desteklenen ölçümler

### <a name="application-gateway-metrics"></a>Application Gateway ölçümleri

Application Gateway için aşağıdaki ölçümler kullanılabilir:

- **CPU Kullanımı**

  Application Gateway’e ayrılan CPU’ların kullanımını görüntüler.  Normal koşullar altında CPU kullanımı, Application Gateway’in arkasında barındırılan web sitelerinde gecikmeye neden olabileceğinden ve istemci deneyimini kesintiye uğratabileceğinden düzenli olarak %90’ı aşmamalıdır. Örnek sayısını artırarak veya daha büyük bir SKU boyutuna geçerek ya da her ikisini de yaparak Application Gateway yapılandırmasını değiştirebilir ve CPU kullanımını dolaylı olarak denetleyebilir veya iyileştirebilirsiniz.

- **Geçerli bağlantılar**

  Application Gateway ile kurulan geçerli bağlantı sayısı

- **Başarısız Istekler**

  Bağlantı sorunları nedeniyle başarısız olan istek sayısı. Bu sayı, uygulama ağ geçidi ile arka uç arasındaki bağlantı sorunları nedeniyle başarısız olan "Istek zaman aşımı" HTTP ayarı ve isteklerin aşılmasından dolayı başarısız olan istekleri içerir. Bu sayı, sağlıklı bir arka uç kullanılamadığından başarısız olan sorunları içermez. arka uçtan 4xx ve 5 xx yanıt, bu ölçümün bir parçası olarak kabul edilmez.

- **Yanıt durumu**

  Application Gateway tarafından döndürülen HTTP yanıt durumu. Yanıt durum kodu dağıtımı, 5 xx, 3xx, 4xx ve 5xx kategorilerindeki yanıtları göstermek için daha fazla kategorilere ayrılabilir.

- **İşleme hızı**

  Application Gateway saniyede hizmet verilen bayt sayısı

- **Toplam Istek sayısı**

  Application Gateway tarafından sunulan başarılı istek sayısı. İstek sayısı, her/belirli bir arka uç havuzu için sayıyı göstermek üzere daha fazla filtrelenebilir-http ayar birleşimi.

- **Web uygulaması güvenlik duvarı engellenen Istek sayısı**
- **Web uygulaması güvenlik duvarı engellenen Istek dağıtımı**
- **Web uygulaması güvenlik duvarı toplam kural dağıtımı**

### <a name="backend-metrics"></a>Arka uç ölçümleri

Application Gateway için aşağıdaki ölçümler kullanılabilir:

- **İyi durumda olan konak sayısı**

  Sistem durumu araştırması tarafından sağlıklı olarak belirlenen arka uç sayısı. Belirli bir arka uç havuzundaki sağlıklı ana bilgisayar sayısını göstermek için arka uç havuzu başına filtre uygulayabilirsiniz.

- **İyi durumda olmayan konak sayısı**

  Durum araştırmasının sağlıksız olduğunu belirlenen arka uçların sayısı. Belirli bir arka uç havuzundaki sağlıksız ana bilgisayar sayısını göstermek için arka uç havuzu başına filtre uygulayabilirsiniz.

## <a name="metrics-visualization"></a>Ölçüm görselleştirme

Uygulama ağ geçidine gidin, **izleme** ' nin altında **ölçümleri** seçin. Kullanılabilir değerleri görüntülemek için **ÖLÇÜM** açılan listesini seçin.

Aşağıdaki görüntüde, son 30 dakika boyunca üç ölçüm görüntülenirken bir örnek görürsünüz:

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="Ölçüm görünümü." lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

Geçerli ölçüm listesini görmek için bkz. [Azure izleyici Ile desteklenen ölçümler](../azure-monitor/essentials/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Ölçümlerde uyarı kuralları

Bir kaynağın ölçümlerine göre uyarı kuralları başlatabilirsiniz. Örneğin, bir uyarı, uygulama ağ geçidinin üretilen işi belirli bir dönem üzerinde, aşağıda veya bir eşiğin üzerinde ise bir Web kancasını veya bir yöneticiye e-posta gönderebilir.

Aşağıdaki örnek, aktarım hızı bir eşiğe ulaştığında bir yöneticiye e-posta gönderen bir uyarı kuralı oluşturma işleminde size yol gösterir:

1. **Kural Ekle** sayfasını açmak için **ölçüm uyarısı Ekle** ' yi seçin. Bu sayfaya ölçüm sayfasından da ulaşabilirsiniz.

   !["Ölçüm uyarısı Ekle" düğmesi][6]

2. **Kural Ekle** sayfasında, ad, koşul ve bildirim bölümlerini doldurun ve **Tamam**' ı seçin.

   * **Koşul** seçicide dört değerden birini seçin: **büyüktür**, **büyüktür veya eşittir**, **küçüktür** veya **küçüktür veya eşittir**.

   * **Süre** seçicide beş dakikadan altı saat arasında bir dönem seçin.

   * **E-posta sahipleri, katkıda bulunanlar ve okuyucular**' ı seçerseniz, e-posta söz konusu kaynağa erişimi olan kullanıcılara göre dinamik olabilir. Aksi takdirde, **ek yönetici e-postaları** kutusunda kullanıcıların virgülle ayrılmış bir listesini sağlayabilirsiniz.

   ![Kural sayfası ekle][7]

Eşiğe ihlal olursa, aşağıdaki görüntüde olana benzer bir e-posta gönderilir:

![İhlal eşiği için e-posta][8]

Bir ölçüm uyarısı oluşturduktan sonra uyarıların bir listesi görüntülenir. Tüm uyarı kurallarına genel bir bakış sağlar.

![Uyarıların ve kuralların listesi][9]

Uyarı bildirimleri hakkında daha fazla bilgi edinmek için bkz. [uyarı bildirimleri alma](../azure-monitor/alerts/alerts-overview.md).

Web kancaları ve bunları uyarılarla nasıl kullanabileceğiniz hakkında daha fazla bilgi edinmek için [Azure ölçüm uyarısında Web kancası yapılandırma](../azure-monitor/alerts/alerts-webhooks.md)makalesini ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure izleyici günlüklerini](../azure-monitor/insights/azure-networking-analytics.md)kullanarak sayaç ve olay günlüklerini görselleştirin.
* [Azure etkinlik günlüğünüzü Power BI blog gönderisi Ile görselleştirin](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) .
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
