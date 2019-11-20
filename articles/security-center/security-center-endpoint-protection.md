---
title: Azure Güvenlik merkezlerindeki Endpoint Protection önerileri
description: Endpoint Protection çözümlerinin nasıl keşfedildiği ve sağlıklı olduğu nasıl tanımlandığı.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2019
ms.author: memildin
ms.openlocfilehash: 140361b7ba3a6a618d4c416447525f8a73690b81
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748435"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde Endpoint Protection değerlendirmesi ve önerileri

Azure Güvenlik Merkezi, Endpoint Protection çözümlerinin [desteklenen](https://docs.microsoft.com/azure/security-center/security-center-os-coverage) sürümlerinin sistem durumu değerlendirmeleri sağlar. Bu makalede, güvenlik merkezi 'nin aşağıdaki iki öneriyi üretmesine neden olan senaryolar açıklanmaktadır:

* **Uç nokta koruma çözümlerini sanal makinenize yükler**
* **Makinelerinizdeki Endpoint Protection sistem durumu sorunlarını çözün**

## <a name="windows-defender"></a>Windows Defender

* Güvenlik Merkezi, [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) çalıştırıldığında ve Result **amserviceenabled** olduğunda **"Endpoint Protection çözümlerini sanal makineye yüklemenizi"** önerir.

* Güvenlik Merkezi, [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) çalıştırıldığında ve aşağıdakilerden biri gerçekleştiğinde **"makinelerinizdeki Endpoint Protection sistem durumu sorunlarını çözme"** işlemi yapmanızı önerir:

  * Aşağıdaki özelliklerden herhangi biri false şeklindedir:

    **AMServiceEnabled**

    **AntispywareEnabled**

    **RealTimeProtectionEnabled**

    **BehaviorMonitorEnabled**

    **Ioavprotectionenabled**

    **OnAccessProtectionEnabled**

  * Aşağıdaki özelliklerden biri veya her ikisi 7 veya daha fazla.

    **AntispywareSignatureAge**

    **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center Endpoint Protection

* Güvenlik Merkezi, **Scepmpmodule ("$env:P rogramFiles\Microsoft Security Client\mpprovider\mpprovider.exe")** içeri aktarırken ve çalışırken **"Endpoint Protection çözümlerini sanal makineye yüklemenizi" önerir.** **Amserviceenabled = false** Ile Get-MProtComputerStatus sonuçları

* Güvenlik Merkezi, **Get-MprotComputerStatus** çalıştırıldığında ve aşağıdakilerden biri gerçekleştiğinde **"makinelerinizdeki Endpoint Protection sistem durumu sorunlarını çözme"** işlemi yapmanızı önerir:

    * Aşağıdaki özelliklerden en az biri false şeklindedir:

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * Aşağıdaki Imza güncelleştirmelerinden biri veya her ikisi 7 ' ye eşit veya daha büyükse. 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Güvenlik Merkezi, aşağıdaki denetimlerden herhangi biri karşılanmazsa **"Endpoint Protection çözümlerini sanal makineye yüklemenizi"** önerir:
    * **HKLM: \ Software\trendmikro \ derin güvenlik Aracısı** var
    * **HKLM: \ Software\trendmikro \ derin güvenlik alanı \ ınstalyüklemeklasörü** var
    * **Dsa_query. cmd** dosyası yükleme klasöründe bulunur
    * **Dsa_query. cmd** Ile sonuçları **bileşen. mod. Mode: eğilim üzerine mikro derin güvenlik Aracısı algılandı**

## <a name="symantec-endpoint-protection"></a>Symantec uç nokta koruması
Güvenlik Merkezi, aşağıdaki denetimlerden herhangi biri karşılanmazsa **"Endpoint Protection çözümlerini sanal makineye yüklemenizi"** önerir:

* **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Veya

* **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Güvenlik Merkezi, aşağıdaki denetimlerden herhangi biri karşılanmazsa **"makinelerinizdeki Endpoint Protection sistem durumu sorunlarını çözme"** yapmanızı önerir:

* Symantec sürümünü denetleyin > = 12: kayıt defteri konumu: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion "-değer" PRODUCTVERSION "**

* Gerçek zamanlı koruma durumunu denetle: **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff = = 1**

* Imza güncelleştirme durumunu denetle: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 gün**

* Tam tarama durumunu denetle: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\currentversion\public-opstate\lastbaşarılı fulscandatetime < = 7 gün**

* Symantec 12 için imza sürümüne ait imza sürüm numarası yolunu bul: **kayıt defteri yolları + "CurrentVersion\SharedDefs"-değer "SRTSP"** 

* Symantec 14 için imza sürümü yolu: **kayıt defteri yolları + "CurrentVersion\SharedDefs\SDSDefs"-Value "SRTSP"**

Kayıt defteri yolları:

* **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>Windows için McAfee Endpoint Protection

Güvenlik Merkezi, aşağıdaki denetimlerden herhangi biri karşılanmazsa **"Endpoint Protection çözümlerini sanal makineye yüklemenizi"** önerir:

* **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** var

* **HKLM: \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Güvenlik Merkezi, aşağıdaki denetimlerden herhangi biri karşılanmazsa **"makinelerinizdeki Endpoint Protection sistem durumu sorunlarını çözme"** yapmanızı önerir:

* McAfee sürümü: **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion > = 10**

* Imza bulma sürümü: **HKLM: \ Software\McAfee\AVSolution\DS\DS-value "dwContentMajorVersion"**

* Imza bulma tarihi: **HKLM: \ Software\McAfee\AVSolution\DS\DS-value "szContentCreationDate" > = 7 gün**

* Tarama tarihini bul: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-value "LastFullScanOdsRunTime" > = 7 gün**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Linux tehdit önleme için McAfee Endpoint Security 

Güvenlik Merkezi, aşağıdaki denetimlerden herhangi biri karşılanmazsa **"Endpoint Protection çözümlerini sanal makineye yüklemenizi"** önerir:

- **/Opt/iSEC/ens/threatprevention/bin/isecav** dosyası çıkıyor 

- **"/opt/iSEC/ens/threatprevention/bin/isecav--Version"** çıkışı: **McAfee Name = Linux tehdit önleme ve McAfee sürümü Için mcafee Endpoint Security > = 10**

Güvenlik Merkezi, aşağıdaki denetimlerden herhangi biri karşılanmazsa **"makinelerinizdeki Endpoint Protection sistem durumu sorunlarını çözme"** yapmanızı önerir:

- **"/opt/iSEC/ens/threatprevention/bin/isecav--listtask"** , taramanın her Ikisini de **hızlı tarama, tam tarama** ve her ikisi de döndürür < = 7 gün

- **"/opt/iSEC/ens/threatprevention/bin/isecav--listtask"** , **dat ve Engine güncelleştirme zamanını** ve her ikisini de döndürür < = 7 gün

- **"/opt/iSEC/ens/threatprevention/bin/isecav--getoasconfig--Summary"** , **erişim taraması** durumunda döndürür

## <a name="sophos-antivirus-for-linux"></a>Linux için Sophos Antivirus 

Güvenlik Merkezi, aşağıdaki denetimlerden herhangi biri karşılanmazsa **"Endpoint Protection çözümlerini sanal makineye yüklemenizi"** önerir:

- **/Opt/Sophos-av/bin/savdstatus** dosyası çıkıyor veya özelleştirilmiş **"readlink $ (savscan)"** konumunu arar "

- **"/opt/Sophos-av/bin/savdstatus--Version"** , Sophos Name = **Sophos Anti-Virus ve Sophos Version > = 9** döndürür

Güvenlik Merkezi, aşağıdaki denetimlerden herhangi biri karşılanmazsa **"makinelerinizdeki Endpoint Protection sistem durumu sorunlarını çözme"** yapmanızı önerir:

- **"/opt/Sophos-av/bin/savlog--maxAge = 7 | GREP-i "zamanlanmış tarama.\* tamamlandı "| Tail-1 "** , bir değer döndürür

- **"/opt/Sophos-av/bin/savlog--maxAge = 7 | grep "tarama tamamlandı"** | Tail-1 ", bir değer döndürür

- **"/opt/Sophos-av/bin/savdstatus--LastUpdate"** , < = 7 gün olması gereken LastUpdate döndürür 

- " **/opt/Sophos-av/bin/savdstatus-v"** , **"erişim taraması çalışıyor"** olarak eşit 

- **"/opt/Sophos-av/bin/savconfig LiveProtection al"** işlevi etkin

## <a name="troubleshoot-and-support"></a>Sorun giderme ve destek

### <a name="troubleshoot"></a>Sorun giderme

Microsoft kötü amaçlı yazılımdan koruma uzantısı günlükleri şurada bulunabilir: **%systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (veya PaaSAntimalware) \1.5.5.x (sürüm #) \CommandExecution.log**

### <a name="support"></a>Destek

Daha fazla yardım için [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Ya da bir Azure destek olayı dosyası. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve Destek Al ' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.