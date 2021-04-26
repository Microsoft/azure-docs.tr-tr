---
title: Azure AD Connect Health-sistem sağlığı hizmeti verileri güncel değil uyarısı | Microsoft Docs
description: Bu belgede "sağlık hizmeti verileri güncel değil" uyarısı ve sorun giderme sorunu anlatılmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00518eb91e57efaacb7abc63b6ad4531619be2ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012878"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Sistem sağlığı hizmeti verileri güncel değil uyarısı

## <a name="overview"></a>Genel Bakış

Azure AD Connect Health izleyen şirket içi makinelerde bulunan aracılar, düzenli aralıklarla verileri Azure AD Connect Health hizmetine yükler. Hizmet bir aracıdan veri almazsa, portalın sunduğu bilgiler eski olacaktır. Sorunu vurgulamak için, hizmet **sistem sağlığı hizmeti verilerinin güncel** bir uyarı olduğunu tetiklayacaktır. Bu uyarı, hizmet son iki saat içinde tüm verileri almadığında oluşturulur.  

- Sistem Sağlığı Hizmeti, son iki saatte sunucudan gönderilen **kısmi** veri türleri aldıysa **Uyarı** durumu uyarısı ateşlenir. Uyarı durumu uyarısı, yapılandırılmış alıcılara e-posta bildirimlerini tetiklemez. 
- Sistem Sağlığı Hizmeti, son iki saatte sunucudan herhangi bir veri türü almamışsa **hata** durumu uyarısı ateşlenir. Hata durumu uyarısı, yapılandırılmış alıcılara e-posta bildirimlerini tetikler.

Hizmet, hizmet türüne bağlı olarak, şirket içi makinelerde çalışan aracılardan verileri alır. Aşağıdaki tabloda makinede çalışan aracılar, ne yaptıkları ve hizmetin oluşturduğu veri türleri listelenmektedir. Bazı durumlarda, süreçte yer alan birden çok hizmet bulunur, bu nedenle bunlardan herhangi biri olabilir. 

## <a name="understanding-the-alert"></a>Uyarıyı anlama

Uyarı **ayrıntıları** dikey penceresi, uyarının ne zaman oluştuğunu ve en son algılanmadığını gösterir. Her iki saatte bir çalışan bir arka plan işlemi, uyarıyı oluşturur ve yeniden değerlendirir. Aşağıdaki örnekte, ilk uyarı 03/10 ' de 9:59 ' de gerçekleşti. Uyarı yeniden değerlendiriliyorsa uyarı 10:00 ' de hala 03/12 ' de vardı. Dikey pencere Ayrıca Sistem Sağlığı Hizmeti belirli bir veri türü aldığı son süreyi de ayrıntılardır. 
 
 ![Azure AD Connect Health Uyarı ayrıntıları](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Aşağıdaki tabloda, hizmet türleri ilgili gerekli veri türleriyle eşlenir:

| Hizmet türü | Aracı (Windows hizmeti adı) | Amaç | Oluşturulan veri türü  |
| --- | --- | --- | --- |  
| Azure AD Connect (eşitleme) | Azure AD Connect Health Eşitleme Öngörü Hizmeti | AAD Connect 'e özgü bilgileri toplayın (bağlayıcılar, eşitleme kuralları vb.) | -AadSyncService-SynchronizationRules <br />  -AadSyncService-Connectors <br /> -AadSyncService-GlobalConfigurations  <br />  -AadSyncService-RunProfileResults <br /> -AadSyncService-ServiceConfigurations <br /> -AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Eşitleme İzleme Hizmeti | AAD Connect 'e özgü performans sayaçlarını, ETW izlemelerini, dosyaları toplayın | Performans sayacı |
| AD DS | Azure AD Connect Health AD DS Insights Hizmeti | Yapay testler gerçekleştirme, topoloji bilgilerini toplama, çoğaltma meta verileri |  -Ekler-Topologyıınfo-JSON <br /> -Common-TestData-JSON (test sonuçlarını oluşturur)   | 
|  | Azure AD Connect Health AD DS İzleme Hizmeti | EKLEMELERI özel performans sayaçlarını, ETW izlemelerini, dosyaları toplayın | -Performans sayacı  <br /> -Common-TestData-JSON (test sonuçlarını yükler)  |
| AD FS | Azure AD Connect Health AD FS Tanılama Hizmeti | Yapay testler gerçekleştirme | TestResult (test sonuçlarını oluşturur) | 
| | Azure AD Connect Health AD FS Öngörü Hizmeti  | ADFS kullanım ölçümlerini topla | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS İzleme Hizmeti | ADFS 'e özgü performans sayaçlarını, ETW izlemelerini, dosyaları toplayın | TestResult (test sonuçlarını yükler) |

## <a name="troubleshooting-steps"></a>Sorun giderme adımları 

Sorunu tanılamak için gereken adımlar aşağıda verilmiştir. İlki, tüm hizmet türlerinde ortak olan temel denetimler kümesidir. 

> [!IMPORTANT] 
> Bu uyarı, durum [verilerini bekletme Ilkesi](reference-connect-health-user-privacy.md#data-retention-policy) bağlantısını izler

* Aracıların en son sürümlerinin yüklü olduğundan emin olun. [Sürüm yayınlama geçmişini](reference-connect-health-version-history.md) görüntüleyin. 
* Makinede Azure AD Connect Health Aracıları hizmetlerinin **çalıştığından** emin olun. Örneğin AD FS için Connect Health'in üç hizmeti olmalıdır.
  ![Azure AD Connect Health'i doğrulama](./media/how-to-connect-health-agent-install/install5.png)

* [Gereksinimler bölümünü](how-to-connect-health-agent-install.md#requirements)ziyaret edin ve bunları karşıladığınızdan emin olun.
* Bağlantı sorunlarını saptamak için [Test Bağlantısı aracını](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) kullanın.
* Bir HTTP proxy 'Si varsa, bu [yapılandırma adımlarını](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)izleyin. 


## <a name="next-steps"></a>Sonraki adımlar
Yukarıdaki adımlardan herhangi biri bir sorun tanımladığında, düzeltin ve uyarının çözülmesini bekleyin. Uyarı arka plan işlemi 2 saatte bir çalışır, bu nedenle uyarının çözülmesi 2 saate kadar sürer. 

* [Azure AD Connect Health veri bekletme ilkesi](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health ile ilgili SSS](reference-connect-health-faq.md)
