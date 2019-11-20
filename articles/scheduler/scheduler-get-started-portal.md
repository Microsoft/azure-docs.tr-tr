---
title: Azure Scheduler ile zamanlanmış iş oluşturma - Azure portal | Microsoft Docs
description: Azure Scheduler ile Azure portalda ilk otomatik işinizi oluşturmayı, zamanlamayı ve çalıştırmayı öğrenin
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 48e03f41fa0a39642330288fc05ef709179b4c62
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300984"
---
# <a name="create-and-schedule-your-first-job-with-azure-scheduler---azure-portal"></a>Azure Scheduler ile ilk işinizi oluşturun ve zamanlayın - Azure portal

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) , [devre dışı bırakılmakta](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)olan Azure Scheduler 'ı değiştiriyor. Zamanlayıcı 'da ayarladığınız işlerle çalışmaya devam etmek için lütfen en kısa sürede [Azure Logic Apps geçirin](../scheduler/migrate-from-scheduler-to-logic-apps.md) .

Bu öğreticide bir iş oluşturma, zamanlama ve ardından bu işi izleyip yönetme adımları gösterilmektedir. 

Azure aboneliğiniz yoksa <a href="https://azure.microsoft.com/free/" target="_blank">ücretsiz bir Azure hesabı için kaydolun</a>.

## <a name="create-job"></a>İş oluştur

