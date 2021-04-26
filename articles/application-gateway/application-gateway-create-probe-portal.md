---
title: Portalı kullanarak özel bir araştırma oluşturma
titleSuffix: Azure Application Gateway
description: Portalı kullanarak Application Gateway için özel bir araştırma oluşturmayı öğrenin
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 5d2760415e4f4ef3b181f2fb69802659fec3ef66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95975964"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Portalı kullanarak Application Gateway için özel bir araştırma oluşturma

> [!div class="op_single_selector"]
> * [Azure portalı](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Klasik PowerShell](application-gateway-create-probe-classic-ps.md)

Bu makalede, mevcut bir uygulama ağ geçidine Azure portal aracılığıyla özel bir sistem durumu araştırması eklersiniz. Azure Application Gateway, sistem durumu araştırmalarını kullanarak arka uç havuzundaki kaynakların sistem durumunu izler.

## <a name="before-you-begin"></a>Başlamadan önce

Henüz bir uygulama ağ geçidiniz yoksa, birlikte çalışmak üzere bir uygulama ağ geçidi oluşturmak için [Application Gateway oluştur](./quick-create-portal.md) ' u ziyaret edin.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Application Gateway v2 SKU 'SU için araştırma oluşturma

Yoklamalar, Portal üzerinden iki adımlı bir işlemde yapılandırılır. İlk adım, araştırma yapılandırması için gereken değerleri girdir. İkinci adımda, bu araştırma yapılandırmasını kullanarak arka uç durumunu test edersiniz ve araştırmayı kaydedersiniz. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Araştırma özelliklerini girin

