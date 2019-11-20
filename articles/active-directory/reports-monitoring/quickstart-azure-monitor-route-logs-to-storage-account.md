---
title: Öğretici-dizin günlüklerini bir depolama hesabına arşivleme | Microsoft Docs
description: Azure Active Directory günlüklerini depolama hesabına göndermek için Azure Tanılama ayarlamayı öğrenin
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa2cac7c89a2cb1bd99675727bcd51e82c4a18ef
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007858"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account"></a>Öğretici: Azure AD günlüklerini bir Azure depolama hesabında arşivleme

Bu öğreticide bir Azure depolama hesabına Azure Active Directory (Azure AD) günlüğü yönlendirme amacıyla Azure İzleyici tanılama ayarlarını yapmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar 

Bu özelliği kullanmak için şunlara ihtiyacınız vardır:

* Azure depolama hesabına sahip bir Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz denemeye kaydolabilirsiniz](https://azure.microsoft.com/free/).
* Azure AD kiracısı.
* Azure AD kiracısında *genel yönetici* veya *güvenlik yöneticisi* olan bir kullanıcı.

## <a name="archive-logs-to-an-azure-storage-account"></a>Günlükleri Azure depolama hesabında arşivleme

1. [Azure portalında](https://portal.azure.com) oturum açın. 

2. **Azure Active Directory** > **Etkinlik** > **Denetim günlükleri**'ni seçin. 

3. **Dışarı Aktarma Ayarları**'nı seçin. 

4. **Tanılama ayarları** bölmesinde aşağıdakilerden birini yapın:
   * Var olan ayarları değiştirmek için **Ayarı düzenleyin**'i seçin.
   * Yeni ayar eklemek için **Tanılama ayarı ekle**'yi seçin.  
     En fazla üç ayar kullanabilirsiniz. 

     ![Dışarı aktarma ayarları](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. Ayarın amacını hatırlatması için kolay bir ad girin (örneğin, *Azure depolama hesabına gönder*). 

6. **Bir depolama hesabına arşivle** onay kutusunu işaretleyip **Depolama hesabı**'nı seçin. 

7. Günlükleri yönlendirmek istediğiniz Azure aboneliğini ve depolama hesabını seçin.
 
8. Yapılandırmayı kapatmak için **Tamam**'ı seçin.

9. Aşağıdakilerden birini veya ikisini birden yapın:
    * Denetim günlüklerini depolama hesabına göndermek için **AuditLogs** onay kutusunu işaretleyin. 
    * Oturum açma günlüklerini depolama hesabına göndermek için **SignInLogs** onay kutusunu işaretleyin.

10. Günlük verilerinizi saklama süresini belirlemek için kaydırıcıyı kullanın. Varsayılan olarak *0* değeri kullanılır ve günlükler sonsuza kadar depolama hesabında saklanır. Farklı bir değer belirlerseniz ve bu süreden daha eski olaylar otomatik olarak silinir.

11. Ayarları kaydetmek için **Kaydet**’i seçin.

    ![Tanılama ayarları](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

12. Yaklaşık 15 dakika sonra günlüklerin depolama hesabınıza gönderilip gönderilmediğini kontrol edin. [Azure portala](https://portal.azure.com) gidin, **Depolama hesapları**'nı seçin, önceki adımlarda kullandığınız depolama hesabını ve ardından **Bloblar**'ı seçin. **Denetim günlükleri** için **insights-log-audit** girişini seçin. **Oturum açma günlükleri** için **insights-logs-signin** girişini seçin.

    ![Depolama hesabı](./media/quickstart-azure-monitor-route-logs-to-storage-account/StorageAccount.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure İzleyici denetim günlükleri şemasını yorumlama](reference-azure-monitor-audit-log-schema.md)
* [Azure İzleyici oturum açma günlükleri şemasını yorumlama](reference-azure-monitor-sign-ins-log-schema.md)
* [Sık sorulan sorular ve bilinen sorunlar](concept-activity-logs-azure-monitor.md#frequently-asked-questions)