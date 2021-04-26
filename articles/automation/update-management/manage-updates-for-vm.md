---
title: Azure Otomasyonu 'nda VM 'niz için güncelleştirmeleri ve düzeltme eklerini yönetme
description: Bu makalede, Azure ve Azure olmayan sanal makinelerinize yönelik güncelleştirmeleri ve düzeltme eklerini yönetmek için Güncelleştirme Yönetimi nasıl kullanılacağı açıklanır.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: c86c9049bc0afc81f5dfd8553d2aa98cfd4b1a46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98915991"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>VM 'niz için güncelleştirmeleri ve düzeltme eklerini yönetme

Azure Otomasyonu Güncelleştirme Yönetimi Yazılım güncelleştirmeleri, Azure ve hibrit bulutundaki makinelere yazılım güncelleştirmelerini izleme ve uygulamanın karmaşık görevi yönetimine yardımcı olabilecek bir dizi araç ve kaynak sağlar. İşlemsel verimliliği sürdürmek, güvenlik sorunlarının üstesinden gelmek ve artırılmış siber güvenlik tehditlerine karşı riskleri azaltmak için etkili bir yazılım güncelleştirme yönetimi işlemi gereklidir. Ancak, teknolojinin değişen yapısı ve yeni güvenlik tehditlerinin sürekli ortaya çıkması nedeniyle, etkili yazılım güncelleştirme yönetimi tutarlı ve sürekli bir ilgi gerektirir.

> [!NOTE]
> Güncelleştirme Yönetimi, birinci taraf güncelleştirmelerin dağıtımını ve bunların önceden indirilmesini destekler. Bu destek, güncelleştirilmekte olan sistemlerde değişiklik yapılmasını gerektirir. Bu ayarları sistemlerinizde nasıl yapılandıracağınızı öğrenmek için bkz. [Azure Automation güncelleştirme yönetimi için Windows Update ayarlarını yapılandırma](configure-wuagent.md) .

Sanal makinelerinize yönelik güncelleştirmeleri yönetmeyi denemeden önce, aşağıdaki yöntemlerden birini kullanarak Güncelleştirme Yönetimi üzerinde etkin olduğunuzdan emin olun:

* [Otomasyon hesabından Güncelleştirme Yönetimi’ni etkinleştirme](enable-from-automation-account.md)
* [Azure portal göz atarak Güncelleştirme Yönetimi etkinleştirin](enable-from-portal.md)
* [Runbook’tan Güncelleştirme Yönetimi’ni etkinleştirme](enable-from-runbook.md)
* [Azure VM’den Güncelleştirme Yönetimi’ni etkinleştirme](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Dağıtımın kapsamını sınırlandırma

Güncelleştirme Yönetimi, güncelleştirmeleri almak için bilgisayarları hedeflemek üzere çalışma alanı içinde bir kapsam yapılandırması kullanır. Daha fazla bilgi için bkz. [sınır güncelleştirme yönetimi dağıtım kapsamı](scope-configuration.md).

## <a name="compliance-assessment"></a>Uyumluluk değerlendirmesi

Makinelerinize yazılım güncelleştirmeleri dağıtmadan önce, etkinleştirilmiş makineler için güncelleştirme uyumluluğu değerlendirmesi sonuçlarını gözden geçirin. Her yazılım güncelleştirmesi için, uyumluluk durumu kaydedilir ve değerlendirme tamamlandıktan sonra Azure Izleyici günlüklerine toplu olarak toplanır ve iletilir.

Bir Windows makinesinde, uyumluluk taraması varsayılan olarak her 12 saatte bir çalıştırılır ve Windows için Log Analytics Aracısı başlatıldığında 15 dakika içinde başlatılır. Değerlendirme verileri daha sonra çalışma alanına iletilir ve **güncelleştirmeler** tablosu yenilenir. Güncelleştirme yüklemesinden önce ve sonra, eksik güncelleştirmeleri belirlemek için bir güncelleştirme uyumluluğu taraması yapılır, ancak sonuçlar tablodaki değerlendirme verilerini güncelleştirmek için kullanılmaz.

Doğru yönetilmesini engelleyen sorunlardan kaçınmak için [Windows Update istemcisinin güncelleştirme yönetimi yapılandırma](configure-wuagent.md) önerilerini gözden geçirmeniz önemlidir.

Bir Linux makinesi için, uyumluluk taraması her saat varsayılan olarak gerçekleştirilir. Linux için Log Analytics Aracısı yeniden başlatılırsa, 15 dakika içinde bir uyumluluk taraması başlatılır.

Uyumluluk sonuçları, değerlendirilen her makine için Güncelleştirme Yönetimi olarak sunulur. Panonun yönetim için etkinleştirilen yeni bir makineden güncelleştirilmiş verileri görüntülemesi 30 dakika kadar sürebilir.

Uyumluluk sonuçlarının nasıl görüntüleneceğini öğrenmek için [yazılım güncelleştirmelerini izleyin](view-update-assessments.md) ' i gözden geçirin.

## <a name="deploy-updates"></a>Güncelleştirmeleri dağıt

Uyumluluk sonuçlarını inceledikten sonra yazılım güncelleştirme dağıtım aşaması, yazılım güncelleştirmelerini dağıtma işlemidir. Güncelleştirmeleri yüklemek için, sürüm zamanlamanız ve hizmet pencerenize göre hizalanan bir dağıtım zamanlayın. Dağıtıma hangi güncelleştirme türlerinin dahil edileceğini seçebilirsiniz. Örneğin, kritik güncelleştirmeleri veya güvenlik güncelleştirmelerini dahil edip güncelleştirme paketlerini dışlayabilirsiniz.

Güncelleştirme dağıtımını zamanlamayı öğrenmek için [yazılım güncelleştirmelerini dağıtma](deploy-updates.md) konusunu gözden geçirin.

## <a name="review-update-deployments"></a>Güncelleştirme dağıtımlarını gözden geçirme

Dağıtım tamamlandıktan sonra, makineye veya hedef gruba göre güncelleştirme dağıtımının başarısını belirleme işlemini gözden geçirin. Dağıtım durumunu nasıl izleyebileceğinizi öğrenmek için bkz. [dağıtım durumunu gözden geçirme](deploy-updates.md#check-deployment-status) .

## <a name="next-steps"></a>Sonraki adımlar

* Dağıtım sonuçlarını güncelleştirme hakkında sizi bilgilendirmek üzere uyarılar oluşturmayı öğrenmek için bkz. [güncelleştirme yönetimi uyarı oluşturma](configure-alerts.md).

* Güncelleştirme değerlendirmelerini, dağıtımları ve diğer ilgili yönetim görevlerini çözümlemek için [Azure izleyici günlüklerini sorgulayabilirsiniz](query-logs.md) . Başlamanıza yardımcı olmak için önceden tanımlanmış sorgular içerir.