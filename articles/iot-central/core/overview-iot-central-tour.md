---
title: Azure IoT Central Kullanıcı Arabirimi turuna katılın | Microsoft Docs
description: Oluşturucu olarak, bir IoT çözümü oluşturmak için kullanabileceğiniz Azure IoT Central kullanıcı arabiriminin temel alanlarını tanıyın.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 6e375e809308da8be0723fb2fcbdf718e9fb5479
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957772"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Azure IoT Central Kullanıcı Arabirimi turuna katılın

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bu makalede, Microsoft Azure IoT Central kullanıcı arabirimi tanıtılmaktadır. Kullanıcı arabirimini kullanarak bir Azure IoT Central çözümü ile bağlı cihazlarını oluşturabilir, yönetebilir ve kullanabilirsiniz.

_Oluşturucu_ olarak, Azure IoT Central kullanıcı arabirimini kullanarak Azure IoT Central çözümünüzü tanımlayabilirsiniz. Kullanıcı arabirimini kullanarak şunları yapabilirsiniz:

- Çözümünüze bağlanan cihaz türlerini tanımlama.
- Cihazlarınız için kurallar ve eylemler yapılandırma.
- Kullanıcı arabirimini, çözümünüzü kullanan bir _operatör_ için özelleştirme.

_Operatör_ olarak, Azure IoT Central kullanıcı arabirimini kullanarak Azure IoT Central çözümünüzü yönetebilirsiniz. Kullanıcı arabirimini kullanarak şunları yapabilirsiniz:

- Cihazlarınızı izleme.
- Cihazlarınızı yapılandırma.
- Cihazlarınızla ilgili sorunları giderme ve düzeltme.
- Yeni cihazları hazırlama.

## <a name="use-the-left-pane"></a>Sol bölmeyi kullanın

Uygulamanın farklı bölümlerine erişmek için sol bölmeyi kullanın. Gezinti çubuğunu genişleterek veya daraltarak **<** veya **>** seçeneğini belirleyebilirsiniz:

:::row:::
  :::column span="":::
      ![sol bölme](media/overview-iot-central-tour/navigationbar.png)
  :::column-end:::
  :::column span="2":::
     **Pano** , uygulama panonuzu görüntüler. Bir Oluşturucu olarak, operatörlerinizi panosunu özelleştirebilirsiniz. Kullanıcılar kendi panoları da oluşturabilir.
    
     **Device Explorer** uygulamadaki her bir cihaz şablonuyla ilişkili olan sanal ve gerçek cihazları listeler. Operatör olarak, **Device Explorer**’ı kullanarak bağlı cihazlarınızı yönetebilirsiniz.
    
     **Cihaz kümeleri** , cihaz kümelerini görüntülemenize ve oluşturmanıza olanak sağlar. Operatör olarak, bir sorgu tarafından belirtilen cihazların mantıksal bir koleksiyonu olarak cihaz kümeleri oluşturabilirsiniz.
    
     **Analytics** , cihazlar ve cihaz kümeleri için cihaz telemetrisinden türetilmiş analizi gösterir. Operatör olarak, uygulamanızdan içgörüler türetmek için cihaz verileri üzerinde özel görünümler oluşturabilirsiniz.
    
     **İşler** , güncelleştirmeleri ölçekli bir şekilde gerçekleştirmek için işler oluşturup çalıştırdığınızda toplu cihaz yönetimine olanak sağlar.
    
     **Cihaz şablonları** , bir oluşturucunun cihaz şablonları oluşturmak ve yönetmek için kullandığı araçları gösterir.
    
     **Sürekli veri dışa aktarma** , yöneticinin depolama ve kuyruklar gibi diğer Azure hizmetlerine sürekli bir dışarı aktarma yapılandırmasına olanak sağlar.
    
     **Yönetim** , bir yöneticinin uygulama ayarlarını, kullanıcıları ve rolleri yönetebildiği uygulama yönetimi sayfalarını gösterir.
   :::column-end:::
:::row-end:::

## <a name="search-help-and-support"></a>Arama, yardım ve destek

Üstteki menü her sayfada görünür:

![Araç Çubuğu](media/overview-iot-central-tour/toolbar.png)

- Cihaz şablonlarını ve cihazlarını aramak için bir **arama** değeri girin.
- Kullanıcı arabirimi dilini veya temasını değiştirmek için **Ayarlar** simgesini seçin.
- Uygulamanın oturumunu kapatmak için **Hesap** simgesini seçin.
- Yardım ve destek almak için, kaynak listesinden **Yardım** açılır listesini seçin. Deneme uygulamasında, destek kaynakları [canlı sohbete](howto-show-hide-chat.md)erişim içerir.

Kullanıcı arabirimi için açık renkli tema veya koyu renkli temayı seçebilirsiniz:

![Kullanıcı arabirimi için tema seçme](media/overview-iot-central-tour/themes.png)

> [!NOTE]
> Yönetici, uygulama için özel bir tema yapılandırmışsa açık ve koyu temalar arasında seçim yapmak için kullanılabilir değildir.

## <a name="dashboard"></a>Pano

![Pano](media/overview-iot-central-tour/homepage.png)

