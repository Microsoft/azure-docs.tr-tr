---
title: Azure IoT Central uygulamanızda cihaz verilerini çözümleme | Microsoft Docs
description: Azure IoT Central uygulamanızda cihaz verilerini çözümleyin.
author: ankitgup
ms.author: ankitgup
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 77ee15d581cfc17af01ae0fa53139edc331a4fea
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896014"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Cihaz verilerini çözümlemek için Analytics 'i kullanma

*Bu makale, işleçler, oluşturucular ve yöneticiler için geçerlidir.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Azure IoT Central, geçmiş eğilimleri çözümlemek ve cihazlarınızdan çeşitli Telemetriler ilişkilendirmek için zengin analiz özellikleri sağlar. Başlamak için sol bölmedeki **analiz** ' i ziyaret edin.

## <a name="understanding-the-analytics-ui"></a>Analytics Kullanıcı arabirimini anlama
Analiz Kullanıcı arabirimi üç ana bileşenden oluşur:
- **Veri yapılandırma paneli:** Yapılandırma panelinde, verileri çözümlemek istediğiniz cihaz grubunu seçerek başlatın. Ardından, çözümlemek istediğiniz Telemetriyi seçin ve her telemetri için toplama yöntemini seçin. Denetime **göre bölme** , cihaz özelliklerini boyut olarak kullanarak verileri gruplandırmaya yardımcı olur.

- **Zaman denetimi:** Veri çözümlemek istediğiniz süreyi seçmek için zaman denetimi kullanılır. Zaman aralığını seçmek için zaman kaydırıcısının iki ucundan birini sürükleyebilirsiniz. Zaman denetimi Ayrıca, verileri toplamak için kullanılan demetini veya Aralık boyutunu denetleyen bir **Aralık boyutu** kaydırıcısının de bulunur. 

