---
title: Azure IoT Central uygulamanızdaki cihazları yönetme | Microsoft Docs
description: Bir operatör olarak, Azure IoT Central uygulamanızda cihazların nasıl yönetileceğini öğrenin.
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 07afe441b9463cace9076a07d89e762586730e1b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894779"
---
# <a name="manage-devices-in-your-azure-iot-central-application-preview-features"></a>Azure IoT Central uygulamanızda cihazları yönetme (Önizleme özellikleri)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu makalede, Azure IoT Central uygulamanızdaki cihazları yönetmek için bir operatör olarak nasıl yapılacağı açıklanır. Bir işleç olarak şunları yapabilirsiniz:

- Azure IoT Central uygulamanıza bağlı cihazları görüntülemek, eklemek ve silmek için **cihazlar** sayfasını kullanın.
- Cihazlarınızın güncel envanterini saklayın.
- Cihaz özelliklerinde depolanan değerleri görünümlerinizle değiştirerek cihazınızın meta verilerini güncel tutun.
- Belirli bir cihazdaki bir ayarı görünümlarınızdan güncelleştirerek cihazlarınızın davranışını denetleyin.

## <a name="view-your-devices"></a>Cihazlarınızı görüntüleme

Tek bir cihazı görüntülemek için:

1. Sol bölmedeki **cihazlar** ' ı seçin. Burada tüm cihazların ve cihaz şablonlarınızın bir listesini görürsünüz.

1. Bir cihaz şablonu seçin.

1. **Cihazlar** sayfasının sağ bölmesinde, bu cihaz şablonundan oluşturulan cihazların bir listesini görürsünüz. Bu cihaz için cihaz ayrıntıları sayfasını görmek için tek bir cihaz seçin:

    ![Cihaz ayrıntıları sayfası](./media/howto-manage-devices/devicelist.png)


## <a name="add-a-device"></a>Cihaz ekleme

Azure IoT Central uygulamanıza bir cihaz eklemek için:

1. Sol bölmedeki **cihazlar** ' ı seçin.

1. Cihaz oluşturmak istediğiniz cihaz şablonunu seçin.

1. \+ **Yeni**seçeneğini belirleyin.

1. **Benzetimli** geçişi **Açık** veya **kapalı**olarak açın. Gerçek bir cihaz, Azure IoT Central uygulamanıza bağlandığınız fiziksel bir cihaza yöneliktir. Sanal cihaz, Azure IoT Central tarafından sizin için oluşturulan örnek verilere sahiptir.

1. **Oluştur**'a tıklayın.

1. Bu cihaz artık cihaz listenizde bu şablon için görünüyor. Cihazın tüm görünümlerini içeren cihaz ayrıntıları sayfasını görmek için cihazı seçin.

## <a name="import-devices"></a>Cihazları içeri aktar

Çok sayıda cihazı uygulamanıza bağlamak için, cihazları bir CSV dosyasından toplu içeri aktarabilirsiniz. CSV dosyası aşağıdaki sütunlara ve üstbilgilere sahip olmalıdır:

* **IOTC_DeviceID** -cihaz kimliği tümüyle küçük harfle yazılmalıdır.
* **IOTC_DeviceName** -bu sütun isteğe bağlıdır.

Uygulamanızdaki cihazları toplu olarak kaydetmek için:

1. Sol bölmedeki **cihazlar** ' ı seçin.

1. Sol bölmede, cihazlarını toplu olarak oluşturmak istediğiniz cihaz şablonunu seçin.

    > [!NOTE]
    > Henüz bir cihaz şablonunuz yoksa, cihazları **tüm cihazlarda** içeri aktarabilir ve şablon olmadan kaydedebilirsiniz. Cihazlar alındıktan sonra bunları bir şablona geçirebilirsiniz.

1. **Içeri aktar**' ı seçin.

    ![İçeri aktarma eylemi](./media/howto-manage-devices/bulkimport1a.png)


1. İçeri aktarılacak cihaz kimliklerinin listesini içeren CSV dosyasını seçin.

1. Dosya karşıya yüklendikten sonra cihaz içeri aktarma işlemi başlar. İçeri aktarma durumunu cihaz Işlemleri panelinde izleyebilirsiniz. Bu panel, içeri aktarma başladıktan sonra otomatik olarak görünür veya sağ üst köşedeki zil simgesiyle erişebilirsiniz.

1. İçeri aktarma işlemi tamamlandıktan sonra, cihaz Işlemler panelinde bir başarı iletisi gösterilir.

    ![İçeri aktarma başarılı](./media/howto-manage-devices/bulkimport3a.png)


Cihaz içeri aktarma işlemi başarısız olursa, cihaz Işlemleri panelinde bir hata iletisi görürsünüz. İndirebileceğiniz tüm hataları yakalayan bir günlük dosyası oluşturulur.

**Cihazları bir şablona geçirme**

**Tüm cihazlarda**içeri aktarma işlemi başlatarak cihazları kaydedersiniz, cihazlar herhangi bir cihaz şablonu ilişkilendirmesi olmadan oluşturulur. Cihazların verileri ve cihazla ilgili diğer ayrıntıları araştırmak için bir şablonla ilişkilendirilmesi gerekir. Cihazları bir şablonla ilişkilendirmek için aşağıdaki adımları izleyin:

1. Sol bölmedeki **cihazlar** ' ı seçin.

