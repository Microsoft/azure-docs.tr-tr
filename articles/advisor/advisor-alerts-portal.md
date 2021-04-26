---
title: Azure portal kullanarak yeni öneriler için Azure Advisor uyarıları oluşturma
description: Yeni öneri için Azure Advisor uyarıları oluşturma
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 3c51479821914ef34edcd13d8708344169f17aae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590110"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations-using-the-azure-portal"></a>Azure portal kullanarak yeni önerilerle Azure Advisor uyarıları oluşturun 

Bu makalede, Azure Advisor 'ın Azure portal kullanarak yeni öneriler için nasıl uyarı ayarlanacağı gösterilmektedir. 

Azure Advisor kaynaklarınızdan biri için yeni bir öneri algıladığında, [Azure etkinlik günlüğünde](../azure-monitor/essentials/platform-logs-overview.md)bir olay depolanır. Bu olaylara yönelik uyarıları, önerisine özgü uyarılar oluşturma deneyimini kullanarak Azure Advisor 'dan ayarlayabilirsiniz. Uyarıları almak istediğiniz kaynakları belirtmek için bir abonelik ve isteğe bağlı olarak bir kaynak grubu seçebilirsiniz. 

Aşağıdaki özellikleri kullanarak öneri türlerini de belirleyebilirsiniz:

* Kategori
* Etki düzeyi
* Öneri türü

Ayrıca, bir uyarının tetiklendiği zaman gerçekleşecek eylemi de yapılandırabilirsiniz:  

* Mevcut bir eylem grubu seçiliyor
* Yeni bir eylem grubu oluşturuluyor

Eylem grupları hakkında daha fazla bilgi edinmek için bkz. [Eylem grupları oluşturma ve yönetme](../azure-monitor/alerts/action-groups.md).

> [!NOTE] 
> Danışman uyarıları Şu anda yalnızca yüksek kullanılabilirlik, performans ve maliyet önerileri için kullanılabilir. Güvenlik önerileri desteklenmez. 

## <a name="create-alert-rule"></a>Uyarı kuralı oluşturma
1. **Portalda** **Azure Danışmanı**' nı seçin.

    ![Portalda Azure Danışmanı](./media/advisor-alerts/create1.png)

