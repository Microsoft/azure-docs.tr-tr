---
title: Azure Otomasyonu ve Log Analytics çalışma alanı eşlemeleri
description: Bu makalede, bir Otomasyon hesabı ile Log Analytics çalışma alanı arasında çözümü desteklemeye izin verilen eşlemeler açıklanmaktadır
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd4500b4ef6492f0b6499bb1e9aa1a773313e860
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498360"
---
# <a name="workspace-mappings"></a>Çalışma alanı eşlemeleri

Güncelleştirme Yönetimi, Değişiklik İzleme ve envanter gibi çözümleri veya çalışma saatleri dışında VM 'Leri Başlat/Durdur çözümü etkinleştirirken, bir Log Analytics çalışma alanı ve bir Otomasyon hesabı bağlamak için yalnızca belirli bölgeler desteklenir. Bu eşleme yalnızca Automation hesabı ve Log Analytics çalışma alanı için geçerlidir. Otomasyon hesabınıza veya Log Analytics çalışma alanınıza rapor veren kaynaklar diğer bölgelerde bulunabilir.

## <a name="supported-mappings"></a>Desteklenen eşlemeler

Aşağıdaki tabloda desteklenen eşlemeler gösterilmektedir:

|**Log Analytics çalışma alanı bölgesi**|**Azure Otomasyonu bölgesi**|
|---|---|
|**ABD**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Kanada**||
|Canadaorta|Canadaorta|
|**Asya Pasifik**||
|AustraliaSoutheast|AustraliaSoutheast|
|Güneydoğu|Güneydoğu|
|Merkezileştirme Hindistan|Merkezileştirme Hindistan|
|JapanEast|JapanEast|
|**Avrupa**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> EastUS Log Analytics çalışma alanları Için Otomasyon hesaplarına yönelik eşleme, bölge eşlemesinin tam bir bölgesi değildir ancak doğru eşleme olur.

<sup>2</sup> kapasite depoları nedeniyle bölge, yeni kaynaklar oluşturulurken kullanılamaz. Bu, Otomasyon hesaplarını ve Log Analytics çalışma alanlarını içerir. Ancak, bölgede önceden var olan bağlı kaynakların çalışmaya devam etmesi gerekir.

## <a name="unlink-workspace"></a>Çalışma alanının bağlantısını kaldır

Artık Otomasyon hesabınızı bir Log Analytics çalışma alanıyla tümleştirmenize karar verirseniz, Hesabınızın bağlantısını doğrudan Azure portal kaldırabilirsiniz. Devam etmeden önce, ilk olarak Güncelleştirme Yönetimi, Değişiklik İzleme ve envanteri ya da bunları kullanıyorsanız, çalışma saatleri dışında VM 'Leri Başlat/Durdur ' u kaldırmanız gerekir. Bunları kaldırmayın, bu işlemin devam etmesi engellenir. Kaldırmak için gereken adımları anlamak için, içeri aktardığınız belirli çözüme yönelik makaleyi gözden geçirin.

Bu çözümleri kaldırdıktan sonra, Otomasyon Hesabınızın bağlantısını kaldırmak için aşağıdaki adımları uygulayabilirsiniz.

> [!NOTE]
> Azure SQL izleme çözümünün önceki sürümleri de dahil olmak üzere bazı çözümler Otomasyon varlıkları oluşturmuş olabilir ve çalışma alanının bağlantısı kaldırılmadan önce de kaldırılması gerekebilir.

1. Azure portal Otomasyon hesabınızı açın ve Otomasyon hesabı sayfasında, sol taraftaki **Ilgili kaynaklar** bölümünde **bağlantılı çalışma alanı** ' nı seçin.

2. Çalışma alanının bağlantısını Kaldır sayfasında, **çalışma alanının bağlantısını kaldır**' a tıklayın. Devam etmek istediğinizi doğrulayan bir istem alacaksınız.

3. Azure Otomasyonu, Log Analytics çalışma alanınızdaki hesabın bağlantısını kaldırmayı denediğinde, ilerleme durumunu menüdeki **Bildirimler** bölümünden izleyebilirsiniz.

Güncelleştirme Yönetimi çözümünü kullandıysanız, isteğe bağlı olarak, çözümü kaldırdıktan sonra artık gerekli olmayan aşağıdaki öğeleri kaldırmak isteyebilirsiniz.

* Zamanlamayı Güncelleştir-her birinin, oluşturduğunuz güncelleştirme dağıtımlarıyla eşleşen adları olur)

* Çözüm için oluşturulan karma çalışan grupları-her biri, benzer `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`şekilde adlandırılır).

VM 'Leri çalışma saatleri dışında Başlat/Durdur çözümünü kullandıysanız, isteğe bağlı olarak, çözümü kaldırdıktan sonra artık gerekli olmayan aşağıdaki öğeleri kaldırmak isteyebilirsiniz.

* VM runbook zamanlamalarını başlatma ve durdurma
* VM runbook 'larını başlatma ve durdurma
* Değişkenler

Alternatif olarak, Log Analytics çalışma alanınızdan Otomasyon hesabınızdan çalışma alanınızın bağlantısını da kaldırabilirsiniz. Çalışma alanınızda **Ilgili kaynaklar**altında **Otomasyon hesabı** ' nı seçin. Otomasyon hesabı sayfasında **Hesap bağlantısını kaldır**' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki çözümleri eklemeyi öğrenin:

Güncelleştirme Yönetimi ve Değişiklik İzleme ve envanter:

* Bir [sanal makineden](../automation-onboard-solutions-from-vm.md)
* [Otomasyon hesabınızdan](../automation-onboard-solutions-from-automation-account.md)
* [Birden çok makineye gözatarken](../automation-onboard-solutions-from-browse.md)
* Bir [runbook](../automation-onboard-solutions.md) 'tan

Hizmetin kapalı olduğu saatlerde Sanal Makineleri Başlatma/Durdurma

* [VM 'Leri çalışma saatleri dışında Başlat/Durdur](../automation-solution-vm-management.md)
