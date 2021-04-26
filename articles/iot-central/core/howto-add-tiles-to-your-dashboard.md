---
title: Azure IoT Central panonuza yapılandırma | Microsoft Docs
description: Oluşturucu olarak, varsayılan Azure IoT Central uygulama panosunu kutucuklar ile yapılandırmayı öğrenin.
author: philmea
ms.author: philmea
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 8a8ba765a966409c06dbba636932f7777624f6d4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864262"
---
# <a name="configure-the-application-dashboard"></a>Uygulama panosunu yapılandırma

**Pano** , bir IoT Central uygulamasına bağlandığınızda gördüğünüz ilk sayfasıdır. Uygulamanızı sektör odaklı [uygulama şablonlarından](./concepts-app-templates.md)biri ile oluşturursanız, uygulamanızda başlamak için önceden tanımlanmış bir pano vardır. Uygulamanızı özel bir [uygulama şablonundan](./concepts-app-templates.md)oluşturursanız, panonuz başlamak için bazı ipuçları gösterir.

> [!TIP]
> Kullanıcılar, varsayılan uygulama panosuna ek olarak [birden çok Pano oluşturabilir](howto-create-personal-dashboards.md) . Bu panolar yalnızca Kullanıcı tarafından kişisel olabilir veya uygulamanın tüm kullanıcıları arasında paylaşılabilir.  

## <a name="add-tiles"></a>Kutucuk ekle

Aşağıdaki ekran görüntüsünde, Pano **özel uygulama** şablonundan oluşturulan bir uygulamada gösterilmektedir. Geçerli panoyu özelleştirmek için **Düzenle**' yi seçin, özel bir kişisel veya paylaşılan Pano eklemek için **Yeni**' yi seçin:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Özel uygulama şablonunu temel alan uygulamalar için Pano":::

**Düzenle** veya **Yeni** seçeneğini belirledikten sonra Pano *düzenleme* modundadır. Panoya kutucuk eklemek ve panodaki kutucukları özelleştirmek ve kaldırmak için **Pano düzenleme** bölmesindeki araçları kullanabilirsiniz. Örneğin, bir veya daha fazla cihaz tarafından bildirilen geçerli sıcaklığın gösterilmesi için bir **telemetri** kutucuğu eklemek için:

1. Bir **cihaz grubu** seçin ve ardından kutucukta göstermek için cihazlar **açılan menüsünde cihazlarınızı seçin** . Artık cihazlarda kullanılabilir telemetri, Özellikler ve komutları görürsünüz.

1. Gerekirse, kutucukta gösterilecek bir telemetri değeri seçmek için açılan menüyü kullanın. Bir kutucuğa **+ telemetri**, **+ özellik** veya **+ bulut özelliği** seçerek daha fazla öğe ekleyebilirsiniz.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Panoya bir sıcaklık telemetri kutucuğu ekleme":::

Kutucukta gösterilecek tüm değerleri seçtiğinizde **kutucuk Ekle** ' ye tıklayın. Kutucuk artık Pano üzerinde görüntülenir, burada görselleştirmeyi değiştirebilir, yeniden boyutlandırabilir, taşıyabilir ve yapılandırabiliriz.

Panodaki kutucukları ekleme ve özelleştirmeyi bitirdiğinizde **Kaydet** ' i seçerek değişiklikleri panoda kaydederek düzenleme modundan çıkar.

## <a name="customize-tiles"></a>Kutucukları Özelleştir

