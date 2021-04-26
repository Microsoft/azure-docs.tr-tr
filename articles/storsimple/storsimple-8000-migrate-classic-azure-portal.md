---
title: StorSimple Device Manager depolama hesaplarını, abonelikleri geçirin
description: StorSimple Device Manager hizmetiniz için abonelikler, depolama hesapları geçirmeyi öğrenin.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 523788dc727fc5fa32b2f57250fb738352ff6d31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491529"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>StorSimple Cihaz Yöneticisi hizmetiyle ilişkilendirilmiş abonelikleri ve depolama hesaplarını geçirme

StorSimple hizmetinizi yeni bir kayda veya yeni bir aboneliğe taşımanız gerekebilir. Bu geçiş senaryoları, hesap değişiklikleri ya da veri merkezi değişiklerdir. Taşıma adımları dahil olmak üzere bu senaryolardan hangisinin desteklendiğini anlamak için aşağıdaki tabloyu kullanın.

## <a name="account-changes"></a>Hesap değişiklikleri

| Hareket edebilir...| Desteklenir| Downtime| Azure destek süreci| Yaklaşım|
|-----|-----|-----|-----|-----|
| Bir aboneliğin tamamı (StorSimple hizmeti ve depolama hesapları dahil) başka bir kayda mı? | Yes       | Hayır       | **Kayıt aktarımı**<br>Kullanırsınız<li>Yeni bir sözleşme çerçevesinde yeni bir Azure taahhüt satın aldığınızda.</li><li>Tüm hesapları ve abonelikleri eski kayıttan yenisine geçirmek istiyorsunuz. Bu, eski abonelik kapsamındaki tüm Azure hizmetlerini içerir.</li> | **1. Adım: bir Azure Kurumsal Işlem destek bileti açın.**<li>Adresine gidin [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) .</li><li> **Kayıt yönetimi** ' ni seçin ve ardından **bir kayıttan yeni kayda aktar**' ı seçin.<br>**2. Adım: istenen bilgileri sağlayın**<br>İçeriyor<li>kaynak kayıt numarası</li><li> hedef kayıt numarası</li><li>Aktarım geçerlilik tarihi|
| Mevcut bir hesaptan yeni bir kayda StorSimple hizmeti mi?    | Yes       | Hayır       | **Hesap aktarımı**<br>Kullanırsınız<li>Tam kayıt aktarımı istemezsiniz.</li><li>Yalnızca belirli hesapları yeni bir kayda taşımak istiyorsunuz.</li>| **1. Adım: bir Azure Kurumsal Işlem destek bileti açın.**<li>Adresine gidin [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) .</li><li>**Kayıt yönetimi** ' ni seçin ve ardından **bir EA hesabını yeni** bir kayda Aktar ' ı seçin.<br>**2. Adım: istenen bilgileri sağlayın**<br>İçeriyor<li>kaynak kayıt numarası</li><li> hedef kayıt numarası</li><li>Aktarım geçerlilik tarihi|
| Bir aboneliğden başka bir aboneliğe StorSimple hizmeti mi?      | Hayır        |    Yes         | Hiçbiri, el ile işlem|<li>StorSimple cihazından veri geçirin.</li><li>Cihazın fabrika sıfırlamasını gerçekleştirin, bu, cihazdaki tüm yerel verileri siler.</li><li>Yeni aboneliği StorSimple Device Manager hizmetine kaydedin.</li><li>Verileri cihaza geri geçirin.|
|Bir Azure aboneliğinin sahipliğini başka bir dizine aktarabilir miyim? | Yes       | Hayır       | Mevcut bir aboneliği Azure AD dizininizle ilişkilendirin | [Mevcut bir aboneliği Azure AD dizininizle ilişkilendirme](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)bölümüne bakın. Her şeyin doğru şekilde gösterilmesi 10 dakikaya kadar sürebilir.|
| Tek bir StorSimple Device Manager hizmetinden farklı bir bölgedeki başka bir hizmete StorSimple cihazı mı kullanıyorsunuz?      | Hayır        | Yes            | Hiçbiri, el ile işlem |Yukarıdakiyle aynıdır.|
| Depolama hesabı yeni bir aboneliğe veya kaynak grubuna mı?     | Yes        | Hayır             |Depolama hesabını farklı bir aboneliğe veya kaynak grubuna taşıyın |Taşıma işleminden sonra, depolama hesabı erişim anahtarları güncelleştirilirse, kullanıcının, StorSimple Device Manager hizmeti aracılığıyla, geçirilen depolama hesabı için erişim anahtarlarını el ile yapılandırması gerekir.|
| Azure Resource Manager depolama hesabına klasik depolama hesabı      | Yes        | Hayır             |Klasikten Azure Resource Manager’a geçiş |<li>Bir depolama hesabını klasik ' ten Azure Resource Manager geçirme hakkında ayrıntılı yönergeler için, [Klasik depolama hesabını geçirme](../virtual-machines/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account)' ye gidin.</li><li> Depolama hesabı erişim anahtarları geçişten sonra güncelleştirilirse, kullanıcının, StorSimple Device Manager hizmeti aracılığıyla geçirilmiş depolama hesabı için erişim anahtarlarını eşitlemesi gerekecektir. Bu, StorSimple cihazlarının normal şekilde çalışmaya devam etmesini ve birincil/yedekleme verilerini Azure 'a katabilebilmesini sağlamaktır. Erişim anahtarlarını eşitlemeye ilişkin ayrıntılı yönergeler için, [döndürme iş akışına](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)gidin.</li><li> StorSimple Cloud Appliance söz konusu olduğunda, klasik depolama hesabı geçirilir ancak temeldeki sanal makine yine de klasik halde kalırsa, Gereç düzgün şekilde çalışır. Bulut gereci için temel alınan sanal makine geçirilirse, devre dışı bırak ve Sil işlevselliği çalışmaz.</li><li> Azure portal yeni bir StorSimple bulut gereçini oluşturmanız ve daha sonra eski bulut gereçlerinde yük devredebilmeniz gerekir. Klasik depolama hesabı kullanarak yeni Azure portal StorSimple Cloud Appliance oluşturamazsınız, bir Azure Resource Manager depolama hesabına sahip olmaları gerekir. Daha fazla bilgi için, [StorSimple Cloud Appliance dağıtma ve yönetme](storsimple-8000-cloud-appliance-u2.md)konusuna gidin.</li>|

## <a name="datacenter-changes"></a>Veri merkezi değişiklikleri

| Hareket edebilir...| Desteklenir|Downtime| Azure destek süreci| Yaklaşım|
|-----|-----|-----|-----|-----|
| Bir Azure veri merkezinden diğerine bir StorSimple hizmeti mi kullanıyorsunuz? | Hayır | Yes |Hiçbiri, el ile işlem  |<li>StorSimple cihazından veri geçirin.</li><li>Cihazın fabrika sıfırlamasını gerçekleştirin, bu, cihazdaki tüm yerel verileri siler.</li><li>Yeni bir StorSimple Device Manager hizmetine yeni aboneliğe sahip cihazı kaydedin.</li><li>Verileri cihaza geri geçirin.|
| Bir Azure veri merkezinden diğerine bir depolama hesabı mı var? | Hayır |Yes  |Hiçbiri, el ile işlem  | Yukarıdakiyle aynıdır.|

## <a name="next-steps"></a>Sonraki adımlar

* [StorSimple Device Manager hizmetini dağıtma](storsimple-8000-manage-service.md)
* [Azure portal StorSimple 8000 serisi cihazı dağıtma](storsimple-8000-deployment-walkthrough-u2.md)