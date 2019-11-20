---
title: 'Öğretici: Azure IoT Central operatör panosunu özelleştirme'
description: Bu öğreticide, bir IoT Central uygulamasında operatör panosunun nasıl özelleştirileceği ve cihazların yönetilmesi gösterilmektedir.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 0dcabada522bf8189d163936551ea94cb4dd2657
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112908"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Öğretici: işletmen panosunu özelleştirme ve Azure IoT Central cihazları yönetme

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu öğreticide, bir Oluşturucu olarak Azure IoT Central mağaza içi analiz uygulamanızda operatör panosunu nasıl özelleştireceğiniz gösterilmektedir. Uygulama işleçleri, uygulamayı çalıştırmak ve ekli cihazları yönetmek için özelleştirilmiş panoyu kullanabilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Pano adını değiştirme
> * Panodaki görüntü kutucukları özelleştirme
> * Yerleşimi değiştirmek için kutucukları düzenleyin
> * Görüntüleme koşullarına telemetri kutucukları ekleme
> * Cihaz ayrıntılarını göstermek için özellik kutucukları ekleyin
> * Çalıştır komutlarına komut kutucukları ekleyin

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce, Oluşturucu Azure IoT Central mağaza 'da analiz uygulaması oluşturmak ve cihaz eklemek için ilk öğreticiyi tamamlaması gerekir:

