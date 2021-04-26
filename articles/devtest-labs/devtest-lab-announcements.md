---
title: Azure DevTest Labs bir laboratuvara duyuru gönderin | Microsoft Docs
description: Kullanıcıları, Azure DevTest Labs laboratuvardaki son değişiklikleri veya eklemeleri hakkında bilgilendirmek için, mevcut bir laboratuvarda nasıl özel duyuru göndereceğinizi öğrenin.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 72c6ed7ab691ece094673ff5a158ffa769a9f7a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88270776"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs bir laboratuvara duyuru gönderin

Laboratuvar Yöneticisi olarak, kullanıcıya son değişiklikler veya laboratuvardaki eklemeler hakkında bilgilendirmek için, mevcut bir laboratuvarda özel duyuru gönderebilirsiniz. Örneğin, kullanıcılara şunları bildirmek isteyebilirsiniz:

- Kullanılabilir yeni VM boyutları
- Şu anda kullanılamayan görüntüler
- Laboratuvar ilkelerine yönelik güncelleştirmeler

Yayımlandıktan sonra, duyuru, laboratuvarın Genel Bakış sayfasında görüntülenir ve daha fazla ayrıntı için Kullanıcı tarafından seçim yapabilir.

Duyuru özelliği geçici bildirimler için kullanılmak üzere tasarlanmıştır.  Bir duyuruyu artık gerekli olmadığında kolayca devre dışı bırakabilirsiniz.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Mevcut laboratuvarda duyuru gönderme adımları

1. [Azure portalında](https://go.microsoft.com/fwlink/p/?LinkID=525040) oturum açın.
1. Gerekirse, **tüm hizmetler**' i seçin ve ardından listeden **DevTest Labs** ' i seçin. (Laboratuvarınız panoda zaten **tüm kaynaklar** altında gösteriliyor olabilir).
1. Laboratuvarlar listesinden, bir duyuru göndermek istediğiniz Laboratuvarı seçin.
1. Laboratuvarın **genel bakış** alanında **yapılandırma ve ilkeler**' i seçin.

    ![Yapılandırma ve ilkeler düğmesi](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Sol taraftaki **Ayarlar**' ın altında **Laboratuvar duyurusu**' nu seçin.

    ![Laboratuvar duyurusu düğmesi](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Bu laboratuvardaki kullanıcılara bir ileti oluşturmak için, **etkin** ' i **Evet** olarak ayarlayın.

1. Duyurunun artık kullanıcılara gösterilmemesi için bir tarih ve saat belirtmek üzere bir **sona erme tarihi** girebilirsiniz. Sona erme tarihi girmezseniz, duyuru devre dışı bırakana kadar kalır.

   > [!NOTE]
   > Duyurunun süresi dolduktan sonra artık kullanıcılara gösterilmez, ancak **Laboratuvar duyurusu** bölmesinde hala mevcuttur. Üzerinde düzenleme yapabilir ve yeniden etkin hale getirmek için yeniden etkinleştirebilirsiniz.
   >
   >

1. **Duyuru başlığı** ve **duyuru metni** girin.

   Başlık en fazla 100 karakter uzunluğunda olabilir ve kullanıcıya laboratuvar Genel Bakış sayfasında gösterilir. Kullanıcı başlığı seçerse, duyuru metni görüntülenir.

   Duyuru metni markın kabul eder. Duyuru metnini girerken, iletiyi ekranın altındaki önizleme alanında görüntüleyebilirsiniz.

    ![İletiyi oluşturmak için laboratuvar duyurusu ekranı.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Duyurunuzun göndermeye hazırladıktan sonra **Kaydet** ' i seçin.

Artık bu duyuruyu laboratuvar kullanıcılarına göstermek istemediğinizde, **Laboratuvar duyurusu** sayfasına dönün ve **etkin** ' i **Hayır** olarak ayarlayın. Sona erme tarihi belirttiyseniz, duyuru bu tarih ve saatte otomatik olarak devre dışı bırakılır.

## <a name="steps-for-users-to-view-an-announcement"></a>Kullanıcılara bir duyuruyu görüntüleme adımları

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)bir laboratuvar seçin.

1. Laboratuvarın kendisi için gönderilen bir duyurusu varsa, laboratuvarın Genel Bakış sayfasının en üstünde bir bilgi bildirimi gösterilir. Bu bilgi bildirimi, duyuru oluşturulduğunda belirtilen duyuru başlığıdır.

    ![Genel Bakış sayfasında laboratuvar duyurusu](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. Kullanıcı, tüm duyuruyu görüntülemek için iletiyi seçebilir.

    ![Laboratuvar duyurusu hakkında daha fazla bilgi](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu
Aşağıdaki örnekte gösterildiği gibi, bir Azure Resource Manager şablonun parçası olarak bir duyuru belirtebilirsiniz:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "devtestlabfromarm"
        },
        "regionId": {
            "type": "string",
            "defaultValue": "eastus"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-26-preview",
            "name": "[parameters('name')]",
            "type": "Microsoft.DevTestLab/labs",
            "location": "[parameters('regionId')]",
            "tags": {},
            "properties": {
                "labStorageType": "Premium",
                "announcement":
                {
                    "title": "Important! Three images are currently inaccessible. Click for more information.",
                    "markdown":"# Images are inaccessible\n\nThe following 3 images are currently not available for use: \n\n- image1\n- image2\n- image3\n\nI am working to fix the problem ASAP.",
                    "enabled": "Enabled",
                    "expirationDate":"2018-12-31T06:00:00+00:00",
                    "expired": "false"
                },
                "support": {
                    "markdown": "",
                    "enabled": "Enabled"
                }
            },
            "resources": [
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "LabVmsShutdown",
                    "location": "[parameters('regionId')]",
                    "type": "schedules",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ],
                    "properties": {
                        "status": "Enabled",
                        "timeZoneId": "Eastern Standard Time",
                        "dailyRecurrence": {
                            "time": "1900"
                        },
                        "taskType": "LabVmsShutdownTask",
                        "notificationSettings": {
                            "status": "Disabled",
                            "timeInMinutes": 30
                        }
                    }
                },
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "[concat('Dtl', parameters('name'))]",
                    "type": "virtualNetworks",
                    "location": "[parameters('regionId')]",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ]
                }
            ]
        }
    ]
}
```

Aşağıdaki yollarla bir Azure Resource Manager şablonu dağıtabilirsiniz:

- [Azure portalındaki](../azure-resource-manager/templates/deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
- [REST API](../azure-resource-manager/templates/deploy-rest.md)

## <a name="next-steps"></a>Sonraki adımlar
* Bir laboratuvar ilkesini değiştirir veya ayarlarsanız, kullanıcıları bilgilendirmek için bir duyuru göndermek isteyebilirsiniz. [İlke ve zamanlamalar ayarlama](devtest-lab-set-lab-policy.md) , özelleştirilmiş ilkeleri kullanarak aboneliğinizde kısıtlama ve kural uygulama hakkında bilgi sağlar.
* [DevTest Labs Azure Resource Manager hızlı başlangıç şablonu galerisini](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)gezin.