1. [Azure Portal](https://portal.azure.com/) oturum açın.  

1. Azure ana menüsünde **Kaynak oluştur**'u seçin. Arama kutusuna "scheduler" yazın. Sonuç listesinden **Scheduler**'ı ve ardından **Oluştur**'u seçin.

   ![Scheduler kaynağı oluşturma](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   Şimdi şu URL'ye GET isteği gönderen bir iş oluşturun: `https://www.microsoft.com/` 

1. **Scheduler İşi** bölümüne şu bilgileri girin:

   | Özellik | Örnek değer | Açıklama |
   |----------|---------------|-------------| 
   | **Name** | getMicrosoft | İşinizin adı | 
   | **İş koleksiyonu** | <*job-collection-name*> | İş koleksiyonu oluşturun veya var olan bir koleksiyonu seçin. | 
   | **Abonelik** | <*Azure-subscription-name*> | Azure aboneliğinizin adı | 
   |||| 

1. **Eylem ayarları - Yapılandır**'ı seçin, bu bilgileri girin ve işlemi tamamladığınızda **Tamam**'ı seçin:

   | Özellik | Örnek değer | Açıklama |
   |----------|---------------|-------------| 
   | **Eylem** | **Http** | Çalıştırılacak eylemin türü | 
   | **Yöntem** | **Get** | Çağrılacak yöntem | 
   | **URL** | **https://www.microsoft.com** | Hedef URL | 
   |||| 
   
   ![İşi tanımlama](./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png)

1. **Zamanlama - Yapılandırma**'yı seçin, zamanlamayı tanımlayın ve işlemi tamamladığınızda **Tamam**'ı seçin:

   Tek seferlik bir iş oluşturabilirsiniz ancak bu örnekte yineleme zamanlaması ayarlanmaktadır.

   | Özellik | Örnek değer | Açıklama |
   |----------|---------------|-------------| 
   | **Yineleme** | **Yinelenen** | Tek seferlik veya yinelenen bir iş | 
   | **Başlangıç saati:** | <*bugünün tarihi*> | İşin başlangıç tarihi | 
   | **Yineleme sıklığı:** | **1 Saat** | Yineleme aralığı ve sıklığı | 
   | **Bitiş** | **Bitiş tarihi:** Bugünden itibaren iki gün | İşin bitiş tarihi | 
   | **UTC farkı** | **UTC +08:00** | Eşgüdümlü Evrensel Saat (UTC) ile konumunuzda kullanılan saat arasındaki zaman farkı | 
   |||| 

   ![Zamanlamayı tanımlama](./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png)

1. Hazır olduğunuzda **Oluştur**’u seçin.

   İşiniz oluşturulduktan sonra Azure tarafından dağıtılır ve Azure panosunda görüntülenir. 

1. Azure, dağıtımın başarılı olduğunu belirten bir bildirim gösterdiğinde **Panoya sabitle**'yi seçin. Alternatif olarak Azure araç çubuğundaki **Bildirimler** simgesini (zil) ve ardından **Panoya sabitle**'yi seçebilirsiniz.

## <a name="monitor-and-manage-jobs"></a>İşleri izleme ve yönetme

Gözden geçirmek, izlemek ve yönetmek için işinizi Azure panosundan seçin. **Ayarlar** sayfasında işinizi gözden geçirip yönetebileceğiniz şu alanlar vardır:

![İş ayarları](./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png)

Bu alanlar hakkında daha fazla bilgi için birini seçebilirsiniz:

* [**Özellikler**](#properties)
* [**Eylem ayarları**](#action-settings)
* [**Zamanlama**](#schedule)
* [**Geçmiş**](#history)
* [**Kullanıcılar**](#users)

<a name="properties"></a>

### <a name="properties"></a>properties

İşinizin yönetim meta verilerini tanımlayan salt okunur özellikleri görüntülemek için **Özellikler**'i seçin.

![İş özelliklerini görüntüleme](./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png)

<a name="action-settings"></a>

### <a name="action-settings"></a>Eylem ayarları

İşinizin gelişmiş ayarlarını değiştirmek için **Eylem ayarları**'nı seçin. 

![Eylem ayarlarını gözden geçirme](./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png)

| Eylem türü | Açıklama | 
|-------------|-------------| 
| Tüm türler | **Yeniden deneme ilkesi** ve **Hata eylemi** ayarlarını değiştirebilirsiniz. | 
| HTTP ve HTTPS | **Yöntem** için izin verilen değerlerden birini seçebilirsiniz. Ayrıca başlık ve temel kimlik doğrulama bilgileri ekleyebilir, silebilir veya değiştirebilirsiniz. | 
| Depolama kuyruğu| Depolama hesabını, kuyruk adını, SAS belirtecini ve gövdeyi değiştirebilirsiniz. | 
| Service Bus | Ad alanı, konu veya kuyruk yolu, kimlik doğrulama ayarları, aktarım türü, ileti özellikleri ve ileti gövdesini değiştirebilirsiniz. | 
||| 

<a name="schedule"></a>

### <a name="schedule"></a>Zamanlama

Zamanlamayı iş sihirbazı aracılığıyla ayarladıysanız yinelenen işler için başlangıç tarihi ile saati, yineleme zamanlaması ve bitiş tarihi ile saati gibi zamanlama ayarlarını değiştirebilirsiniz.
Daha [karmaşık zamanlamalar ve gelişmiş yinelemeler](scheduler-advanced-complexity.md) de oluşturabilirsiniz.

İşinizin zamanlamasını görüntülemek veya değiştirmek için **Zamanlama**'yı seçin:

![İş zamanlamasını görüntüleme](./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png)

<a name="history"></a>

### <a name="history"></a>Geçmiş

Seçilen bir işin her bir çalıştırmasıyla ilgili ölçümleri görüntülemek için **Geçmiş**'i seçin. Bu ölçümler durum, yeniden deneme sayısı, yineleme sayısı, başlangıç zamanı ve bitiş zamanı gibi işinizin durumuyla ilgili gerçek zamanlı değerler sunar.

![İş geçmişini ve ölçümlerini görüntüleme](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png)

Çalıştırma yanıtının tamamı gibi bir çalıştırmanın geçmiş ayrıntılarını görüntülemek için **Geçmiş** bölümünde istediğiniz çalıştırmayı seçin. 

![İş geçmişi ayrıntılarını görüntüleme](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png)

<a name="users"></a>

### <a name="users"></a>Kullanıcılar

Azure Rol Tabanlı Erişim Denetimi (RBAC) ile her bir kullanıcının Azure Scheduler erişimini ayrıntılı bir şekilde yönetebilirsiniz. Rol tabanlı erişim denetimi ayarları hakkında daha fazla bilgi için bkz. [RBAC kullanarak erişimi yönetme](../role-based-access-control/role-assignments-portal.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Kavramlar, terminoloji ve varlık hiyerarşisi](scheduler-concepts-terms.md) hakkında bilgi edinin
* [Karmaşık zamanlamalar ve gelişmiş yinelemeler oluşturma](scheduler-advanced-complexity.md)
* [Yüksek Scheduler kullanılabilirliği ve güvenilirliği](scheduler-high-availability-reliability.md) hakkında bilgi edinin
* [Sınırlar, kotalar, varsayılan değerler ve hata kodları](scheduler-limits-defaults-errors.md) hakkında bilgi edinin
