---
title: Özel CA sertifikası ekleme-Azure API Management | Microsoft Docs
description: Azure API Management özel bir CA sertifikası eklemeyi öğrenin.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 21d5869f2bcdfb6383b6ef89869d8098135ea7ee
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073599"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Azure API Management özel CA sertifikası ekleme

Azure API Management, CA sertifikalarının güvenilen kök ve ara sertifika depoları içindeki makineye yüklenmesini sağlar. Hizmetleriniz özel bir CA sertifikası gerektiriyorsa bu işlev kullanılmalıdır.

Makalede, Azure portal bir Azure API Management hizmet örneğinin CA sertifikalarının nasıl yönetileceği gösterilmektedir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>CA sertifikasını karşıya yükleme

![CA sertifikaları ekleme](media/api-management-howto-ca-certificates/00.png)

Yeni bir CA sertifikasını karşıya yüklemek için aşağıdaki adımları izleyin. Henüz bir API Management hizmet örneği oluşturmadıysanız, [API Management hizmet örneği oluşturma](get-started-create-service-instance.md)öğreticisine bakın.

1. Azure portal Azure API Management hizmet örneğinize gidin.

2. Menüden **CA sertifikaları** ' nı seçin.

3. **+ Ekle** düğmesine tıklayın.  

    ![CA sertifikaları ekleme](media/api-management-howto-ca-certificates/01.png)  

4. Sertifikaya gözatıp sertifika deposuna karar verin. Yalnızca ortak anahtar gereklidir, bu nedenle parola gerekli değildir.

    ![CA sertifikaları ekleme](media/api-management-howto-ca-certificates/02.png)  

5. **Kaydet**’e tıklayın. Bu işlem birkaç dakika sürebilir.

    ![CA sertifikaları ekleme](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> `New-AzApiManagementSystemCertificate` PowerShell komutunu kullanarak bir CA sertifikasını karşıya yükleyebilirsiniz.

## <a name="step1a"> </a>İstemci sertifikasını silme

Bir sertifikayı silmek için bağlam menüsü **...** öğesine tıklayın ve sertifikanın yanındaki **Sil** ' i seçin.

![CA sertifikalarını silme](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
