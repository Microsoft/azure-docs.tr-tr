---
title: Koşullu erişim API 'Leri ve PowerShell-Azure Active Directory
description: Kod gibi ilkeleri yönetmek için Azure AD koşullu erişim API 'Lerini ve PowerShell 'i kullanma
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d4dd6e64053af41d66e3accbb7fc9b21d951fc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860222"
---
# <a name="conditional-access-programmatic-access"></a>Koşullu erişim: programlı erişim

Birçok kuruluş, mümkün olduğunca kod gibi ortamlarının çoğunu yönetme ihtiyacını ifade ediyoruz. Microsoft Graph kullanarak, koşullu erişim ilkelerini ortamınızdaki diğer kod parçaları gibi işleme sağlayabilirsiniz.

Microsoft Graph, kuruluşların Microsoft 365, Windows 10 ve Enterprise Mobility + Security verilerle etkileşimde bulunmak için kullanabileceği Birleşik bir programlama modeli sağlar. Microsoft Graph hakkında daha fazla bilgi için, [Microsoft Graph genel bakış](/graph/overview)makalesine bakın.

![Grafiğin parçası olan birincil kaynakları ve ilişkileri gösteren bir görüntü](./media/howto-conditional-access-apis/microsoft-graph.png)

Aşağıdaki örnekler, desteği olmayan olarak sunulmaktadır. Bu örnekleri, kuruluşunuzda araç için temel olarak kullanabilirsiniz. 

Aşağıdaki örneklerin birçoğu [Yönetilen kimlikler](../managed-identities-azure-resources/overview.md), [Logic Apps](../../logic-apps/logic-apps-overview.md), [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage), [takımlar](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/)ve [Azure Key Vault](../../key-vault/general/overview.md)gibi araçları kullanır.

## <a name="configure"></a>Yapılandırma

### <a name="powershell"></a>PowerShell

Birçok yönetici için PowerShell zaten anlamış bir betik aracıdır. Aşağıdaki örnekte, koşullu erişim ilkelerini yönetmek için [Azure AD PowerShell modülünün](https://www.powershellgallery.com/packages/AzureAD) nasıl kullanılacağı gösterilmektedir.

- [Azure AD PowerShell komutlarıyla koşullu erişim ilkelerini yapılandırma](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>Graph API

Bu örnek, koşullu erişim Graph API 'Lerinde bulunan temel oluşturma, okuma, güncelleştirme ve silme (CRUD) seçeneklerini gösterir. Örnek, bazı örnek ilkeler oluşturmak için kullanabileceğiniz bazı JSON şablonlarını da içerir.

- [Koşullu erişim ilkelerini Microsoft Graph API çağrıları ile yapılandırma](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>Şablonları kullanarak yapılandırma

Bir şablon kullanarak, ön üretim ortamınızda koşullu erişim ilkeleri dağıtmak için koşullu erişim API 'Lerini kullanın.

- [Microsoft Graph API şablonlarıyla koşullu erişim ilkelerini yapılandırma](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>Test

Bu örnek, üretim ortamınız gibi bir ortamdan, koşullu erişim ilkelerini bir ortamdan diğerine kopyalayasağlayan onay iş akışlarıyla daha güvenli dağıtım uygulamalarını modeller.

- [Koşullu erişim ilkelerini test ortamlarından yükseltme](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>Dağıtma

Bu örnek, aşamalı bir dağıtım koşullu erişim ilkelerini Kullanıcı popülasyoninize aşamalı olarak gerçekleştirmeye yönelik bir mekanizma sağlar. bu sayede, destek etkisini yönetebilir ve sorunları erken yapabilirsiniz.

- [Koşullu erişim ilkelerini, onay iş akışlarıyla üretim ortamlarına dağıtma](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>İzleyici

Bu örnek, zaman içinde koşullu erişim ilkesi değişikliklerini izlemek için bir mekanizma sağlar ve anahtar ilkeleri değiştirildiğinde uyarıları tetikleyebilirsiniz.

- [Değişiklikler ve tetikleyici uyarıları için dağıtılan koşullu erişim ilkelerini izleme](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>Yönetme

### <a name="backup-and-restore"></a>Yedekleme ve geri yükleme

Bu örneği kullanarak ekipteki onaylarla koşullu erişim ilkelerinin yedeklenmesini ve geri yüklenmesini otomatikleştirin.

- [Microsoft Graph API çağrılarını kullanarak koşullu erişim ilkelerinin yedekleme ve geri yükleme işlemini yönetme](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>Acil durum erişim hesapları

Birden çok yönetici, koşullu erişim ilkeleri oluşturabilir ve [acil durum erişim hesaplarınızı](../roles/security-emergency-access.md) bu ilkelere bir dışlama olarak eklemeyi unutabilirler. Bu örnek, tüm ilkelerin belirlenmiş acil durum erişim hesaplarınızı içerecek şekilde güncelleştirilmesini sağlar.

- [Acil durum erişim hesaplarının Microsoft Graph API çağrılarını kullanarak koşullu erişim ilkelerine atanmasını yönetme](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>Beklenmedik durum planı yapma

Her zaman istediğiniz şekilde çalışmadığınızda, bu durumda işin devam edebildiği bir duruma geri dönmek için bir yol gerekir. Aşağıdaki örnek, ilkelerinizi bilinen iyi bir durum planına döndürmenin ve diğer koşullu erişim ilkelerini devre dışı bırakabilmeniz için bir yol sağlar.

- [Microsoft Graph API çağrılarını kullanarak koşullu erişim yedek ilkelerinin etkinleştirilmesini yönetme](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>Topluluk katkısı

Bu örnekler [GitHub deponuzda](https://github.com/Azure-Samples/azure-ad-conditional-access-apis)mevcuttur. Kapsamlı GitHub sorunları ve çekme Istekleri için topluluk katkılarını desteklemeye mutluluk duyuyoruz.

## <a name="next-steps"></a>Sonraki adımlar

- [Microsoft Graph’a genel bakış](/graph/overview)

- [Koşullu erişim API 'SI](/graph/api/resources/conditionalaccesspolicy)

- [Adlandırılmış Konum API 'SI](/graph/api/resources/namedlocation)
