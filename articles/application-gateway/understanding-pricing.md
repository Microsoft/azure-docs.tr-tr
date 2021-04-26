---
title: Fiyatlandırmayı anlama-Azure Application Gateway
description: Bu makalede, Azure Application Gateway ve Web uygulaması güvenlik duvarı için hem v1 hem de v2 SKU 'Larına yönelik faturalandırma süreci açıklanmaktadır.
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/01/2020
ms.author: azhussai
ms.openlocfilehash: 89ba6b7a69c95951a083628f23be68d811c7768c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96601620"
---
# <a name="understanding-pricing-for-azure-application-gateway-and-web-application-firewall"></a>Azure Application Gateway ve Web uygulaması güvenlik duvarı için fiyatlandırmayı anlama

>[!NOTE]
>Bu makalede gösterilen fiyatlar örnektir ve yalnızca çizim amaçlıdır. Bölgenize göre fiyatlandırma bilgileri için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/application-gateway/).

Azure Application Gateway, Azure üzerinde ölçeklenebilir, yüksek oranda kullanılabilir ve güvenli Web uygulaması teslimine izin veren bir katman 7 Yük Dengeleme çözümüdür.

Application Gateway ile ilişkili ön maliyet veya sonlandırma maliyeti yoktur.
Yalnızca önceden sağlanmış olan ve gerçek saatlik tüketim temelinde kullanılan kaynaklar için faturalandırılacaksınız. Application Gateway ilişkili maliyetler iki bileşene sınıflandırılır: sabit maliyetler ve değişken maliyetler. Her bir bileşen içindeki gerçek maliyetler, kullanılan SKU 'ya göre farklılık gösterir.

Bu makalede, her SKU ile ilişkili maliyetler açıklanmakta ve kullanıcıların Azure Application Gateway ile ilişkili maliyetleri planlamak ve yönetmek için bu belgeyi kullanmaları önerilir.

## <a name="v1-skus"></a>V1 SKU 'Ları

Standart Application Gateway ve WAF v1 SKU 'Ları bir birleşimi olarak faturalandırılır:

* Sabit maliyet

    İstekleri işlemek için belirli bir Application Gateway/WAF türünün sağlandığı ve çalıştığı zamana göre maliyet.
    Sabit maliyet bileşeni aşağıdaki faktörleri dikkate alır:
    * Katman standardı Application Gateway veya WAF
    * Boyut-küçük, orta & büyük
    * Örnek sayısı-dağıtılacak örnek sayısı

    N örnekleri için, ilişkili maliyetler belirli bir katmanın bir örneğinin (Standart & WAF) & Boyutu (küçük, orta & büyük) birleşimi olacak şekilde N * maliyetlidir.

* Değişken maliyeti

    Application Gateway/WAF tarafından işlenen veri miktarına göre maliyet. Application Gateway tarafından işlenen istek ve yanıt baytları, faturalandırma için kabul edilir.

Toplam maliyet = sabit maliyet + değişken maliyet

### <a name="standard-application-gateway"></a>Standart Application Gateway

Sabit maliyet & değişken maliyeti Application Gateway türüne göre hesaplanır.
Aşağıdaki tabloda, Doğu ABD fiyatlandırmasının bir anlık görüntüsüne dayalı örnek fiyatlar gösterilmektedir ve yalnızca çizim amaçlıdır.

#### <a name="fixed-cost-east-us-region-pricing"></a>Sabit maliyet (Doğu ABD bölge fiyatlandırması)

|              Application Gateway türü             |  Maliyetler ($/hr)  |
| ------------------------------------------------- | ---------------|
|                     Küçük                         |    $0,025      |
|                     Orta                        |    $0,07       |
|                     Büyük                         |    $0,32       |

Aylık fiyat tahminleri, ayda 730 saatlik kullanım üzerinden hesaplanır.

#### <a name="variable-cost-east-us-region-pricing"></a>Değişken maliyeti (Doğu ABD bölge fiyatlandırması)

|              Işlenen veri             |  Küçük ($/GB)  |  Orta (/GB) |  Büyük ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| İlk 10 TB/ay                       |     $0,008     |      Ücretsiz      |     Ücretsiz      |
| Sonraki 30 TB (10 – 40 TB)/ay             |     $0,008     |     $0,007     |     Ücretsiz      |
| 40 TB/ay                        |     $0,008     |     $0,007     |     $0,0035   |

