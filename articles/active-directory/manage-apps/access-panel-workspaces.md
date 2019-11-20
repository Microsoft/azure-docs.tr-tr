---
title: Azure Active Directory | Uygulama portallarım için çalışma alanları oluşturun | Microsoft Docs
description: Son kullanıcılarınıza yönelik uygulamalar sayfalarımı daha basit bir deneyimle özelleştirmek için uygulamalarım çalışma alanlarını kullanın. Uygulamaları ayrı sekmeler içeren gruplar halinde düzenleyin.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a986c2032fc41bbd34230c5b0ead745f88b000f
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199833"
---
# <a name="create-workspaces-on-the-my-apps-preview-portal"></a>Uygulamalarım (Önizleme) portalında çalışma alanları oluşturma

Kullanıcılarınız, erişimi olan bulut tabanlı uygulamaları görüntülemek ve başlatmak için My Apps (Önizleme) portalını kullanabilir. Varsayılan olarak, bir kullanıcının erişebileceği tüm uygulamalar tek bir sayfada birlikte listelenir. Kullanıcılarınıza yönelik bu sayfayı daha iyi düzenlemek için, Azure AD Premium P1 veya P2 lisansınız varsa, çalışma alanlarını ayarlayabilirsiniz. Bir çalışma alanı ile ilişkili uygulamaları (örneğin, iş rolü, görev veya proje) gruplandırabilirsiniz ve bunları ayrı bir sekmede görüntüleyebilirsiniz. Bir çalışma alanı temelde, kullanıcının zaten erişebileceği uygulamalara bir filtre uygular, böylece Kullanıcı yalnızca çalışma alanında kendisine atanmış olan uygulamaları görür.

> [!NOTE]
> Bu makalede, bir yöneticinin çalışma alanlarını nasıl etkinleştirip oluşturabileceğiniz ele alınmaktadır. Son kullanıcıya ait My Apps Portal ve çalışma alanlarını kullanma hakkında bilgi için bkz. [erişim ve çalışma alanları kullanma](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces).

## <a name="enable-my-apps-preview-features"></a>Uygulamalarımın Önizleme özelliklerini etkinleştir

1. [**Azure Portal**](https://portal.azure.com/) açın ve Kullanıcı Yöneticisi veya genel yönetici olarak oturum açın.

2. **Azure Active Directory** > **Kullanıcı ayarları**' na gidin.

3. **Kullanıcı özelliği önizlemeleri**altında **Kullanıcı özelliği Önizleme ayarlarını yönet**' i seçin.

4. **Kullanıcılar Uygulamalarım için Önizleme özelliklerini kullanabilir**, aşağıdaki seçeneklerden birini belirleyin:
   * **Selected** -belirli bir grup için Önizleme özelliklerini sunar. Önizleme özelliklerini etkinleştirmek istediğiniz grubu seçmek için **Grup Seç** seçeneğini kullanın.  
   * **All** -tüm kullanıcılar için Önizleme özelliklerini etkinleştirilir.

   ![Kullanıcı Önizleme özellikleri](media/access-panel-workspaces/user-preview-features.png)

> [!NOTE]
> Uygulamalar portalı 'nı açmak için, kullanıcılar bağlantı `https://myapps.microsoft.com` veya kuruluşunuzun `https://myapps.microsoft.com/contoso.com`gibi özelleştirilmiş bağlantıyı kullanabilir. Kullanıcılar uygulamalarım önizleme sürümüne yönlendirilmemişse, kullanıcılar `https://myapplications.microsoft.com` veya `https://myapplications.microsoft.com/contoso.com`denemelidir.

## <a name="create-a-workspace"></a>Çalışma alanı oluşturma

Bir çalışma alanı oluşturmak için bir Azure AD Premium P1 veya P2 lisansına sahip olmanız gerekir.

1. [**Azure Portal**](https://portal.azure.com/) açın ve Azure AD Premium P1 veya P2 lisansıyla yönetici olarak oturum açın.

2. **Kurumsal uygulamalar** **Azure Active Directory** > gidin.

3. **Yönet**altında **çalışma alanları (Önizleme)** öğesini seçin.

4. **Yeni çalışma alanı**' nı seçin. **Yeni çalışma alanı** sayfasında, çalışma alanı Için bir **ad** girin (adında "çalışma alanı" kullanmaktan önermiyoruz. Sonra bir **Açıklama**girin.

   ![Yeni çalışma alanı oluştur](media/access-panel-workspaces/new-workspace.png)

5. **Gözden geçir + oluştur**' u seçin. Yeni çalışma alanının özellikleri görüntülenir.

6. **Uygulamalar** sekmesini seçin. **Uygulama Ekle**' nin altında, çalışma alanına eklemek istediğiniz tüm uygulamaları seçin ya da uygulamaları bulmak için **arama** kutusunu kullanın. 

   ![Çalışma alanına uygulama ekleme](media/access-panel-workspaces/add-applications.png)

7. **Add (Ekle)** seçeneğini belirleyin. Seçilen uygulamaların listesi görüntülenir. Listedeki uygulamaların sırasını değiştirmek için yukarı ve aşağı okları kullanabilirsiniz.

   ![Çalışma alanındaki uygulamaların listesi](media/access-panel-workspaces/add-applications-list.png)

8. **Kullanıcılar ve gruplar** sekmesini seçin. Kullanıcı veya grup eklemek için **Kullanıcı Ekle**' yi seçin. 

9. **Üyeleri seçin** sayfasında, çalışma alanını atamak istediğiniz kullanıcıları veya grupları seçin. Ya da kullanıcıları veya grupları bulmak için **arama** kutusunu kullanın.

   ![Çalışma alanına kullanıcı ve Grup atama](media/access-panel-workspaces/add-users-and-groups.png)

10. Kullanıcılar ve gruplar seçimini tamamladıktan sonra **Seç**' i seçin.

11. Bir kullanıcının rolünü **Sahibe** veya bunun tersini **değiştirmek için,** geçerli role tıklayın ve yeni bir rol seçin.

    ![Kullanıcılara ve gruplara roller atama](media/access-panel-workspaces/users-groups-list-role.png)

## <a name="view-audit-logs"></a>Denetim günlüklerini görüntüleme

Denetim günlükleri, çalışma alanı oluşturma Son Kullanıcı eylemleri dahil olmak üzere uygulama çalışma alanları operasyonlarımı kaydeder. Uygulamalarımın aşağıdaki olayları oluşturulmuştur:

* Çalışma alanı oluşturma
* Çalışma alanını Düzenle
* Çalışma alanını sil
* Uygulama başlatma (Son Kullanıcı)
* Self Servis uygulama ekleme (Son Kullanıcı)
* Self Servis uygulama silme (Son Kullanıcı)

Etkinlik bölümündeki Denetim **günlükleri** > **Azure Active Directory** > **Kurumsal uygulamaları** [Azure Portal](https://portal.azure.com) seçerek denetim günlüklerine erişebilirsiniz. **Hizmet**Için **uygulamalarım**' ı seçin.

   ![Kullanıcılara ve gruplara roller atama](media/access-panel-workspaces/audit-log-myapps.png)


## <a name="next-steps"></a>Sonraki adımlar
[Azure Active Directory uygulamalar için son kullanıcı deneyimleri](end-user-experiences.md)