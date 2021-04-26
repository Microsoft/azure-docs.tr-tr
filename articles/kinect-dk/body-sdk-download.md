---
title: Azure Kinect gövde Izleme SDK indirmesi
description: Windows veya Linux 'ta Azure Kinect algılayıcı SDK 'sının her bir sürümünün nasıl indirileceği hakkında bilgi edinin.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: Azure, Kinect, SDK, indirme güncelleştirme, en son, kullanılabilir, yükleme, gövde, izleme
ms.openlocfilehash: 60018df56433785f3cb723dd54cc96a8e5289b67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654501"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Azure Kinect gövde Izleme SDK 'sını indirin

Bu belge, Azure Kinect gövde Izleme SDK 'sının her bir sürümünü yüklemek için bağlantılar sağlar.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Azure Kinect gövdesi Izleme SDK içeriği

- Azure Kinect DK kullanarak bir gövde izleme uygulaması derlemek için üst bilgiler ve kitaplıklar.
- Azure Kinect DK kullanan gövde izleme uygulamaları için yeniden dağıtılabilir DLL 'Ler gereklidir.
- Örnek gövde izleme uygulamaları.

## <a name="windows-download-links"></a>Windows indirme bağlantıları

Sürüm       | İndir
--------------|----------
1.1.0 | [defteri](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)

## <a name="linux-installation-instructions"></a>Linux yükleme yönergeleri

Şu anda desteklenen tek dağıtım Ubuntu 18,04 ve 20,04 ' dir. Diğer dağıtımlar için destek istemek için [Bu sayfaya](https://aka.ms/azurekinectfeedback)bakın.

İlk olarak, [buradaki](/windows-server/administration/linux-package-repository-for-microsoft-software)yönergeleri izleyerek [Microsoft 'un paket deposunu](https://packages.microsoft.com/)yapılandırmanız gerekecektir.

`libk4abt<major>.<minor>-dev`Paket, oluşturulacak üst bilgileri ve CMake dosyalarını içerir `libk4abt` .
`libk4abt<major>.<minor>`Paket, `libk4abt` örnek görüntüleyiciye ve ayrıca bağlı olan yürütülebilir dosyaları çalıştırmak için gereken paylaşılan nesneleri içerir.

Temel öğreticiler `libk4abt<major>.<minor>-dev` paketini gerektirir. Yüklemek için şunu çalıştırın

`sudo apt install libk4abt<major>.<minor>-dev`

Komut başarılı olursa SDK kullanıma hazırlanmaya devam edilir.

> [!NOTE]
> SDK 'yı yüklerken, yüklediğiniz yolu unutmayın. Örneğin, "C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0". Bu yoldaki makalelerde başvurulan örnekleri bulacaksınız.
> Gövde izleme örnekleri, Azure-Kinect-Samples deposundaki [Body-Tracking-Samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) klasöründe bulunur. Burada makalelerde başvurulan örnekleri bulacaksınız.

## <a name="change-log"></a>Değişiklik günlüğü

### <a name="v110"></a>v 1.1.0
* Özellik Poz tahmini modelinin DirectML (yalnızca Windows) ve TensorRT yürütmesi için destek ekleyin. Yeni yürütme ortamlarında SSS bölümüne bakın.
* Özellik `model_path` Struct öğesine ekleyin `k4abt_tracker_configuration_t` . Kullanıcıların, poz tahmini modeli için yol adını belirlemesine izin verir. `dnn_model_2_0_op11.onnx`Geçerli dizinde bulunan standart poz tahmin modelinin varsayılan değeri.
* Özellik `dnn_model_2_0_lite_op11.onnx` Lite poz tahmin modelini dahil edin. Bu model, yaklaşık %5 doğruluk azalması için 2x performans artışı.
* Özellik Doğrulanan örnekler Visual Studio 2019 ile derleyin ve bu sürümü kullanmak için güncelleştirmeler örnekleri.
* [Son değişiklik] CPU, CUDA 11,1, DirectML (yalnızca Windows) ve TensorRT 7.2.1 yürütme ortamları desteğiyle ONNX Runtime 1,6 ' a güncelleştirin. R455 veya daha iyi bir NVıDıA sürücü güncelleştirmesi gerektirir.
* [Son değişiklik] NuGet yüklemesi yok.
* [Hata düzeltildi] NVıDıA RTX 30xx serisi GPU [bağlantısı](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481) desteği ekleme
* [Hata düzeltildi] AMD ve Intel ile tümleşik GPU 'Lar (yalnızca Windows) [bağlantısı](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481) için destek ekleme
* [Hata düzeltildi] CUDA 11,1 [bağlantısına](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125) Güncelleştir
* [Hata düzeltildi] Algılayıcı SDK 1.4.1 [bağlantısına](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248) Güncelleştir

### <a name="v101"></a>v 1.0.1
* [Hata düzeltildi] Windows Build 19025 veya sonraki sürümlerde yolundan onnxruntime.dll yüklüyorsanız SDK 'nın çöktüğü sorunu çözme: [bağlantı](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v 1.0.0
* Özellik MSI yükleyicisine C# sarmalayıcı ekleyin.
* [Hata düzeltildi] Baş dönüşün doğru algılanamadığından sorunu çözme: [bağlantı](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Hata düzeltildi] CPU kullanımının Linux makinesinde %100 ' e varan bir sorunu giderme: [bağlantı](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Lerinizi Örnek depoya iki örnek ekleyin. Örnek 1 ' de, gövde izleme sonuçlarının derinlik alanından renk alanı [bağlantısına](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)nasıl dönüştürülebileceğiniz gösterilmektedir; Örnek 2 ' de zemin düzlemi [bağlantısının](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) nasıl algılanması gösterilmektedir

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Kinect DK genel bakış](about-azure-kinect-dk.md)

- [Azure Kinect DK’yi kurma](set-up-azure-kinect-dk.md)

- [Azure Kinect gövdesi izlemeyi ayarlama](body-sdk-setup.md)