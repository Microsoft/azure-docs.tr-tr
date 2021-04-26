---
title: Uygulama değişikliği Analizi sorunlarını giderme-Azure Izleyici
description: Uygulama değişikliği analizinde sorunları nasıl giderebileceğinizi öğrenin.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 7200978ce31a47f7bd0d023cecf359b10a1c96de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100521184"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>Uygulama değişiklik Analizi sorunlarını giderme (Önizleme)

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>Microsoft 'a kaydolurken sorun yaşıyor. Değişiklik geçmişi sekmesinden analiz kaynak sağlayıcısını değiştirme

Uygulama değişikliği analizi ile tümleştirmesinden sonra değişiklik geçmişini ilk kez görüntülediğinizde, **Microsoft. ChangeAnalysis** adlı bir kaynak sağlayıcısını otomatik olarak kaydettiğini görürsünüz. Nadir durumlarda, aşağıdaki nedenlerden dolayı başarısız olabilir:

- **Microsoft. ChangeAnalysis kaynak sağlayıcısını kaydetmek için yeterli izniniz** yok. Bu hata iletisi, geçerli abonelikteki rolünüzün onunla ilişkili **Microsoft. support/Register/Action** kapsamına sahip olmadığı anlamına gelir. Bu durum, bir aboneliğin sahibi değilseniz ve bir iş arkadaşıyla (bir kaynak grubuna erişimi görüntüleme) paylaşılan erişim izinlerine sahip değilseniz meydana gelebilir. Bunu yapmak için, **Microsoft. ChangeAnalysis** kaynak sağlayıcısı 'nı kaydetmek üzere aboneliğinizin sahibine başvurabilirsiniz. Bu, abonelikler üzerinden Azure portal yapılabilir **| Kaynak sağlayıcıları** ve ```Microsoft.ChangeAnalysis``` Kullanıcı arabiriminde arama yapın, veya Azure PowerShell ya da Azure CLI aracılığıyla kaydolun.

    Kaynak sağlayıcısını PowerShell aracılığıyla Kaydet:
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Microsoft. ChangeAnalysis kaynak sağlayıcısı kaydettirilemedi**. Bu ileti, kaynak sağlayıcısını kaydetmek için Kullanıcı arabirimi gönderme isteği olarak hemen başarısız olan bir şeydir ve izin sorunuyla ilgili değildir. Büyük olasılıkla, geçici bir internet bağlantısı sorunu olabilir. Sayfayı yenilemeyi ve internet bağlantınızı kontrol etmeyi deneyin. Hata devam ederse, iletişime geçin changeanalysishelp@microsoft.com

