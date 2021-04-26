---
title: Yeni IoT simülasyonu oluşturma - Azure | Microsoft Docs
description: Bu öğreticide, Cihaz Simülasyonu'nu kullanarak yeni bir simülasyon oluşturuyor ve çalıştırıyorsunuz.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: df658a95069f39026daab7afb14c5f1d0f47c5a5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713903"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>Öğretici: IoT cihaz simülasyonu oluşturma ve çalıştırma

Bu öğreticide, bir veya birden çok simülasyon cihazı kullanıp yeni bir IoT simülasyonu oluşturmak ve çalıştırmak için Cihaz Simülasyonu'nu kullanıyorsunuz.

Bu öğreticide şunları yaptınız:

>[!div class="checklist"]
> * Tüm etkin ve geçmiş simülasyonlarını görüntüleme
> * Yeni simülasyon oluşturma ve çalıştırma
> * Simülasyonun ölçümlerini görüntüleme
> * Simülasyonu durdurma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi izlemek için Azure aboneliğinizde Cihaz Simülasyonu'nun dağıtılmış örneğine sahip olmanız gerekir.

Henüz cihaz benzetimi dağıtmadıysanız, GitHub 'da [cihaz benzetimi dağıtımı](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) ' na bakın.

## <a name="view-simulations"></a>Simülasyonları görüntüleme

Menü çubuğunda **Simülasyonlar**'ı seçin. **Simülasyonlar** sayfasında tüm kullanılabilir simülasyonlar hakkındaki bilgiler görüntüler. Bu sayfada, şu anda çalıştırılmakta olan ve daha önce çalıştırılmış simülasyonları görebilirsiniz. Önceki bir simülasyonu yeniden çalıştırmak için, simülasyon kutucuğuna tıklayarak simülasyon ayrıntılarını açın:

![Simülasyonlar](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>Simülasyon oluşturma

Simülasyon oluşturmak için, sağ üst köşedeki **+ Yeni Simülasyon**'a tıklayın.

Simülasyonlar bir veya birden çok cihaz modelinden oluşur. Cihaz modeli, simülasyon cihazının davranışını, telemetrisini ve ileti biçimini tanımlar.

Cihaz modeli eklemek için **+ Cihaz türü ekle**'ye tıklayın ve listeden cihaz modelini seçin. Simülasyonunuzun birden çok cihaz modeli olabilir. Her cihaz modelinin farklı cihaz sayısı ve ileti frekansı olabilir.

Yeni simülasyon formu tamamlandığında, **Simülasyonu Başlat**'a tıklayarak simülasyonu başlatın.

Simülasyon cihazlarının sayısına bağlı olarak, simülasyonunuzun yapılandırılması ve başlatılması birkaç dakika sürebilir:

![Yeni simülasyon](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>Simülasyonu durdurma

**Simülasyonu Başlat**'a tıkladığınızda simülasyon ayrıntıları sayfasını görürsünüz. Bu ayrıntılar sayfası IoT Hub'dan canlı simülasyon istatistiklerini ve ölçümlerini gösterir. **Simülasyonlar** sayfasında simülasyonun kutucuğuna tıklayarak da bu ayrıntılar sayfasına gidebilirsiniz.

Simülasyonu durdurmak için, sağ üst kısımdaki eylem çubuğunda **Simülasyonu Durdur**'a tıklayın.

![Simülasyonu durdurma](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, simülasyon oluşturmayı, çalıştırmayı ve durdurmayı öğrendiniz. Ayrıca simülasyon ayrıntılarını görüntülemeyi de öğrendiniz. Simülasyonları çalıştırma hakkında daha fazla bilgi edinmek için sonraki öğreticiye geçin:

> [!div class="nextstepaction"]
> [Özel simülasyon cihazı oluşturma](iot-accelerators-device-simulation-create-custom-device.md)
