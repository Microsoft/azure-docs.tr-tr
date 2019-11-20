---
title: Azure IoT Central Azure IoT Edge bir cihaz ekleme | Microsoft Docs
description: Bir operatör olarak, Azure IoT Central Azure IoT Edge bir cihaz ekleyin
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: ae80a624ed1f85a1f59fea79b152a4bc31067ad1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893483"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>Öğretici: Azure IoT Central uygulamanıza Azure IoT Edge bir cihaz ekleme (Önizleme özellikleri)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu öğreticide, *Azure IOI Edge cihazını* Microsoft Azure IoT Central uygulamanıza nasıl ekleyeceğiniz ve yapılandırabileceğiniz gösterilmektedir. Bu öğreticide, Azure Marketi 'nden Azure IoT Edge etkinleştirilmiş bir Linux sanal makinesi seçtik.

Bu öğretici iki bölümden oluşur:

* İlk olarak, bir operatör olarak, bir Azure IoT Edge cihazının bulut ilk sağlamasını yapmayı öğrenirsiniz.
* Daha sonra, cihaz Azure IoT Edge cihazının ilk sağlamasını yapmayı öğreneceksiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yeni bir Azure IoT Edge cihaz ekleme
> * SAS anahtarı kullanarak sağlamaya yardımcı olmak için Azure IoT Edge cihazı yapılandırma
> * IoT Central panoları, modül durumunu görüntüleme
> * Azure IoT Edge cihazda çalışan bir modüle Komut gönder
> * Azure IoT Edge cihazda çalışan bir modülün özelliklerini ayarlama

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için bir Azure IoT Central uygulamanızın olması gerekir. [Azure IoT Central uygulaması oluşturmak](./quick-deploy-iot-central.md)için bu hızlı başlangıcı izleyin.

## <a name="enable-azure-iot-edge-enrollment-group"></a>Azure IoT Edge kayıt grubunu etkinleştir
Yönetim sayfasından Azure IoT Edge kayıt grubu için SAS anahtarlarını etkinleştirin.

![Cihaz şablonu-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>İlk Azure IoT Edge cihaz hazırlama bulutu   
Bu bölümde, **ortam algılayıcı şablonunu** kullanarak yeni bir Azure IoT Edge cihaz oluşturacak ve bir cihaz sağlayacaksınız. Sol gezinti bölmesinde cihazlar ' a tıklayın ve ortam algılayıcı şablonu ' na tıklayın. 

![Cihaz şablonu-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

**+ Yeni** ' ye tıklayın ve size uygun BIR cihaz kimliği ve adı girin. 

![Cihaz şablonu-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Cihaz **kayıtlı** moda gider.

![Cihaz şablonu-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>Azure IoT Edge özellikli bir Linux sanal makinesi dağıtma

>Note: herhangi bir makineyi veya cihazı kullanmayı seçebilirsiniz. İşletim sistemi: Linux veya Windows)

Bu öğreticide, Azure 'da oluşturabileceğiniz bir Azure IoT etkin Linux VM 'si seçtik. [Azure Market](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) 'e götürülecektir ve **Şimdi al** düğmesine tıklayabilirsiniz. 

![Azure Market](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

**Devam** ' a tıklayın

![Azure Market](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Azure portal alınacaktır. **Oluştur** düğmesine tıklayın

![Azure Market](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Abonelik ' ı seçin, yeni bir kaynak grubu oluşturun tercihen VM kullanılabilirliği için ABD Batı 2 ' yi seçin, Kullanıcı ve parola girin. Kullanıcıyı anımsa, sonraki adımlar için parola gerekli olacaktır. **Gözden geçir + oluştur** 'a tıklayın

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Doğrulandıktan sonra **Oluştur** ' a tıklayın.

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Kaynakların oluşturulması birkaç dakika sürer. **Kaynağa** git ' e tıklayın

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>VM 'yi Azure IoT Edge cihaz olarak sağlama 

Sol gezinti bölmesinde destek + sorun giderme ' nin altında, Seri konsol ' ye tıklayın.

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Aşağıda olduğu gibi bir ekran göreceksiniz

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

ENTER tuşuna basın ve istendiğinde Kullanıcı adı ve parola girin ve ENTER 'a basın. 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Bir komutu yönetici/kök olarak çalıştırmak için şu komutu çalıştırın: **sudo su –**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Azure IoT Edge çalışma zamanı sürümünü denetleyin. Geçerli GA sürümü 1.0.8

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

VIM Düzenleyicisi 'ni veya tercihinize sahipseniz nano 'yi kullanın. 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Azure IoT Edge config. YAML dosyasını Düzenle

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

YAML dosyasının bağlantı dizesi kısmını aşağı kaydırın ve not edin. 

**Eklenmeden**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Sonra** (ESC tuşuna basın ve küçük harf a 'ya basın, düzenlenmek için)

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

YAML dosyasının simetrik anahtar bölümünün açıklamasını kaldırın. 

**Eklenmeden**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Sonra**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

IoT Central gidin ve cihaz bağlantısı ![Azure IoT Edge cihazın kapsam KIMLIĞINI, cihaz KIMLIĞINI ve simetrik anahtarını alın](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Linux kutusuna gidin ve kapsam KIMLIĞINI, kayıt KIMLIĞINI cihaz KIMLIĞI ve simetrik anahtarla değiştirin

**ESC** tuşuna basın ve şunu yazın **: WQ!** ve yaptığınız değişiklikleri kaydetmek için **ENTER** tuşuna basın

Değişikliklerinizi işlemek için Azure IoT Edge yeniden başlatın ve **ENTER** 'a basın

![Cihaz bağlantısı](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Tür: **ıotedge listesi**, birkaç dakika sürer ve dağıtılan üç modül görüntülenir

![Cihaz bağlantısı](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>Cihaz Gezgini IoT Central 

IoT Central cihazınız, sağlanan duruma geçer

![Cihaz bağlantısı](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

Modüller sekmesi IoT Central üzerindeki cihaz ve modülün durumunu gösterir 

![Cihaz bağlantısı](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Bulut özellikleri bir formda görünür (önceki adımlarda oluşturduğunuz cihaz şablonundan). Değerleri girin ve **Kaydet**' e tıklayın. 

![Cihaz bağlantısı](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Pano kutucuğu

![Cihaz bağlantısı](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

* Yeni bir Azure IoT Edge cihaz ekleme
* SAS anahtarı kullanarak sağlamaya yardımcı olmak için Azure IoT Edge cihazı yapılandırma
* IoT Central panoları, modül durumunu görüntüleme
* Azure IoT Edge cihazda çalışan bir modüle Komut gönder
* Azure IoT Edge cihazda çalışan bir modülün özelliklerini ayarlama

## <a name="next-steps"></a>Sonraki adımlar

Artık IoT Central Azure IoT Edge cihazları yönetme ile nasıl çalışabileceğinizi öğrendiğinize göre, aşağıda önerilen sonraki adım verilmiştir:

<!-- Next how-tos in the sequence -->

Saydam bir ağ geçidi yapılandırma, bu öğreticiyi izleyin

> [!div class="nextstepaction"]
> [Saydam ağ geçidini yapılandırma](../../iot-edge/how-to-create-transparent-gateway.md)
