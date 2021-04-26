---
title: include dosyası
description: include dosyası
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 9a003b5c42a6ef4c699a3768d15ae08f86d56e52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367308"
---
## <a name="create-a-new-cdn-profile"></a>Yeni bir CDN profili oluşturma

CDN profili, CDN uç noktaları için bir kapsayıcı olup bir fiyatlandırma katmanını belirtir.

1. Azure portal, **kaynak oluştur** ' u seçin (sol üst tarafta). **Yeni** bölmesi görüntülenir.
   
1. **CDN**'yi arayıp seçin, sonra **Oluştur**' u seçin:
   
    ![CDN kaynağını seçin](./media/cdn-create-profile/cdn-new-resource.png)

    **CDN profili** bölmesi görüntülenir.

1. Aşağıdaki değerleri girin:
   
    | Ayar  | Değer |
    | -------- | ----- |
    | **Ad** | Profil adınız için *CDN-profile-123* girin. |
    | **Abonelik** | Açılan listeden bir Azure aboneliği seçin. |
    | **Kaynak grubu** | **Yeni oluştur** ' u seçin ve kaynak grubunuzun adı Için *cdnquickstart-RG* girin veya **var olanı kullan** ' ı seçin ve zaten grubunuz varsa *cdnquickstart-RG* öğesini seçin. | 
    | **Kaynak grubu konumu** | Aşağı açılan listeden yakınınızdaki bir konum seçin. |
    | **Fiyatlandırma katmanı** | Açılan listeden bir **Standart Akamai** seçeneği belirleyin. (Akamai katmanının dağıtım süresi yaklaşık bir dakikadır. Microsoft katmanı yaklaşık 10 dakika sürer ve Verizon katmanları yaklaşık 30 dakika sürer.) |
    | **Şimdi yeni bir CDN uç noktası oluşturun** | Seçilmemiş şekilde bırakın. |  
   
    ![Yeni CDN profili](./media/cdn-create-profile/cdn-new-profile.png)

1. Profili oluşturmak için **Oluştur**’u seçin.

