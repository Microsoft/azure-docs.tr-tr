---
title: Azure Otomasyonu Değişiklik İzleme ve envanteri için uyarı oluşturma
description: Bu makalede, Azure uyarılarını Değişiklik İzleme ve envanter tarafından algılanan değişikliklerin durumu hakkında bilgilendirmek üzere nasıl yapılandıracağınız açıklanır.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 91be2f8641a061d009962cdcd03a8d56048594da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594514"
---
# <a name="how-to-create-alerts-for-change-tracking-and-inventory"></a>Değişiklik İzleme ve envanter için uyarı oluşturma

Azure 'daki uyarılar, runbook işlerinin, hizmet durumu sorunlarının veya Otomasyon hesabınızla ilgili diğer senaryolardan sonuçları size bildirir. Azure Otomasyonu önceden yapılandırılmış uyarı kuralları içermez, ancak oluşturduğu verilere bağlı olarak kendi kendinize de oluşturabilirsiniz. Bu makale, Değişiklik İzleme ve envanter tarafından tanımlanan değişikliklere dayalı olarak uyarı kuralları oluşturmaya yönelik rehberlik sağlar.

Azure Izleyici uyarılarını bilmiyorsanız, başlamadan önce bkz. [Microsoft Azure uyarılara genel bakış](../../azure-monitor/alerts/alerts-overview.md) . Günlük sorgularını kullanan uyarılar hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici 'de günlük uyarıları](../../azure-monitor/alerts/alerts-unified-log.md).

## <a name="create-alert"></a>Uyarı oluşturma

Aşağıdaki örnek, bir makinede **c:\Windows\system32\drivers\etc\hosts** dosyasının değiştirildiğini gösterir. Windows bunu, ana bilgisayar adlarını IP adreslerine çözümlemek üzere kullandığından, bu dosya önemlidir. Bu işlem DNS 'den önceliklidir ve bağlantı sorunlarına yol açabilir. Ayrıca, trafiğin kötü amaçlı ya da başka türlü tehlikeli Web sitelerine yönlendirilmesine de yol açabilir.

![Hosts dosyası değişikliğini gösteren grafik](./media/configure-alerts/changes.png)

Bu örneği bir değişiklik üzerinde uyarı oluşturma adımlarını tartışmak için kullanalım.

1. Otomasyon hesabınızdan **değişiklik izleme** sayfasında **Log Analytics**' yi seçin.

2. Günlükler aramasında, **Hosts** dosyasında bulunan içerik değişikliklerini sorgu ile arayın `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . Bu sorgu, sözcüğü içeren tam nitelikli yol adlarına sahip dosyalar için içerik değişikliklerini arar `hosts` . Ayrıca, örneğin kullanarak yol bölümünü tam nitelikli biçimde değiştirerek belirli bir dosyayı sorabilirsiniz `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Sorgu sonuçları geri döndüğünde, **uyarı oluşturma** sayfasını açmak için günlük aramasında **Yeni uyarı kuralı** ' nı seçin. Ayrıca, Azure portal **Azure izleyici** aracılığıyla bu sayfaya gidebilirsiniz.

4. Sorgunuzu yeniden denetleyin ve uyarı mantığını değiştirin. Bu durumda, ortamdaki tüm makinelerde tek bir değişiklik algılanırsa uyarının tetiklenmesi istersiniz.

    ![Ana bilgisayar dosyasındaki değişiklikleri izlemek için sorguya Değiştir](./media/configure-alerts/change-query.png)

5. Uyarı mantığı ayarlandıktan sonra, uyarının tetiklenme yanıt olarak eylem gerçekleştirmek için eylem grupları atayın. Bu durumda, gönderilecek e-postaları ve oluşturulacak bir BT hizmet yönetimi (ıTSM) anahtarını ayarlıyoruz.

Güncelleştirme dağıtımının durumunu bilmenizi sağlamak üzere uyarıları ayarlamak için aşağıdaki adımları izleyin. Azure uyarılarınız hakkında daha fazla bilgi için bkz. [Azure Alerts 'e genel bakış](../../azure-monitor/alerts/alerts-overview.md).

## <a name="configure-action-groups-for-your-alerts"></a>Uyarılarınız için eylem gruplarını yapılandırma

Uyarılarınız yapılandırıldıktan sonra, birden çok uyarı arasında kullanılacak bir eylem grubu olan bir eylem grubu ayarlayabilirsiniz. Eylemler e-posta bildirimleri, runbook 'lar, Web kancaları ve çok daha fazlasını içerebilir. Eylem grupları hakkında daha fazla bilgi edinmek için bkz. [Eylem grupları oluşturma ve yönetme](../../azure-monitor/alerts/action-groups.md).

1. Bir uyarı seçin ve ardından **Yeni oluştur** **eylem grupları**' nı seçin.

2. Eylem grubu için bir tam ad ve kısa bir ad girin. Güncelleştirme Yönetimi, belirtilen grubu kullanarak bildirim gönderirken kısa adı kullanır.

3. **Eylemler**' in altında, eylemi belirten bir ad girin, örneğin **e-posta bildirimi**.

4. **Eylem türü** için uygun türü seçin (örneğin, **e-posta/SMS ileti/gönderim/ses**).

5. Eylem ayrıntılarını düzenlemek için kalem simgesini seçin.

6. Eylem türü için bölmeyi girin. Örneğin, e-posta göndermek için **e-posta/SMS iletisi/gönderim/ses** kullanılıyorsa, bir eylem adı girin, **e-posta** kutusunu seçin, geçerli bir e-posta adresi girin ve ardından **Tamam**' ı seçin.

    ![E-posta eylem grubu yapılandırma](./media/configure-alerts/configure-email-action-group.png)

7. Eylem grubu ekle bölmesinde **Tamam**'ı seçin.

8. Bir uyarı e-postası için e-posta konusunu özelleştirebilirsiniz. **Kural oluştur** altında **eylemleri Özelleştir** ' i seçin ve **e-posta konusu**' ı seçin

9. İşleminiz bittiğinde **Uyarı kuralı oluştur**'u seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure izleyici 'de uyarılar](../../azure-monitor/alerts/alerts-overview.md)hakkında daha fazla bilgi edinin.

* Log Analytics çalışma alanından verileri almak ve analiz etmek için [günlük sorguları](../../azure-monitor/logs/log-query-overview.md) hakkında bilgi edinin.

* [Azure Izleyici günlükleri ile kullanımı ve maliyetleri](../../azure-monitor/logs/manage-cost-storage.md) yönetme, veri saklama dönemini değiştirerek maliyetlerinizi nasıl denetleyeceğinizi ve veri kullanımınızın nasıl analiz edileceğini ve uyarılanacağını açıklar.