---
title: Azure Analysis Services modelleri için Logic Apps ile yenileme | Microsoft Docs
description: Bu makalede, Azure Logic Apps kullanılarak Azure Analysis Services için zaman uyumsuz yenilemenin nasıl kodleneceğini açıklar.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a44aa5b355bea675f5d99761d97b8876a9b2a7d7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572333"
---
# <a name="refresh-with-logic-apps"></a>Logic Apps ile yenileme

Logic Apps ve REST çağrılarını kullanarak, Azure Analysis tablolu modellerinizde, sorgu ölçeği için salt okuma çoğaltmalarının eşitlenmesi dahil otomatik veri yenileme işlemleri gerçekleştirebilirsiniz.

Azure Analysis Services ile REST API 'Leri kullanma hakkında daha fazla bilgi için bkz. [REST API Ile zaman uyumsuz yenileme](analysis-services-async-refresh.md).

## <a name="authentication"></a>Kimlik Doğrulaması

Tüm çağrıların kimliği geçerli bir Azure Active Directory (OAuth 2) belirteciyle doğrulanmalıdır.  Bu makaledeki örneklerde Azure Analysis Services kimlik doğrulaması için bir hizmet sorumlusu (SPN) kullanılır. Daha fazla bilgi için bkz. [Azure Portal kullanarak hizmet sorumlusu oluşturma](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Mantıksal uygulamayı tasarlama

> [!IMPORTANT]
> Aşağıdaki örneklerde Azure Analysis Services güvenlik duvarının devre dışı bırakıldığını kabul edilir.  Güvenlik Duvarı etkinse, istek başlatıcısının genel IP adresi Azure Analysis Services güvenlik duvarında beyaz listeye alınmalıdır. Bölge başına mantıksal uygulama IP aralıkları hakkında daha fazla bilgi için bkz. [Azure Logic Apps Için sınırlara ve yapılandırma bilgileri](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses).

### <a name="prerequisites"></a>Ön koşullar

#### <a name="create-a-service-principal-spn"></a>Hizmet sorumlusu oluşturma (SPN)

Hizmet sorumlusu oluşturma hakkında bilgi edinmek için, bkz. [Azure Portal kullanarak hizmet sorumlusu oluşturma](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Azure Analysis Services izinlerini yapılandırma
 
Oluşturduğunuz hizmet sorumlusu sunucuda Sunucu Yöneticisi izinlerine sahip olmalıdır. Daha fazla bilgi için bkz. [Sunucu Yöneticisi rolüne hizmet sorumlusu ekleme](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Mantıksal uygulamayı yapılandırma

Bu örnekte, mantıksal uygulama bir HTTP isteği alındığında tetiklemek üzere tasarlanmıştır. Bu, Azure Analysis Services modeli yenilemeyi tetiklemek için Azure Data Factory gibi bir Orchestration aracı kullanımını etkinleştirir.

Bir mantıksal uygulama oluşturduktan sonra:

1. Mantıksal uygulama tasarımcısında, **BIR http isteği alındığında**ilk eylemi seçin.

   ![HTTP alındı etkinliği Ekle](./media/analysis-services-async-refresh-logic-app/1.png)

Mantıksal uygulama kaydedildikten sonra bu adım HTTP POST URL 'SI ile doldurulur.

2. Yeni bir adım ekleyin ve **http**araması yapın.  

   ![HTTP etkinliği Ekle](./media/analysis-services-async-refresh-logic-app/9.png)

   ![HTTP etkinliği Ekle](./media/analysis-services-async-refresh-logic-app/10.png)

3. Bu eylemi eklemek için **http** 'yi seçin.

   ![HTTP etkinliği Ekle](./media/analysis-services-async-refresh-logic-app/2.png)

HTTP etkinliğini şu şekilde yapılandırın:

|Özellik  |Değer  |
|---------|---------|
|**Yöntem**     |Yayınla         |
|**KULLANıLMAMıŞSA**     | https://*sunucu bölgenizi*/Servers/*AAS sunucu adı*/models/*veritabanınızın adı*/yenilemeler <br /> <br /> Örneğin: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**Üst Bilgiler**     |   İçerik türü, uygulama/JSON <br /> <br />  ![Üst bilgiler](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Gövde**     |   İstek gövdesini oluşturan hakkında daha fazla bilgi edinmek için, [REST API-Post/Refresh Ile zaman uyumsuz yenileme](analysis-services-async-refresh.md#post-refreshes)bölümüne bakın. |
|**Kimlik doğrulaması**     |Active Directory OAuth         |
|**Kiracı**     |Azure Active Directory Tenantıd 'nizi girin         |
|**Grubu**     |https://*. aşama zure. Windows. net         |
|**İstemci KIMLIĞI**     |Hizmet asıl adı ClientID değerini girin         |
|**Kimlik bilgisi türü**     |Gizli dizi         |
|**Gizli dizi**     |Hizmet sorumlusu adı gizli anahtarını girin         |

Örnek:

![Tamamlanan HTTP etkinliği](./media/analysis-services-async-refresh-logic-app/7.png)

Artık mantıksal uygulamayı test edin.  Mantıksal uygulama tasarımcısında **Çalıştır**' a tıklayın.

![Mantıksal uygulamayı test etme](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Mantıksal uygulamayı Azure Data Factory kullanma

Mantıksal uygulama kaydedildikten sonra, **http isteği alındığında** öğesini gözden geçirin ve ardından oluşturulan **http post URL 'sini** kopyalayın.  Bu, mantıksal uygulamayı tetiklemek için zaman uyumsuz çağrıyı yapmak üzere Azure Data Factory tarafından kullanılabilen URL 'dir.

Bu eylemi gerçekleştiren bir Web etkinliği Azure Data Factory örneği aşağıda verilmiştir.

![Data Factory Web etkinliği](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Kendi içinde mantıksal uygulama kullanma

Model yenilemeyi tetiklemek için Data Factory gibi bir Orchestration aracı kullanmayı planlamıyorsanız, mantıksal uygulamayı bir zamanlamaya göre yenilemeyi tetikleyecek şekilde ayarlayabilirsiniz.

Yukarıdaki örneği kullanarak, ilk etkinliği silip bir **zamanlama** etkinliği ile değiştirin.

![Zamanlama etkinliği](./media/analysis-services-async-refresh-logic-app/12.png)

![Zamanlama etkinliği](./media/analysis-services-async-refresh-logic-app/13.png)

Bu örnek, **yinelenme**kullanır.

Etkinlik eklendikten sonra aralığı ve sıklığı yapılandırın, sonra yeni bir parametre ekleyin ve **bu saatlere**seçin.

![Zamanlama etkinliği](./media/analysis-services-async-refresh-logic-app/16.png)

İstediğiniz saatleri seçin.

![Zamanlama etkinliği](./media/analysis-services-async-refresh-logic-app/15.png)

Mantıksal uygulamayı kaydedin.

## <a name="next-steps"></a>Sonraki adımlar

[Örnekler](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