Bölgenize göre daha fazla fiyatlandırma bilgisi için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/application-gateway/).

### <a name="waf-v1"></a>WAF V1

Sabit maliyet & değişken maliyetleri, sağlanan Application Gateway türüne göre hesaplanır.

Aşağıdaki tabloda, Doğu ABD fiyatlandırmasının bir anlık görüntüsüne dayalı örnek fiyatlar gösterilmektedir ve yalnızca çizim amaçlıdır.

#### <a name="fixed-cost-east-us-region-pricing"></a>Sabit maliyet (Doğu ABD bölge fiyatlandırması)

|              Application Gateway türü             |  Maliyetler ($/hr)  |
| ------------------------------------------------- | ---------------|
|                     Küçük                         |       NA       |
|                     Orta                        |     $0,126     |
|                     Büyük                         |     $0,448     |

Aylık fiyat tahminleri, ayda 730 saatlik kullanım üzerinden hesaplanır.

#### <a name="variable-cost-east-us-region-pricing"></a>Değişken maliyeti (Doğu ABD bölge fiyatlandırması)

|              Işlenen veri             |  Küçük ($/GB)  |  Orta (/GB) |  Büyük ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| İlk 10 TB/ay                       |     $0,008     |      Ücretsiz      |     Ücretsiz      |
| Sonraki 30 TB (10 – 40 TB)/ay             |     $0,008     |     $0,007     |     Ücretsiz      |
| 40 TB/ay                        |     $0,008     |     $0,007     |     $0,0035   |

