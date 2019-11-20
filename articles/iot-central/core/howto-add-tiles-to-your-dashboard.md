---
title: Panolarınıza kutucuk ekleme | Microsoft Docs
description: Oluşturucu olarak, varsayılan Azure IoT Central uygulama panosunu yapılandırmayı öğrenin.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: affd5b58c312e07177fbfdaaf88f689b1a8caebd
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954714"
---
# <a name="configure-the-application-dashboard"></a>Uygulama panosunu yapılandırma

**Pano** , uygulamaya erişimi olan KULLANıCıLAR uygulamanın URL 'sine gitince yüklenen sayfasıdır. Uygulamanızı **uygulama şablonlarından**birinde oluşturduysanız, uygulamanız başlamak için önceden tanımlanmış bir panoya sahip olur. Uygulamanızı **özel uygulama** uygulaması şablonundan oluşturduysanız, panonuz başlamak için boş olur.

> [!NOTE]
> Kullanıcılar, varsayılan uygulama panosuna ek olarak [birden çok Pano oluşturabilir](howto-create-personal-dashboards.md) . Bu panolar yalnızca Kullanıcı tarafından kişisel olabilir veya uygulamanın tüm kullanıcıları arasında paylaşılabilir. 

## <a name="add-tiles"></a>Kutucuk Ekle

Aşağıdaki ekran görüntüsünde, Pano **özel uygulama** şablonundan oluşturulan bir uygulamada gösterilmektedir. Uygulamanıza ilişkin varsayılan panoyu özelleştirmek için sayfanın sol üst kısmında **Düzenle** ' yi seçin.

