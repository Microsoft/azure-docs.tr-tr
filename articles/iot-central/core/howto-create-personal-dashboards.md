---
title: Azure IoT Central panoları oluşturma | Microsoft Docs
description: Panolarınızı oluşturma ve yönetme hakkında bilgi edinin.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6fc99470fdc52a2dc6553056f305226f8348550c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366730"
---
# <a name="create-and-manage-multiple-dashboards"></a>Birden çok pano oluşturma ve yönetme

**Pano** , uygulamanıza ilk gittiğinizde yüklenen sayfasıdır. Uygulamanızdaki bir **Oluşturucu** , tüm kullanıcılar için varsayılan uygulama panosunu tanımlar. Ayrıca kendi kişiselleştirilmiş uygulama panonuzu de oluşturabilirsiniz. Farklı verileri görüntüleyen ve aralarında geçiş yapan çeşitli panolarınız olabilir.

Uygulamanın yöneticisiyseniz, uygulamanın  diğer kullanıcılarıyla paylaşmak için en fazla 10 uygulama düzeyi Pano da oluşturabilirsiniz. Yalnızca **Yöneticiler** uygulama düzeyi panoları oluşturabilir, düzenleyebilir ve silebilir.  

## <a name="create-dashboard"></a>Pano Oluştur

Aşağıdaki ekran görüntüsünde, Pano **özel uygulama** şablonundan oluşturulan bir uygulamada gösterilmektedir. Varsayılan uygulama panosunu bir kişisel Pano ile veya bir yönetici ise başka bir uygulama düzeyi panosu ile değiştirebilirsiniz. Bunu yapmak için sayfanın sol üst kısmındaki **+ Yeni** ' yi seçin.

> [!div class="mx-imgBorder"]
> !["Özel uygulama" şablonunu temel alan uygulamalar için Pano](media/howto-create-personal-dashboards/dashboard-custom-app.png)

**+ Yeni** seçildiğinde Pano Düzenleyicisi açılır. Düzenleyicide, panonuza bir ad verebilir ve kitaplıktan öğe seçebilirsiniz. Kitaplık, Panoyu özelleştirmek için kullanabileceğiniz kutucukları ve Pano temel öğelerini içerir.

> [!div class="mx-imgBorder"]
> ![Pano kitaplığı](media/howto-create-personal-dashboards/dashboard-library.png)

Uygulamanın yöneticisiyseniz, kişisel  düzey pano veya uygulama düzeyi panosu oluşturma seçeneği sunulur. Kişisel bir düzey Pano oluşturursanız, yalnızca siz görebilirsiniz. Uygulama düzeyi panosu oluşturursanız, uygulamanın her kullanıcısı bu panoyu görebilir. Bir başlık girdikten ve oluşturmak istediğiniz Pano türünü seçtikten sonra, daha sonra kutucukları kaydedebilir ve ekleyebilirsiniz. Ya da şimdi hazır olduğunuzda ve bir cihaz şablonu ve cihaz örneği eklediyseniz, devam edebilir ve ilk Kutucuğunuzu oluşturabilirsiniz.  

> [!div class="mx-imgBorder"]
> ![Sıcaklık ayrıntıları ile cihaz ayrıntılarını yapılandırma "formu](media/howto-create-personal-dashboards/device-details.png)

Örneğin, cihazın geçerli sıcaklığını için bir **telemetri** kutucuğu ekleyebilirsiniz. Bunun için:

1. Bir **cihaz şablonu** seçin
1. Bir pano kutucuğunda görmek istediğiniz cihaz için **cihazlardan** bir cihaz seçin. Daha sonra, kutucukta kullanılabilecek cihaz özelliklerinin bir listesini görürsünüz.
1. Panoda kutucuk oluşturmak için **sıcaklık** ' ı seçin ve Pano alanına sürükleyin. Ayrıca, **sıcaklık** ' ın yanındaki onay kutusunu seçip **kutucuk Ekle**' yi seçebilirsiniz. Aşağıdaki ekran görüntüsünde bir cihaz şablonu ve cihazı seçip panoda bir sıcaklık telemetri kutucuğu oluşturulması gösterilmektedir.
1. Değişikliklerinizi panoya kaydetmek için sol üst kısımdaki **Kaydet** ' i seçin.

> [!div class="mx-imgBorder"]
> ![Denetim, sıcaklık kutucuğunun ayrıntılarını içeren sekmesi](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Artık kişisel panonuzu görüntülediğinizde, cihaz için **sıcaklık** ayarıyla yeni kutucuğu görürsünüz:

> [!div class="mx-imgBorder"]
> ![Cihaz için sıcaklıklar ayarıyla yeni kutucuğu gösteren ekran görüntüsü.](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Kişisel panolarınızı daha fazla özelleştirmeyi öğrenmek için Kitaplıktaki diğer döşeme türlerini keşfedebilirsiniz.

Azure IoT Central 'de kutucukları kullanma hakkında daha fazla bilgi için bkz. [panonuza kutucuk ekleme](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Panoları yönetme

Birkaç kişisel panonuz olabilir ve bunlar arasında geçiş yapabilir ya da varsayılan uygulama panolarından birini seçebilirsiniz:

> [!div class="mx-imgBorder"]
> ![Panolar arasında geçiş yap](media/howto-create-personal-dashboards/switch-dashboards.png)

Kişisel panolarınızı düzenleyebilir ve artık ihtiyacınız olmayan panoları silebilirsiniz. **Yönetici** değilseniz, uygulama düzeyi panoları da düzenleyebilir veya silebilirsiniz.

> [!div class="mx-imgBorder"]
> ![Panoları silme](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Sonraki adımlar

Artık kişisel panolar oluşturmayı ve yönetmeyi öğrendiğinize göre, [uygulama tercihlerinizi nasıl yöneteceğinizi öğrenebilirsiniz](howto-manage-preferences.md).
