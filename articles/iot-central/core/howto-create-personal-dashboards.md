---
title: Azure IoT Central kişisel panoları oluşturma | Microsoft Docs
description: Kullanıcı olarak, kişisel panolarınızı oluşturma ve yönetme hakkında bilgi edinin.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 9da7efad816a466eb9d2902e36a95c5ae0fa626b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950671"
---
# <a name="create-and-manage-multiple-dashboards"></a>Birden çok pano oluşturma ve yönetme

**Pano** , uygulamanıza ilk gittiğinizde yüklenen sayfasıdır. Uygulamanızdaki bir **Oluşturucu** , tüm kullanıcılar için varsayılan uygulama panosunu tanımlar. Bu varsayılan panoyu kendi kişiselleştirilmiş uygulama panonuz ile değiştirebilirsiniz. Farklı verileri görüntüleyen ve aralarında geçiş yapan çeşitli panolarınız olabilir. 

Uygulamanın yöneticisiyseniz, uygulamanın diğer kullanıcılarıyla paylaşmak için en fazla 10 uygulama düzeyi Pano da oluşturabilirsiniz. Yalnızca **yöneticilerin** uygulama düzeyi panoları oluşturma, düzenleme ve silme olanağı vardır. 

## <a name="create-dashboard"></a>Pano Oluştur

Aşağıdaki ekran görüntüsünde, Pano **özel uygulama** şablonundan oluşturulan bir uygulamada gösterilmektedir. Varsayılan uygulama panosunu bir kişisel Pano ile veya bir yönetici ise başka bir uygulama düzeyi panosu ile değiştirebilirsiniz. Bunu yapmak için sayfanın sol üst kısmındaki **+ Yeni** ' yi seçin.
 
> [!div class="mx-imgBorder"]
> "özel uygulama" şablonuna dayalı uygulamalar için Pano ![](media/howto-create-personal-dashboards/dashboard-custom-app.png)

**+ Yeni** seçildiğinde Pano Düzenleyicisi açılır. Düzenleyicide, panonuza bir ad verebilir ve kitaplıktan öğe seçebilirsiniz. Kitaplık, Panoyu özelleştirmek için kullanabileceğiniz kutucukları ve Pano temel öğelerini içerir.

> [!div class="mx-imgBorder"]
> Pano kitaplığı](media/howto-create-personal-dashboards/dashboard-library.png) ![

Uygulamanın yöneticisiyseniz **, bir kişisel** düzey pano veya uygulama düzeyi panosu oluşturmak istediğinizde değiştirme seçeneği sunulur. Kişisel bir düzey Pano oluşturursanız, yalnızca sizin görebileceksiniz. Uygulama düzeyi panosu oluşturursanız, uygulamanın her kullanıcısı onu görebilir. Bir başlık girdikten ve oluşturmak istediğiniz Pano türünü seçtikten sonra, daha sonra kutucukları kaydedebilir ve ekleyebilirsiniz. Ya da şimdi hazır olduğunuzda ve bir cihaz şablonu ve cihaz örneği eklediyseniz, devam edebilir ve ilk Kutucuğunuzu oluşturabilirsiniz. 

> [!div class="mx-imgBorder"]
> ![cihaz ayrıntılarını yapılandırma "sıcaklık ayrıntılarını içeren form](media/howto-create-personal-dashboards/device-details.png)

Örneğin, cihazın geçerli sıcaklığını için bir **telemetri** kutucuğu ekleyebilirsiniz. Bunu yapmak için:
1. Bir **cihaz şablonu** seçin
1. Bir pano kutucuğunda görmek istediğiniz cihaz için bir **cihaz örneği** seçin. Daha sonra, kutucukta kullanılabilecek cihaz özelliklerinin bir listesini görürsünüz.
1. Kutucuğu panoda oluşturmak için **sıcaklık** ' ya tıklayın ve Pano alanına sürükleyin. Ayrıca, **sıcaklık** ' ın yanındaki onay kutusuna tıklayabilir ve **Birleştir**' e tıklayabilirsiniz. Aşağıdaki ekran görüntüsünde bir cihaz şablonu ve cihaz örneği seçip panoda bir sıcaklık telemetri kutucuğu oluşturma gösterilmektedir.
1. Kutucuğu panoya kaydetmek için sol üst kısımdaki **Kaydet** ' i seçin.

> [!div class="mx-imgBorder"]
> ![Pano "sekmesi sıcaklık kutucuğu için Ayrıntılar](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Artık kişisel panonuzu görüntülediğinizde, cihaz için **sıcaklık** ayarıyla yeni kutucuğu görürsünüz:

> [!div class="mx-imgBorder"]
> ![Pano "sekmesi sıcaklık kutucuğu için Ayrıntılar](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Kişisel panolarınızı daha fazla özelleştirmeyi öğrenmek için Kitaplıktaki diğer döşeme türlerini keşfedebilirsiniz.

Azure IoT Central 'de kutucukları kullanma hakkında daha fazla bilgi için bkz. [panonuza kutucuk ekleme](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Panoları yönetme

Birkaç kişisel panonuz olabilir ve bunlar arasında geçiş yapabilir ya da varsayılan uygulama panolarından birini seçebilirsiniz:

> [!div class="mx-imgBorder"]
> Panolar arasında geçiş ![](media/howto-create-personal-dashboards/switch-dashboards.png)

Kişisel panolarınızı düzenleyebilir ve artık ihtiyacınız olmayan panoları silebilirsiniz. **Yönetici**kullanıyorsanız, uygulama düzeyi panoları da düzenleme veya silme imkanına sahip olursunuz.

> [!div class="mx-imgBorder"]
> panoları silme ![](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Sonraki adımlar

Kişisel panolar oluşturmayı ve yönetmeyi öğrendiğinize göre, [uygulama tercihlerinizi nasıl yönetebileceğinizi öğrenebilirsiniz](howto-manage-preferences.md)