> [!div class="mx-imgBorder"]
> "örnek contoso" şablonunu temel alan uygulamalar için Pano ![](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

**Düzenle** seçildiğinde Pano kitaplığı bölmesi açılır. Kitaplık, Panoyu özelleştirmek için kullanabileceğiniz kutucukları ve Pano temel öğelerini içerir.

> [!div class="mx-imgBorder"]
> Pano kitaplığı](media/howto-add-tiles-to-your-dashboard/dashboard-library.png) ![

Örneğin, cihazın geçerli sıcaklığını için bir **telemetri** kutucuğu ekleyebilirsiniz. Bunu yapmak için:
1. Bir **cihaz şablonu** seçin
1. Bir pano kutucuğunda görmek istediğiniz cihaz için bir **cihaz örneği** seçin. Daha sonra, kutucukta kullanılabilecek cihaz özelliklerinin bir listesini görürsünüz.
1. Kutucuğu panoda oluşturmak için **sıcaklık** ' ya tıklayın ve Pano alanına sürükleyin. Ayrıca, **sıcaklık** ' ın yanındaki onay kutusuna tıklayabilir ve **Birleştir**' e tıklayabilirsiniz. Aşağıdaki ekran görüntüsünde bir cihaz şablonu ve cihaz örneği seçip panoda bir sıcaklık telemetri kutucuğu oluşturma gösterilmektedir.
1. Kutucuğu panoya kaydetmek için sol üst kısımdaki **Kaydet** ' i seçin.

> [!div class="mx-imgBorder"]
> ayarlar ve özellikler hakkındaki ayrıntılarla "cihaz ayrıntılarını yapılandırma" formunu ![](media/howto-add-tiles-to-your-dashboard/device-details.png)

Artık bir operatör varsayılan uygulama panosunu görüntülediğinde, cihaz için **sıcaklığa** sahip yeni kutucuğu görürler. Her kutucukta, kutucuk oluşturulduğunda görüntülenecek olan önceden seçilmiş bir grafik, grafik, vb. bulunur. Ancak, kullanıcılar bu görselleştirmeyi düzenlemeyi ve değiştirmeyi seçebilir. 

> [!div class="mx-imgBorder"]
> "Pano" sekmesini, kutucuk için](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png) ve özellikleriyle ![.


## <a name="edit-tiles"></a>Kutucukları Düzenle

Panodaki bir kutucuğu düzenlemek için, önce sayfanın sol üst kısmında **Düzenle** ' ye tıklayın, bu, pano ve tüm kutucukları için düzenleme modunu açar. 

> [!div class="mx-imgBorder"]
> Seçili kutucuk için düzenleme modu etkinleştirilmiş Pano ekranına ![](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Ardından, düzenlemek istediğiniz kutucuğun sağ üst köşesindeki **dişli** simgesine tıklayın. Burada, kutucuğun başlığını, görselleştirmesini, toplamayı, vb. gibi düzenleyebilirsiniz.

> [!div class="mx-imgBorder"]
> kutucuk toplaması ayarları için ![açılan listesi](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

Ayrıca, kutucukta **cetvel** simgesine tıklayarak grafik görselleştirmeyi değiştirebilirsiniz.

> [!div class="mx-imgBorder"]
> kutucuk görselleştirme ayarları için ![açılan listesi](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Döşeme türleri

Aşağıdaki tabloda Azure IoT Central kutucukların kullanımı özetlenmektedir:
 
| Kaldır | Pano | Açıklama
| ----------- | ------- | ------- |
| İçerik | Uygulama ve cihaz kümesi panoları |Markın desteklenen kutucuklar başlık ve açıklama metnini görüntüleyen tıklatılabilir kutucuklardır. Ayrıca, bir kullanıcının uygulamanızla ilgili bir URL 'ye gitmesini sağlamak için bu kutucuğu bağlantı kutucuğu olarak da kullanabilirsiniz.|
| Görüntü | Uygulama ve cihaz kümesi panoları |Görüntü kutucukları özel bir görüntü görüntüler ve tıklatılabilir olabilir. Panoya grafik eklemek ve isteğe bağlı olarak bir kullanıcının uygulamanızla ilgili bir URL 'ye gitmesini sağlamak için bir görüntü kutucuğu kullanın.|
| Etiket | Uygulama panoları |Etiket kutucukları, bir panoda özel metin görüntüler. Metnin boyutunu seçebilirsiniz. İlgili bilgileri panoya, iletişim ayrıntılarına veya yardım 'a eklemek için bir etiket kutucuğu kullanın.|
| Eşleme | Uygulama ve cihaz kümesi panoları |Harita kutucukları bir haritadaki bir cihazın konumunu ve durumunu görüntüler. Örneğin, bir cihazın nerede olduğunu ve fanı 'nin geçiş yapıp yapmadığını görüntüleyebilirsiniz.|
| Çizgi grafik | Uygulama ve cihaz panoları |Çizgi grafik kutucukları bir zaman dönemi için bir cihazın toplam ölçümünün bir grafiğini görüntüler. Örneğin, son saat için bir cihazın ortalama sıcaklığını ve basıncını gösteren bir çizgi grafik görüntüleyebilirsiniz.|
| Çubuk grafik | Uygulama ve cihaz panoları |Çubuk grafik kutucukları bir zaman dönemi için bir cihazın toplam ölçümlerinin bir grafiğini görüntüler. Örneğin, son saat için bir cihazın ortalama sıcaklığını ve basıncını gösteren bir çubuk grafiği görüntüleyebilirsiniz.|
| Pasta grafik | Uygulama ve cihaz kümesi panoları |Pasta grafik kutucukları, bir zaman dönemi için bir cihazın toplam ölçümlerinin bir grafiğini görüntüler.|
| Isı Haritası | Uygulama ve cihaz kümesi panoları |Isı haritası kutucukları, renk olarak temsil edilen cihaz kümesi hakkındaki bilgileri görüntüler.|
| Olay geçmişi | Uygulama ve cihaz panoları |Olay geçmişi kutucukları bir zaman dönemi içinde bir cihaz için olayları görüntüler. Örneğin, son bir saat içinde bir cihaz için tüm sıcaklık değişikliklerini göstermek üzere onu kullanabilirsiniz.|
| Durum geçmişi | Uygulama ve cihaz panoları |Durum geçmişi kutucukları bir zaman döneminin ölçüm değerlerini görüntüler. Örneğin, son bir saat içinde bir cihaz için sıcaklık değerlerini göstermek üzere onu kullanabilirsiniz.|
| KPI | Uygulama ve cihaz panoları | KPI kutucukları bir dönem için bir toplama telemetrisi veya olay ölçümü görüntüler. Örneğin, son bir saat içinde bir cihaz için ulaşılan en fazla sıcaklık sayısını göstermek için bunu kullanabilirsiniz.|
| Bilinen son değer | Uygulama ve cihaz panoları |Son bilinen değer kutucukları, bir telemetri veya durum ölçümü için en son değeri görüntüler. Örneğin, bu kutucuğu, bir cihaz için en son sıcaklık, basınç ve nem ölçümlerini göstermek için kullanabilirsiniz.|

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central varsayılan uygulama panonuzun nasıl yapılandırılacağını öğrendiğinize göre, [görüntüleri hazırlama ve karşıya yükleme hakkında bilgi](howto-prepare-images.md)edinebilirsiniz.