---
title: Azure Güvenlik Merkezi 'nde dosya bütünlüğü Izleme
description: Taban çizgilerini Azure Güvenlik Merkezi 'nde dosya bütünlüğü Izlemeyle karşılaştırmayı öğrenin.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: bb45e1d1ee17a6daf16bd688982f79fda986bde5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664402"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Dosya bütünlüğü Izleme (FIM) kullanarak temelleri karşılaştırın

Dosya bütünlüğü Izleme (FIM), kaynaklarınızda hassas alanlarda değişiklik olduğunda sizi bilgilendirir. böylece, yetkisiz etkinlikleri araştırıp ele alabilirsiniz. FIM Windows dosyalarını, Windows kayıt defterlerini ve Linux dosyalarını izler.

Bu konu, dosyalarda ve kayıt defterlerinde FIM 'nin nasıl etkinleştirileceğini açıklamaktadır. FIM hakkında daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'Nde dosya bütünlüğü izleme](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>FIM neden kullanılmalıdır?

İşletim sistemi, uygulamalar ve ilişkili yapılandırma kaynaklarınızın davranışını ve güvenlik durumunu denetler. Bu nedenle, saldırganlar, bir kaynağın işletim sistemini fazla almak ve/veya etkinlikleri algılanmadan yürütmek için kaynaklarınızı denetleyen dosyaları hedefler.

Aslında, PCI DSS & ISO 17799 gibi birçok yasal uyumluluk standardı FIM denetimleri uygulamayı gerektirir.  

## <a name="enable-built-in-recursive-registry-checks"></a>Yerleşik özyinelemeli kayıt defteri denetimlerini etkinleştir

FIM kayıt defteri Hive Varsayılanları, ortak güvenlik alanlarındaki özyinelemeli değişiklikleri izlemek için kullanışlı bir yol sağlar.  Örneğin, bir saldırgan başlatma veya kapatmada bir yürütme yapılandırarak LOCAL_SYSTEM bağlamında yürütülecek bir betiği yapılandırabilir.  Bu türdeki değişiklikleri izlemek için yerleşik denetimi etkinleştirin.  

![Kayıt Defteri](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Özyinelemeli denetimler yalnızca önerilen güvenlik kovanları için geçerlidir ve özel kayıt defteri yollarına uygulanmaz.  

## <a name="adding-a-custom-registry-check"></a>Özel kayıt defteri denetimi ekleme

FIM temelleri, işletim sistemi ve destekleyici uygulama için bilinen iyi durumun özelliklerini tanımlayarak başlar.  Bu örnekte, Windows Server 2008 ve üzeri için parola ilkesi yapılandırmalarına odaklanacağız.


|İlke Adı                 | Kayıt defteri ayarı|
|---------------------------------------|-------------|
|Etki alanı denetleyicisi: makine hesabı parola değişikliklerini reddetme| Machıne\system\currentcontrolset\services \Netlogon\Parameters\RefusePasswordChange|
|Etki alanı üyesi: güvenli kanal verilerini dijital olarak şifrele veya imzala (her zaman)|Machıne\system\currentcontrolset\services \Netlogon\parameters\requiresignorcontası|
|Etki alanı üyesi: güvenli kanal verilerini dijital olarak şifrele (mümkün olduğunda)|Machıne\system\currentcontrolset\services \Netlogon\Parameters\SealSecureChannel|
|Etki alanı üyesi: güvenli kanal verilerini dijital olarak imzala (mümkün olduğunda)|Machıne\system\currentcontrolset\services \Netlogon\Parameters\SignSecureChannel|
|Etki alanı üyesi: makine hesabı parola değişikliklerini devre dışı bırak|Machıne\system\currentcontrolset\services \Netlogon\Parameters\DisablePasswordChange|
|Etki alanı üyesi: en fazla makine hesabı parola yaşı|Machıne\system\currentcontrolset\services \Netlogon\parameters\maximferasswordavge|
|Etki alanı üyesi: güçlü (Windows 2000 veya üzeri) oturum anahtarı gerektir|Machıne\system\currentcontrolset\services \Netlogon\Parameters\RequireStrongKey|
|Ağ güvenliği: NTLM 'yi kısıtla: Bu etki alanında NTLM kimlik doğrulaması|Machıne\system\currentcontrolset\services \Netlogon\Parameters\RestrictNTLMInDomain|
|Ağ güvenliği: NTLM 'yi kısıtla: Bu etki alanında sunucu özel durumları Ekle|Machıne\system\currentcontrolset\services \Netlogon\Parameters\DCAllowedNTLMServers|
|Ağ güvenliği: NTLM 'yi kısıtla: Bu etki alanında NTLM kimlik doğrulamasını denetle|Machıne\system\currentcontrolset\services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Çeşitli işletim sistemi sürümleri tarafından desteklenen kayıt defteri ayarları hakkında daha fazla bilgi edinmek için [Grup İlkesi ayarları başvuru elektronik tablosuna](https://www.microsoft.com/download/confirmation.aspx?id=25250)bakın.

*FIM 'yi kayıt defteri temellerini izleyecek şekilde yapılandırmak için:*

1. **Değişiklik izleme Için Windows kayıt defteri Ekle** penceresinde, **Windows kayıt defteri anahtarı** metin kutusuna kayıt defteri anahtarını girin.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Kayıt defterinde FIM 'yi etkinleştirme](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Windows dosyalarında yapılan değişiklikleri izleme

1. **Değişiklik izleme Için Windows dosyası Ekle** penceresinde, **yolu girin** metin kutusuna izlemek istediğiniz dosyaları içeren klasörü girin. Aşağıdaki şekildeki örnekte **contoso Web uygulaması** d:\ konumunda bulunur. **ContosWebApp** klasör yapısı içindeki sürücü.  
1. Ayar sınıfının bir adını sağlayarak, özyinelemeyi etkinleştirerek ve en üst klasörü bir joker karakter (*) sonekiyle belirterek özel bir Windows dosya girişi oluşturun.

    ![Dosya üzerinde FIM 'yi etkinleştirme](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Değişiklik verilerini alma

Dosya bütünlüğü Izleme verileri Azure Log Analytics/ConfigurationChange tablo kümesi içinde bulunur.  

 1. Kaynak tarafından yapılan değişikliklerin özetini almak için bir zaman aralığı ayarlayın.
Aşağıdaki örnekte, kayıt defteri ve dosya kategorilerindeki son on dört gündeki tüm değişiklikleri aldık:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Kayıt defteri değişikliklerinin ayrıntılarını görüntülemek için:

    1. **WHERE** yan tümcesindeki **dosyaları** kaldırma, 
    1. Özetleme satırını kaldırın ve bir sıralama yan tümcesiyle değiştirin:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Raporlar, arşiv ve/veya channefor Power BI raporuna bir CSV 'ye aktarılabilir.  

![FIM verileri](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)