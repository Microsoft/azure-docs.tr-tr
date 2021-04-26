---
title: Azure IoT Hub uyumluluğu için cihaz güncelleştirmesini anlama | Microsoft Docs
description: Cihaz güncelleştirme uyumluluğunu ölçmek IoT Hub için cihaz güncelleştirme 'nin nasıl yapıldığını anlayın.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ac6094efde8a32b1fcc04c55bbc537afeb4166f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101664002"
---
# <a name="device-update-compliance"></a>Cihaz Güncelleştirme Uyumluluğu

IoT Hub cihaz güncelleştirmesinde, uyumluluk, en yüksek sürüm ile uyumlu güncelleştirmeyi kaç cihazın yükletiğinin ölçüğünü ölçer. Bir cihaz, kendisi için uyumlu olan en yüksek sürüm kullanılabilir güncelleştirmeyi yüklemiştir uyumlu olur. 

Örneğin, aşağıdaki güncelleştirmelerle bir cihaz güncelleştirmesi örneği düşünün:

|Güncelleştirme adı|Güncelleştirme sürümü|Uyumlu cihaz modeli|
|-----------|--------------|-----------------------|
|Güncelleştirme 1    |1.0    |Model1|
|Update2    |1.0    |Model2|
|Update3    |2.0    |Model1|

Aşağıdaki dağıtımlar oluşturulalım:

|Dağıtım adı    |Güncelleştirme adı    |Hedeflenen Grup|
|-----------|--------------|-------------------|
|Deployment1    |Güncelleştirme 1    |Group1|
|Deployment2    |Update2    |Grup2|
|Deployment3    |Update3    |Grup3|

Şimdi, grup üyelikleri ve yüklü sürümleri ile aşağıdaki cihazları göz önünde bulundurun:

|DeviceId   |Cihaz modeli   |Güncelleştirme sürümü yüklendi|Grup |Uyumluluk|
|-----------|--------------|-----------------------|-----|---------|
|Device1    |Model1 |1.0    |Group1 |Yeni güncelleştirmeler var</span>|
|Device2    |Model1 |2.0    |Grup3 |En son güncelleştirmede|
|Device3    |Model2 |1.0    |Grup2 |En son güncelleştirmede|
|Device4    |Model1 |1.0    |Grup3 |Güncelleştirme devam ediyor|

Device1 ve Device4 uyumlu değildir çünkü, daha yüksek bir sürüm güncelleştirmesi 1,0 olan Update3, cihaz güncelleştirme örneğindeki modeliyle uyumlu olsa bile. Device2 ve Device3, modelleriyle uyumlu en yüksek sürüm güncelleştirmelerine sahip olduklarından uyumludur.

Uyumluluk, bir güncelleştirmenin bir cihazın grubuna dağıtılıp dağıtılmadığını düşünmez; Cihaz güncelleştirmesine yayımlanan tüm güncelleştirmelere bakar. Bu nedenle, yukarıdaki örnekte, Device1 tarafından dağıtılan güncelleştirmeyi yüklemiş olmasına rağmen uyumlu değil olarak kabul edilir. Device1, Update3 'yi başarıyla yükleene kadar uyumlu değil olarak kabul edilmesine devam edecektir. Uyumluluk durumu, yeni dağıtımların gerekli olup olmadığını belirlemenize yardımcı olabilir. 

Yukarıda gösterildiği gibi, IoT Hub için cihaz güncelleştirmesinde üç uyumluluk durumu vardır:

*   **En son güncelleştirmede** – cihaz, cihaz güncelleştirmesine yayımlanmış en yüksek sürüm uyumluluğu güncelleştirmesini yükledi.
*   **Güncelleştirme devam ediyor** – etkin bir dağıtım, cihaza en yüksek sürüm ile uyumlu güncelleştirme sunma sürecinizdeki bir işlemdir.
*   **Kullanılabilir yeni güncelleştirmeler** – bir cihaz henüz en yüksek sürüme uyumlu güncelleştirmeyi yüklememiş ve bu güncelleştirme için etkin bir dağıtımda değil.
