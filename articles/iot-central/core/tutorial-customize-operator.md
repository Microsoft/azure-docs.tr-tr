---
title: 'Öğretici: işlecin Azure IoT Central görünümlerini özelleştirme'
description: 'Öğretici: Oluşturucu olarak, Azure IoT Central uygulamanızda işlecin görünümlerini özelleştirin.'
author: sandeeppujar
ms.author: sandeepu
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: d835ce5a1cbdcbd9741c775c4e9bd2eea7c803d7
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112330"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Öğretici: Azure IoT Central operatör görünümünü özelleştirme

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bu öğreticide, bir oluşturucu olarak uygulamanızın operatör görünümünü nasıl özelleştireceğiniz gösterilmektedir. Oluşturucu olarak uygulamada bir değişiklik yaptığınızda, Microsoft Azure IoT Central uygulamasında operatör görünümünün önizlemesini görebilirsiniz.

Bu öğreticide, bağlı klima cihazıyla ilgili bilgileri bir operatöre gösterecek şekilde uygulamayı özelleştireceksiniz. Özelleştirmeleriniz sayesinde operatör, uygulamaya bağlı klima cihazlarını yönetebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Cihaz panonuzu yapılandırma
> * Cihaz ayarları düzeninizi yapılandırma
> * Cihaz özellikleri düzeninizi yapılandırma
> * Bir operatör olarak cihazın önizlemesini görme
> * Varsayılan uygulama panonuzu yapılandırma
> * Varsayılan uygulama panosunu operatör olarak önizleyin

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, önceki iki öğreticiyi tamamlamanız gerekir:

* [Azure IoT Central uygulamanızda yeni bir cihaz türü tanımlama](tutorial-define-device-type.md).
* [Cihazınız için kurallar ve eylemler yapılandırma](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Cihaz panonuzu yapılandırma

Oluşturucu olarak, bir cihaz panosunda hangi bilgilerin gösterileceğini tanımlayabilirsiniz. [Uygulamanızda yeni bir cihaz türü tanımlayın](tutorial-define-device-type.md) öğreticide, **bağlantılı hava koşullayıcı** panosuna bir çizgi grafik ve başka bilgiler eklediniz.

1. **Bağlı hava conditioner** cihaz şablonunu düzenlemek için sol bölmedeki **cihaz şablonları** ' nı seçin:

    ![Cihaz şablonları sayfası](media/tutorial-customize-operator/devicetemplates.png)

2. Cihaz panonuzu özelleştirmek için, [uygulama öğreticinizdeki yeni bir cihaz türü tanımlama](tutorial-define-device-type.md) bölümünde oluşturduğunuz **bağlı AIR conditioner (1.0.0)** cihaz şablonu cihazını seçin.

3. Panoyu düzenlemek için **Pano** sekmesini seçin.

4. Panoya Ana Performans Göstergesi (KPI) kutucuğu eklemek için **KPI**'yı seçin:

    KPI’yı tanımlamak için aşağıdaki tabloda yer alan bilgileri kullanın:

    | Ayar     | Değer |
    | ----------- | ----- |
    | Ad        | En fazla sıcaklık |
    | Zaman aralığı  | Geçen 1 hafta |
    | Ölçüm türü | Telemetri |
    | Ölçüm | sıcaklık |
    | Toplama | Maksimum |
    | Görünürlük  | Etkin |

    ![KPI ekleme](media/tutorial-customize-operator/addkpi.png)

5. **Kaydet**’i seçin. Artık KPI kutucuğunu panoda görebilirsiniz:

    ![KPI kutucuğu](media/tutorial-customize-operator/temperaturekpi.png)

6. Panodaki bir kutucuğu taşımak veya yeniden boyutlandırmak için, fare işaretçisini kutucuğun üzerine getirin. Kutucuğu yeni bir konuma sürükleyebilir veya yeniden boyutlandırabilirsiniz.

## <a name="configure-your-settings-layout"></a>Ayar düzeninizi değiştirme

Oluşturucu olarak, cihaz ayarlarının operatör görünümünü de yapılandırabilirsiniz. Bir operatör cihaz yapılandırmak için cihaz ayarları sekmesini kullanır. Örneğin, bir işleç bağlantılı hava koşullayıcı için hedef sıcaklığını ayarlamak üzere ayarlar sekmesini kullanır.

1. Bağlı hava koşullarınızın ayar yerleşimini düzenlemek için **Ayarlar** sekmesini seçin.

2. Ayar kutucuklarını taşıyabilir ve yeniden boyutlandırabilirsiniz:

    ![Ayarlar düzenini değiştirme](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>Özellikler düzeninizi yapılandırma

Pano ve ayarlara ek olarak cihaz özelliklerinin operatör görünümünü de yapılandırabilirsiniz. Bir operatör cihaz meta verilerini yönetmek için cihaz özellikleri sekmesini kullanır. Örneğin, bir operatör üreticinin cihaz seri numarasını veya güncelleştirme iletişim ayrıntılarını görüntülemek için Özellikler sekmesini kullanır.

1. Bağlı hava koşullarınızın Özellikler yerleşimini düzenlemek için **Özellikler** sekmesini seçin.

2. Özellikler alanlarını taşıyabilir ve yeniden boyutlandırabilirsiniz:

    ![Özellikler düzenini değiştirme](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>Cihazın önizlemesini görüntüleyin

Bir işlecin Pano, ayarlar ve Özellikler sekmelerini özelleştirmek için **cihaz şablonları** sayfasını kullanın. Cihaz şablonunu görüntülemek ve kullanmak için **Device Explorer** sayfasını kullanın.

1. Bağlı hava koşullayıcı şablonunu bir operatör olarak görüntülemek ve kullanmak için **Device Explorer** sayfasına gidin ve şablonunuzda oluşturulan IoT Central sanal cihazı seçin:

    ![Cihaz şablonunu görüntüleme ve kullanma](media/tutorial-customize-operator/usetemplate.png)

2. Bu cihazın konumunu güncelleştirmek için **Özellikler** ' i seçin ve konum kutucuğunda değeri düzenleyin. Sonra **Kaydet**' i seçin:

    ![Özellik değerini düzenleme](media/tutorial-customize-operator/editproperty.png)

3. Bağlı klimanıza bir ayar göndermek için **Ayarlar**’ı seçin, bir kutucuktaki ayar değerini değiştirin ve **Güncelleştir**’i seçin:

    ![Cihaza ayar gönderme](media/tutorial-customize-operator/sendsetting.png)

    Cihaz yeni ayar değerini kabul ettiğinde, ayar kutucukta **eşitlendi** olarak görünür.

4. Operatör olarak, cihaz panosunu oluşturucu tarafından yapılandırıldığı şekilde görüntüleyebilirsiniz:

    ![Cihaz panosunun operatör görünümü](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>Varsayılan panoyu yapılandırma

Bir Oluşturucu veya operatör bir Azure IoT Central uygulamasında oturum açtığında, uygulama panosunu görürler. Bir Oluşturucu olarak, varsayılan panonun içeriğini bir operatör için en yararlı ve ilgili içeriği içerecek şekilde yapılandırabilirsiniz.

> [!NOTE]
> Kullanıcılar ayrıca kendi kişisel panoları oluşturabilir ve varsayılan olarak bir tane seçebilirler.

1. Varsayılan uygulama panosunu özelleştirmek için **Pano** sayfasına gidin ve sayfanın sağ üst kısmında **Düzenle** ' yi seçin. Panoya ekleyebileceğiniz nesne kitaplığıyla bir panel görüntülenir.

    ![Pano sayfası](media/tutorial-customize-operator/builderhome.png)

2. Panoyu özelleştirmek için **Kitaplıktan**kutucukları ekleyin. **Bağlantı**’yı seçin ve kuruluşunuzun web sitesinin ayrıntılarını ekleyin. Ardından **Kaydet**'i seçin:

    ![Panoya bağlantı ekle](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Azure IoT Central uygulamanızdaki sayfalara bağlantılar da ekleyebilirsiniz. Örneğin, bir cihazın panosuna ya da ayarlar sayfasına bağlantı ekleyebilirsiniz.

3. İsteğe bağlı olarak, **görüntü** ' i seçin ve panonuzda görüntülenecek bir resim yükleyin. Bir görüntü, seçtiğinizde gittiğiniz bir URL 'ye sahip olabilir:

    ![Panoya resim ekleme](media/tutorial-customize-operator/addimage.png)

    Daha fazla bilgi için bkz. [Görüntüleri hazırlama ve Azure IoT Central uygulamanıza yükleme](howto-prepare-images.md).

## <a name="preview-the-dashboard"></a>Panonun önizlemesini görüntüleyin

Uygulama panosunu bir operatör olarak önizlemek için sayfanın sağ üst kısmında **bitti** ' yi seçin.

![Tasarım Modunu Açma/Kapatma](media/tutorial-customize-operator/operatorviewhome.png)

Oluşturucu olarak ayarladığınız URL 'Lere gitmek için bağlantı ve görüntü kutucuklarını seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, uygulamanın operatör görünümünü özelleştirme hakkında bilgi edindiniz.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Cihaz panonuzu yapılandırma
> * Cihaz ayarları düzeninizi yapılandırma
> * Cihaz özellikleri düzeninizi yapılandırma
> * Bir operatör olarak cihazın önizlemesini görme
> * Varsayılan giriş sayfanızı yapılandırma
> * Bir operatör olarak varsayılan giriş sayfasının önizlemesini görme

Uygulamanın operatör görünümünü özelleştirmeyi öğrendiğinize göre, önerilen sonraki adımlar şunlardır:

* [Cihazlarınızı izleme (operatör olarak)](tutorial-monitor-devices.md)
* [Uygulamanıza yeni cihaz ekleme (operatör ve cihaz geliştiricisi olarak)](tutorial-add-device.md)