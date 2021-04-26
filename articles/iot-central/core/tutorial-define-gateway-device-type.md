---
title: Öğretici-Azure IoT Central yeni bir ağ geçidi cihaz türü tanımlama | Microsoft Docs
description: Bu öğreticide, Azure IoT Central uygulamanızda yeni bir IoT Ağ Geçidi cihaz türünün nasıl tanımlanacağı, bir Oluşturucu olarak gösterilir.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: cdace22072e4122150c878d43b99765cd6fd59e1
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490786"
---
# <a name="tutorial---define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Öğretici-Azure IoT Central uygulamanızda yeni bir IoT Ağ Geçidi cihaz türü tanımlama

*Bu makale, çözüm oluşturucular ve cihaz geliştiricileri için geçerlidir.*

Bu öğreticide, bir çözüm Oluşturucu olarak, IoT Central uygulamanızda bir ağ geçidi cihazı tanımlamak için bir ağ geçidi cihaz şablonunun nasıl kullanılacağı gösterilmektedir. Daha sonra, ağ geçidi cihazı aracılığıyla IoT Central uygulamanıza bağlanan birkaç aşağı akış aygıtını yapılandırırsınız. 

Bu öğreticide, **akıllı bir bina** ağ geçidi cihaz şablonu oluşturacaksınız. **Akıllı bir bina** ağ geçidi cihazının diğer aşağı akış cihazlarıyla ilişkileri vardır.