2. Sol menünün **izleme** bölümünde **Uyarılar**' ı seçin. 

    ![Advisor 'daki uyarılar](./media/advisor-alerts/create2.png)

3. **Yeni danışman uyarısı**' nı seçin.

    ![Yeni danışman uyarısı](./media/advisor-alerts/create3.png)

4. **Kapsam** bölümünde, bir abonelik ve isteğe bağlı olarak uyarılmak istediğiniz kaynak grubunu seçin. 

    ![Advisor uyarı kapsamı](./media/advisor-alerts/create4.png)

5. **Koşul** bölümünde, uyarınızı yapılandırmak için kullanmak istediğiniz yöntemi seçin. Belirli bir kategori ve/veya etki düzeyi için tüm önerilere uyarı vermek istiyorsanız **Kategori ve etki düzeyi**' ni seçin. Belirli bir türün tüm önerilerini uyarmak istiyorsanız **öneri türü**' nü seçin.

    ![Azure Advisor uyarı koşulu](./media/advisor-alerts/create5.png)

6. Seçtiğiniz yapılandırma ölçütü seçeneğine bağlı olarak, ölçütleri belirtebilirsiniz. Tüm önerileri istiyorsanız, kalan alanları boş bırakmanız yeterlidir. 

    ![Danışman uyarı eylem grubu](./media/advisor-alerts/create6.png)

7. **Eylem grupları** bölümünde, önceden oluşturduğunuz bir eylem grubunu kullanmak Için **Varolanı Ekle** ' yi seçin veya yeni bir [eylem grubu](../azure-monitor/alerts/action-groups.md)ayarlamak için **Yeni oluştur** ' u seçin. 

    ![Danışman uyarısı varolanı Ekle](./media/advisor-alerts/create7.png)

8. Uyarı ayrıntıları bölümünde, uyarıya bir ad ve kısa açıklama verin. Uyarınızın etkinleştirilmesini istiyorsanız, **oluşturma seçimi sonrasında kuralı etkinleştir** ' i **Evet** olarak ayarlayın. Ardından, uyarılarınızın kaydedileceği kaynak grubunu seçin. Bu işlem, önerinin hedefleme kapsamını etkilemez. 

    :::image type="content" source="./media/advisor-alerts/create8.png" alt-text="Uyarı ayrıntıları bölümünün ekran görüntüsü.":::


## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Web kancası kullanmak için öneri uyarılarını yapılandırma
Bu bölümde, mevcut sistemlerinize Web kancaları aracılığıyla öneri verileri göndermek için Azure Advisor uyarılarını nasıl yapılandıracağınız gösterilmektedir. 

Kaynaklarınızdan birinde yeni bir danışman önerisine sahip olduğunuzda, uyarıları bildirilecek şekilde ayarlayabilirsiniz. Bu uyarılar size e-posta veya kısa mesaj aracılığıyla bildirimde bulunabilir, ancak aynı zamanda bir Web Kancası aracılığıyla mevcut sistemlerinizle tümleştirme için de kullanılabilir. 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Danışman önerisi uyarı yükünü kullanma
Advisor uyarılarını bir Web kancası kullanarak kendi sistemlerinizle tümleştirebilmek istiyorsanız, bildirimden gönderilen JSON yükünü ayrıştırmanıza gerek duyarsınız. 

Bu uyarı için eylem grubunuzu ayarlarken, ortak uyarı şemasını kullanmak istiyorsanız bu seçeneği belirleyin. Ortak uyarı şemasını seçerseniz, yükünüzü şöyle görünecektir: 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

Ortak şemayı kullanmıyorsanız, yükünüze aşağıdakiler gibi görünür: 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

Herhangi bir şemada, **EventSource** ve OperationName ' ı arayarak Advisor öneri olaylarını belirleyebilirsiniz `Recommendation`  `Microsoft.Advisor/recommendations/available/action` .

Kullanmak isteyebileceğiniz diğer önemli alanlardan bazıları şunlardır: 

* *Alerttargetıds* (ortak şemada) veya *RESOURCEID* (eski şema)
* *recommendationType*
* *recommendationName*
* *recommendationCategory*
* *recommendationImpact*
* *recommendationResourceLink*


## <a name="manage-your-alerts"></a>Uyarılarınızı yönetme 

Azure Advisor 'da, öneriler uyarılarınızı düzenleyebilir, silebilir veya devre dışı bırakabilir ve etkinleştirebilirsiniz. 

1. **Portalda** **Azure Danışmanı**' nı seçin.

    :::image type="content" source="./media/advisor-alerts/create1.png" alt-text="Azure Advisor 'ın seçili olduğunu gösteren Azure portal menüsünün ekran görüntüsü.":::

2. Sol menünün **izleme** bölümünde **Uyarılar**' ı seçin.

    :::image type="content" source="./media/advisor-alerts/create2.png" alt-text="Seçili uyarıların gösterildiği Azure portal menüsünün ekran görüntüsü.":::

3. Bir uyarıyı düzenlemek için uyarı adına tıklayarak uyarıyı açın ve düzenlemek istediğiniz alanları düzenleyin.

4. Bir uyarıyı silmek, etkinleştirmek veya devre dışı bırakmak için, satırın sonundaki elipsi tıklatın ve ardından istediğiniz eylemi seçin.
 

## <a name="next-steps"></a>Sonraki adımlar
- [Etkinlik günlüğü uyarılarına genel bir bakış](../azure-monitor/alerts/alerts-overview.md)elde edin ve uyarıları alma hakkında bilgi edinin.
- [Eylem grupları](../azure-monitor/alerts/action-groups.md)hakkında daha fazla bilgi edinin.