Bölgenize göre daha fazla fiyatlandırma bilgisi için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Giden veri aktarımları-uygulama ağ geçitlerinden Azure veri merkezlerinden çıkan veriler standart [veri aktarımı fiyatları](https://azure.microsoft.com/pricing/details/bandwidth/)üzerinden ücretlendirilir.

### <a name="example-1-a--standard-application-gateway-with-1-instance-count"></a>Örnek 1 (a) – 1 örnek sayısı ile standart Application Gateway

1 örnek içeren bir orta tür standart Application Gateway sağladığınızı ve bir ayda 500 GB işleme sağladığını varsayalım. Yukarıda bahsedilen fiyatlandırmayı kullanarak Application Gateway maliyetleriniz aşağıdaki şekilde hesaplanır:

Sabit fiyat = $0,07 * 730 (saatler) = $51,1 aylık fiyat tahminleri, ayda 730 saat kullanım üzerinden hesaplanır.

Değişken maliyetler = ücretsiz (Orta katmanda ayda ilk 10 TB işlenen için maliyet yoktur) toplam maliyet = $51,1 + 0 = $51,1 

> [!NOTE]
> Yüksek kullanılabilirlik senaryolarını desteklemek için, v1 SKU 'Ları için en az 2 örnek ayarlama yapmanız gerekir. [Application Gateway Için SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/) 'ya bakın

### <a name="example-1-b--standard-application-gateway-with--1-instance-count"></a>Örnek 1 (b) – > 1 örnek sayısı ile standart Application Gateway

Beş örnek içeren bir orta tür standart Application Gateway sağladığınızı ve ayda 500 GB işlem sağladığını varsayalım. Yukarıda bahsedilen fiyatlandırmayı kullanarak Application Gateway maliyetleriniz aşağıdaki şekilde hesaplanır:

Sabit fiyat = 5 (örnek sayısı) * $0,07 * 730 (saatler) = $255,5 aylık fiyat tahminleri, ayda saat başına 730 saat kullanım üzerinden hesaplanır.

Değişken maliyetler = ücretsiz (Orta katmanda ayda ilk 10 TB işlenen için maliyet yoktur) toplam maliyet = $255,5 + 0 = $255,5 

### <a name="example-2--waf-application-gateway"></a>Örnek 2 – WAF Application Gateway

Ayın ilk 15 günü için küçük bir tür standart Application Gateway ve büyük tür WAF Application Gateway sağladığınızı varsayalım. Küçük uygulama ağ geçidi, etkin olduğu süre içinde 15 TB işler ve büyük WAF uygulama ağ geçidi 100 TB 'yi etkin olduğu süre içinde işler. Yukarıda bahsedilen fiyatlandırmayı kullanarak Application Gateway maliyetleriniz aşağıdaki şekilde hesaplanır: 

###### <a name="small-instance-standard-application-gateway"></a>Küçük örnek standart Application Gateway

24 saat * 15 gün = 360 saat

Sabit fiyat = $0,025 * 360 (saatler) = $9

Değişken maliyetler = 10 * 1000 * $0.008/GB + 5 * 1000 * $0.008/GB = $120

Toplam maliyet = $9 + $120 = $129

###### <a name="large-instance-waf-application-gateway"></a>Büyük örnek WAF Application Gateway
24 saat * 15 gün = 360 saat

Sabit fiyat = $0,448 * 360 (saatler) = $161,28

Değişken maliyetler = 60 * 1000 * $0.0035/GB = $210 (büyük katmanda aylık olarak işlenen ilk 40 TB 'nin maliyeti yoktur)

Toplam maliyet = $161,28 + $210 = $371,28

## <a name="v2-skus"></a>V2 SKU 'Ları  

Application Gateway v2 ve WAF v2 SKU 'Ları otomatik ölçeklendirmeyi destekler ve varsayılan olarak yüksek kullanılabilirlik sağlanmasına neden olur.

### <a name="key-terms"></a>Anahtar terimleri

##### <a name="capacity-unit"></a>Kapasite birimi 
Kapasite birimi, birden çok parametre arasında bir Application Gateway için kapasite kullanımının ölçüsüdür.

Tek bir kapasite birimi aşağıdaki parametrelerden oluşur:
* 2500 kalıcı bağlantı
* 2,22-Mbps aktarım hızı
* 1 işlem birimi

Bu parametrelerden herhangi biri aşılırsa, diğer iki parametre bu tek kapasite biriminin sınırlarını aşmasa bile başka n kapasite birimi gerekir.
Yukarıdaki üç kullanım arasında en yüksek kullanıma sahip parametre, sırasıyla faturalandırılan kapasite birimlerini hesaplamak için dahili olarak kullanılır.

##### <a name="compute-unit"></a>İşlem birimi
İşlem birimi, tüketilen işlem kapasitesini ölçüdür. İşlem birimi tüketimini etkileyen faktörler, TLS bağlantısı/sn, URL yeniden yazma hesaplamaları ve WAF kural işleme. İşlem biriminin işleyebileceği isteklerin sayısı, TLS sertifikası anahtar boyutu, anahtar değişim algoritması, üst bilgi yeniden oluşturma ve WAF gelen istek boyutu gibi çeşitli ölçütlere bağlıdır.

İşlem birimi Kılavuzu:
* Standard_v2-her işlem birimi, RSA 2048 bit anahtar TLS sertifikası ile saniyede yaklaşık 50 bağlantı kapasitesine sahiptir.

* WAF_v2-her işlem birimi,% 70-30 oranında 70 trafik karması için saniyede yaklaşık 10 eşzamanlı isteği destekleyebilir. WAF performansı şu anda yanıt boyutundan etkilenmez.

##### <a name="instance-count"></a>Örnek Sayısı 
Application Gateway v2 SKU 'Larının kaynak ön sağlamasını, örnek sayısı bakımından tanımlanmıştır. Her örnek, işleme yeteneği bakımından en az 10 Kapasite birimi garantisi verir. Aynı örnek, kapasite birimi parametrelerine bağlı olarak farklı trafik desenleri için 10 ' dan fazla kapasite biriminden daha fazla destek sağlayabilir.

Otomatik ölçeklendirme için ayarlanan el ile tanımlanan ölçek ve sınırlar (minimum veya maksimum), örnek sayısı bakımından ayarlanır. Örnek sayısı için el ile ayarlanan ölçek ve otomatik ölçeklendirme yapılandırmasında minimum örnek sayısı, 10 Kapasite birimi/örneği ayıracaktır. Bu ayrılmış cu düzeyinde kapsanır, gerçek kaynak tüketiminden bağımsız olarak Application Gateway etkin olacağı sürece faturalandırılır. Gerçek tüketim 10 Kapasite birimi/örnek eşiğini kesişse, ek kapasite birimleri değişken bileşeni altında faturalandırılır.

V2 SKU 'Ları, iki bölümden oluşan tüketim ve koşula göre faturalandırılır:

* Sabit maliyetler

    Application Gateway v2/WAF v2 'nin sağlandığı ve istekleri işlemek için kullanılabildiği zamana göre maliyet. Bu, otomatik ölçeklendirmenin bir parçası olarak minimum örnek sayısı olarak 0 belirterek, 0 örnek ayrılsa bile yüksek kullanılabilirlik sağlar. 
    
    Sabit maliyet Ayrıca Application Gateway bağlı genel IP ile ilişkili maliyeti de içerir.

    Herhangi bir zamanda çalışan örneklerin sayısı, v2 SKU 'Larının sabit maliyetleri için bir faktör olarak değerlendirilmez. Bir Standard_V2 (veya WAF_V2) çalıştırmanın sabit maliyetleri aynı Azure bölgesinde çalışan örnek sayısından bağımsız olarak saat başına aynı olacaktır.

* Kapasite birim maliyetleri 

    Gelen istekleri işlemek için gerekli olduğu gibi, ayrılan veya kullanılan kapasite birimi sayısını temel alan maliyet.  Tüketim tabanlı maliyetler saatlik olarak hesaplanır.

Toplam maliyetler = sabit maliyetler + kapasite birim maliyetleri

> [!NOTE]
> Kısmi bir saat, tam bir saat olarak faturalandırılır.

Aşağıdaki tabloda, Doğu ABD fiyatlandırmasının bir anlık görüntüsüne dayalı örnek fiyatlar gösterilmektedir ve yalnızca çizim amaçlıdır.

#### <a name="fixed-costs-east-us-region-pricing"></a>Sabit maliyetler (Doğu ABD bölge fiyatlandırması)

|              V2 SKU             |  Maliyetler ($/hr)  |
| ------------------------------- | ---------------|
|            Standard_V2          |     $0,246     |
|              WAF_V2             |     $0,443     |

Aylık fiyat tahminleri, ayda 730 saatlik kullanım üzerinden hesaplanır.

#### <a name="variable-costs-east-us-region-pricing"></a>Değişken maliyetler (Doğu ABD bölge fiyatlandırması)

|        Kapasite birimi         |  Standard_V2 ($/hr)  |  WAF_V2 ($/hr) |
| ---------------------------- | -------------------- | -------------- |
|             1 CU             |        $0,008        |     $0,0144    |

Bölgenize göre daha fazla fiyatlandırma bilgisi için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Giden veri aktarımları-uygulama ağ geçitlerinden Azure veri merkezlerinden çıkan veriler standart [veri aktarımı fiyatları](https://azure.microsoft.com/pricing/details/bandwidth/)üzerinden ücretlendirilir.

### <a name="example-1-a--manual-scaling"></a>Örnek 1 (a) – manuel ölçeklendirme 
El ile ölçeklendirirken, tüm ay için 8 örneğe ayarlanmış bir Standard_V2 Application Gateway sağladığınızı varsayalım. Bu süre boyunca ortalama 88,8 Mbps veri aktarımı alır.

Yukarıda bahsedilen fiyatlandırmayı kullanarak Application Gateway maliyetleriniz aşağıdaki şekilde hesaplanır:

1 CU 2,22 Mbps aktarım hızını işleyebilir.

Cu düzeyinde kapsanır 88,8 Mbps = 88,8/2,22 = 40 cu düzeyinde kapsanır 

Önceden sağlanmış cu düzeyinde kapsanır = 8 (örnek sayısı) * 10 = 80 

80 (ayrılmış kapasite) > 40 (gerekli kapasite), ek bir cu düzeyinde kapsanır gerekmez. 

Sabit fiyat = $0,246 * 730 (saatler) = $179,58

Değişken maliyetler = $0,008 * 8 (örnek birimleri) * 10 (Kapasite birimleri) * 730 (saatler) = $467,2

Toplam maliyet = $179,58 + $467,2 = $646,78

![El Ile ölçeklendirme 1 diyagramı.](./media/pricing/manual-scale-1.png)

### <a name="example-1-b--manual-scaling-with-traffic-going-beyond-provisioned-capacity"></a>Örnek 1 (b) – sağlanan kapasitenin ötesine geçen trafikle El Ile ölçeklendirme

El ile ölçeklendirmenin, tüm ayın 3 örneğine ayarlandığı bir Standard_V2 Application Gateway sağladığınızı varsayalım. Bu süre boyunca ortalama 88,8 Mbps veri aktarımı alır.

Yukarıda bahsedilen fiyatlandırmayı kullanarak Application Gateway maliyetleriniz aşağıdaki şekilde hesaplanır:

1 CU 2,22 Mbps aktarım hızını işleyebilir.

88,8 Mbps = 88,8/2,22 = 40 cu düzeyinde kapsanır 

Önceden sağlanmış cu düzeyinde kapsanır = 3 (örnek sayısı) * 10 = 30 

40 (gerekli kapasite) > 30 (ayrılmış kapasite) olduğundan ek cu düzeyinde kapsanır gereklidir.
Kullanılan ek cu düzeyinde kapsanır sayısı, her örnekle kullanılabilen boş kapasiteye bağlıdır.

10 ek cu düzeyinde kapsanır işleme kapasitesi, 3 ayrılmış örnek içinde kullanılmak üzere kullanılabilirse.

Sabit fiyat = $0,246 * 730 (saatler) = $179,58

Değişken maliyetler = $0,008 * (3 (örnek birimleri) * 10 (Kapasite birimleri) + 10 (ek kapasite birimleri)) * 730 (saatler) = $233,6

Toplam maliyet = $179,58 + $233,6 = $413,18

Ancak, yalnızca 7 ek cu düzeyinde kapsanır, işleme kapasitesi eşdeğerini 3 ayrılmış örnek içinde kullanılmak üzere kullanılabilir.
Bu senaryoda Application Gateway kaynak ölçeklendirildi ve gecikme süresi veya isteklerin düşürülme süresini arttırabilecek olabilir.

Sabit fiyat = $0,246 * 730 (saatler) = $179,58

Değişken maliyetler = $0,008 * (3 (örnek birimleri) * 10 (Kapasite birimleri) + 7 (ek kapasite birimleri)) * 730 (saatler) = $216,08

Toplam maliyet = $179,58 + $216,08 = $395,66


![Manuel ölçek 2 diyagramı.](./media/pricing/manual-scale-2.png)

> [!NOTE]
> El Ile ölçeklendirilmesi durumunda, ayrılmış örneklerin maksimum işlem kapasitesini aşan ek istekler, uygulamanızın kullanılabilirliğine neden olabilir. Yüksek yük durumunda, ayrılmış örnekler, gelen isteklerin yapılandırmasına ve türüne bağlı olarak 10 ' dan fazla kapasite işlem kapasitesi sağlayabiliyor olabilir. Ancak trafik gereksinimlerinize göre örnek sayısının sağlanması önerilir.

### <a name="example-2--waf_v2-instance-with-autoscaling"></a>Örnek 2 – otomatik ölçeklendirmeyle WAF_V2 örneği

Otomatik Ölçeklendirme özelliği etkinken bir WAF_V2 sağladığınızı ve en küçük örnek sayısını ayın tamamı için 6 olarak ayarlayadığınızı varsayalım. İstek yükü WAF örneğinin ölçeğini ölçeklendirmek ve 65 kapasite birimi kullanmasını (5 kapasite birimi kadar ölçeklendirirken, 60 birim ayrıldığından), tüm ay için.
Yukarıda bahsedilen fiyatlandırmayı kullanarak Application Gateway maliyetleriniz aşağıdaki şekilde hesaplanır:

Aylık fiyat tahminleri, ayda 730 saatlik kullanım üzerinden hesaplanır.

Sabit fiyat = $0,443 * 730 (saatler) = $323,39

Değişken maliyetler = $0,0144 * 65 (Kapasite birimleri) * 730 (saatler) = $683,28

Toplam maliyet = $323,39 + $683,28 = $1006,67

![Otomatik ölçek 2 diyagramı.](./media/pricing/auto-scale-1.png)

> [!NOTE]
> Application Gateway için gözlemlenen gerçek trafik, bu tür bir sabit trafik düzenine sahip değildir ve Application Gateway gözlemlenen yükün gerçek kullanıma göre dalgalanmasına neden olur.

### <a name="example-3-a--waf_v2-instance-with-autoscaling-and-0-min-scale-config"></a>Örnek 3 (a) – otomatik ölçeklendirme ve 0 dakikalık ölçek yapılandırması ile WAF_V2 örnek

Otomatik ölçeklendirmeyi etkin bir WAF_V2 sağladığınızı ve tüm ay için en düşük örnek sayısını 0 olarak ayarlayadığınızı varsayalım. WAF üzerindeki istek yükü, tüm ay için en az, saat başına bir saat olarak mevcuttur. Yük, tek bir kapasite birimi kapasitesinin altında.
Yukarıda bahsedilen fiyatlandırmayı kullanarak Application Gateway maliyetleriniz aşağıdaki şekilde hesaplanır:

Aylık fiyat tahminleri, ayda 730 saatlik kullanım üzerinden hesaplanır.

Sabit fiyat = $0,443 * 730 (saatler) = $323,39

Değişken maliyetler = $0,0144 * 1 (Kapasite birimleri) * 730 (saatler) = $10,512

Toplam maliyet = $323,39 + $10,512 = $333,902

### <a name="example-3-b--waf_v2-instance-with-autoscaling-with-0-min-instance-count"></a>Örnek 3 (b) – 0 dakikalık örnek sayısıyla otomatik ölçeklendirmeyle WAF_V2 örnek

Otomatik ölçeklendirmeyi etkin bir WAF_V2 sağladığınızı ve tüm ay için en düşük örnek sayısını 0 olarak ayarlayadığınızı varsayalım. Ancak, tüm ay için WAF örneğine yönlendirilen 0 trafik vardır.
Yukarıda bahsedilen fiyatlandırmayı kullanarak Application Gateway maliyetleriniz aşağıdaki şekilde hesaplanır:

Sabit fiyat = $0,443 * 730 (saatler) = $323,39

Değişken maliyetler = $0,0144 * 0 (Kapasite birimleri) * 730 (saatler) = $0

Toplam maliyet = $323,39 + $0 = $323,39

### <a name="example-3-c--waf_v2-instance-with-manual-scaling-set-to-1-instance"></a>Örnek 3 (C) – el ile ölçeklendirilmesi 1 örneğe ayarlanmış WAF_V2 örnek

Bir WAF_V2 sağladığınızı ve tüm ay için kabul edilebilir en düşük değer olan 1 örnek ile el ile ölçeklendirmeye ayarlandığını varsayalım. Ancak, tüm ay için WAF 'ye yöneltilen 0 trafik vardır.
Yukarıda bahsedilen fiyatlandırmayı kullanarak Application Gateway maliyetleriniz aşağıdaki şekilde hesaplanır:

Aylık fiyat tahminleri, ayda 730 saatlik kullanım üzerinden hesaplanır.

Sabit fiyat = $0,443 * 730 (saatler) = $323,39

Değişken maliyetler = $0,0144 * 1 (örnek sayısı) * 10 (Kapasite birimleri) * 730 (saatler) = $105,12

Toplam maliyet = $323,39 + $105,12 = $428,51

### <a name="example-4--waf_v2-with-autoscaling-capacity-unit-calculations"></a>Örnek 4 – otomatik ölçeklendirmeyle WAF_V2, kapasite birimi hesaplamaları

Otomatik ölçeklendirmeyi etkin bir WAF_V2 sağladığınızı ve tüm ay için en düşük örnek sayısını 0 olarak ayarlayadığınızı varsayalım. Bu süre boyunca, ortalama 8,88 Mbps veri aktarımı ile 25 yeni TLS bağlantısı/sn alır.
Yukarıda bahsedilen fiyatlandırmayı kullanarak Application Gateway maliyetleriniz aşağıdaki şekilde hesaplanır:

Aylık fiyat tahminleri, ayda 730 saatlik kullanım üzerinden hesaplanır.

Sabit fiyat = $0,443 * 730 (saatler) = $323,39

Değişken maliyetler = $0,0144 * 730 (saatler) * {Max (25/50, 8.88/2.22)} = $23,36 (4 MB/sn 8,88 işlemek için 4 Kapasite birimi gerekir)

Toplam maliyet = $323,39 + $23,36 = $346,75

### <a name="example-5-a--standard_v2-with-autoscaling-time-based-calculations"></a>Örnek 5 (a) – otomatik ölçeklendirmeyle Standard_V2, zaman tabanlı hesaplamalar

Otomatik ölçeklendirme özellikli bir standard_V2 sağladığınızı ve minimum örnek sayısını 0 olarak ayarlamış ve bu uygulama ağ geçidinin 2 saat boyunca etkin olduğunu varsayalım.
İlk saat boyunca, 10 Kapasite birimi tarafından işlenebilen ve ikinci saat içinde yükü işlemek için 20 kapasite birimi gereken trafiği alan trafiği alır.
Yukarıda bahsedilen fiyatlandırmayı kullanarak Application Gateway maliyetleriniz aşağıdaki şekilde hesaplanır:

Sabit fiyat = $0,246 * 2 (saat) = $0,492

Değişken maliyetler = $0,008 * 10 (Kapasite birimleri) * 1 (saatler) + $0,008 * 20 (Kapasite birimleri) * 1 (saat) = $0,24

Toplam maliyet = $0,492 + $0,24 = $0,732


## <a name="monitoring-billed-usage"></a>Faturalandırılan kullanım izleniyor

Farklı parametrelerin (işlem birimi, işleme & kalıcı bağlantılar) ve **izleme** bölümü altındaki Application Gateway ölçümlerinin bir parçası olarak kullanılan kapasite birimlerinin tüketim miktarını görüntüleyebilirsiniz.

![Ölçümler bölümünün diyagramı.](./media/pricing/metrics-1.png)

### <a name="useful-metrics-for-cost-estimation"></a>Maliyet tahmini için yararlı ölçümler

* Geçerli kapasite birimleri

    Trafiğin üç parametre arasında yük dengelenmesi için kullanılan kapasite birimi sayısı-geçerli bağlantılar, verimlilik ve Işlem birimi

* Sabit Faturalanabilir Kapasite Birimleri

    Application Gateway yapılandırmasındaki en düşük örnek sayısı ayarı (bir örnek 10 kapasite birimine çevrilir) için sağlanan Kapasite birimi sayısı alt sınırı.

* Tahmini Faturalanan Kapasite birimleri

    Tahmini Faturalanan Kapasite birimleri, faturalamayı tahmin etmek için kullanılan kapasite birimi sayısını belirtir. Bu, Geçerli kapasite birimleri (trafiğin yükünü dengelemek için gereken kapasite birimleri) ve Sabit faturalanabilir kapasite birimleri (sağlanan minimum kapasite birimleri) değerlerinden daha büyük olanıdır.

Performans sorunlarını anlamak ve gereken kapasite birimi sayısını tahmin etmek için aktarım hızı, geçerli bağlantılar ve işlem birimleri gibi daha fazla ölçüm de mevcuttur. Ayrıntılı bilgiler [Application Gateway ölçümlerinde](application-gateway-metrics.md) mevcuttur

#### <a name="example---estimating-capacity-units-being-utilized"></a>Örnek-kullanılan kapasite birimleri tahmin ediliyor

**Gözlemlenen ölçümler:**

* İşlem birimleri = 17,38
* Verimlilik = 1.37 d bayt/sn-10,96 Mbps
* Geçerli bağlantılar = 123.08 k
* Hesaplanan kapasite birimleri = Max (17.38, 10.96/2.22, 123.08 k/2500) = 49,232

Ölçümler 'de gözlemlenen Kapasite birimleri = 49,23

## <a name="next-steps"></a>Sonraki adımlar

Fiyatlandırma Application Gateway Azure 'da nasıl çalıştığı hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Azure Application Gateway fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/application-gateway/)
* [Azure Application Gateway Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/en-us/pricing/calculator/?service=application-gateway)
