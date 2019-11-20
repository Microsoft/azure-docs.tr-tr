---
title: Azure Güvenlik Merkezi 'nde Kullanıcı verilerini yönetme | Microsoft Docs
description: " Azure Güvenlik Merkezi 'nde Kullanıcı verilerini yönetmeyi öğrenin. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 0d05650ff349cb9e9750b0cefe0de217a986b32a
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254601"
---
# <a name="manage-user-data-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde Kullanıcı verilerini yönetme
Bu makalede, Azure Güvenlik Merkezi 'nde Kullanıcı verilerini nasıl yönetebileceğinizi gösteren bilgiler sağlanmaktadır. Kullanıcı verilerini yönetmek, verilere erişme, verileri silme veya dışa aktarma olanağını içerir.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Okuyucu, sahip, katkıda bulunan veya hesap yöneticisinin rolünü atayan bir güvenlik merkezi kullanıcısına araç içindeki müşteri verilerine erişim sağlayabilir. Hesap Yöneticisi rolü hakkında daha fazla bilgi edinmek için bkz. [Azure rol tabanlı erişim denetimi Için yerleşik roller](../role-based-access-control/built-in-roles.md) , okuyucu, sahip ve katkıda bulunan rolleri hakkında daha fazla bilgi edinin. Bkz. [Azure abonelik yöneticileri](../billing/billing-add-change-azure-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Kişisel verileri arama ve tanımlama
Bir güvenlik merkezi kullanıcısı, kişisel verilerini Azure portal aracılığıyla görüntüleyebilir. Güvenlik Merkezi yalnızca e-posta adresleri ve telefon numaraları gibi güvenlik iletişim bilgilerini depolar. Daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'nde güvenlik iletişim bilgilerini sağlama](security-center-provide-security-contact-details.md).

Azure portal, bir Kullanıcı, güvenlik merkezi 'nin tam zamanında VM erişimi özelliğini kullanarak izin verilen IP yapılandırmalarının görüntülemesini sağlayabilir. Daha fazla bilgi için bkz. [tam zamanında sanal makine erişimini yönetme](security-center-just-in-time.md).

Azure portal, bir Kullanıcı, Güvenlik Merkezi tarafından sunulan ve IP adresleri ve saldırgan ayrıntıları dahil güvenlik uyarılarını görüntüleyebilir. Daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Kişisel verileri sınıflandırma
Güvenlik Merkezi 'nin güvenlik ilgili kişisi özelliğinde bulunan kişisel verileri sınıflandırmanız gerekmez. Kaydedilen veriler bir e-posta adresi (veya birden fazla e-posta adresi) ve telefon numarasıdır. [İletişim verileri](security-center-provide-security-contact-details.md) Güvenlik Merkezi tarafından onaylanır.

Güvenlik Merkezi 'nin [tam zamanında](security-center-just-in-time.md) özelliği tarafından kaydedilen IP adreslerini ve bağlantı noktası numaralarını sınıflandırmanıza gerek yoktur.

Yalnızca yönetici rolü atanan bir Kullanıcı, güvenlik merkezi 'ndeki [uyarıları görüntüleyerek](security-center-managing-and-responding-alerts.md) kişisel verileri sınıflandırabilir.

## <a name="securing-and-controlling-access-to-personal-data"></a>Kişisel verilere erişimi güvenli hale getirme ve denetleme
Okuyucu, sahip, katkıda bulunan veya hesap yöneticisinin rolünü atayan bir güvenlik merkezi kullanıcısına [güvenlik ilgili kişi verilerine](security-center-provide-security-contact-details.md)erişim sağlayabilir.

Okuyucu, sahip, katkıda bulunan veya hesap yöneticisinin rolünü atayan bir güvenlik merkezi kullanıcısı, [tam zamanında](security-center-just-in-time.md) ilkelerine erişebilir.

Okuyucu, sahip, katkıda bulunan veya hesap yöneticisinin rolüne atanan bir güvenlik merkezi kullanıcısı [uyarılarını](security-center-managing-and-responding-alerts.md)görüntüleyebilir.

