---
title: Azure IoT Central uygulamanızdaki cihazları yönetme | Microsoft Docs
description: Bir operatör olarak, Azure IoT Central uygulamanızda cihazların nasıl yönetileceğini öğrenin.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: adcaa29ed894f2d61871f467369bcdd05f8cc593
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601612"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Azure IoT Central uygulamanızda cihazları yönetme

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bu makalede, Azure IoT Central uygulamanızdaki cihazları yönetmek için bir operatör olarak nasıl yapılacağı açıklanır. Bir işleç olarak şunları yapabilirsiniz:

- Azure IoT Central uygulamanıza bağlı cihazları görüntülemek, eklemek ve silmek için **cihazlar** sayfasını kullanın.
- Cihazlarınızın güncel envanterini saklayın.
- Cihaz özelliklerinde depolanan değerleri değiştirerek cihaz meta verilerinizi güncel tutun.
- **Ayarlar** sayfasından belirli bir cihazdaki bir ayarı güncelleştirerek cihazlarınızın davranışını denetleyin.

## <a name="view-your-devices"></a>Cihazlarınızı görüntüleme

Tek bir cihazı görüntülemek için:

1. Sol bölmedeki **cihazlar** ' ı seçin. Burada, [cihaz şablonlarınızın](howto-set-up-template.md)bir listesini görürsünüz.

1. **Şablonlar** listesinden bir cihaz şablonu seçin.

