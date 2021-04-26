---
title: Azure Kinect gövdesi Dizin eşlemesi
description: Azure Kinect DK 'de bir gövde izleme dizini eşlemesinin nasıl sorgulanalınacağını anlayın.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, taşıma, gövde, izleme, dizin, segment oluşturma, eşleme
ms.openlocfilehash: 8c128e59cef515aafb4f59794f0f644b90fd625d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277708"
---
# <a name="azure-kinect-body-tracking-index-map"></a>Azure Kinect gövde izleme Dizin eşlemesi

Gövde dizin eşlemesi, her gövde için örnek kesimleme haritasını, derinlik kamera yakalamadaki içerir. Her piksel, derinlik veya IR görüntüsündeki karşılık gelen pikselle eşlenir. Her pikselin değeri, pikselin ait olduğu gövdeyi temsil eder. Arka plan (değer `K4ABT_BODY_INDEX_MAP_BACKGROUND` ) ya da algılanan dizin olabilir `k4abt_body_t` .

![Gövde dizini eşleme örneği](./media/concepts/body-index-map.png)

>[!NOTE]
> Gövde dizini, gövde kimliğinden farklı. API: [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92)öğesini çağırarak, belirtilen bir gövde dizininden gövde kimliğini sorgulayabilirsiniz.


## <a name="using-body-index-map"></a>Gövde dizin eşlemesini kullanma

Gövde dizini eşlemesi bir olarak depolanır `k4a_image_t` ve derinlik ya da IR görüntüsüyle aynı çözünürlüğe sahiptir. Her piksel 8 bitlik bir değerdir. , `k4abt_frame_t` Çağırarak bir ile sorgulanabilir `k4abt_frame_get_body_index_map` . Geliştirici, çağırarak gövde dizini eşlemesi için belleği serbest bırakmaktan sorumludur `k4a_image_release()` .

## <a name="next-steps"></a>Sonraki adımlar

[İlk gövde izleme uygulamanızı oluşturma](build-first-body-app.md)