1. Sol panelde **tüm cihazlar**' ı seçin:

    ![İlişkilendirilmemiş cihazlar](./media/howto-manage-devices/unassociateddevices1a.png)


1. **Cihaz şablonu** sütunundaki değerin cihazlarınızdan herhangi biri Için "ilişkilendirilmemiş" olup olmadığını anlamak için kılavuzdaki filtreyi kullanın.

1. Bir şablonla ilişkilendirmek istediğiniz cihazları seçin:

1. **Geçişi**seçin:

    ![Cihazları ilişkilendir](./media/howto-manage-devices/unassociateddevices2a.png)


1. Kullanılabilir şablonlar listesinden şablonu seçin ve **geçir**' i seçin.

1. Seçilen cihazlar seçtiğiniz cihaz şablonuyla ilişkili.


## <a name="export-devices"></a>Cihazları dışarı aktar

Gerçek bir cihazı IoT Central bağlamak için, bağlantı dizesine ihtiyacınız vardır. Cihaz bağlantı dizeleri oluşturmak için gereken bilgileri almak üzere cihaz ayrıntılarını toplu olarak dışarı aktarabilirsiniz. Dışarı aktarma işlemi, seçilen tüm cihazların cihaz kimliği, cihaz adı ve anahtarlarıyla bir CSV dosyası oluşturur.

Uygulamanızdaki cihazları toplu olarak dışarı aktarmak için:

1. Sol bölmedeki **cihazlar** ' ı seçin.

1. Sol bölmede, cihazlarını dışarı aktarmak istediğiniz cihaz şablonunu seçin.

1. Dışarı aktarmak istediğiniz cihazları seçin ve ardından **dışarı aktarma** eylemini seçin.

    ![Dışarı Aktarma](./media/howto-manage-devices/export1a.png)


1. Dışarı aktarma işlemi başlar. Cihaz Işlemleri panelini kullanarak durumu izleyebilirsiniz.

1. Dışarı aktarma işlemi tamamlandığında, oluşturulan dosyayı indirmek için bir bağlantı ile birlikte bir başarı iletisi gösterilir.

1. Dosyayı diskteki yerel bir klasöre indirmek için **dosya indir** bağlantısını seçin.

    ![Dışarı aktarma başarısı](./media/howto-manage-devices/export2a.png)


1. İçe aktarılmış CSV dosyası şu sütunları içerir: cihaz KIMLIĞI, cihaz adı, cihaz anahtarları ve x509 sertifikası parmak izleri:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Bağlantı dizeleri ve gerçek Cihazları IoT Central uygulamanıza bağlama hakkında daha fazla bilgi için bkz. [Azure IoT Central cihaz bağlantısı](overview-iot-central-get-connected.md).

## <a name="delete-a-device"></a>Bir cihazı silme

Azure IoT Central uygulamanızdan gerçek ya da sanal bir cihazı silmek için:

1. Sol bölmedeki **cihazlar** ' ı seçin.

1. Silmek istediğiniz cihazın cihaz şablonunu seçin.

1. Cihazlarınızı filtrelemek ve aramak için filtre araçlarını kullanın. Silinecek cihazların yanındaki kutuyu işaretleyin.

1. **Sil**' i seçin. Bu silmenin durumunu cihaz Işlemleri panelinden izleyebilirsiniz.

## <a name="change-a-property"></a>Bir özelliği değiştirme

Bulut özellikleri, cihazla ilişkili, şehir ve seri numarası gibi cihaz meta verileriydi. Yazılabilir Özellikler bir cihazın davranışını denetler. Diğer bir deyişle, cihazınıza giriş sağlamanıza olanak tanır.  Cihaz özellikleri cihaz tarafından ayarlanır ve IoT Central içinde salt okunurdur. Cihazınızın **cihaz ayrıntıları** görünümlerinde özellikleri görüntüleyebilir ve güncelleştirebilirsiniz.

1. Sol bölmedeki **cihazlar** ' ı seçin.

1. Özelliklerini değiştirmek istediğiniz cihazın cihaz şablonunu seçin ve hedef cihazı seçin.

1. Cihazınızın özelliklerini içeren görünümü seçin. Bu görünüm, değerleri girmenizi ve sayfanın en üstünde **Kaydet** ' i seçmenizi sağlar. Burada, cihazınızın sahip olduğu özellikleri ve bunların geçerli değerlerini görürsünüz. Bulut özellikleri ve yazılabilir özellikler, cihaz özellikleri salt okunurdur, düzenlenebilir alanlara sahiptir. Yazılabilir özellikler için alanın en altında eşitleme durumlarını görebilirsiniz. 

1. Özellikleri, ihtiyacınız olan değerlerle değiştirin. Aynı anda birden çok özelliği değiştirebilir ve tümünü aynı anda güncelleştirebilirsiniz.

1. **Kaydet**'i seçin. Yazılabilir özellikleri kaydettiyseniz, değerler cihazınıza gönderilir. Cihaz yazılabilir özelliğin değişikliğini onayladığında, durum **eşitlenmiş**olarak geri döner. Bir bulut özelliğini kaydettiyseniz, değer güncellenir.


## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanızda cihazların nasıl yönetileceğini öğrendiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Cihaz gruplarını kullanma](tutorial-use-device-groups.md)

<!-- Next how-tos in the sequence -->