1. [Azure portalında](https://portal.azure.com) oturum açın. Henüz bir hesabınız yoksa, [bir aylık ücretsiz deneme sürümü](https://azure.microsoft.com/free) için kaydolabilirsiniz

2. Azure Portal Sık Kullanılanlar bölmesinde, Tüm kaynaklar’a tıklayın. Tüm kaynaklar dikey penceresinde uygulama ağ geçidine tıklayın. Seçili abonelikte zaten çeşitli kaynaklar varsa, DNS bölgesine kolaylıkla erişmek için Ada göre filtrele... kutusuna partners.contoso.net girebilirsiniz.

3. **Durum araştırmaları** ' nı seçin ve yeni bir sistem durumu araştırması eklemek için **Ekle** ' yi seçin.

   ![Yeni araştırma ekle][4]

4. **Durum araştırması Ekle** sayfasında, araştırma için gerekli bilgileri doldurun ve tamamlandığında **Tamam**' ı seçin.

   |**Ayar** | **Değer** | **Ayrıntılar**|
   |---|---|---|
   |**Ad**|Customaraştırması|Bu değer, portalda erişilebilen araştırmayı verilen kolay bir addır.|
   |**Protokol**|HTTP veya HTTPS | Sistem durumu araştırmasının kullandığı protokol. |
   |**Konak**|Yani contoso.com|Bu değer, uygulama sunucusunda çalışan sanal ana bilgisayarın (VM ana bilgisayar adından farklı) adıdır. Araştırma \<protocol\> ://: öğesine gönderiliyor \<host name\>\<port\>/\<urlPath\>|
   |**Arka uç HTTP ayarlarından konak adı Seç**|Evet veya Hayır|Araştırmanın *konak* üstbilgisini, bu ARAŞTıRMANıN ilişkilendirildiği http ayarlarından ana bilgisayar adına ayarlar. Azure App Service gibi çok kiracılı arka uçlar söz konusu olduğunda özel olarak gereklidir. [Daha fazla bilgi edinin](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Arka uç HTTP ayarlarından bağlantı noktası seç**| Evet veya Hayır|Sistem durumu araştırmasının *bağlantı noktasını* , bu ARAŞTıRMANıN ilişkilendirildiği http ayarlarından bağlantı noktasına ayarlar. Hayır ' ı seçerseniz, kullanmak üzere özel bir hedef bağlantı noktası girebilirsiniz |
   |**Bağlantı noktası**| 1-65535 | Sistem durumu araştırmaları için kullanılacak özel bağlantı noktası | 
   |**Yol**|/veya herhangi bir geçerli yol|Özel araştırma için tam URL 'nin geri kalanı. Geçerli bir yol '/' ile başlar. Http:/contoso.com ' nin varsayılan yolu için \/ yalnızca '/' kullanın |
   |**Aralık (saniye)**|30|Araştırmanın sistem durumunu denetlemek için ne sıklıkta çalıştırıldığı. 30 saniyeden daha düşük bir değer ayarlamanız önerilmez.|
   |**Zaman aşımı (saniye)**|30|Araştırmanın zaman aşımından önce bekleyeceği süre. Bu zaman aşımı süresi içinde geçerli bir yanıt alınmıyorsa, araştırma başarısız olarak işaretlenir. Zaman aşımı aralığı, arka uç sistem durumu sayfasının kullanılabilir olduğundan emin olmak için bir http çağrısının yüksek olması gerekir. Zaman aşımı değeri bu araştırma ayarında kullanılan ' Interval ' değerinden daha fazla olmamalıdır veya HTTP ayarındaki ' Istek zaman aşımı ' değeri bu yoklamasıyla ilişkilendirilecek.|
   |**İyi durumda olmayan durum eşiği**|3|Hatalı olarak kabul edilecek ardışık başarısız deneme sayısı. Eşik 1 veya daha fazlasına ayarlanabilir.|
   |**Araştırma eşleştirme koşullarını kullanma**|Evet veya Hayır|Varsayılan olarak, 200 ve 399 arasındaki durum koduna sahip HTTP (S) yanıtı sağlıklı olarak değerlendirilir. Kabul edilebilir arka uç yanıt kodu aralığını veya arka uç yanıtı gövdesini değiştirebilirsiniz. [Daha fazla bilgi edinin](./application-gateway-probe-overview.md#probe-matching)|
   |**HTTP Ayarları**|açılan menüden seçim|Araştırma, burada seçilen HTTP ayarları ile ilişkilendirilir ve bu nedenle, seçilen HTTP ayarıyla ilişkili olan arka uç havuzunun sistem durumunu izler. Seçilen HTTP ayarında kullanılmakta olan araştırma isteği için aynı bağlantı noktasını kullanacaktır. Yalnızca diğer özel yoklamalar ile ilişkilendirilmemiş olan bu HTTP ayarlarını seçebilirsiniz. <br>Bu araştırma yapılandırmasında seçilen protokolle aynı protokolüne sahip ve yalnızca *arka uç http ayar anahtarından konak adı seçin* için aynı duruma sahip olan Association için yalnızca bu http ayarlarının kullanılabilir olduğunu unutmayın.|
   
   > [!IMPORTANT]
   > Araştırma, arka ucun durumunu yalnızca bir veya daha fazla HTTP ayarıyla ilişkilendirildiğinde izler. Bu, bu araştırmanın ilişkilendirildiği HTTP ayarları ile ilişkili olan arka uç havuzlarının arka uç kaynaklarını izler. Araştırma isteği \<protocol\> ://: olarak gönderilecek \<hostName\> \<port\> / \<urlPath\> .

### <a name="test-backend-health-with-the-probe"></a>Araştırma ile arka uç durumunu test etme

Araştırma özelliklerini girdikten sonra, araştırma yapılandırmasının doğru olduğunu ve arka uç kaynaklarının beklendiği gibi çalıştığını doğrulamak için arka uç kaynaklarının sistem durumunu test edebilirsiniz.

1. **Test** ' i seçin ve araştırmanın sonucunu aklınızda edin. Application Gateway, bu araştırma için kullanılan HTTP ayarları (ler) ile ilişkili arka uç havuzlarındaki tüm arka uç kaynaklarının sistem durumunu sınar. 

   ![Test arka uç durumu][5]

2. Sağlıksız bir arka uç kaynağı varsa, kaynağın sağlıksız durumunun nedenini anlamak için **Ayrıntılar** sütununu kontrol edin. Kaynak yanlış bir araştırma yapılandırması nedeniyle sağlıksız olarak işaretlenmişse, **araştırma bağlantısına geri dön** ' ü seçin ve araştırma yapılandırmasını düzenleyin. Aksi takdirde, arka uç ile ilgili bir sorun nedeniyle kaynak sağlıksız olarak işaretlenmişse, arka uç kaynağıyla ilgili sorunları çözün ve sonra arka ucunu tekrar inceleyerek **araştırma bağlantısına geri dönün** ve **Test**' i seçin.

   > [!NOTE]
   > Araştırmayı sağlıklı olmayan arka uç kaynaklarıyla bile kaydetmeyi seçebilirsiniz, ancak önerilmez. Bunun nedeni Application Gateway, istekleri yoklamanın sağlıksız olduğu belirlenen arka uç havuzundan arka uç sunucularına iletmeyecektir. Arka uç havuzunda sağlıklı kaynak olmaması durumunda uygulamanıza erişemeyeceksiniz ve bir HTTP 502 hatası alacaksınız.

   ![Araştırma sonucunu görüntüle][6]

3. Araştırmayı kaydetmek için **Ekle** ' yi seçin. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Application Gateway v1 SKU 'SU için araştırma oluşturma

Yoklamalar, Portal üzerinden iki adımlı bir işlemde yapılandırılır. İlk adım araştırmanın oluşturulması. İkinci adımda, uygulama ağ geçidinin arka uç http ayarlarına araştırma eklersiniz.

### <a name="create-the-probe"></a><a name="createprobe"></a>Araştırma oluşturma

1. [Azure portalında](https://portal.azure.com) oturum açın. Henüz bir hesabınız yoksa, [bir aylık ücretsiz deneme sürümü](https://azure.microsoft.com/free) için kaydolabilirsiniz

2. Azure Portal Sık Kullanılanlar bölmesinde, **Tüm kaynaklar**’ı seçin. **Tüm kaynaklar** sayfasında uygulama ağ geçidini seçin. Seçili abonelikte zaten çeşitli kaynaklar varsa, DNS bölgesine kolaylıkla erişmek için Ada göre filtrele... kutusuna partners.contoso.net girebilirsiniz.

3. **Yoklamalar** ' ı seçin ve ardından araştırma eklemek için **Ekle** ' yi seçin.

   ![Doldurulmuş bilgiler içeren araştırma dikey penceresi ekleme][1]

4. **Sistem durumu araştırması Ekle** dikey penceresinde, araştırma için gerekli bilgileri doldurun ve tamamlandığında **Tamam**' ı seçin.

   |**Ayar** | **Değer** | **Ayrıntılar**|
   |---|---|---|
   |**Ad**|Customaraştırması|Bu değer, portalda erişilebilen araştırmayı verilen kolay bir addır.|
   |**Protokol**|HTTP veya HTTPS | Sistem durumu araştırmasının kullandığı protokol. |
   |**Konak**|Yani contoso.com|Bu değer, uygulama sunucusunda çalışan sanal ana bilgisayarın (VM ana bilgisayar adından farklı) adıdır. Araştırma, (protokol)://(ana bilgisayar adı):(bağlantı noktası httpsetting)/Urlpath' e gönderilir.  Bu, Application Gateway üzerinde birden çok site yapılandırıldığında geçerlidir. Application Gateway tek bir site için yapılandırıldıysa, ' 127.0.0.1 ' girin.|
   |**Arka uç HTTP ayarlarından konak adı Seç**|Evet veya Hayır|Araştırmanın *konak* üstbilgisini, bu ARAŞTıRMANıN Ilişkilendirildiği http ayarıyla ilişkili arka uç havuzundaki arka uç kaynağının ana bilgisayar adına ayarlar. Azure App Service gibi çok kiracılı arka uçlar söz konusu olduğunda özel olarak gereklidir. [Daha fazla bilgi edinin](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Yol**|/veya herhangi bir geçerli yol|Özel araştırma için tam URL 'nin geri kalanı. Geçerli bir yol '/' ile başlar. Http:/contoso.com ' nin varsayılan yolu için \/ yalnızca '/' kullanın |
   |**Aralık (saniye)**|30|Araştırmanın sistem durumunu denetlemek için ne sıklıkta çalıştırıldığı. 30 saniyeden daha düşük bir değer ayarlamanız önerilmez.|
   |**Zaman aşımı (saniye)**|30|Araştırmanın zaman aşımından önce bekleyeceği süre. Bu zaman aşımı süresi içinde geçerli bir yanıt alınmıyorsa, araştırma başarısız olarak işaretlenir. Zaman aşımı aralığı, arka uç sistem durumu sayfasının kullanılabilir olduğundan emin olmak için bir http çağrısının yüksek olması gerekir. Zaman aşımı değeri bu araştırma ayarında kullanılan ' Interval ' değerinden daha fazla olmamalıdır veya HTTP ayarındaki ' Istek zaman aşımı ' değeri bu yoklamasıyla ilişkilendirilecek.|
   |**İyi durumda olmayan durum eşiği**|3|Hatalı olarak kabul edilecek ardışık başarısız deneme sayısı. Eşik 1 veya daha fazlasına ayarlanabilir.|
   |**Araştırma eşleştirme koşullarını kullanma**|Evet veya Hayır|Varsayılan olarak, 200 ve 399 arasındaki durum koduna sahip HTTP (S) yanıtı sağlıklı olarak değerlendirilir. Kabul edilebilir arka uç yanıt kodu aralığını veya arka uç yanıtı gövdesini değiştirebilirsiniz. [Daha fazla bilgi edinin](./application-gateway-probe-overview.md#probe-matching)|

   > [!IMPORTANT]
   > Ana bilgisayar adı sunucu adı ile aynı değil. Bu değer, uygulama sunucusunda çalışan sanal konağın adıdır. Araştırma \<protocol\> ://: öğesine gönderiliyor \<hostName\>\<port from http settings\>/\<urlPath\>

### <a name="add-probe-to-the-gateway"></a>Ağ geçidine araştırma ekleme

Artık araştırma oluşturuldığına göre, ağ geçidine eklemek zaman alabilir. Araştırma ayarları, uygulama ağ geçidinin arka uç http ayarları üzerinde ayarlanır.

1. Uygulama ağ geçidinde **http ayarları** ' na tıklayın, yapılandırma dikey penceresini açmak için pencerede listelenen geçerli arka uç http ayarları ' na tıklayın.

   ![https ayarları penceresi][2]

2. **Appgatewaybackendhttpsettings** ayarları sayfasında, **özel araştırma kullan** onay kutusunu işaretleyin ve **özel araştırma** açılır penceresinde [araştırma oluştur](#createprobe) bölümünde oluşturulan araştırmayı seçin.
   Tamamlandığında **Kaydet** ' e tıklayın ve ayarlar uygulanır.

## <a name="next-steps"></a>Sonraki adımlar

Arka uç kaynaklarının sistem durumunu, [arka uç sistem durumu görünümü](./application-gateway-diagnostics.md#back-end-health)kullanılarak araştırarak belirlendiği şekilde görüntüleyin.

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