## <a name="updating-personal-data"></a>Kişisel verileri güncelleştirme
Sahip, katkıda bulunan veya hesap yöneticisinin rolünü atayan bir güvenlik merkezi kullanıcısına Azure portal aracılığıyla [güvenlik iletişim verileri](security-center-provide-security-contact-details.md) güncelleştirebilir.

Sahip, katkıda bulunan veya hesap yöneticisinin rolüne atanan bir güvenlik merkezi kullanıcısı, [tam zamanında ilkelerini](security-center-just-in-time.md)güncelleştirebilir.

Hesap Yöneticisi uyarı olaylarını düzenleyemez. Bir [uyarı olayı](security-center-managing-and-responding-alerts.md) güvenlik verileri olarak değerlendirilir ve salt okunurdur.

## <a name="deleting-personal-data"></a>Kişisel verileri silme
Sahip, katkıda bulunan veya hesap yöneticisinin rolünü atayan bir güvenlik merkezi kullanıcısına Azure portal aracılığıyla [güvenlik iletişim verileri](security-center-provide-security-contact-details.md) silinebilir.

Sahip, katkıda bulunan veya hesap yöneticisinin rolüne atanan bir güvenlik merkezi kullanıcısı, Azure portal aracılığıyla [tam zamanında ilkeleri](security-center-just-in-time.md) silebilir.

Bir güvenlik merkezi kullanıcısı uyarı olaylarını silemiyor. Güvenlik nedenleriyle, bir [uyarı olayı](security-center-managing-and-responding-alerts.md) salt okunurdur verileri kabul edilir.

## <a name="exporting-personal-data"></a>Kişisel verileri dışarı aktarma
Okuyucu, sahip, katkıda bulunan veya hesap yöneticisinin rolünü atayan bir güvenlik merkezi kullanıcısına, [güvenlik ilgili kişi verilerini](security-center-provide-security-contact-details.md) şu şekilde dışarı aktarabilirsiniz:

- Azure portal kopyalama
- Azure REST API çağrısı yürütülüyor, HTTP Al:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Hesap Yöneticisi rolünü atayan bir güvenlik merkezi kullanıcısı, IP adreslerini içeren [tam zamanında ilkelerini şu şekilde](security-center-just-in-time.md) dışarı aktarabilir:

- Azure portal kopyalama
- Azure REST API çağrısı yürütülüyor, HTTP Al:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Hesap Yöneticisi uyarı ayrıntılarını şu şekilde dışarı aktarabilir:

- Azure portal kopyalama
- Azure REST API çağrısı yürütülüyor, HTTP Al:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Daha fazla bilgi için bkz. [güvenlik uyarılarını edinme (KOLEKSIYONU al)](https://msdn.microsoft.com/library/mt704050.aspx).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Onay olmadan profil oluşturma veya pazarlama için kişisel verilerin kullanımını kısıtlama
Bir güvenlik merkezi kullanıcısı, [güvenlik iletişim verilerini](security-center-provide-security-contact-details.md)silerek devre dışı bırakabilirsiniz.

[Tam zamanında veriler](security-center-just-in-time.md) tanınabilir olmayan veriler olarak değerlendirilir ve 30 gün boyunca tutulur.

[Uyarı verileri](security-center-managing-and-responding-alerts.md) , güvenlik verileri olarak değerlendirilir ve iki yıl boyunca korunur.

## <a name="auditing-and-reporting"></a>Denetim ve raporlama
Güvenlik kişisinin denetim günlükleri, tam zamanında ve uyarı güncelleştirmeleri [Azure etkinlik günlüklerinde](../azure-monitor/platform/activity-logs-overview.md)saklanır.

## <a name="next-steps"></a>Sonraki adımlar
Kullanıcı verilerini yönetme hakkında daha fazla bilgi için bkz. [Azure Güvenlik Merkezi araştırmada bulunan Kullanıcı verilerini yönetme](security-center-investigation-user-data.md).
