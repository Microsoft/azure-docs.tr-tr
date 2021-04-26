---
title: Azure Kinect DK Windows karşılaştırması
description: Windows V2 için Azure Kinect DK ve Kinect arasındaki donanım ve yazılım farklılıkları
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Kinect, Windows, v2, Azure Kinect, karşılaştırma, SDK, farklar, donanım, yazılım
ms.openlocfilehash: 1791767e842043bbb0eda058cc3ea92be90a00fb
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078307"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Azure Kinect ve Kinect Windows V2 karşılaştırması

Azure Kinect DK donanım ve yazılım geliştirme setlerinin Windows V2 için Kinect farkı vardır. Windows V2 uygulamaları için mevcut herhangi bir Kinect, doğrudan Azure Kinect DK ile çalışmaz ve yeni SDK 'ya taşıma gerektirir.  

## <a name="hardware"></a>Donanım

Windows V2 için Azure Kinect geliştirme seti ve Kinect arasındaki üst düzey farklılıklar aşağıdaki tabloda listelenmiştir.

| Özellik | Tür | Azure Kinect DK | Windows V2 için Kinect |
| ------- | ---- | --------------- | --------------------- |
| **Ses** | Ayrıntılar  | 7 MIC dairesel dizi | 4 MIC doğrusal aşamalı dizi |
| **Hareket algılayıcısı** | Ayrıntılar | 3 eksenli ivme ölçer 3-eksen cayro | 3 eksenli ivme ölçer |
| **RGB Kamerası**    | Ayrıntılar | 3840 x 2160 px @30 fps | 1920 x 1080 px @30 fps |
| **Derinlik Kamerası**  | Yöntem   | Uçuş süresi | Uçuş süresi |
|                   | Çözüm | 640 x 576 px @30 fps | 512 x 424 px @ 30 fps |
|                   |            | 512 x 512 px @30 fps |                       |
|                   |            | 1024x1024 piksel @15 fps |                       |
| **Bağlantı** | Veriler | USB 3.1 Gen 1-C türü  | USB 3,1 Gen 1|
|  | Güç | Dış PSU veya USB-C | Dış PSU |
|  | Eşitleme | RGB & derinliği iç, dış cihazdan cihaza| Yalnızca RGB & derinliği iç |
| **Mekanik** | Boyutlar | 103 x 39 x 126 mm | 249 x 66 x 67 mm |
|  | Alıcısına | 440 g | 970 g |
| | Takarak | Bir 1/4-20 UNC. Dört adet iç vidalı noktası | Bir 1/4-20 UNC |

[Azure Kinect dk donanım](hardware-specification.md) belgesinde ek ayrıntılar bulun.

## <a name="sensor-access"></a>Algılayıcı erişimi

Aşağıdaki tabloda alt düzey cihaz algılayıcısı erişim yeteneği karşılaştırması sunulmaktadır.

| **İşlev**| **Azure Kinect** | **Windows için Kinect** | **Notlar** |
|---------|---------|------------|---------|
| **Derinliğini** | ✔️ | ✔️ |    | 
| **IR** | ✔️ | ✔️ |  |
| **Renk** | ✔️ | ✔️ | Renk biçimi farkları destekler, Azure Kinect DK şu kamera denetimlerini destekler: pozlama, beyaz dengesi, parlaklık, karşıtlık, doygunluk, netlik ve kazanç denetimi |
| **Ses** | ✔️ | ✔️ | Azure Kinect DK miklerine konuşma SDK 'Sı veya Windows Native API aracılığıyla erişilir |
| **IMU** | ✔️ |  | Azure Kinect DK tam 6 eksenli ıMU ve Windows için Kinect yalnızca 1 eksen sağlar |
| **Ayarlama verileri** | ✔️ | ✔️ | OpenCV uyumlu kamera modeli ayarlaması |
| **Derinlik-RGB iç eşitleme** | ✔️ | ✔️ |  |
| **Dış eşitleme**| ✔️|  | Azure Kinect DK, dış eşitleme için programlanabilir gecikmeye izin veriyor |
| **Birden çok istemcilerle erişimi paylaşma** | | ✔️ | Azure Kinect algılayıcı SDK 'Sı, cihaza erişmek için WinUSB/libUSB 'yi kullanır ve birden çok işlemle cihaz erişiminin paylaşılmasını sağlamak için uygulanan bir hizmete sahip değildir |
| **Akış kaydı/kayıttan yürütme aracı** | ✔️ | ✔️ | Azure Kinect DK, açık kaynaklı Matroska kapsayıcı tabanlı bir uygulama kullanır |

## <a name="features"></a>Özellikler

Azure Kinect SDK özelliği kümesi, aşağıda açıklandığı gibi Windows V2 için Kinect 'tan farklıdır:

| **Kinect v2 özelliği** | **Kinect v2 veri türü** | **Azure Kinect SDK/hizmeti** |
|--------|--------|------|
| Algılayıcı veri erişimi |DepthFrame| [Algılayıcı SDK-görüntüleri alma](retrieve-images.md) 
| |InfraredFrame | [Algılayıcı SDK-görüntüleri alma](retrieve-images.md) 
| | ColorFrame | [Algılayıcı SDK-görüntüleri alma](retrieve-images.md) | 
| | AudioBeamFrame |Şu anda desteklenmiyor 
| Gövde Izleme | BodyFrame | Gövde Izleme SDK 'Sı |
| | Bodyındexframe | Gövde Izleme SDK 'Sı  |
| Koordinat eşleme|Koordinatör Temapper| [Algılayıcı SDK-görüntü dönüşümleri](use-image-transformation.md) |
|Yüz Izleme | Çok yönlü çerçeve | [Bilişsel hizmetler: yüz](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    Konuşma tanıma    |    Yok                      |    [Bilişsel hizmetler: konuşma](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>Sonraki adımlar

[Windows Geliştirici sayfaları için Kinect](https://developer.microsoft.com/windows/kinect)
