---
title: Azure IoT Central bir RuuviTag bağlantısı | Microsoft Docs
description: IoT Central uygulamanıza bir RuuviTag ortam algılayıcısı bağlamayı öğrenin.
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 10/19/2019
ms.openlocfilehash: a068ea3f51e4faa6497c7b1a93192a924753c7c9
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73895104"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Azure IoT Central uygulamanıza bir RuuviTag algılayıcısı bağlama

Bu makalede, bir çözüm Oluşturucusu olarak, bir RuuviTag algılayıcısı Microsoft Azure IoT Central uygulamanıza nasıl bağlayabileceğinizi açıklanmaktadır.

Ruuvi etiketi nedir?

RuuviTag, iş müşterilerinin, geliştiricilerin, mekanizmalarının, öğrencilerin ve barındırmacıların ihtiyaçlarını karşılamak için tasarlanan, gelişmiş bir açık kaynaklı algılayıcı işaret platformudur. Cihaz, kendi kutudan çıkar almaz ve ihtiyacınız olan yere dağıtmanız için hazır hale gelir. Bu, bir ortam algılayıcısı ve yerleşik hızlandırma içeren bir Bluetooth LE işaret ediyor.

RuuviTag, BLE üzerinden iletişim kurar (Bluetooth düşük enerji) ve Azure IoT Central ile iletişim kurmak için bir ağ geçidi cihazı gerektirir. IoT Central bağlantı kurmak üzere bir RuuviTag 'i etkinleştirmek için Rigado Cascade 500 gibi bir ağ geçidi cihazına sahip olduğunuzdan emin olun.

Bir Rigado basamaklı 500 ağ geçidi cihazı kurmak istiyorsanız lütfen [buradaki yönergeleri](./howto-connect-rigado-cascade-500.md) izleyin.

## <a name="prerequisites"></a>Ön koşullar

RuuviTag sensörlerinden bağlantı kurmak için aşağıdaki kaynaklara ihtiyacınız vardır:

* RuuviTag algılayıcısı. Daha fazla bilgi için lütfen [Ruuvitag](https://ruuvi.com/)adresini ziyaret edin.
* Bir Rigado Cascade 500 cihazı veya başka bir BLE ağ geçidi. Daha fazla bilgi için lütfen [Rigado](https://www.rigado.com/)adresini ziyaret edin.
* Önizleme uygulaması şablonlarından birinden oluşturulmuş bir Azure IoT Central uygulaması. Daha fazla bilgi için [Yeni uygulama oluşturma](./quick-deploy-iot-central.md)bölümüne bakın.

## <a name="add-a-ruuvitag-device-template"></a>RuuviTag cihaz şablonu ekleme

Azure IoT Central uygulama örneğinize bir RuuviTag algılayıcısı eklemek için, uygulamanız içinde karşılık gelen bir cihaz şablonunu yapılandırmanız gerekir.

Bir RuuviTag cihaz şablonu eklemek için:

1. Sol bölmedeki ***cihaz şablonları*** sekmesine giderek **+ Yeni**' yi seçin ![yeni cihaz şablonu oluştur ' a tıklayın](./media/howto-connect-ruuvi/devicetemplate-new.png) sayfa size ***özel şablon oluşturma*** veya ***önceden yapılandırılmış bir cihaz şablonu kullanma*** seçeneği sunar.
1. Aşağıda gösterildiği gibi önceden yapılandırılmış cihaz şablonları listesinden RuuviTag cihaz şablonunu seçin: ![RuuviTag cihaz şablonunu seçin](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Sonraki adıma devam etmek için Ileri 'yi seçin ***: Özelleştir*** .
1. Bir sonraki ekranda, C500 cihaz şablonunu IoT Central uygulamanıza eklemek için ***Oluştur*** ' u seçin.

## <a name="connect-a-ruuvitag-sensor"></a>RuuviTag algılayıcısı bağlama

Daha önce belirtildiği gibi, RuuviTag ' i IoT Central uygulamanızla bağlamak için bir ağ geçidi cihazı ayarlamanız gerekir. Aşağıdaki adımlarda, bir Rigado basamaklı 500 ağ geçidi cihazı ayarlamış olduğunuz varsayılmaktadır.  

1. Rigado Cascade 500 cihazınızda güç yapın ve ağ bağlantınıza bağlayın (Ethernet veya kablosuz aracılığıyla)
1. RuuviTag 'in kapağını kapatın ve pille bağlantıyı güvenli hale getirmek için plastik sekmesini çekin.
1. IoT Central uygulamanızda zaten yapılandırılmış bir Rigado basamaklı 500 ağ geçidine RuuviTag Close koyun.
1. Yalnızca birkaç saniye içinde, RuuviTag 'niz IoT Central içindeki cihazlar listenizde görünmelidir.  
    ![RuuviTag cihaz listesi](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Artık IoT Central uygulamanızda bu RuuviTag ' i kullanabilirsiniz.  

## <a name="create-a-simulated-ruuvitag"></a>Benzetimli RuuviTag oluştur

Fiziksel bir RuuviTag cihazınız yoksa, Azure IoT Central uygulamanızda test için kullanılacak bir benzetimli Ruuvıtag algılayıcısı oluşturabilirsiniz.

Benzetimli RuuviTag oluşturmak için:

1. **Cihaz > RuuviTag**' ı seçin.
1. **+ Yeni**seçeneğini belirleyin.
1. Benzersiz bir **CIHAZ kimliği** ve kolay bir **Cihaz adı**belirtin.  
1. **Benzetimli** ayarı etkinleştirin.
1. **Oluştur**'u seçin.  

## <a name="next-steps"></a>Sonraki Adımlar

Azure IoT Central uygulamanıza bir RuuviTag bağlamayı öğrendiğinize göre, önerilen sonraki adım bir uçtan uca çözüm oluşturmak için [IoT Central uygulamanızı nasıl özelleştireceğinizi](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) öğrenmektedir.