![Ağ geçidi cihazı ve aşağı akış cihazları arasındaki ilişkinin diyagramı](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Aşağı akış cihazların IoT Central uygulamanızla iletişim kurmasını olanaklı kılın yanı sıra, bir ağ geçidi cihazı da şunları yapabilir:

* Sıcaklık gibi kendi telemetrisini gönderin.
* Bir operatör tarafından yapılan yazılabilir Özellik güncelleştirmelerine yanıt verir. Örneğin, bir işleç telemetri gönderme aralığını değiştiriyor.
* Cihazı yeniden başlatma gibi komutları yanıtlayın.

> [!div class="checklist"]
> Aşağı akış cihaz şablonları oluşturma bir ağ geçidi cihaz şablonu oluşturma cihaz şablonunu yayımlama sanal cihazları oluşturma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için [bir Azure IoT Central uygulaması oluşturmanız](./quick-deploy-iot-central.md)gerekir.

## <a name="create-downstream-device-templates"></a>Aşağı akış cihaz şablonları oluşturma

Bu öğreticide, bir **S1 algılayıcısı** cihazının cihaz şablonları ve sanal aşağı akış cihazları oluşturmak Için bir **RS40 issörlü** cihaz kullanılır.

**S1 algılayıcısı** cihazına yönelik bir cihaz şablonu oluşturmak için:

1. Sol bölmede **cihaz şablonları**' nı seçin. Sonra **+** şablonu eklemeye başlamak için seçin.

1. **S1 algılayıcı** cihazının kutucuğunu görene kadar aşağı kaydırın. Kutucuğu seçin ve ardından **İleri: Özelleştir**' i seçin.

1. **İnceleme** sayfasında, cihaz şablonunu uygulamanıza eklemek için **Oluştur** ' u seçin. 

***RS40 doluluk algılayıcısı** cihazı için bir cihaz şablonu oluşturmak için:

1. Sol bölmede **cihaz şablonları**' nı seçin. Sonra **+** şablonu eklemeye başlamak için seçin.

1. ***RS40 doluma algılayıcısı** cihazının kutucuğunu görene kadar aşağı kaydırın. Kutucuğu seçin ve ardından **İleri: Özelleştir**' i seçin.

1. **İnceleme** sayfasında, cihaz şablonunu uygulamanıza eklemek için **Oluştur** ' u seçin. 

Şu anda iki aşağı akış cihaz türü için cihaz şablonlarına sahipsiniz:

![Aşağı akış cihazları için cihaz şablonları](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Ağ geçidi cihaz şablonu oluşturma

Bu öğreticide, sıfırdan bir ağ geçidi cihazı için bir cihaz şablonu oluşturacaksınız. Bu şablonu daha sonra uygulamanızda bir sanal ağ geçidi cihazı oluşturmak için kullanırsınız.

Uygulamanıza yeni bir ağ geçidi cihaz şablonu eklemek için:

1. Sol bölmede **cihaz şablonları**' nı seçin. Sonra **+** şablonu eklemeye başlamak için seçin.

1. **Şablon türü seç** sayfasında **IoT cihaz** kutucuğunu seçin ve ardından **İleri: Özelleştir**' i seçin.

1. **Cihazı Özelleştir** sayfasında, **ağ geçidi cihazı** onay kutusunu seçin.

1. **İnceleme** sayfasında **Oluştur**' u seçin. 

1. Şablon adı olarak **akıllı bina ağ geçidi cihazı** girin.

1. **Yetenek modeli oluştur** sayfasında, **özel** kutucuğu seçin.

1. **+** Arabirim eklemek için seçin.  **Cihaz bilgileri** standart arabirimini seçin.

### <a name="add-relationships"></a>İlişki Ekle

Daha sonra, aşağı akış cihaz şablonlarının şablonlarına ilişkiler eklersiniz:

1. **Akıllı bina ağ geçidi cihaz** şablonunda **ilişkiler**' i seçin.

1. **+ Ilişki Ekle**' yi seçin. Görünen ad olarak **ortam algılayıcısı** ' nı girin ve hedef olarak **S1 algılayıcısı** ' nı seçin.

1. **+ Ilişki Ekle** ' yi seçin. Görünen ad olarak **sahiplik algılayıcısı** ' nı girin ve hedef olarak **RS40 sahiplik algılayıcısı** ' nı seçin.

1. **Kaydet**’i seçin.

![İlişkileri gösteren akıllı derleme ağ geçidi cihaz şablonu](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Bulut özelliklerini ekleme

Bir ağ geçidi cihaz şablonu, bulut özellikleri içerebilir. Bulut özellikleri yalnızca IoT Central uygulamasında bulunur ve bir cihazdan hiçbir şekilde gönderilmez veya buradan alınmaz.

**Akıllı bina ağ geçidi cihaz** şablonuna bulut özellikleri eklemek için.

1. **Akıllı bina ağ geçidi cihaz** şablonunda, **bulut özellikleri**' ni seçin.

1.  Ağ Geçidi cihaz şablonunuza iki bulut özelliği eklemek için aşağıdaki tablodaki bilgileri kullanın.

    | Görünen ad      | Anlam türü | Şema |
    | ----------------- | ------------- | ------ |
    | Son Hizmet Tarihi | Yok          | Tarih   |
    | Müşteri Adı     | Yok          | Dize |

2. **Kaydet**’i seçin.

### <a name="create-views"></a>Görünümler oluşturma

Bir Oluşturucu olarak, uygulamayı bir işlecine çevresel algılayıcı cihazı hakkındaki ilgili bilgileri görüntüleyecek şekilde özelleştirebilirsiniz. Özelleştirmeleriniz, uygulamaya bağlı çevresel algılayıcı cihazlarını yönetmek için işlecini etkinleştirir. Cihazlarla etkileşim kurmak için kullanılacak bir operatör için iki tür görünüm oluşturabilirsiniz:

* Cihaz ve bulut özelliklerini görüntüleme ve düzenleme için formlar.
* Cihazları görselleştirmek için panolar.

**Akıllı bina ağ geçidi cihaz** şablonu için varsayılan görünümleri oluşturmak için:

1. **Akıllı bina ağ geçidi cihaz** şablonunda, **Görünümler**' i seçin.

1. **Varsayılan görünümleri oluştur** kutucuğunu seçin ve tüm seçeneklerin seçili olduğundan emin olun.

1. **Varsayılan pano Görünüm Oluştur ' u** seçin.

## <a name="publish-the-device-template"></a>Cihaz şablonunu yayımlama

Bir sanal ağ geçidi cihazı oluşturmadan veya gerçek bir ağ geçidi cihazını bağlayabilmeniz için önce cihaz şablonunuzu yayımlamanız gerekir.

Ağ Geçidi cihaz şablonunu yayımlamak için:

1. **Cihaz şablonları** sayfasından **akıllı bina ağ geçidi cihaz** şablonunu seçin.

2. **Yayımla**’yı seçin.

3. **Cihaz şablonu Yayımla** Iletişim kutusunda **Yayımla**' yı seçin.

Bir cihaz şablonu yayımlandıktan sonra **cihazlar** sayfasında ve işlecine görünür. Yayımlanmış bir cihaz şablonunda, yeni bir sürüm oluşturmadan bir cihaz modelini düzenleyemezsiniz. Ancak, yayımlanmış bir cihaz şablonunda bulut özellikleri, özelleştirmeler ve görünümlerde güncelleştirmeler yapabilirsiniz. Bu güncelleştirmeler yeni bir sürümün oluşturulmasına neden olmaz. Herhangi bir değişiklik yaptıktan sonra, bu değişiklikleri işletmenizin dışına göndermek için **Yayımla**  ' yı seçin.

## <a name="create-the-simulated-devices"></a>Sanal cihazları oluşturma

Bu öğretici, sanal aşağı akış cihazlarını ve sanal ağ geçidi cihazını kullanır.

Sanal ağ geçidi cihazı oluşturmak için:

1. **Cihazlar** sayfasında, cihaz şablonları listesinde **akıllı derleme ağ geçidi cihazı** ' nı seçin.

1. **+** Yeni bir cihaz eklemeye başlamak için seçin.

1. Oluşturulan **CIHAZ kimliğini** ve **cihaz adını** saklayın. **Benzetimli** anahtarın **Açık** olduğundan emin olun. **Oluştur**’u seçin.

Sanal bir aşağı akış cihazları oluşturmak için:

1. **Cihazlar** sayfasında, cihaz şablonları listesinden **RS40 doluluk algılayıcısı** ' nı seçin.

1. **+** Yeni bir cihaz eklemeye başlamak için seçin.

1. Oluşturulan **CIHAZ kimliğini** ve **cihaz adını** saklayın. **Benzetimli** anahtarın **Açık** olduğundan emin olun. **Oluştur**’u seçin.

1. **Cihazlar** sayfasında, cihaz şablonları listesinden **S1 algılayıcısı** ' nı seçin.

1. **+** Yeni bir cihaz eklemeye başlamak için seçin.

1. Oluşturulan **CIHAZ kimliğini** ve **cihaz adını** saklayın. **Benzetimli** anahtarın **Açık** olduğundan emin olun. **Oluştur**’u seçin.

![Uygulamanızdaki sanal cihazlar](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Bir ağ geçidi cihazına aşağı akış cihaz ilişkileri ekleme

Artık uygulamanızda sanal cihazlara sahip olduğunuza göre, aşağı akış cihazları ve ağ geçidi cihazı arasında ilişkiler oluşturabilirsiniz:

1. **Cihazlar** sayfasında, cihaz şablonları listesinden **S1 algılayıcısı** ' nı seçin ve ardından sanal **S1 algılayıcısı** cihazınızı seçin.

1. **Ağ geçidine Bağlan**' ı seçin.

1. **Ağ geçidine Bağlan** iletişim kutusunda, **akıllı bina ağ geçidi cihaz** şablonunu seçin ve daha önce oluşturduğunuz sanal örneği seçin.

1. **Birleştir**' i seçin.

1. **Cihazlar** sayfasında, cihaz şablonları listesinden **RS40 doluluk algılayıcısı** ' nı seçin ve ardından sanal **RS40 sahiplik algılayıcısı** cihazınızı seçin.

1. **Ağ geçidine Bağlan**' ı seçin.

1. **Ağ geçidine Bağlan** iletişim kutusunda, **akıllı bina ağ geçidi cihaz** şablonunu seçin ve daha önce oluşturduğunuz sanal örneği seçin.

1. **Birleştir**' i seçin.

Hem sanal aşağı akış cihazlarınız hem de sanal ağ geçidi cihazınıza bağlı. Ağ Geçidi cihazınız için **aşağı akış cihazları** görünümüne gittiğinizde ilgili aşağı akış cihazlarını görebilirsiniz:

![Aşağı akış cihazları görünümü](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Bir ağ geçidi cihaz şablonu ve ağ geçidi cihaz örneği seçin ve **Birleştir**' i seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

* Cihaz şablonu olarak yeni bir IoT Ağ Geçidi oluşturun.
* Bulut özellikleri oluşturun.
* Özelleştirmeler oluşturun.
* Cihaz telemetrisi için görselleştirme tanımlayın.
* İlişkiler ekleyin.
* Cihaz şablonunuzu yayımlayın.

Daha sonra, bir cihaz geliştiricisi olarak şunları yapmayı öğrenebilirsiniz:

> [!div class="nextstepaction"]
> [Azure IoT Central uygulamanıza Azure IoT Edge bir cihaz ekleme](tutorial-add-edge-as-leaf-device.md)