* [Azure IoT Central 'da bir mağaza Analytics uygulaması oluşturma](./tutorial-in-store-analytics-create-app-pnp.md) (gerekli)

## <a name="change-the-dashboard-name"></a>Pano adını değiştirme
Operatör panosunu özelleştirmek için, uygulamanızda Varsayılan panoyu düzenlersiniz. İsteğe bağlı olarak, ek yeni panolar oluşturabilirsiniz. Uygulamanızda panoyu özelleştirmenin ilk adımı, adı değiştirbir addır.

1. [Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) Web sitesine gidin.

1. [Azure 'da bir mağaza içi analiz uygulaması oluşturma IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) öğreticide oluşturduğunuz durum izleme uygulamasını açın.

1. Pano araç çubuğunda **Düzenle** ' yi seçin. Düzenleme modunda, panonun görünüm, düzen ve içeriğini özelleştirebilirsiniz.

    ![Azure IoT Central panoyu düzenleme](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-edit.png)

1. İsterseniz sol bölmeyi gizleyin. Sol bölmenin gizlenmesi panoyu düzenlemekte daha büyük bir çalışma alanı sağlar.

1. Pano adı bölümünde panonuz için kolay bir ad girin **.** Bu öğretici contoso adlı kurgusal bir şirket kullanır ve örnek Pano adı *contoso panodur*. 

1. **Kaydet**’i seçin. Bu, yaptığınız değişiklikleri panoya kaydeder ve düzenleme modunu devre dışı bırakır.

    ![Azure IoT Central Pano adını değiştir](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Panodaki görüntü kutucukları özelleştirme
Bir Azure IoT Central uygulama panosu, bir veya daha fazla kutucukla oluşur. Kutucuk, panoda içerik görüntülemek için dikdörtgen bir kapsayıcıdır. Çeşitli içerik türlerini kutucuklar ile ilişkilendirirsiniz ve bir pano mizanpajını özelleştirmek için kutucukları sürükleyip bırakın ve yeniden boyutlandırın. İçeriği görüntülemek için birkaç kutucuk türü vardır. Görüntü kutucukları görüntüleri içerir ve kullanıcıların görüntüye tıklamalarını sağlayan bir URL ekleyebilirsiniz. Etiket kutucukları düz metin görüntüler. Markı kutucukları, biçimlendirilen içerik içerir ve HTML olarak işleyen bir görüntü, URL, başlık ve markı kodu ayarlamanıza olanak sağlar. Telemetri, özellik veya komut kutucukları, cihaza özgü verileri görüntüler. 

Bu bölümde, panodaki görüntü kutucukları özelleştirmeyi öğreneceksiniz.

Panoda marka resmi görüntüleyen görüntü kutucuğunu özelleştirmek için:

1. Pano araç çubuğunda **Düzenle** ' yi seçin. 

1. Northwind marka görüntüsünü görüntüleyen görüntü kutucuğunda **Yapılandır** ' ı seçin. 

    ![Azure IoT Central marka görüntüsünü düzenleme](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-edit.png)

1. **Başlığı**değiştirin. Bir Kullanıcı görüntünün üzerine geldiğinde başlık görüntülenir.

1. **Görüntü**seçin. Bir iletişim kutusu açılır ve özel bir görüntü yüklemeniz sağlanır. 

1. İsteğe bağlı olarak, görüntü için bir URL belirtin.

1. **Güncelleştirme yapılandırması**' nı seçin. **Güncelleştirme yapılandırması** düğmesi panodaki değişiklikleri kaydeder ve düzenleme modunu etkin bırakır.

    ![Azure IoT Central marka görüntüsünü Kaydet](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-save.png)

1. İsteğe bağlı olarak, **Belgeler**başlıklı kutucukta **Yapılandır** ' ı seçin ve destek içeriği için bir URL belirtin. 

Depodaki algılayıcı bölgelerinin haritasını görüntüleyen görüntü kutucuğunu özelleştirmek için:

1. Varsayılan depo bölgesi haritasını görüntüleyen görüntü kutucuğunda **Yapılandır** ' ı seçin. 

1. **Görüntü**' i seçin ve bir depolama bölgesi eşlemesinin özel bir görüntüsünü karşıya yüklemek için iletişim kutusunu kullanın. 

1. **Güncelleştirme yapılandırması**' nı seçin.

    ![Azure IoT Central mağaza haritasını Kaydet](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-map-save.png)

    Contoso Mağazası eşlemesinde örnek dört bölge gösterir: iki kullanıma alma bölgesi, giyim ve kişisel bakım için bir bölge ve Market ve deli için bir bölge. Bu öğreticide, telemetri sağlamak için algılayıcıları bu bölgelerle ilişkilendirirsiniz.

    ![Azure IoT Central depolama alanları](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-zones.png)

1. **Kaydet**’i seçin. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Yerleşimi değiştirmek için kutucukları düzenleyin
Panoyu özelleştirirken önemli bir adım, yararlı bir görünüm oluşturmak için kutucukları yeniden düzenlemez. Uygulama işleçleri cihaz telemetrisini görselleştirmek, cihazları yönetmek ve bir depodaki koşulları izlemek için panoyu kullanır. Azure IoT Central, pano oluşturmaya yönelik uygulama Oluşturucu görevini basitleştirir. Pano düzenleme modu, kutucukları hızlı bir şekilde eklemenizi, taşımanızı, yeniden boyutlandırmanızı ve silmenizi sağlar. **Mağaza içi analiz-kullanıma alma** uygulama şablonu, bir pano oluşturma görevini de basitleştirir. Sensörlerle bağlantılı bir pano düzeni ve kullanıma alma satır sayılarını ve çevre koşullarını görüntüleyen kutucukları sağlar.

Bu bölümde, özel bir düzen oluşturmak için **Mağaza içi analiz-kullanıma alma** uygulama şablonunda Panoyu yeniden düzenleyebilirsiniz.

Uygulamanızda kullanmayı planlamadığınız kutucukları kaldırmak için:

1. Pano araç çubuğunda **Düzenle** ' yi seçin. 

1. Aşağıdaki kutucukları kaldırmak için **X Delete** ' i seçin: **tüm bölgelere geri döndüğünüzde**mağaza panosu, **bekleme süresi**ve **kullanıma alma 3**ile ilişkili üç kutucuk **ziyaret edin**. Contoso mağaza panosu Bu kutucukları kullanmaz. 

    ![Azure IoT Central kutucukları silme](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles.png)

1. Kalan Pano kutucuklarını görünüme getirmek için kaydırın.

1. Aşağıdaki kutucukları kaldırmak için **X Delete** ' i seçin: **Isınma kullanıma alma bölgesi**, seyrek erişimli **kullanıma alma bölgesi**, **doluluk algılayıcısı ayarları**, **termostat algılayıcı ayarları**ve **ortam koşulları**. 

   ![Azure IoT Central kalan kutucukları Sil](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles-2.png)

1. **Kaydet**’i seçin. Kullanılmayan kutucukları kaldırmak düzenleme sayfasında alan boşaltır ve işleçler için Pano görünümünü basitleştirir.

1. Panodaki değişikliklerinizi görüntüleyin.

   ![Kutucukları sildikten sonra Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/after-delete-tiles.png)

Kullanılmayan kutucukları kaldırdıktan sonra, düzenlenmiş bir düzen oluşturmak için kalan kutucukları yeniden düzenleyin. Yeni düzen, daha sonraki bir adımda eklediğiniz kutucuklar için boşluk içerir.

Kalan kutucukları yeniden düzenlemek için:

1. **Düzenle**’yi seçin.

1. **İskişüretici yazılımı** kutucuğunu seçin ve yukarı **doğru pil kutucuğunun sağına** sürükleyin.

1. **Termostat üretici yazılımı** kutucuğunu seçin ve **termostat** pil kutucuğunun sağına sürükleyin.

1. **Kaydet**’i seçin.

1. Düzen değişikliklerinizi görüntüleyin. 

    ![Azure IoT Central üretici yazılımı pil kutucukları](./media/tutorial-in-store-analytics-customize-dashboard-pnp/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Görüntüleme koşullarına telemetri kutucukları ekleme
Pano mizanpajını özelleştirdikten sonra, telemetrisini göstermek için kutucuk eklemeye hazırız. Bir telemetri kutucuğu oluşturmak için bir cihaz şablonu ve cihaz örneği seçin, sonra kutucukta görüntülenecek cihaza özgü telemetri ' i seçin. **Mağaza içi analiz-kullanıma alma** uygulama şablonu, panoda çeşitli telemetri kutucukları içerir. İki kullanıma alma bölgesindeki dört kutucuk, sanal doluluk sensörlerine ait Telemetriyi görüntüler. **Kişiler trafik** kutucuğu, iki kullanıma alma bölgesindeki sayıları gösterir. 

Bu bölümde, [IoT Central Azure 'da yerleşik bir Analytics uygulaması oluşturma](./tutorial-in-store-analytics-create-app-pnp.md) öğreticisinde eklediğiniz RuuviTag sensörlerinden çevresel telemetri göstermek için iki daha fazla telemetri kutucuğu eklersiniz. 

RuuviTag sensörlerinden çevresel verileri görüntüleyen kutucuklar eklemek için:

1. **Düzenle**’yi seçin.

1. **Cihaz şablonu** listesinde `RuuviTag` ' yi seçin. 

1. İki Ruuvıtag sensörlerinden birinin **cihaz örneğini** seçin. Örnek contoso deposunda, Bölge 1 için telemetri kutucuğu oluşturmak üzere `Zone 1 Ruuvi` ' yi seçin. 

1. **Telemetri** listesinde `Relative humidity` ve `temperature` ' yı seçin. Bunlar, kutucukta her bölge için görüntülenen telemetri öğeleridir.

1. **Birleştir**' i seçin. 

    ![Azure IoT Central RuuviTag kutucuğu 1 ekle](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-zone1-ruuvi.png)

    Seçili algılayıcı için Birleşik nem ve sıcaklık telemetrisini görüntüleyen yeni bir kutucuk görünür. 

1. RuuviTag algılayıcısı için yeni kutucukta **Yapılandır** ' ı seçin. 

1. **Başlığı** *bölge 1 ortamı*olarak değiştirin. 

1. **Güncelleştirme yapılandırması**' nı seçin.

1. İkinci algılayıcı örneği için bir kutucuk oluşturmak üzere önceki adımları tekrarlayın. **Başlığı** *bölge 2 ortamına* ayarlayın ve ardından **yapılandırmayı Güncelleştir** ' i seçin.

1. **Termostat üretici yazılımı** kutucuğunun altında **bölge 2 ortamı** başlıklı kutucuğu sürükleyin. 

1. **Bölge 1 ortamı** başlıklı kutucuğu **kişiler trafik** kutucuğunun altına sürükleyin. 

1. **Kaydet**’i seçin. Pano, bölge telemetrisini iki yeni kutucuğa göre görüntüler.

    ![Tüm RuuviTag kutucukları IoT Central Azure](./media/tutorial-in-store-analytics-customize-dashboard-pnp/all-ruuvitag-tiles.png)

Yalnızca iki kullanıma alma bölgesi için telemetri göstermek üzere **kişiler trafik** kutucuğunu düzenlemek için:

1. **Düzenle**’yi seçin. 

1. **Kişiler trafik** kutucuğunda **Yapılandır** ' ı seçin.

1. **Telemetri** ' te **sayı 1**, **Sayı 2**ve **Sayı 3**' ü seçin. 

1. **Güncelleştirme yapılandırması**' nı seçin. Bu, kutucukta varolan yapılandırmayı temizler. 

1. **Kişiler trafik** kutucuğunda yeniden **Yapılandır** ' ı seçin.

1. **Telemetri** ' de, **sayı 1**ve **Sayı 2**' yi seçin. 

1. **Güncelleştirme yapılandırması**' nı seçin. 

1. **Kaydet**’i seçin.  Güncelleştirilmiş pano yalnızca, sanal doluluk algılayıcısı temelinde olan iki kullanıma alma bölgesi için sayıları görüntüler.

    ![Azure IoT Central kişiler trafiği iki kulvar](./media/tutorial-in-store-analytics-customize-dashboard-pnp/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Cihaz ayrıntılarını göstermek için özellik kutucukları ekleyin
Uygulama işleçleri cihazları yönetmek ve durumu izlemek için panoyu kullanır. İşleçlerin yazılım sürümünü görüntülemesini sağlamak için her RuuviTag için bir kutucuk ekleyin. 

Her RuuviTag için bir özellik kutucuğu eklemek için:

1. **Düzenle**’yi seçin.

1. **Cihaz şablonu** listesinde `RuuviTag` ' yi seçin. 

1. İki Ruuvıtag sensörlerinden birinin **cihaz örneğini** seçin. Örnek contoso deposunda, Bölge 1 için telemetri kutucuğu oluşturmak üzere `Zone 1 Ruuvi` ' yi seçin. 

1. **Yazılım sürümü > Özellikler**' i seçin.

1. **Birleştir**' i seçin. 

1. **Yazılım sürümü**başlıklı yeni oluşturulan kutucukta **Yapılandır** ' ı seçin. 

1. **Başlığı** *ruuvi 1 yazılım sürümü*olarak değiştirin.

1. **Güncelleştirme yapılandırması**' nı seçin. 

1. **Bölge 1 ortam** kutucuğunun altında **ruuv 1 yazılım sürümü** adlı kutucuğu sürükleyin.

1. İkinci RuuviTag için bir yazılım sürümü Özellik kutucuğu oluşturmak için önceki adımları tekrarlayın. 

1. **Kaydet**’i seçin.  

    ![Azure IoT Central RuuviTag Özellik kutucukları](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Çalıştır komutlarına komut kutucukları ekleyin
Uygulama işletmenleri, komutları çalıştırarak cihazları yönetmek için panoyu de kullanır. Panoya önceden tanımlanmış komutları yürütecek komut kutucukları ekleyebilirsiniz. Bu bölümde, operatörlerin Rigado ağ geçidini yeniden başlatması için bir komut kutucuğu eklersiniz. 

Ağ geçidini yeniden başlatmak için bir komut kutucuğu eklemek için:

1. **Düzenle**’yi seçin. 

1. **Cihaz şablonu** listesinde `C500` ' yi seçin. Bu, Rigado C500 ağ geçidi şablonudur. 

1. **Cihaz örneğindeki**ağ geçidi örneğini seçin.

1. **Yeniden başlatma > komutu** seçin ve mağaza haritasının yanındaki panoya sürükleyin. 

1. **Kaydet**’i seçin. 

1. Tamamlanan contoso panonuzu görüntüleyin. 

    ![Azure IoT Central Pano özelleştirmesini tamamen](./media/tutorial-in-store-analytics-customize-dashboard-pnp/completed-dashboard.png)

1. İsteğe bağlı olarak, ağ geçidinizdeki yeniden başlatma komutunu çalıştırmak için **yeniden başlatma** kutucuğunu seçin.

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

* Pano adını değiştirme
* Panodaki görüntü kutucukları özelleştirme
* Yerleşimi değiştirmek için kutucukları düzenleyin
* Görüntüleme koşullarına telemetri kutucukları ekleme
* Cihaz ayrıntılarını göstermek için özellik kutucukları ekleyin
* Çalıştır komutlarına komut kutucukları ekleyin

Artık panoyu Azure IoT Central Store 'da analiz uygulamanızda özelleştirdiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Verileri dışa aktarın ve öngörüleri görselleştirin](./tutorial-in-store-analytics-export-data-visualize-insights-pnp.md)