- **Grafik denetimi:** Grafik denetimi, verileri çizgi grafik olarak görselleştirir. Grafik göstergesine etkileşimde bulunarak belirli satırların görünürlüğünü değiştirebilirsiniz. 


  ![Analytics Kullanıcı arabirimine genel bakış](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Verilerinizi sorgulama

Bir **cihaz grubunu**ve çözümlemek istediğiniz Telemetriyi seçerek başlamanız gerekir. İşiniz bittiğinde, verileri görselleştirmeye başlamak için **Çözümle** ' yi seçin.

- **Cihaz grubu:** [Cihaz grubu](tutorial-use-device-groups.md) , cihazlarınızın Kullanıcı tanımlı grubudur. Örneğin, Oakland 'deki tüm soğutma veya tüm sürüm 2,0 rüzgar türbines.

- **Telemetri:** Çözümlemek ve araştırmak istediğiniz Telemetriyi seçin. Birlikte çözümlemek için birden çok Telemetriler seçebilirsiniz. Varsayılan toplama yöntemi, sırasıyla dize veri türü için sayısal ve sayı için Average olarak ayarlanır. Sayısal veri türleri için desteklenen toplama yöntemleri ortalama, en yüksek, en düşük, sayı ve toplam toplamdır.  Dize veri türü için desteklenen toplama yöntemleri Count.

- **Bölme ölçütü:** ' Bölünmüş ' denetim, cihaz özelliklerini boyut olarak kullanarak verileri gruplandırmaya yardımcı olur. Cihaz ve bulut özelliklerinin değerleri, telemetriyle birlikte ve cihaz tarafından gönderildiğinde birleştirilir. Bulut veya cihaz özelliği güncellendiyse, telemetri grafik üzerinde farklı değerlere göre gruplanmış olarak görüntülenir.

    > [!TIP]
    > Her bir cihaz için verileri ayrı olarak görüntülemek için ' bölünmüş ' denetimde cihaz kimliği ' ni seçin.

## <a name="interacting-with-your-data"></a>Verilerinizle etkileşim kurma

Verilerinizi sorguladıktan sonra çizgi grafik üzerinde görselleştirmeyi başlatabilirsiniz. Telemetriyi gösterebilir/gizleyebilirsiniz, zaman süresini değiştirebilir, bir veri kılavuzunda Telemetriyi görüntüleyebilirsiniz.

- **Zaman Düzenleyicisi bölmesi:** Varsayılan olarak, geçen bir günden verileri alacağız. Zaman süresini değiştirmek için zaman kaydırıcısının her iki ucundan birini sürükleyebilirsiniz. Takvim denetimini, önceden tanımlanmış zaman demetlerinden birini seçmek veya özel bir zaman aralığı seçmek için de kullanabilirsiniz. Zaman denetimi Ayrıca, verileri toplamak için kullanılan demetini veya Aralık boyutunu denetleyen bir **Aralık boyutu** kaydırıcısının de bulunur.

    ![Zaman Düzenleyicisi](media/howto-create-analytics/timeeditorpanel.png)

    - **İç tarih aralığı kaydırıcı aracı**: iki uç nokta denetimini istediğiniz zaman aralığında sürükleyerek kullanın. Bu iç tarih aralığı, dış tarih aralığı kaydırıcı denetimi tarafından sınırlandırılır.
    
   
    - **Dış tarih aralığı kaydırıcı denetimi**: bitiş tarihi aralığını seçmek için uç nokta denetimlerini kullanın. Bu, iç tarih aralığı denetiminizdeki kullanılabilir olacak.

    - **Tarih aralığı düğmelerini artırma ve azaltma**: istediğiniz Aralık için herhangi bir düğmeyi seçerek zaman aralığınızı artırın veya azaltın.

    - **Aralık boyutu kaydırıcısı**: aynı zaman dilimi boyunca aralıkların ölçeğini ve ölçeğini yakınlaştırmak için kullanın. Bu eylem, büyük zaman dilimleri arasında hareket konusunda daha kesin bir denetim sağlar. Bu uygulamayı, verilerin ayrıntılı, yüksek çözünürlüklü görünümlerini, hatta milisaniyeye doğru bir şekilde görmek için kullanabilirsiniz. Kaydırıcının varsayılan başlangıç noktası, Seçiminizdeki verilerin en uygun görünümü olarak ayarlanır, bu da çözümleme, sorgu hızı ve ayrıntı düzeyi dengeler.
    
    - **Tarih aralığı Seçicisi**: Bu Web denetimiyle, istediğiniz tarih ve saat aralıklarını kolayca seçebilirsiniz. Farklı saat dilimleri arasında geçiş yapmak için de denetimini kullanabilirsiniz. Geçerli çalışma alanınıza uygulanacak değişiklikleri yaptıktan sonra Kaydet ' i seçin.

    > [!TIP]
    > Aralık boyutu, seçilen zaman aralığına göre dinamik olarak belirlenir. Daha az zaman yayılmaları, verileri birkaç saniyelik çok parçalı aralıklarla toplama olanağı sağlar.


- **Grafik göstergesi:** Grafik göstergesi, seçili Telemetriyi grafikte gösterir. Göstergedeki her bir öğenin üzerine giderek grafiğe odaklanmak için bu öğeyi üzerine gelebilmeniz gerekir. ' Split By ' kullanılırken telemetri, seçilen boyutun ilgili değerlerine göre gruplandırılır. Grup adına tıklayarak belirli bir Telemetriyi veya tüm grubu görünürlüğünü değiştirebilirsiniz.  


- **Y ekseni biçim denetimi:** y ekseni modu, kullanılabilir y ekseni görünüm seçenekleri boyunca geçiş yapar. Bu denetim yalnızca farklı Telemetriler görselleştirildiği zaman kullanılabilir. Y eksenini üç moddan birini seçerek ayarlayabilirsiniz:

    - **Yığılmış:** Her telemetri için bir grafik yığılır ve grafiklerin her birinin kendi y ekseni vardır. Bu mod varsayılan olarak ayarlanır.
    - **Paylaşılan:** Her telemetri için bir grafik aynı y eksenine karşı çizilir.
    - **Çakışma:** Y ekseni verileri, seçilen satıra göre değişen y ekseninde birden çok satırı yığmak için kullanın.

  ![Farklı görselleştirme modlarıyla y ekseni genelinde veri düzenleme](media/howto-create-analytics/yaxiscontrol.png)

- **Yakınlaştırma denetimi:** Yakınlaştırma, verilerinize daha fazla ayrıntıya erişmenizi sağlar. Sonuç kümesi içinde odaklanmak istediğiniz bir zaman dilimi bulursanız, alanı almak için fare işaretçinizi kullanın ve seçtiğiniz uç noktaya sürükleyin. Ardından seçili alana sağ tıklayıp Yakınlaştır ' a tıklayın.

  ![Verileri yakınlaştırın](media/howto-create-analytics/zoom.png)

Üç nokta altında verilerle etkileşimde bulunmak için daha fazla grafik denetimi vardır.

- **Görüntüleme Kılavuzu:** Sonuçlarınız, her bir veri noktası için belirli bir değeri görüntülemenize olanak sağlayan bir tablo biçiminde kullanılabilir.

- **Bir Işaret bırakın:** ' Bırakma Işaretleyicisi ' denetimi, grafikteki belirli veri noktalarını bağlamak için bir yol sağlar. Farklı zaman dönemlerinde birden çok satır için verileri karşılaştırmaya çalışırken yararlı olur.

  ![Analizlerinizin kılavuz görünümünü gösterme](media/howto-create-analytics/additionalchartcontrols.png)