---
title: Azure Güvenlik Merkezi 'ndeki önerileri düzeltin | Microsoft Docs
description: Bu makalede, Azure Güvenlik Merkezi 'ndeki kaynaklarınızın korunması ve güvenlik ilkeleriyle uyumlu olması için önerilerin nasıl düzeltileceği açıklanır.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5217c4c7b68c487d7285ec03700266ad2768606d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571518"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure Güvenlik Merkezi 'ndeki önerileri düzeltin

Öneriler, kaynaklarınızın daha iyi güvenliğini sağlamaya yönelik öneriler sağlar. Öneri bölümünde belirtilen düzeltme adımlarını izleyerek bir öneri uygulıyordu. 

## Düzeltme adımları<a name="remediation-steps"></a>

Tüm önerileri inceledikten sonra, hangisi önce düzeltileceğine karar verin. Öncelikle ne yapacaklarınızı önceliklendirmeye yardımcı olması için [güvenli puan etkisini](security-center-recommendations.md#monitor-recommendations) kullanmanızı öneririz.

1. Listeden öneri ' e tıklayın.

1. **Düzeltme adımları** bölümündeki yönergeleri izleyin. Her öneri kendi yönerge kümesine sahiptir. Aşağıdaki ekran görüntüsünde, uygulamaları yalnızca HTTPS üzerinden trafiğe izin verecek şekilde yapılandırmaya yönelik düzeltme adımları gösterilmektedir.

    ![Öneri ayrıntıları](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Tamamlandıktan sonra, düzeltme başarılı olursa bildirim görüntülenir.

## Hızlı düzeltme düzeltme<a name="one-click"></a>

Hızlı düzeltme, birden fazla kaynak üzerinde bir öneriyi hızlıca düzeltmenizi sağlar. Yalnızca belirli öneriler için kullanılabilir. Hızlı düzeltme, düzeltmeyi basitleştirir ve ortamınızın güvenliğini iyileştirmek için güvenli puanınızı hızla artırmanıza olanak sağlar.

Hızlı düzeltme düzeltmesini uygulamak için:

1. **Hızlı düzelme** sahip öneriler listesinden! etiket ' e tıklayın.

    [Hızlı düzelme ![seçin!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. **Sağlıksız kaynaklar** sekmesinden, öneriyi uygulamak istediğiniz kaynakları seçin ve **Düzelt**' e tıklayın. 

    > [!NOTE]
    > Listelenen bazı kaynaklar devre dışı bırakılabilir, çünkü bunları değiştirmek için uygun izinlere sahip değilsiniz.

1. Onay kutusunda düzeltme ayrıntılarını ve etkilerini okuyun. 

    ![Hızlı çözüm](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Bu etkiler, **Düzelt**' i tıkladıktan sonra açılan **kaynakları Düzelt** penceresindeki gri kutusunda listelenir. Hızlı düzeltme düzeltmesine devam eden değişikliklerin ne olduğunu listeler.

1. Gerekirse ilgili parametreleri ekleyin ve düzeltmeyi onaylayın.

    > [!NOTE]
    > **Sağlıklı kaynaklar** sekmesindeki kaynakları görmek için düzeltme tamamlandıktan sonra işlem birkaç dakika sürebilir. Düzeltme eylemlerini görüntülemek için [etkinlik günlüğünü](#activity-log)kontrol edin.

1. Tamamlandıktan sonra, düzeltme başarılı olursa bildirim görüntülenir.

## Etkinlik günlüğünde düzeltme günlüğü hızlı düzeltme<a name="activity-log"></a>

Düzeltme işlemi, kaynak üzerinde yapılandırmayı uygulamak için bir şablon dağıtımı veya REST PATCH API çağrısı kullanır. Bu işlemler [Azure etkinlik günlüğünde](../azure-resource-manager/resource-group-audit.md)günlüğe kaydedilir.


## <a name="recommendations-with-quick-fix-remediation"></a>Hızlı düzeltme düzeltme ile öneriler

|Öneri|Olsa|
|---|---|
|SQL Server 'lar üzerinde denetim etkinleştirilmelidir|Bu eylem, bu sunucularda ve veritabanlarında SQL denetimini etkinleştirir. <br>**Not**: <ul><li>Seçili SQL sunucularının her bir bölgesi için, Denetim günlüklerini kaydetmek üzere bir depolama hesabı bu bölgedeki tüm sunucular tarafından oluşturulur ve paylaşılır.</li><li>Doğru denetim sağlamak için kaynak grubunu veya depolama hesaplarını silmeyin veya yeniden adlandırmayın.</li></ul>|
|Gelişmiş veri güvenliği, SQL yönetilen örneklerinizin üzerinde etkinleştirilmelidir|Bu eylem, seçili SQL yönetilen örneklerinde SQL gelişmiş veri güvenliğini (ADS) etkinleştirir. <br>**Not**: <ul><li>Seçilen SQL yönetilen örneklerinin her bölgesi ve kaynak grubu için, tarama sonuçlarını kaydetmek üzere bir depolama hesabı bu bölgedeki tüm örnekler tarafından oluşturulur ve paylaşılır.</li><li> REKLAMLARı SQL tarafından yönetilen örnek başına $15 üzerinden ücretlendirilir.</li></ul>|
|SQL yönetilen örneklerinizin üzerinde güvenlik açığı değerlendirmesi etkinleştirilmelidir|Bu eylem, seçili SQL yönetilen örneklerinde SQL güvenlik açığı değerlendirmesini etkinleştirir. <br>**Not**:<ul><li>SQL güvenlik açığı değerlendirmesi, SQL gelişmiş veri güvenliği (ADS) paketinin bir parçasıdır. REKLAMLARı zaten etkinleştirilmemişse, yönetilen örnekte otomatik olarak etkinleştirilir.</li><li>Seçilen SQL yönetilen örneklerinin her bir bölgesi ve kaynak grubu için, tarama sonuçlarını depolamak için bir depolama hesabı bu bölgedeki tüm örnekler tarafından oluşturulur ve paylaşılır.</li><li>REKLAMLARı SQL Server başına $15 üzerinden ücretlendirilir.</li></ul>||
|Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir|Bu eylem, bu seçili sunucularda ve veritabanlarında gelişmiş veri güvenliği (ADS) sağlar. <br>**Not**:<ul><li>Seçili SQL sunucularının her bir bölgesi ve kaynak grubu için, tarama sonuçlarını depolamak için bir depolama hesabı bu bölgedeki tüm sunucular tarafından oluşturulur ve paylaşılır. <</li><li>REKLAMLARı SQL Server başına $15 üzerinden ücretlendirilir.</li></ul>||
|Güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir|Bu eylem, bu seçili sunucularda ve veritabanlarında SQL güvenlik açığı değerlendirmesini etkinleştirir. <br>**Not**:<ul><li>SQL güvenlik açığı değerlendirmesi, SQL gelişmiş veri güvenliği (ADS) paketinin bir parçasıdır. REKLAMLAR zaten etkin değilse, SQL Server 'da otomatik olarak etkinleştirilir.</li><li>Seçili SQL sunucularının her bir bölgesi ve kaynak grubu için, tarama sonuçlarını depolamak için bir depolama hesabı bu bölgedeki tüm örnekler tarafından oluşturulur ve paylaşılır.</li><li>REKLAMLARı SQL Server başına $15 üzerinden ücretlendirilir.</li></ul>||
|SQL veritabanlarında saydam veri şifrelemesi etkinleştirilmelidir|Bu eylem, seçili veritabanlarında SQL veritabanı Saydam Veri Şifrelemesi (TDE) etkinleştirilir. <br>**Note**: varsayılan olarak, hizmet tarafından yönetilen TDE anahtarları kullanılacaktır. 
|Depolama hesaplarına Güvenli aktarım etkinleştirilmelidir|Bu eylem, depolama hesabı güveninizi yalnızca güvenli bağlantılar tarafından isteklere izin verecek şekilde güncelleştirir. (HTTPS). <br>**Not**:<ul><li>HTTP kullanan istekler reddedilir.</li><li>Azure dosyaları hizmetini kullanırken, SMB 2,1, şifreleme olmadan SMB 3,0 ve Linux SMB istemcisinin bazı türleri dahil olmak üzere şifreleme olmadan bağlantı başarısız olur. Daha fazla bilgi edinin.</li></ul>|
|Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|Bu eylem, tüm trafiği seçili kaynaklarda HTTP 'den HTTPS 'ye yönlendirir. <br>**Not**:<ul><li>SSL sertifikası olmayan bir HTTPS uç noktası tarayıcıda ' Gizlilik hatası ' ile görünür. Bu nedenle, özel bir etki alanı olan kullanıcıların bir SSL sertifikası ayarladıklarından emin olması gerekir.</li><li>Uygulama hizmetini koruyan paket ve Web uygulaması güvenlik duvarlarının HTTPS oturumlarının iletilmesine izin ver ' i doğrulayın.</li></ul>|
|İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|Bu eylem, tüm trafiği seçili kaynaklarda HTTP 'den HTTPS 'ye yönlendirir. <br>**Not**:<ul><li>SSL sertifikası olmayan bir HTTPS uç noktası tarayıcıda ' Gizlilik hatası ' ile görünür. Bu nedenle, özel bir etki alanı olan kullanıcıların bir SSL sertifikası ayarladıklarından emin olması gerekir.</li><li>Uygulama hizmetini koruyan paket ve Web uygulaması güvenlik duvarlarının HTTPS oturumlarının iletilmesine izin ver ' i doğrulayın.</li></ul>|
|API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|Bu eylem, tüm trafiği seçili kaynaklarda HTTP 'den HTTPS 'ye yönlendirir. <br>**Not**:<ul><li>SSL sertifikası olmayan bir HTTPS uç noktası tarayıcıda ' Gizlilik hatası ' ile görünür. Bu nedenle, özel bir etki alanı olan kullanıcıların bir SSL sertifikası ayarladıklarından emin olması gerekir.</li><li>Uygulama hizmetini koruyan paket ve Web uygulaması güvenlik duvarlarının HTTPS oturumlarının iletilmesine izin ver ' i doğrulayın.</li></ul>|
|Web uygulaması için uzaktan hata ayıklama kapatılmalıdır|Bu eylem, uzaktan hata ayıklamayı devre dışı bırakır.|
|İşlev Uygulaması için uzaktan hata ayıklama kapatılmalıdır|Bu eylem, uzaktan hata ayıklamayı devre dışı bırakır.|
|API uygulaması için uzaktan hata ayıklama kapatılmalıdır|Bu eylem, uzaktan hata ayıklamayı devre dışı bırakır.|
|CORS, her kaynağın Web uygulamanıza erişmesine izin vermemelidir|Bu eylem, diğer etki alanlarının Web uygulamanıza erişmesini engeller. Belirli etki alanlarına izin vermek için, bu kaynakları Izin verilen Kaynaklar alanına (virgülle ayırarak) girin. <br>**Note**: alanı boş bırakmak, tüm çapraz kaynak çağrılarını engeller. ' param alan başlığı: ' izin verilen çıkış '|
|CORS, her kaynağın İşlev Uygulaması erişmesine izin vermemelidir|Bu eylem, diğer etki alanlarının Işlev uygulamanıza erişmesini engeller. Belirli etki alanlarına izin vermek için, bu kaynakları Izin verilen Kaynaklar alanına (virgülle ayırarak) girin. <br>**Note**: alanı boş bırakmak, tüm çapraz kaynak çağrılarını engeller. ' param alan başlığı: ' izin verilen çıkış '|
|CORS, her kaynağın API uygulamanıza erişmesine izin vermemelidir|Bu eylem, diğer etki alanlarının API uygulamanıza erişmesini engeller. Belirli etki alanlarına izin vermek için, bu kaynakları Izin verilen Kaynaklar alanına (virgülle ayırarak) girin. <br>**Note**: alanı boş bırakmak, tüm çapraz kaynak çağrılarını engeller. ' param alan başlığı: ' izin verilen çıkış '|
|İzleme Aracısı sanal makinelerinizde etkinleştirilmelidir|Bu eylem, seçili sanal makinelere bir izleme Aracısı kurar. Aracının raporlamak için bir çalışma alanı seçin.<ul><li>Güncelleştirme ilkeniz otomatik olarak ayarlandıysa, yeni mevcut örneklere dağıtılır.</li><li>Güncelleştirme ilkeniz el ile olarak ayarlandıysa ve aracıyı mevcut örneklere yüklemek isterseniz onay kutusu seçeneğini belirleyin. [Daha fazla bilgi](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Key Vault tanılama günlükleri etkinleştirilmelidir|Bu eylem, anahtar kasalarında tanılama günlüklerini sunar. Tanılama günlükleri ve ölçümleri seçili çalışma alanına kaydedilir.|
|Service Bus 'daki tanılama günlükleri etkinleştirilmelidir|Bu eylem, Service Bus 'da tanılama günlüklerini sunar. Tanılama günlükleri ve ölçümleri seçili çalışma alanına kaydedilir.|

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, güvenlik merkezi 'nde önerilerin nasıl düzeltileceği gösterildi. Güvenlik Merkezi hakkında daha fazla bilgi edinmek için aşağıdaki konulara bakın:

* [Azure Güvenlik Merkezi 'nde güvenlik Ilkelerini ayarlama](tutorial-security-policy.md) -Azure abonelikleriniz ve kaynak gruplarınız için güvenlik ilkelerini yapılandırma hakkında bilgi edinin.
* [Azure Güvenlik Merkezi 'Nde güvenlik durumu izleme](security-center-monitoring.md) -Azure kaynaklarınızın sistem durumunu izlemeyi öğrenin.