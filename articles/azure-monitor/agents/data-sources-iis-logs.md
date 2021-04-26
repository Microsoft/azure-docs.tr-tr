---
title: Azure Izleyici 'de Log Analytics Agent ile IIS günlükleri toplama
description: Internet Information Services (IIS), Azure Izleyici tarafından toplanabilecek günlük dosyalarındaki Kullanıcı etkinliklerini depolar.  Bu makalede, Azure Izleyici 'de oluşturdukları kayıtların IIS günlüklerinin ve ayrıntılarının nasıl yapılandırılacağı açıklanır.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 87cb19daa23c9fcca601771a9fe168b98be02627
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586276"
---
# <a name="collect-iis-logs-with-log-analytics-agent-in-azure-monitor"></a>Azure Izleyici 'de Log Analytics Agent ile IIS günlükleri toplama
Internet Information Services (IIS), Log Analytics Aracısı tarafından toplanabilecek ve [Azure Izleyici günlüklerinde](../data-platform.md)depolanan günlük dosyalarındaki Kullanıcı etkinliklerini depolar.

> [!IMPORTANT]
> Bu makalede, Azure Izleyici tarafından kullanılan aracılardan biri olan [Log Analytics ARACıSıYLA](./log-analytics-agent.md) IIS günlüklerinin toplanması ele alınmaktadır. Diğer aracılar farklı veriler toplar ve farklı şekilde yapılandırılır. Kullanılabilir aracıların ve toplayabilecekleri verilerin bir listesi için bkz. [Azure izleyici aracılarına genel bakış](../agents/agents-overview.md) .

![IIS günlükleri](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>IIS günlüklerini yapılandırma
Azure Izleyici, IIS tarafından oluşturulan günlük dosyalarından girişler toplar, bu nedenle [IIS 'yi günlüğe kaydetmek için yapılandırmanız](/previous-versions/orphan-topics/ws.11/hh831775(v=ws.11))gerekir.

Azure İzleyici yalnızca W3C biçiminde depolanan IIS günlük dosyalarını destekler ve özel alanları veya IIS Gelişmiş Günlüğünü desteklemez. NCSA veya IIS yerel biçimindeki günlükleri toplamaz.

Azure Izleyici 'de Log Analytics Aracısı için [Aracı yapılandırma menüsünden](../agents/agent-data-sources.md#configuring-data-sources) IIS günlüklerini yapılandırın.  **W3C BIÇIM IIS günlük dosyalarını topla** seçeneğinin belirlenmesi dışında bir yapılandırma gerekmez.


## <a name="data-collection"></a>Veri toplama
Azure İzleyici, günlük zaman damgası her değiştiğinde her aracıdan IIS günlük girdilerini toplar. Günlük her **5 dakikada** bir okunurdur. Herhangi bir nedenden dolayı IIS, yeni bir dosya oluşturulduğunda geçiş zamanından önce zaman damgasını güncelleştirmezse, yeni dosya oluşturulduktan sonra girdiler toplanır. Yeni dosya oluşturma sıklığı, varsayılan olarak günde bir kez olan IIS sitesi için **günlük dosyası aktarma zamanlaması** ayarıyla denetlenir. Ayar **saatlik** ise, Azure İzleyici günlüğü her saat toplar. Ayar **günlük** Ise Azure izleyici, günlüğü her 24 saatte bir toplar.

> [!IMPORTANT]
> **Günlük dosyası geçiş zamanlamasını** **saatlik** olarak ayarlamanız önerilir. **Günlük** olarak ayarlandıysa, verilerinizde yalnızca bir kez toplandığından bu yana verilerinize ilişkin ani artışlar yaşayabilirsiniz.

## <a name="iis-log-record-properties"></a>IIS günlük kaydı özellikleri
IIS günlük kayıtları bir tür **W3CIISLog** ve aşağıdaki tabloda bulunan özelliklere sahiptir:

| Özellik | Açıklama |
|:--- |:--- |
| Bilgisayar |Olayın toplandığı bilgisayarın adı. |
| CIP |İstemcinin IP adresi. |
| csMethod |İsteğin GET veya POST gibi yöntemi. |
| csReferer |Kullanıcının geçerli siteye bir bağlantıyı takip ettiği site. |
| csUserAgent |İstemcinin tarayıcı türü. |
| csUserName |Sunucuya erişen kimliği doğrulanmış kullanıcının adı. Anonim kullanıcılar bir tire işaretiyle gösterilir. |
| csUriStem |İsteğin hedefi bir Web sayfası. |
| csUriQuery |Varsa, istemcisinin gerçekleştirmeye çalıştığı sorgu. |
| ManagementGroupName |Operations Manager aracıları için yönetim grubunun adı.  Diğer aracılar için, bu AOI 'dir\<workspace ID\> |
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
| TimeTaken |İsteğin milisaniye cinsinden işlenmesi için geçen süre. |
| csHost | Ana bilgisayar adı. |
| csBytes | Sunucunun aldığı bayt sayısı. |

## <a name="log-queries-with-iis-logs"></a>IIS günlükleri ile sorguları günlüğe kaydet
Aşağıdaki tabloda, IIS günlük kayıtlarını alan günlük sorgularının farklı örnekleri verilmiştir.

| Sorgu | Description |
|:--- |:--- |
| W3CIISLog |Tüm IIS günlük kayıtları. |
| W3CIISLog &#124; burada scStatus = = 500 |Tüm IIS günlük kayıtları 500 dönüş durumuna sahiptir. |
| W3CIISLog &#124;, CIP tarafından Count () özetleme |İstemci IP adresine göre IIS günlük girişi sayısı. |
| W3CIISLog &#124;, csHost = = "www \. contoso.com" &#124; Count () öğesini csUriStem ile özetleme |Host www contoso.com için URL 'ye göre IIS günlük girişi sayısı \. . |
| W3CIISLog &#124; toplam (csBytes) bilgisayar &#124; 500000 al |Her IIS bilgisayarı tarafından alınan toplam bayt sayısı. |

## <a name="next-steps"></a>Sonraki adımlar
* Azure Izleyici 'yi analize yönelik diğer [veri kaynaklarını](../agents/agent-data-sources.md) toplayacak şekilde yapılandırın.
* Veri kaynaklarından ve çözümlerinden toplanan verileri analiz etmek için [günlük sorguları](../logs/log-query-overview.md) hakkında bilgi edinin.
