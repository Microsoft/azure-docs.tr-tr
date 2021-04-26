---
title: Azure Güvenlik Merkezi'nde Dosya Bütünlüğü İzleme
description: Taban çizgilerini Azure Güvenlik Merkezi 'nde dosya bütünlüğü Izlemeyle karşılaştırmayı öğrenin.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f160948a06d8a3175158e0100345ee2a330048c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634711"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Dosya Bütünlüğünü İzleme (FIM) kullanarak ana hatları karşılaştırma

Dosya bütünlüğü Izleme (FIM), kaynaklarınızda hassas alanlarda değişiklik olduğunda sizi bilgilendirir. böylece, yetkisiz etkinlikleri araştırıp ele alabilirsiniz. FIM Windows dosyalarını, Windows kayıt defterlerini ve Linux dosyalarını izler.

Bu konu, dosyalarda ve kayıt defterlerinde FIM 'nin nasıl etkinleştirileceğini açıklamaktadır. FIM hakkında daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'Nde dosya bütünlüğü izleme](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>FIM neden kullanılmalıdır?

İşletim sistemi, uygulamalar ve ilişkili yapılandırma kaynaklarınızın davranışını ve güvenlik durumunu denetler. Bu nedenle, saldırganlar, bir kaynağın işletim sistemini fazla almak ve/veya etkinlikleri algılanmadan yürütmek için kaynaklarınızı denetleyen dosyaları hedefler.

Aslında, PCI DSS & ISO 17799 gibi birçok yasal uyumluluk standardı FIM denetimleri uygulamayı gerektirir.  

## <a name="enable-built-in-recursive-registry-checks"></a>Yerleşik özyinelemeli kayıt defteri denetimlerini etkinleştir

FIM kayıt defteri Hive Varsayılanları, ortak güvenlik alanlarındaki özyinelemeli değişiklikleri izlemek için kullanışlı bir yol sağlar.  Örneğin, bir saldırgan başlatma veya kapatmada bir yürütme yapılandırarak LOCAL_SYSTEM bağlamda yürütülecek bir betiği yapılandırabilir.  Bu türdeki değişiklikleri izlemek için yerleşik denetimi etkinleştirin.  

![Kayıt Defteri](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Özyinelemeli denetimler yalnızca önerilen güvenlik kovanları için geçerlidir ve özel kayıt defteri yollarına uygulanmaz.  

## <a name="add-a-custom-registry-check"></a>Özel bir kayıt defteri denetimi ekleme

FIM temelleri, işletim sistemi ve destekleyici uygulama için bilinen iyi durumun özelliklerini tanımlayarak başlar.  Bu örnekte, Windows Server 2008 ve üzeri için parola ilkesi yapılandırmalarına odaklanacağız.


|İlke Adı                 | Kayıt defteri ayarı|
|---------------------------------------|-------------|
|Etki alanı denetleyicisi: Makine hesabı parola değişikliklerini reddet| Machıne\system\currentcontrolset\services \Netlogon\Parameters\RefusePasswordChange|
|Etki alanı üyesi: Güvenli kanal verisini dijital olarak şifrele veya imzala (her zaman)|Machıne\system\currentcontrolset\services \Netlogon\parameters\requiresignorcontası|
|Etki alanı üyesi: Güvenli kanal verisini dijital olarak şifrele (uygun olduğunda)|Machıne\system\currentcontrolset\services \Netlogon\Parameters\SealSecureChannel|
|Etki alanı üyesi: Güvenli kanal verisini dijital olarak imzala (uygun olduğunda)|Machıne\system\currentcontrolset\services \Netlogon\Parameters\SignSecureChannel|
|Etki alanı üyesi: Makine hesabı parola değişikliklerini devreden çıkar|Machıne\system\currentcontrolset\services \Netlogon\Parameters\DisablePasswordChange|
|Etki alanı üyesi: En uzun hesap parolası yaşı|Machıne\system\currentcontrolset\services \Netlogon\parameters\maximferasswordavge|
|Etki alanı üyesi: Güçlü (Windows 2000 veya daha sonraki) oturum anahtarı gerektir|Machıne\system\currentcontrolset\services \Netlogon\Parameters\RequireStrongKey|
|Ağ güvenliği: NTLM 'yi kısıtla: Bu etki alanında NTLM kimlik doğrulaması|Machıne\system\currentcontrolset\services \Netlogon\Parameters\RestrictNTLMInDomain|
|Ağ güvenliği: NTLM'yi kısıtla: Bu etki alanında sunucu özel durumları ekle|Machıne\system\currentcontrolset\services \Netlogon\Parameters\DCAllowedNTLMServers|
|Ağ güvenliği: NTLM 'yi kısıtla: Bu etki alanında NTLM kimlik doğrulamasını denetle|Machıne\system\currentcontrolset\services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Çeşitli işletim sistemi sürümleri tarafından desteklenen kayıt defteri ayarları hakkında daha fazla bilgi edinmek için [Grup İlkesi ayarları başvuru elektronik tablosuna](https://www.microsoft.com/download/confirmation.aspx?id=25250)bakın.

FIM 'yi kayıt defteri temellerini izleyecek şekilde yapılandırmak için:

1. **Değişiklik izleme Için Windows kayıt defteri Ekle** penceresinde, **Windows kayıt defteri anahtarı** metin kutusuna aşağıdaki kayıt defteri anahtarını girin:

    ```
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    ```

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png" alt-text="Kayıt defterinde FIM 'yi etkinleştirme":::

## <a name="track-changes-to-windows-files"></a>Windows dosyalarındaki değişiklikleri izle

1. **Değişiklik izleme Için Windows dosyası Ekle** penceresinde, **yolu girin** metin kutusuna izlemek istediğiniz dosyaları içeren klasörü girin. Aşağıdaki şekildeki örnekte **contoso Web uygulaması** d:\ konumunda bulunur. **ContosWebApp** klasör yapısı içindeki sürücü.  
1. Ayar sınıfının bir adını sağlayarak, özyinelemeyi etkinleştirerek ve en üst klasörü bir joker karakter (*) sonekiyle belirterek özel bir Windows dosya girişi oluşturun.

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png" alt-text="Dosya üzerinde FIM 'yi etkinleştirme":::

## <a name="retrieve-change-data"></a>Değişiklik verilerini al

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