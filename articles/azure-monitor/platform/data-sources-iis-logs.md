---
title: Azure Izleyici 'de IIS günlükleri | Microsoft Docs
description: Internet Information Services (IIS), Azure Izleyici tarafından toplanabilecek günlük dosyalarındaki Kullanıcı etkinliklerini depolar.  Bu makalede, Azure Izleyici 'de oluşturdukları kayıtların IIS günlüklerinin ve ayrıntılarının nasıl yapılandırılacağı açıklanır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: a865f43585ccbb31569e2ca0987aae62a89a9281
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932497"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Azure Izleyici 'de IIS günlükleri toplama
Internet Information Services (IIS), Azure Izleyici tarafından toplanabilecek ve [günlük verileri](data-platform.md)olarak depolanan günlük dosyalarındaki Kullanıcı etkinliklerini depolar.

![IIS günlükleri](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>IIS günlüklerini yapılandırma
Azure Izleyici, IIS tarafından oluşturulan günlük dosyalarından girişler toplar, bu nedenle [IIS 'yi günlüğe kaydetmek için yapılandırmanız](https://technet.microsoft.com/library/hh831775.aspx)gerekir.

Azure Izleyici yalnızca W3C biçiminde depolanan IIS günlük dosyalarını destekler ve özel alanları veya IIS gelişmiş günlüğe kaydetmeyi desteklemez. NCSA veya IIS yerel biçiminde Günlükler toplanmaz.

[Gelişmiş ayarlar menüsünden](agent-data-sources.md#configuring-data-sources)Azure IZLEYICI 'de IIS günlüklerini yapılandırın.  **W3C BIÇIM IIS günlük dosyalarını topla**seçeneğinin belirlenmesi dışında bir yapılandırma gerekmez.


## <a name="data-collection"></a>Veri toplama
Azure Izleyici, günlük zaman damgasının her değiştirilişinde her bir aracıdan IIS günlük girişleri toplar. Günlük her **5 dakikada**bir okunurdur. Herhangi bir nedenden dolayı IIS, yeni bir dosya oluşturulduğunda geçiş zamanından önce zaman damgasını güncelleştirmezse, yeni dosya oluşturulduktan sonra girdiler toplanır. Yeni dosya oluşturma sıklığı, varsayılan olarak günde bir kez olan IIS sitesi için **günlük dosyası aktarma zamanlaması** ayarıyla denetlenir. Ayar **saatlik**ise, Azure İzleyici günlüğü her saat toplar. Ayar **günlük**Ise Azure izleyici, günlüğü her 24 saatte bir toplar.


## <a name="iis-log-record-properties"></a>IIS günlük kaydı özellikleri
IIS günlük kayıtları bir tür **W3CIISLog** ve aşağıdaki tabloda bulunan özelliklere sahiptir:

| Özellik | Açıklama |
|:--- |:--- |
| Bilgisayar |Olayın toplandığı bilgisayarın adı. |
| CIP |İstemcinin IP adresi. |
| csMethod |İsteğin GET veya POST gibi yöntemi. |
| csReferer |Kullanıcının geçerli siteye bir bağlantıyı takip ettiği site. |
| csUserAgent |İstemcinin tarayıcı türü. |
| csUserName |Sunucuya erişen kimliği doğrulanmış kullanıcının adı. Anonim kullanıcılar bir tire ile belirtilir. |
| csUriStem |İsteğin hedefi bir Web sayfası. |
| csUriQuery |Varsa, istemcisinin gerçekleştirmeye çalıştığı sorgu. |
| ManagementGroupName |Operations Manager aracıları için yönetim grubunun adı.  Diğer aracılar için bu, AOI-\<çalışma alanı KIMLIĞIDIR\> |
| RemoteIPCountry |İstemcinin IP adresinin ülkesi/bölgesi. |
| RemoteIPLatitude |İstemci IP adresinin enlem. |
| RemoteIPLongitude |İstemci IP adresinin boylam. |
| scStatus |HTTP durum kodu. |
| Scalt durum |Alt durum hata kodu. |
| scWin32Status |Windows durum kodu. |
| Istemcinin |Web sunucusunun IP adresi. |
| SourceSystem |OpsMgr |
| Spor |İstemcinin bağlandığı sunucudaki bağlantı noktası. |
| sSiteName |IIS sitesinin adı. |
| TimeGenerated |Girişin günlüğe kaydedildiği tarih ve saat. |
| timeTaken |İsteğin milisaniye cinsinden işlenmesi için geçen süre. |

## <a name="log-queries-with-iis-logs"></a>IIS günlükleri ile sorguları günlüğe kaydet
Aşağıdaki tabloda, IIS günlük kayıtlarını alan günlük sorgularının farklı örnekleri verilmiştir.

| Sorgu | Açıklama |
|:--- |:--- |
| W3CIISLog |Tüm IIS günlük kayıtları. |
| W3CIISLog &#124; burada scstatus = = 500 |Tüm IIS günlük kayıtları 500 dönüş durumuna sahiptir. |
| W3CIISLog &#124; özetleme sayısı (), CIP |İstemci IP adresine göre IIS günlük girişi sayısı. |
| W3CIISLog &#124; WHERE cshost = = "www\.contoso.com" &#124; özetleme sayısı () csUriStem |Ana Bilgisayar www\.contoso.com için URL 'ye göre IIS günlük girişi sayısı. |
| W3CIISLog &#124; özetleme toplamı (csbytes) bilgisayar &#124; tarafından 500000 al |Her IIS bilgisayarı tarafından alınan toplam bayt sayısı. |

## <a name="next-steps"></a>Sonraki adımlar
* Azure Izleyici 'yi analize yönelik diğer [veri kaynaklarını](agent-data-sources.md) toplayacak şekilde yapılandırın.
* Veri kaynaklarından ve çözümlerinden toplanan verileri analiz etmek için [günlük sorguları](../log-query/log-query-overview.md) hakkında bilgi edinin.