* Pano, Azure IoT Central uygulamanızda oturum açtığınızda gördüğünüz ilk sayfasıdır. Bir Oluşturucu olarak, kutucuk ekleyerek uygulama panosunu diğer kullanıcılar için özelleştirebilirsiniz. Daha fazla bilgi almak için [Azure IoT Central operatör görünümünü özelleştirme](tutorial-customize-operator.md) öğreticisine bakın.

* Bir operatör olarak, kişiselleştirilmiş panolar oluşturabilir ve bunlar arasında ve varsayılan pano arasında geçiş yapabilirsiniz. Daha fazla bilgi edinmek için bkz. [Azure IoT Central kişisel panolar](howto-create-personal-dashboards.md) nasıl yapılır makalesi oluşturma.

## <a name="device-explorer"></a>Device Explorer

![Gezgin sayfası](media/overview-iot-central-tour/explorer.png)

Gezgin sayfası, Azure IoT Central uygulamanızda _cihaz şablonuna_göre gruplanmış _cihazları_ gösterir.

* Cihaz şablonu, uygulamanıza bağlanabilen bir cihaz türünü tanımlar. Daha fazla bilgi almak için bkz. [Azure IoT Central uygulamanızda yeni bir cihaz türü tanımlama](tutorial-define-device-type.md).
* Cihaz, uygulamanızdaki gerçek veya sanal bir cihazı temsil eder. Daha fazla bilgi almak için bkz. [Azure IoT Central uygulamanıza yeni bir cihaz ekleme](tutorial-add-device.md).

## <a name="device-sets"></a>Cihaz kümeleri

![Cihaz Kümeleri sayfası](media/overview-iot-central-tour/devicesets.png)

_Cihaz kümeleri_ sayfası, oluşturucu tarafından oluşturulan cihaz kümelerini gösterir. Cihaz kümesi, ilişkili cihazların bir koleksiyonudur. Oluşturucu, bir cihaz kümesine eklenen cihazları belirlemek üzere bir sorgu tanımlar. Uygulamanızdaki analizleri özelleştirirken cihaz kümelerini kullanırsınız. Daha fazla bilgi almak için [Azure IoT Central uygulamanızda cihaz kümelerini kullanma](howto-use-device-sets.md) makalesine bakın.

## <a name="analytics"></a>Analiz

![Analiz sayfası](media/overview-iot-central-tour/analytics.png)

Analiz sayfasında, uygulamanıza bağlı cihazların nasıl davrandığını anlamanıza yardımcı olan grafikler gösterilir. Operatör bu sayfayı kullanarak bağlı cihazlarla ilgili sorunları izler ve araştırır. Oluşturucu, bu sayfada gösterilen grafikleri tanımlayabilir. Daha fazla bilgi almak için [Azure IoT Central uygulamanız için özel analizler oluşturma](howto-use-device-sets.md) makalesine bakın.

## <a name="jobs"></a>İş

![İşler sayfası](media/overview-iot-central-tour/jobs.png)

İşler sayfası cihazlarınızda toplu cihaz yönetimi işlemlerini çalıştırmanızı sağlar. Oluşturucu, cihaz özelliklerini, ayarları ve komutları güncelleştirmek için bu sayfayı kullanır. Daha fazla bilgi edinmek için [İş çalıştırma](howto-run-a-job.md) makalesine bakın.

## <a name="device-templates"></a>Cihaz şablonları

![Cihaz şablonları sayfası](media/overview-iot-central-tour/templates.png)

Cihaz şablonları sayfası, bir oluşturucunun uygulamadaki cihaz şablonlarını oluşturduğu ve yönettiği yerdir. Cihaz şablonu, şöyle bir cihaz özelliği belirtir:

- Telemetri, durum ve olay ölçümleri.
- Ayarlar ve Özellikler.
- Komut.
- Olayları veya telemetri değerlerini temel alan kurallar.

Daha fazla bilgi almak için [Azure IoT Central uygulamanızda yeni bir cihaz türü tanımlama](tutorial-define-device-type.md) öğreticisine bakın.

## <a name="continuous-data-export"></a>Verileri sürekli dışarı aktarma

![Sürekli veri dışa aktarma sayfası](media/overview-iot-central-tour/export.png)

Sürekli veri dışa aktarma sayfası, bir yöneticinin uygulama aracılığıyla telemetri gibi verilerin nasıl akışının tanımlanabileceğini tanımlar. Diğer hizmetler, dışarıya aktarılmış verileri saklayabilir veya analiz için kullanabilir. Daha fazla bilgi edinmek için bkz. [Azure 'da verilerinizi dışarı aktarma IoT Central](howto-export-data-blob-storage.md) makalesi.

## <a name="administration"></a>Yönetim

![Yönetim sayfası](media/overview-iot-central-tour/administration.png)

Yönetim sayfası, yönetici tarafından uygulamadaki kullanıcıları ve rolleri tanımlama ve Kullanıcı arabirimini özelleştirme gibi kullanılan araçların bağlantılarını içerir. Daha fazla bilgi almak için [Azure IoT Central uygulamanızı yönetme](howto-administer.md) makalesine bakın.

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central’a genel bir bakış elde ettiğinize ve kullanıcı arabiriminin düzenini tanıdığınıza göre önerilen sıradaki adım, [Azure IoT Central uygulaması oluşturma](quick-deploy-iot-central.md) hızlı başlangıcının tamamlanmasıdır.