- **Bu, beklenenden uzun sürüyor**. Bu ileti, kaydın 2 dakikadan uzun sürmesi anlamına gelir. Bu olağan dışı bir şeydir, ancak yanlış bir sorun olduğu anlamına gelmez. Abonelikler 'e gidebilirsiniz **|** **Microsoft. changeanalysis** kaynak sağlayıcısı kayıt durumunu denetlemek için kaynak sağlayıcısı. ' Nin yardımcı olup olmadığını görmek için kaydını silmek, yeniden kaydetmek veya yenilemek üzere Kullanıcı arabirimini kullanmayı deneyebilirsiniz. Sorun devam ederse destek 'e başvurun changeanalysishelp@microsoft.com .
    ![RP kaydı çok uzun sürüyor sorunlarını giderme](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![Sorun giderme araçları seçiliyken bir sanal makine için sorunları Tanıla ve çöz aracının ekran görüntüsü.](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Bir sanal makine için son değişiklikleri çözümle sorun giderme aracının kutucuğunun ekran görüntüsü.](./media/change-analysis/analyze-recent-changes.png)

## <a name="azure-lighthouse-subscription-is-not-supported"></a>Azure Aydınlathouse aboneliği desteklenmiyor

- **Microsoft. ChangeAnalysis kaynak sağlayıcısı ile sorgulama başarısız oldu** *, Azure Use aboneliği desteklenmiyor, değişiklikler yalnızca aboneliğin ana kiracısında kullanılabilir*. Değişiklik Analizi kaynak sağlayıcısı 'nın, ev kiracısında olmayan kullanıcılar için Azure ışıklı kullanım aboneliği aracılığıyla kaydedilmesi için bir sınırlama vardır. Bu sınırlamayı ele almak için çalışıyoruz. Sizin için bir engelleyici sorun varsa, hizmet sorumlusu oluşturmayı ve erişime izin vermek üzere rolü açıkça atamayı içeren bir geçici çözüm vardır.  changeanalysishelp@microsoft.comHakkında daha fazla bilgi edinmek için iletişim kurun.

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>Değişiklikler alınırken bir hata oluştu. Lütfen bu sayfayı yenileyin veya daha sonra değişiklikleri görüntülemek için geri dönün

Bu, değişiklikler yüklenemediği zaman uygulama değişiklik Analizi hizmeti tarafından sunulan genel hata iletisidir. Bilinen bazı nedenler şunlardır:

- İstemci cihazından Internet bağlantısı hatası
- Değişiklik Analizi hizmeti, birkaç dakika sonra bu sorunu düzelttiğinde geçici olarak devre dışı bırakılıyor. Hata devam ederse, iletişime geçin changeanalysishelp@micorosoft.com

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>Bazı değişiklikleri görüntülemek için yeterli izniniz yok. Azure abonelik yöneticinize başvurun

Bu, geçerli kullanıcının değişikliği görüntülemek için yeterli izinlere sahip olmadığı belirten genel yetkisiz hata iletisidir. Azure Kaynak Grafiği tarafından döndürülen altyapı değişikliklerini görüntülemek için kaynak üzerinde en az okuyucu erişimi gerekir ve Azure Resource Manager. Web uygulamasının Konuk dosya değişiklikleri ve yapılandırma değişiklikleri için en az katkıda bulunan rolü gereklidir.

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Microsoft. ChangeAnalysis kaynak sağlayıcısı kaydettirilemedi

Bu ileti, kaynak sağlayıcısını kaydetmek için Kullanıcı arabirimi gönderme isteği olarak hemen başarısız olan bir şeydir ve izin sorunuyla ilgili değildir. Büyük olasılıkla, geçici bir internet bağlantısı sorunu olabilir. Sayfayı yenilemeyi ve internet bağlantınızı kontrol etmeyi deneyin. Hata devam ederse, iletişime geçin changeanalysishelp@microsoft.com

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Microsoft. ChangeAnalysis kaynak sağlayıcısını kaydetmek için yeterli izniniz yok. Azure abonelik yöneticinize başvurun

Bu hata iletisi, geçerli abonelikteki rolünüzün onunla ilişkili **Microsoft. support/Register/Action** kapsamına sahip olmadığı anlamına gelir. Bu durum, bir aboneliğin sahibi değilseniz ve bir iş arkadaşıyla (bir kaynak grubuna erişimi görüntüleme) paylaşılan erişim izinlerine sahip değilseniz meydana gelebilir. Bunu yapmak için, **Microsoft. ChangeAnalysis** kaynak sağlayıcısı 'nı kaydetmek üzere aboneliğinizin sahibine başvurabilirsiniz. Bu, abonelikler üzerinden Azure portal yapılabilir **| Kaynak sağlayıcıları** ve ```Microsoft.ChangeAnalysis``` Kullanıcı arabiriminde arama yapın, veya Azure PowerShell ya da Azure CLI aracılığıyla kaydolun.

Kaynak sağlayıcısını PowerShell aracılığıyla Kaydet:

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>Sonraki adımlar

- Power Change analizine yardımcı olan [Azure Kaynak Grafiği](../../governance/resource-graph/overview.md)hakkında daha fazla bilgi edinin.