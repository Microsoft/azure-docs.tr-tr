---
title: Sınırlar ve Kotalar IoT Tak ve Kullan önizlemesi | Microsoft Docs
description: IoT Tak ve Kullan Önizleme kullandığınızda uygulanan sınırları, kotaları ve azaltmayı anlayın.
author: miagdp
ms.author: miag
ms.date: 08/01/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 2df8a8820422a22b0512e24c4b052377cb0e61e0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879571"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT Tak ve Kullan önizleme sınırları, kotaları ve kısıtları

Bu makalede, genel önizlemede uygulanan IoT Tak ve Kullan özgü sınırlar, Kotalar ve azaltma açıklanmaktadır. Aynı zamanda geçerli [IoT Hub kotaları ve azaltma](../iot-hub/iot-hub-devguide-quotas-throttling.md) vardır.

## <a name="iot-hub"></a>IoT Hub

Genel önizleme için aşağıdaki sınırlar ve Kotalar bir IoT Hub 'ı için geçerlidir:

| Sınırlar, kısıtlamalar ve kısıtlar | Value | Notlar |
|-----|-----|-----|
| Hub başına kaydedilenebilir cihaz yeteneği modellerinin (DCMs) veya arabirimlerin sayısı | 1500 ||
| Cihaz başına kaydedilenebilir en fazla arabirim sayısı | 40 ||
| Cihaz başına kaydedilenebilir en fazla DCMs sayısı | 1\. ||
| En büyük arabirim/DCM dosyası boyutu | 512 karakter ||
| Bir arabirim adının en büyük boyutu | 256 karakter ||
| Bir özellik adının en büyük boyutu  | 64 bayt, 7 düzey Derinlik (ve ilk düzey için `$iotin`ayrılmıştır) | İzin verilen karakterler: a-z, A-Z, 0-9 (ilk karakter olarak değil) ve alt çizgi. |
| Bir özellik değerinin en büyük boyutu | 512 bayt ||
| Bir komut adının en büyük boyutu | 100 bayt ||
| Cihaz ikizi boyutu | [IoT Hub limitleriyle](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) aynı ||
| SKU genelinde çözüm API çağrıları (birimlerden bağımsız olarak) | 100 isteği/saniye ||

## <a name="model-repository"></a>Model deposu

Genel önizleme için aşağıdaki sınırlar ve Kotalar bir model deposu için geçerlidir:

| Sınırlar, kısıtlamalar ve kısıtlar| Value |
|-----|-----|
| Azure Active Directory kiracı başına şirket modeli depoları sayısı | 1\. |
| Model deposu başına yetkilendirme anahtarı sayısı | 10  |
| Şirket modeli deposu başına model sayısı (DCMs veya arabirimler)| 1500  |
| Azure Active Directory kiracı başına ortak model deposundaki model sayısı (DCMs veya arabirimler)| 1500  |
| Bir şirket modeli deposunda silinmekte olan DCMs veya arabirimlerin sayısı | saniye başına 10 sorgu (QPS)|
| Bir kiracı tarafından oluşturulan/güncellenen model depoları sayısı| 1 QPS |
| Bir model deposunda oluşturulan/güncellenen/silinen yetkilendirme anahtarlarının sayısı | 1 QPS|
| Bir şirket modeli deposunda oluşturulan DCMs sayısı | 10 QPS |
| Bir şirket modeli deposunda oluşturulan arabirimlerin sayısı | 10 QPS|
| Ortak model deposunda oluşturulan DCMs sayısı | 10 QPS|
| Ortak model deposunda oluşturulan arabirimlerin sayısı | 10 QPS|

## <a name="parser-library"></a>Ayrıştırıcı kitaplığı

Ayrıştırıcı kitaplığı, [dijital Ikizi tanım dili](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)için uygulanan limitleri izler.

## <a name="next-steps"></a>Sonraki adımlar

Önerilen bir sonraki adım, [ıot Tak ve kullan cihazıyla nasıl bağlanacağınızı ve bunlarla etkileşime gireceğini](./howto-develop-solution.md)öğrenirsiniz.
