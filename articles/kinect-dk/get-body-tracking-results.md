---
title: Azure Kinect gövde izleme sonuçlarını al
description: Azure Kinect gövde Izleme SDK 'sını kullanarak gövde izleme sonuçlarının nasıl alınacağını öğrenin.
author: qm13
ms.prod: kinect-dk
ms.author: quentinm
ms.reviewer: yijwan
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, algılayıcı, SDK, gövde, izleme, birleşme
ms.openlocfilehash: 1b62022242144d5db51455a32ac04b67c3e5dd7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277712"
---
# <a name="get-body-tracking-results"></a>Gövde izleme sonuçlarını alma

Gövde Izleme SDK 'Sı, Azure Kinect DK yakalamalarını işlemek için bir gövde izleyici nesnesi kullanır ve gövde izleme sonuçları üretir. Ayrıca izleyici, işleme kuyrukları ve çıkış sırası genel durumunu da korur. Gövde izleyicisini kullanmanın üç adımı vardır:

- İzleyici oluşturma
- Azure Kinect DK kullanarak derinliği ve IR görüntülerini yakalama
- Yakalamayı sıraya al ve sonuçları aç.

## <a name="create-a-tracker"></a>İzleyici oluşturma


Gövde izlemeyi kullanmanın ilk adımı bir izleyici oluşturmaktır ve algılayıcı ayarlaması [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) yapısında geçirilmesi gerekir. Algılayıcı ayarlaması, Azure Kinect algılayıcı SDK [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) işlevi kullanılarak sorgulanabilir.

```C
k4a_calibration_t sensor_calibration;
if (K4A_RESULT_SUCCEEDED != k4a_device_get_calibration(device, device_config.depth_mode, K4A_COLOR_RESOLUTION_OFF, &sensor_calibration))
{
    printf("Get depth camera calibration failed!\n");
    return 0;
}

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
if (K4A_RESULT_SUCCEEDED != k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker))
{
    printf("Body tracker initialization failed!\n");
    return 0;
}
```

## <a name="capture-depth-and-ir-images"></a>Derinliği ve IR görüntülerini yakala

[Görüntüleri al](retrieve-images.md) sayfasında Azure KINECT dk kullanılarak görüntü yakalama ele alınmıştır.

>[!NOTE]
> `K4A_DEPTH_MODE_NFOV_UNBINNED` veya `K4A_DEPTH_MODE_WFOV_2X2BINNED` modlar en iyi performans ve doğruluk için önerilir. `K4A_DEPTH_MODE_OFF`Veya `K4A_DEPTH_MODE_PASSIVE_IR` modlarını kullanmayın.

Desteklenen Azure Kinect DK modları, Azure Kinect DK [donanım belirtiminde](hardware-specification.md) ve [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) numaralandırmalar bölümünde açıklanmaktadır.

```C
// Capture a depth frame
switch (k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a capture\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a capture\n");
    goto Exit;
}
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>Yakalamayı sıraya al ve sonuçları aç

İzleyici, bir giriş kuyruğunu ve bir çıkış sırasını zaman uyumsuz olarak işlemek için Azure Kinect DK 'in daha verimli bir şekilde yakalamalarını sağlar. Giriş kuyruğuna yeni bir yakalama eklemek için [k4abt_tracker_enqueue_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e) işlevini kullanın. [K4abt_tracker_pop_result ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b) işlevi, çıkış sırasından bir sonuç pop 'u kullanın. Zaman aşımı değeri kullanımı uygulamaya bağımlıdır ve sıraya alma bekleme süresini denetler.

### <a name="real-time-processing"></a>Gerçek zamanlı işleme
Gerçek zamanlı sonuçlar gerektiren tek iş parçacıklı uygulamalar için bu stili kullanın ve bırakılan çerçevelere uyum sağlayabilir. `simple_3d_viewer` [GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) ' de bulunan örnek gerçek zamanlı işleme örneğidir.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, 0);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, 0);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

### <a name="synchronous-processing"></a>Zaman uyumlu işleme
Gerçek zamanlı sonuçlar gerektirmeyen veya bırakılan çerçeveleri barındıramayan uygulamalar için bu stili kullanın.

İşleme verimlilik sınırlı olabilir.

`simple_sample.exe` [GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) içinde bulunan örnek, zaman uyumlu işleme örneğidir.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Popping body tracking result failed!\n");
    break;
}
// Successfully popped the body tracking result. Start your processing
...

k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>[Gövde çerçevesindeki verilere erişme](access-data-body-frame.md)
