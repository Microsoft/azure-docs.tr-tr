---
title: Azure Izleyici çalışma kitapları kendi depolama alanınızı getirir
description: Çalışma kitabı içeriğini depolamaya kaydederek çalışma kitabınızı güvenli hale getirme hakkında bilgi edinin
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: lagayhar
author: lgayhardt
ms.openlocfilehash: 16dbd7f7cd178a76b34b58f4bc6f9a0bc00fac73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100624620"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>Çalışma kitaplarını kaydetmek için kendi depolama alanınızı getirin

Güvenli hale getirmek istediğiniz bir sorgunuz veya bazı iş mantığınızın olduğu zamanlar olabilir. Çalışma kitapları, çalışma kitaplarının içeriğini depolamaya kaydederek çalışma kitaplarını güvenli hale getirmeye yönelik bir seçenek sunar. Depolama hesabı daha sonra Microsoft tarafından yönetilen anahtarlarla şifrelenebilir veya kendi anahtarlarınızı sağlayarak şifrelemeyi yönetebilirsiniz. [Depolama Hizmeti Şifrelemesi Azure belgelerine bakın.](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>Çalışma kitabını yönetilen kimliklerle kaydetme

1. Çalışma kitabını depolama alanına kaydedebilmeniz için önce yönetilen bir kimlik (tüm hizmetler-> Yönetilen kimlikler) oluşturmanız ve `Storage Blob Data Contributor` depolama hesabınıza erişim sağlamanız gerekir. [Yönetilen kimlikler hakkında Azure belgelerine bakın.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![Rol ataması eklemeyi gösteren ekran görüntüsü](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. Yeni bir çalışma kitabı oluşturun.
3. Çalışma kitabını kaydetmek için **Kaydet** düğmesini seçin.
4. Bir seçeneği vardır, bir `Save content to an Azure Storage Account` Azure depolama hesabına kaydetmek için onay kutusunu seçin.

    ![Kaydedilen iletişim kutusunu gösteren ekran görüntüsü](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. İstediğiniz depolama hesabını ve kapsayıcıyı seçin. Depolama hesabı listesi, yukarıda seçilen abonelikten.

    ![Depolama seçeneğiyle Kaydet iletişim kutusunu gösteren ekran görüntüsü](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. Daha önce oluşturulmuş bir yönetilen kimliği seçmek için **Değiştir** ' i seçin.

    [![Kimlik Değiştir iletişim kutusunu gösteren ekran görüntüsü](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. Depolama seçeneklerinizi seçtikten sonra, çalışma kitabınızı kaydetmek için **Kaydet** ' e basın.

## <a name="limitations"></a>Sınırlamalar

- Özel depolamaya kaydetme sırasında, tek bir PIN 'in panoda korunan bilgileri içermesi nedeniyle çalışma kitabının tek bir bölümünü bir panoya sabitleyemez. Özel depolama kullanırken, yalnızca çalışma kitabının bağlantılarını panolara sabitleyebilirsiniz.
- Çalışma kitabı özel depolamaya kaydedildikten sonra, her zaman özel depolamaya kaydedilir ve bu işlem kapatılamaz. Başka bir yere kaydetmek için, "farklı kaydet" i kullanabilir ve kopyayı özel depolamaya kaydetmemelidir.
- Application Insights kaynaktaki çalışma kitapları "eski" çalışma kitaplardır ve özel depolamayı desteklemez. Application Insights kaynaktaki son çalışma kitapları "... Daha fazla "seçim. Eski çalışma kitaplarının kaydetme sırasında abonelik seçenekleri yok.

   ![Eski çalışma kitabını gösteren ekran görüntüsü](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>Sonraki adımlar

- Çalışma kitaplarında [harita](workbooks-map-visualizations.md) görselleştirmesi oluşturmayı öğrenin.
- [Çalışma kitaplarında grupları](../visualize/workbooks-groups.md)nasıl kullanacağınızı öğrenin.