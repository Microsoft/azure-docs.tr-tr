---
title: Azure Kinect ayarlama işlevlerini kullanma
description: Azure ınect DK için ayarlama işlevlerini nasıl kullanacağınızı öğrenin.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, algılayıcı, SDK, koordinat sistemi, ayarlama, işlevler, kamera, iç, extrinsic, proje, unproje, dönüşüm, RGB-d, nokta bulutu
ms.openlocfilehash: a501765222154bde4ed35e878a9d18042893fca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277662"
---
# <a name="use-azure-kinect-calibration-functions"></a>Azure Kinect ayarlama işlevlerini kullanma

Ayarlama işlevleri, Azure Kinect cihazında her algılayıcının koordinat sistemleri arasındaki noktaları dönüştürmeye olanak tanır. Tüm görüntülerin dönüştürülmesini gerektiren uygulamalar, [dönüştürme işlevlerinde](use-image-transformation.md)bulunan hızlandırılmış işlemlerden yararlanabilir.

## <a name="retrieve-calibration-data"></a>Ayarlama verilerini alma

Koordinat sistemi dönüştürmeleri gerçekleştirmek için cihaz ayarlama işleminin alınması gerekir. Ayarlama verileri [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) veri türünde depolanır. [K4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78)işlevi aracılığıyla cihazdan elde edilir. Ayarlama verileri yalnızca her cihaza özgü değildir, ayrıca kameraların işletim moduna de sahiptir. Bu nedenle [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) `depth_mode` `color_resolution` giriş olarak ve parametrelerini gerektirir.

### <a name="opencv-compatibility"></a>OpenCV uyumluluğu

Ayarlama parametreleri [OpenCV](https://opencv.org/)ile uyumludur. Ayrı kamera ayarlama parametreleri hakkında daha fazla bilgi için bkz. [OpenCV belgeleri](https://docs.opencv.org/3.2.0/d9/d0c/group__calib3d.html#gga7041b2a9c8f9f8ee93a2796981bc5546a204766e24f2e413e7a7c9f8b9e93f16c). Ayrıca, [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) türü Ile Ilgili OpenCV veri yapıları arasında DÖNÜŞTÜRMEYI gösteren SDK 'Nın [OpenCV uyumluluk örneğine](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility) bakın.

## <a name="coordinate-transformation-functions"></a>Dönüşüm Işlevlerini koordine et

Aşağıdaki şekilde, Azure Kinect 'in farklı koordinat sistemleri ve bunlar arasında dönüştürme işlevleri gösterilmektedir. Şekli basit tutmak için jroscope ve ivometer 3B koordinat sistemlerini atlıyoruz.

   ![Koordinat dönüşümü](./media/how-to-guides/coordinate-transformation.png)

Mercek deformasyonu üzerinde Açıklama: 2B koordinatlar her zaman SDK 'daki bozuk görüntüye başvurur. SDK 'nın [bozulma olmayan örneği](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort) görüntü bozulmayı gösterir. Genel olarak, 3B noktaları mercek deformasyonu tarafından hiçbir şekilde etkilenmeyecektir.

### <a name="convert-between-3d-coordinate-systems"></a>3B koordinat sistemleri arasında dönüştürme

[K4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) işlevi, kameranın extrinsic ayarlamasını kullanarak kaynak koordinat sisteminin 3B noktasını hedef koordinat sisteminin 3B noktasına dönüştürür. Kaynak ve hedef dört 3B koordinat sisteminin, yani renkli kameranın, derinlik kameranın, jroscope veya ivometer 'nin herhangi birine ayarlanabilir. Kaynak ve hedef aynıysa, değiştirilmemiş giriş 3B noktası çıkış olarak döndürülür.

### <a name="convert-between-2d-and-3d-coordinate-systems"></a>2B ve 3B koordinat sistemleri arasında dönüştürme

[K4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) işlevi, kaynak koordinat sisteminin 3B noktasını hedef kameranın 2B piksel koordinatına dönüştürür. Bu işlev genellikle proje işlevi olarak adlandırılır. Kaynak dört 3B koordinat sisteminin herhangi birine ayarlansa da hedef, derinlik veya renk kamerası olmalıdır. Kaynak ve hedef farklıysa, giriş 3B noktası, [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e)kullanılarak hedef kameranın 3B koordinat sistemine dönüştürülür. Hedef kamera koordinat sisteminde 3B noktası temsil edildiğinde, karşılık gelen 2B piksel koordinatları hedef kameranın içsel ayarı kullanılarak hesaplanır. Bir 3B nokta, hedef kameranın görünür alanından geliyorsa geçerli değer 0 olarak ayarlanır.

[K4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) işlevi, kaynak kameranın 2B piksel koordinatını hedef kamera koordinat sisteminin 3B noktasına dönüştürür. Kaynak renk veya derinlik kamerası olmalıdır. Hedef dört 3B koordinat sisteminin herhangi birine ayarlanabilir. 2B piksel koordinatına ek olarak, kaynak kameranın görüntüsündeki piksel Derinlik değeri (milimetre cinsinden) işleve giriş olarak gereklidir; renk Kamerası geometrisinde derinlik değerini Türetmenin bir yolu, [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)işlevini kullanmaktır. İşlevi, kaynak kameranın iç ayarlamasını kullanarak, belirtilen piksel koordinatı aracılığıyla, kaynak kameranın odak noktasından 3B Ray 'un öncü noktasını hesaplar. Derinlik değeri daha sonra bu ışın üzerindeki 3B noktanın tam konumunu bulmak için kullanılır. Bu işlem genellikle proje olmayan işlev olarak adlandırılır. Kaynak ve hedef kameralar farklıysa, işlev 3B noktayı [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e)ile hedefin koordinat sistemine dönüştürür. 2B piksel koordinatı kaynak kameranın görünür alanından geliyorsa geçerli değer 0 olarak ayarlanır.

### <a name="converting-between-2d-coordinate-systems"></a>2B koordinat sistemleri arasında dönüştürme

[K4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) işlevi, kaynak kameranın 2B piksel koordinatını hedef kameranın 2B piksel koordinatına dönüştürür. Kaynak ve hedef, renk veya derinlik kamerası olarak ayarlanmalıdır. İşlev, kaynak kamera görüntüsündeki piksel derinlik değerini (milimetre cinsinden) bir giriş olarak gerektirir, renk Kamerası geometrisinde derinlik değerini Türetmenin bir yolu [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)işlevini kullanmaktır. Kaynak kamera sisteminin 3B noktasına dönüştürmek için [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) çağırır. Daha sonra hedef Kamera görüntüsünün 2B piksel koordinatına dönüştürmek için [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) çağırır. [K4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880ded) veya [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) geçersiz bir sonuç döndürürse geçerli değer 0 olarak ayarlanır.

## <a name="related-samples"></a>İlgili örnekler

- [OpenCV uyumluluk örneği](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)
- [Deformasyon olmadan örnek](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)
- [Hızlı nokta bulutu örneği](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)

## <a name="next-steps"></a>Sonraki adımlar

Kamera ayarlamayı artık öğrenirsiniz, ayrıca
>[!div class="nextstepaction"]
>[Cihaz eşitlemesini yakalama](capture-device-synchronization.md)

Ayrıca şunları gözden geçirebilirsiniz

[Koordinat sistemleri](coordinate-systems.md)