Bir kutucuğu düzenlemek için, düzenleme modunda olmanız gerekir.  Kullanılabilir özelleştirme seçenekleri [kutucuk türüne](#tile-types)bağlıdır:

* Bir kutucukta cetvel simgesi görselleştirmeyi değiştirmenize olanak sağlar. Görselleştirmeler çizgi grafikleri, çubuk grafikler, pasta grafikleri, bilinen son değerler, ana performans göstergeleri (veya KPI 'Lar), heavmaps ve haritalar içerir.

* Kare simgesi kutucuğu yeniden boyutlandırmanızı sağlar.

* Dişli simgesi görselleştirmeyi yapılandırmanızı sağlar. Örneğin, bir çizgi grafik görselleştirmesi için, göstergeyi ve eksenleri göstermeyi seçebilir ve çizilmesi için zaman aralığını seçebilirsiniz.


## <a name="tile-types"></a>Döşeme türleri

Aşağıdaki tabloda, bir panoya ekleyebileceğiniz farklı kutucuk türleri açıklanmaktadır:

| Kutucuk             | Description |
| ---------------- | ----------- |
| Markdown         | Markın kutucukları, markaşağı kullanarak biçimlendirilen bir başlık ve açıklama metnini görüntüleyen tıklatılabilir kutucuklardır. URL, uygulamadaki başka bir sayfanın göreli bağlantısı veya dış siteye mutlak bir bağlantı olabilir.|
| Görüntü            | Görüntü kutucukları özel bir görüntü görüntüler ve tıklatılabilir olabilir. URL, uygulamadaki başka bir sayfanın göreli bağlantısı veya dış siteye mutlak bir bağlantı olabilir.|
| Etiketle            | Etiket kutucukları, bir panoda özel metin görüntüler. Metnin boyutunu seçebilirsiniz. İlgili bilgileri panoya, iletişim ayrıntılarına veya yardım 'a eklemek için bir etiket kutucuğu kullanın.|
| Count            | Sayı kutucukları bir cihaz grubundaki cihaz sayısını görüntüler.|
| Harita              | Harita kutucukları bir haritadaki bir veya daha fazla cihazın konumunu görüntüler. Ayrıca, bir cihazın konum geçmişinin en fazla 100 noktasını görüntüleyebilirsiniz. Örneğin, bir cihazın geçen hafta içinde bulunduğu yerin örneklendiği yolunu görüntüleyebilirsiniz.|
| KPI              |  KPI kutucukları, bir veya daha fazla cihazın bir zaman diliminde toplam telemetri değerlerini görüntüler. Örneğin, son bir saat içinde bir veya daha fazla cihaz için en fazla sıcaklık ve basınç sınırına ulaşılmayı göstermek için bunu kullanabilirsiniz.|
| Çizgi grafik       | Çizgi grafik kutucukları bir veya daha fazla cihaz için bir zaman dilimi için bir veya daha fazla toplama telemetri değeri çizdir. Örneğin, son bir saat için bir veya daha fazla cihazın ortalama sıcaklığını ve basıncını çizmek üzere bir çizgi grafik görüntüleyebilirsiniz.|
| Çubuk Grafik        | Çubuk grafik kutucukları bir veya daha fazla cihaz için bir zaman dilimi için bir veya daha fazla toplama telemetri değeri çizdir. Örneğin, son bir saat içindeki bir veya daha fazla cihazın ortalama sıcaklığını ve basıncını göstermek için bir çubuk grafik görüntüleyebilirsiniz.|
| Pasta grafiği        | Pasta grafik kutucukları bir veya daha fazla cihaz için bir zaman dilimi için bir veya daha fazla toplama telemetri değeri görüntüler.|
| Isı haritası         | Isı haritası kutucukları, renk olarak gösterilen bir veya daha fazla cihaz hakkındaki bilgileri görüntüler.|
| Bilinen son değer | Son bilinen değer kutucukları bir veya daha fazla cihaz için en son telemetri değerlerini görüntüler. Örneğin, bir veya daha fazla cihaz için en son sıcaklık, basınç ve nem değerlerini göstermek üzere bu kutucuğu kullanabilirsiniz. |
| Olay geçmişi    | Olay geçmişi kutucukları bir zaman dönemi içinde bir cihaz için olayları görüntüler. Örneğin, son bir saat içinde bir veya daha fazla cihaz için tüm Vana açık ve kapatma olaylarını göstermek üzere onu kullanabilirsiniz.|
| Özellik         |  Özellik kutucukları, bir veya daha fazla cihazın Özellikler ve bulut özellikleri için geçerli değeri görüntüler. Örneğin, bu kutucuğu, bir cihazın üretici veya bellenim sürümü gibi cihaz özelliklerini göstermek için kullanabilirsiniz. |

Şu anda birden çok cihazı destekleyen kutucuklara en fazla 10 cihaz ekleyebilirsiniz.

### <a name="customizing-visualizations"></a>Görselleştirmeleri özelleştirme

Varsayılan olarak, çizgi grafiklerde verileri bir zaman aralığı üzerinde gösterir. Seçilen zaman aralığı, 50 eşit ölçekli demetlere bölünür ve cihaz verileri, seçili zaman aralığında 50 veri noktası vermek için her demet için toplanır. Ham verileri görüntülemek istiyorsanız, son 100 değerlerini görüntülemek için seçiminizi değiştirebilirsiniz. Zaman aralığını değiştirmek veya ham veri görselleştirmesini seçmek için **grafik Yapılandır** panelinde görüntüleme Aralığı açılan listesini kullanın.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/display-range.png" alt-text="Çizgi grafiğinin Görüntüleme aralığını değiştirme":::

Toplam değerleri görüntüleyen kutucuklar için, toplamayı seçmek üzere **grafiği Yapılandır** panelinde telemetri türünün yanındaki dişli simgesini seçin. Ortalama, toplam, maksimum, en düşük ve sayı seçeneklerinden birini belirleyebilirsiniz.

Çizgi grafikler, çubuk grafikler ve pasta grafikleri için farklı telemetri değerlerinin rengini özelleştirebilirsiniz. Özelleştirmek istediğiniz telemetrinin yanındaki palet simgesini seçin:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Telemetri değerinin rengini değiştirme":::

Dize özelliklerini veya telemetri değerlerini gösteren kutucuklar için metnin nasıl görüntüleneceğini seçebilirsiniz. Örneğin, cihaz bir URL 'YI dize özelliğinde depoluyorsa, bunu tıklatılabilir bir bağlantı olarak gösterebilirsiniz. URL bir resme başvuruyorsa, görüntüyü bilinen son bir değer veya özellik kutucuğunda işleyebilirsiniz. Bir dizenin nasıl görüntüleneceğini değiştirmek için, kutucuk yapılandırmasında telemetri türü veya özelliğin yanındaki dişli simgesini seçin:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Bir kutucukta bir dizenin nasıl görüntüleneceğini değiştirme":::

Sayısal **KPI**, **bilinen son değer** ve **özellik** kutucukları için, geçerli değerine göre kutucuğun rengini özelleştirmek üzere koşullu biçimlendirme kullanabilirsiniz. Koşullu biçimlendirme eklemek için kutucukta **Yapılandır** ' ı seçin ve ardından özelleştireceğiniz değerin yanındaki **koşullu biçimlendirme** simgesini seçin:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="Bir kutucuk için yapılandırma seçeneğinin nasıl bulunacağını ve sonra koşullu biçimlendirme simgesini gösteren ekran görüntüsü":::

Koşullu biçimlendirme kurallarınızı ekleyin:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="Ortalama akış için koşullu biçimlendirme kurallarını gösteren ekran görüntüsü. Üç kural vardır-20 ' den az yeşil, 50 ' den az sarı ve 50 ' den fazla şey kırmızıya sahiptir":::
   
Aşağıdaki ekran görüntüsünde koşullu biçimlendirme kuralının etkisi gösterilmektedir:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="Ortalama su akışı kutucuğunda kırmızı arka plan rengini gösteren ekran görüntüsü. Kutucuktaki sayı 50,54 ' dir":::

### <a name="tile-formatting"></a>"kutucuk" biçimlendirmesi
KPI, LKV ve özellik kutucuklarında kullanılabilen bu özellik, kullanıcıların yazı tipi boyutunu ayarlamasına, ondalık duyarlık, kısaltma sayısal değerlerini (örneğin, 1.7 K olarak format 1.700), veya dize değerlerini kutucuklarına sarmasına olanak tanır.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/tile-format.png" alt-text="Kutucuk biçimi":::

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central varsayılan uygulama panonuzun nasıl yapılandırılacağını öğrendiğinize göre, [Kişisel Pano oluşturmayı öğrenin](howto-create-personal-dashboards.md).