1. **Cihazlar** sayfasının sağ bölmesinde, bu cihaz şablonundan oluşturulan cihazların bir listesini görürsünüz. Bu cihaz için cihaz ayrıntıları sayfasını görmek için tek bir cihaz seçin:

    ![Cihaz ayrıntıları sayfası](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Cihaz ekleme

Azure IoT Central uygulamanıza bir cihaz eklemek için:

1. Sol bölmedeki **cihazlar** ' ı seçin.

1. Cihaz oluşturmak istediğiniz cihaz şablonunu seçin.

1. \+ **Yeni**seçeneğini belirleyin.

1. **Gerçek** veya **benzetimli**' ı seçin. Gerçek bir cihaz, Azure IoT Central uygulamanıza bağlandığınız fiziksel bir cihaza yöneliktir. Sanal cihaz, Azure IoT Central tarafından sizin için oluşturulan örnek verilere sahiptir.

## <a name="import-devices"></a>Cihazları içeri aktar

Çok sayıda cihazı uygulamanıza bağlamak için, cihazları bir CSV dosyasından toplu içeri aktarabilirsiniz. CSV dosyası aşağıdaki sütunlara ve üstbilgilere sahip olmalıdır:

* **IOTC_DeviceID** -cihaz kimliği tümüyle küçük harfle yazılmalıdır.
* **IOTC_DeviceName** -bu sütun isteğe bağlıdır.

Uygulamanızdaki cihazları toplu olarak kaydetmek için:

1. Sol bölmedeki **cihazlar** ' ı seçin.

1. Sol bölmede, cihazlarını toplu olarak oluşturmak istediğiniz cihaz şablonunu seçin.

    > [!NOTE]
    > Henüz bir cihaz şablonunuz yoksa, cihazları **ilişkilendirilmemiş cihazlarda** içeri aktarabilir ve bunları şablon olmadan kaydedebilirsiniz. Cihazlar alındıktan sonra, bunları bir şablonla ilişkilendirebilirsiniz.

1. **Içeri aktar**' ı seçin.

    ![İçeri aktarma eylemi](./media/howto-manage-devices/bulkimport1a.png)

1. İçeri aktarılacak cihaz kimliklerinin listesini içeren CSV dosyasını seçin.

1. Dosya karşıya yüklendikten sonra cihaz içeri aktarma işlemi başlar. İçeri aktarma durumunu cihaz kılavuzunun üst kısmında izleyebilirsiniz.

1. İçeri aktarma işlemi tamamlandıktan sonra, cihaz kılavuzunda bir başarı iletisi gösterilir.

    ![İçeri aktarma başarılı](./media/howto-manage-devices/bulkimport3a.png)

Cihaz içeri aktarma işlemi başarısız olursa, cihaz kılavuzunda bir hata iletisi görürsünüz. İndirebileceğiniz tüm hataları yakalayan bir günlük dosyası oluşturulur.

**Cihazları bir şablonla ilişkilendirme**

**İlişkilendirilmemiş cihazlarda**içeri aktarma işlemi başlatarak cihazları kaydedersiniz, cihazlar herhangi bir cihaz şablonu ilişkilendirmesi olmadan oluşturulur. Cihazların verileri ve cihazla ilgili diğer ayrıntıları araştırmak için bir şablonla ilişkilendirilmesi gerekir. Cihazları bir şablonla ilişkilendirmek için aşağıdaki adımları izleyin:

1. Sol bölmedeki **cihazlar** ' ı seçin.

1. Sol bölmede **ilişkilendirilmemiş cihazlar**' ı seçin:

    ![İlişkilendirilmemiş cihazlar](./media/howto-manage-devices/unassociateddevices1a.png)

1. Bir şablonla ilişkilendirmek istediğiniz cihazları seçin:

1. **İlişkilendir**' i seçin:

    ![Cihazları ilişkilendir](./media/howto-manage-devices/unassociateddevices2a.png)

1. Kullanılabilir şablonlar listesinden şablonu seçin ve **ilişkilendir**' i seçin.

1. Seçilen cihazlar seçtiğiniz cihaz şablonuyla ilişkili.

> [!NOTE]
> Bir cihaz ilişkili bir şablonla ilişkilendirildikten sonra ilişkilendirilmemiş veya farklı bir şablonla ilişkilendirilemez.

## <a name="export-devices"></a>Cihazları dışarı aktar

Gerçek bir cihazı IoT Central bağlamak için, bağlantı dizesine ihtiyacınız vardır. Cihaz bağlantı dizeleri oluşturmak için gereken bilgileri almak üzere cihaz ayrıntılarını toplu olarak dışarı aktarabilirsiniz. Dışarı aktarma işlemi, seçilen tüm cihazların cihaz kimliği, cihaz adı ve anahtarlarıyla bir CSV dosyası oluşturur.

Uygulamanızdaki cihazları toplu olarak dışarı aktarmak için:

1. Sol bölmedeki **cihazlar** ' ı seçin.

1. Sol bölmede, cihazlarını dışarı aktarmak istediğiniz cihaz şablonunu seçin.

1. Dışarı aktarmak istediğiniz cihazları seçin ve ardından **dışarı aktarma** eylemini seçin.

    ![Dışarı Aktarma](./media/howto-manage-devices/export1a.png)

1. Dışarı aktarma işlemi başlar. Durumu Kılavuzun üst kısmında izleyebilirsiniz.

1. Dışarı aktarma işlemi tamamlandığında, oluşturulan dosyayı indirmek için bir bağlantı ile birlikte bir başarı iletisi gösterilir.

1. Dosyayı diskteki yerel bir klasöre indirmek için **başarı iletisini** seçin.

    ![Dışarı aktarma başarısı](./media/howto-manage-devices/export2a.png)

1. İçe aktarılmış CSV dosyası şu sütunları içerir: cihaz KIMLIĞI, cihaz adı, cihaz anahtarları ve x509 sertifikası parmak izleri:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Bağlantı dizeleri hakkında daha fazla bilgi ve gerçek Cihazları IoT Central uygulamanıza bağlama hakkında daha fazla bilgi için bkz. [Azure IoT Central 'de cihaz bağlantısı](concepts-connectivity.md).

## <a name="delete-a-device"></a>Bir cihazı silme

Azure IoT Central uygulamanızdan gerçek ya da sanal bir cihazı silmek için:

1. Sol bölmedeki **cihazlar** ' ı seçin.

1. Silmek istediğiniz cihazın cihaz şablonunu seçin.

1. Silinecek cihazın yanındaki kutuyu işaretleyin.

1. **Sil**' i seçin.

## <a name="change-a-device-setting"></a>Bir cihaz ayarını değiştirme

Ayarlar bir cihazın davranışını denetler. Diğer bir deyişle, cihazınıza giriş sağlamanıza olanak tanır. Cihaz ayarlarını **cihaz ayrıntıları** sayfasında görüntüleyebilir ve güncelleştirebilirsiniz.

1. Sol bölmedeki **cihazlar** ' ı seçin.

1. Ayarlarını değiştirmek istediğiniz cihazın cihaz şablonunu seçin.

1. **Ayarlar** sekmesini seçin. Burada, cihazınızın sahip olduğu tüm ayarları ve bunların geçerli değerlerini görürsünüz. Her ayar için cihazın hala eşitleme olup olmadığını görebilirsiniz.

1. Ayarları, ihtiyacınız olan değerlerle değiştirin. Aynı anda birden çok ayarı değiştirebilir ve tümünü aynı anda güncelleştirebilirsiniz.

1. **Güncelleştir**' i seçin. Değerler cihazınıza gönderilir. Cihaz ayar değişikliğini onayladığında, ayarın durumu **eşitlenmiş**' a geri gider.

## <a name="change-a-property"></a>Bir özelliği değiştirme

Özellikler, cihazla ilişkili, şehir ve seri numarası gibi cihaz meta verileriydi. **Cihaz ayrıntıları** sayfasında özellikleri görüntüleyebilir ve güncelleştirebilirsiniz.

1. Sol bölmedeki **cihazlar** ' ı seçin.

1. Özelliklerini değiştirmek istediğiniz cihazın cihaz şablonunu seçin.

1. Tüm özellikleri gördüğünüz **Özellikler** sekmesini seçin.

1. Uygulama özelliklerini, ihtiyacınız olan değerlerle değiştirin. Aynı anda birden çok özelliği değiştirebilir ve tümünü aynı anda güncelleştirebilirsiniz. **Güncelleştir**' i seçin.

> [!NOTE]
> _Cihaz özelliklerinin_değerini değiştiremezsiniz. Cihaz özellikleri cihaz tarafından ayarlanır ve Azure IoT Central uygulaması içinde salt okunurdur.

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanızda cihazların nasıl yönetileceğini öğrendiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Cihaz kümelerini kullanma](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
