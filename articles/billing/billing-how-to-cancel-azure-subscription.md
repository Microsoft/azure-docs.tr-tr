---
title: Azure aboneliğinizi iptal etme | Microsoft Docs
description: Ücretsiz Deneme aboneliği gibi Azure aboneliğinizin nasıl iptal edileceğini açıklar
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 0c681a3374529c4f973c7cd247ad8d387682b564
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719706"
---
# <a name="cancel-your-azure-subscription"></a>Azure aboneliğinizi iptal etme

Artık abonelik gerekmiyorsa, Azure portalında Azure aboneliğinizi iptal edebilirsiniz. 

Aboneliğinizi iptal etmeden önce:
* Verilerinizi yedekleyin. Örneğin, verileri Azure depolama alanında veya SQL’de depoluyorsanız bir kopya indirin. Sanal makineniz varsa, bir görüntüsünü yerel olarak kaydedin.
* Hizmetlerinizi kapatın. [Yönetim portalında kaynaklar sayfasına](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) gidin ve tüm çalışan sanal makineleri, uygulamaları ve diğer hizmetleri **Durdurun**.
* Verilerinizi geçirmeyi düşünün. Bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/resource-group-move-resources.md).
* Tüm kaynakları ve tüm kaynak gruplarını silin. Bir aboneliği iptal edebilmeniz için önce bunların silinmesi gerekir. Her kaynak grubunun ayrı ayrı silinmesi gerekir. Kaynak grubu silinirken, kaynak grubu adını yazarak silme işlemini onaylamanız gerekir.
* `AssignableScopes` içinde bu aboneliğe başvuran özel bir rolünüz varsa, aboneliği kaldırmak için bu özel rolleri güncelleştirmeniz gerekir. Bir aboneliği iptal ettikten sonra özel bir rolü güncelleştirmeye çalışırsanız bir hata alabilirsiniz. Daha fazla bilgi için bkz. [Özel rollerle ilgili sorunları giderme](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) ve [Azure kaynakları için özel roller](../role-based-access-control/custom-roles.md).

Ücretli bir Azure Desteği planını iptal ederseniz abonelik döneminin kalan bölümü için faturalandırılırsınız. Daha fazla bilgi için bkz. [Azure destek planları](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Azure portalında aboneliği iptal etme

1. [Azure portalındaki abonelik sayfasından](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) aboneliğinizi seçin.
2. İptal etmek istediğiniz aboneliği seçin.
3. **Genel Bakış**’ı ve sonra **Aboneliği iptal et**’i seçin.
    ![İptal düğmesini gösteren ekran görüntüsü](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Yönergeleri izleyerek iptal etme işlemini tamamlayın.


## <a name="who-can-cancel-a-subscription"></a>Aboneliği kim iptal edebilir?

Aşağıdaki tabloda, bir aboneliği iptal etmek için gereken izinler açıklanmaktadır.

|Abonelik türü     |İptal edebilen kişi  |
|---------|---------|
|Azure web sitesi üzerinden Azure’a kaydolduğunuzda oluşturulan abonelikler. Örneğin, bir [Ücretsiz Azure Hesabına](https://azure.microsoft.com/offers/ms-azr-0044p/), [kullandıkça öde fiyatlarına tabi hesaba](https://azure.microsoft.com/offers/ms-azr-0003p/) veya [Visual Studio abonesi](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) olarak kaydolduğunuzda. |  Aboneliğin hesap yöneticisi, sahipleri ve katkıda bulunanları  |
|[Microsoft Kurumsal Anlaşma](https://azure.microsoft.com/pricing/enterprise-agreement/) ve [Kurumsal Geliştirme ve Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Aboneliğin hesap sahibi, sahipleri ve katkıda bulunanları       |
|[Azure planı](https://azure.microsoft.com/offers/ms-azr-0017g/) ve [DevTest için Azure planı](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Aboneliğin sahipleri ve katkıda bulunanları      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>Aboneliğimi iptal edersem ne olur?

İptal etmenizin ardından faturalama hemen durdurulur. Ancak iptalin portalda gösterilmesi 10 dakika kadar sürebilir. Bir faturalama döneminin ortasında iptal ederseniz, son faturayı dönem sona erdikten sonra normal fatura tarihinizde göndeririz.

İptal etmenizin ardından hizmetleriniz devre dışı bırakılır. Başka bir deyişle, sanal makineleriniz serbest bırakılır, geçici IP adresleri serbest bırakılır ve depolama salt okunur olur.

Verilerinize erişmeniz gerekmesi veya fikrinizi değiştirmeniz durumunda verilerinizi kalıcı olarak silmeden önce 90 gün bekleriz. Verileri saklamak için size ücret yansıtmayız. Daha fazla bilgi edinmek için bkz. [Microsoft Güven Merkezi - Verilerinizi nasıl yönetiriz?](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)

## <a name="reactivate-subscription"></a>Aboneliği yeniden etkinleştirme

Kullandıkça Öde fiyatlarına tabi aboneliğinizi yanlışlıkla iptal ederseniz, [Hesap Merkezinde bunu yeniden etkinleştirebilirsiniz](billing-subscription-become-disable.md).

Aboneliğiniz, Kullandıkça Öde fiyatlarına tabi bir abonelik değilse, aboneliğinizi yeniden etkinleştirmek için iptali izleyen 90 gün içinde desteğe başvurun.

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).
