---
title: Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma-Azure Key Vault
description: Azure Key Vault ile kullanmak üzere kendi HSM korumalı anahtarlarınızı planlayıp, oluşturmanıza ve aktarmaya yardımcı olması için bu makaleyi kullanın. BYOK olarak da bilinir veya kendi anahtarınızı getir.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: e8230404d71b0fe46e8a7cc536d2b5f3a4c5004f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588403"
---
# <a name="import-hsm-protected-keys-for-key-vault-ncipher"></a>Key Vault için HSM korumalı anahtarları içeri aktarma (nCipher)

> [!WARNING]
> Bu belgede açıklanan HSM-Key içeri aktarma yöntemi **kullanımdan kaldırılmıştır** ve 30 Haziran 2021 ' den sonra desteklenecektir. Yalnızca üretici yazılımı 12.40.2 veya 12,50 olan nCipher nShield ailesi ile bir düzeltme ile birlikte kullanılabilir. [HSM anahtarları içeri aktarmak için yeni yöntem](hsm-protected-keys-byok.md) kullanılması önemle önerilir.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ek güvence için, Azure Key Vault kullandığınızda, donanım güvenlik modüllerinde (HSM 'ler), hiçbir zaman HSM sınırını bırakmamış anahtarlar içeri aktarabilir veya oluşturabilirsiniz. Bu senaryo genellikle *kendi anahtarını getir* veya KAG olarak adlandırılır. Azure Key Vault anahtarlarınızı korumak için HSM 'lerin nCipher nShield ailesini (FIPS 140-2 düzey 2 doğrulanan) kullanır.


Bu konudaki bilgileri kullanarak, Azure Key Vault ile kullanmak üzere kendi HSM korumalı anahtarlarınızı planlayıp, oluşturmanıza ve daha sonra aktarmanızı sağlamanıza yardımcı olması için bu konudaki bilgileri kullanın. 

Bu işlev, Azure Çin 21Vianet için kullanılamaz.

> [!NOTE]
> Azure Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault nedir?](../general/overview.md)  
> HSM korumalı anahtarlar için Anahtar Kasası oluşturmayı içeren bir başlangıç öğreticisi için bkz. [Azure Key Vault nedir?](../general/overview.md).

HSM korumalı bir anahtarı oluşturma ve Internet üzerinden aktarma hakkında daha fazla bilgi:

* Anahtar, saldırı yüzeyini azaltan çevrimdışı bir iş istasyonundan oluşturulur.
* Anahtar bir anahtar değişim anahtarı (KEK) ile şifrelenir ve bu, Azure Key Vault HSM 'lere aktarılıncaya kadar şifrelenmeden kalır. Yalnızca anahtarınızın şifrelenmiş sürümü özgün iş istasyonunu bırakır.
* Araç takımı, kiracı anahtarınızda anahtarınızı Azure Key Vault güvenlik dünyasına bağlayan özellikleri ayarlar. Bu nedenle Azure Key Vault HSM 'leri anahtarınızı aldıktan ve şifresini çözdüğünde, yalnızca bu HSM 'ler tarafından kullanılabilir. Anahtarınız verilemiyor. Bu bağlama nCipher HSMs tarafından zorlanır.
* Anahtarınızı şifrelemek için kullanılan anahtar değişim anahtarı (KEK) Azure Key Vault HSM 'ler içinde oluşturulur ve dışarı aktarılabilir değildir. HSM'ler, KEK’in HSM'lerin dışında net bir sürümü olmamasını zorlar. Ayrıca, araç takımı, KEK dışa aktarılabilir ve nCipher tarafından üretilmiş orijinal bir HSM içinde oluşturulan nCipher 'dan kanıtlama içerir.
* Araç takımı, Azure Key Vault güvenlik dünyasının nCipher tarafından üretilmiş orijinal bir HSM üzerinde de oluşturulduğunu nCipher 'dan kanıtlama içerir. Bu kanıtlama, Microsoft 'un orijinal donanım kullandığını kanıtlar.
* Microsoft, her coğrafi bölgedeki ayrı KEKs ve ayrı güvenlik dünyalarını kullanır. Bu ayrım, anahtarınızın yalnızca sizin şifrelediğiniz bölgedeki veri merkezlerinde kullanılabilmesini sağlar. Örneğin, Avrupa Müşterideki bir anahtar Kuzey Amerika veya Asya 'daki veri merkezlerinde kullanılamaz.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>NCipher HSMs ve Microsoft hizmetleri hakkında daha fazla bilgi

Entrust Datacard bir şirket olan nCipher güvenliği, iş açısından kritik bilgi ve uygulamalarına güven, bütünlük ve denetim sunarak dünya lideri kuruluşların güçlendirin. nCipher 'in şifreleme çözümleri, bulut, IoT, blok zinciri, Dijital Ödemeler ile güvenli bir şekilde gelişen teknolojiden yararlanarak, küresel kuruluşların, önemli veriler, ağ iletişimleri ve kurumsal altyapılarına yönelik tehditlere karşı korunmalarını sağlamak üzere bugün uygulamasına bağlı olan aynı kanıtlanmış teknolojiyi kullanarak yeni uyumluluk mantarihlerinin sağlanmasına yardımcı olur. nCipher, iş açısından kritik uygulamalar için güven sunar, verilerin bütünlüğünü sağlar ve müşterileri tamamen tüm denetim altına (bugün, yarın, her zaman) yerleştirirler.

Microsoft, HSM 'lerin durumunu iyileştirmek için nCipher güvenliği ile işbirliği içeriyor. Yapılan geliştirmeler, anahtarlarınızın denetimini elden bırakmadan, barındırılan hizmetlere ait tipik avantajlardan yararlanmanıza olanak tanımaktadır. Daha açık belirtilirse, bu geliştirmeler sonrasında HSM’lerin yönetimini sizin yerinize Microsoft yürütebilmektedir. Bulut hizmeti olarak Azure Key Vault, kuruluşunuzun kullanım artışlarını karşılamak için kısa bildirimde ölçeği artırır. Aynı zamanda, anahtarınız Microsoft 'un HSMs içinde korunur: anahtarı oluşturup Microsoft 'un HSMs 'ye aktaracağından, anahtar yaşam döngüsü üzerinde denetimi koruyabilirsiniz.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Azure Key Vault için kendi anahtarını getir (KAG) özelliğini uygulama

Kendi HSM korumalı anahtarınızı oluşturup Azure Key Vault (kendi anahtarını getir (BYOK) senaryosunu aktarırsanız, aşağıdaki bilgileri ve yordamları kullanın.

## <a name="prerequisites-for-byok"></a>BYOK için önkoşullar

Azure Key Vault için kendi anahtarınızı getir (BYOK) önkoşulları listesi için aşağıdaki tabloya bakın.

| Gereksinim | Daha fazla bilgi |
| --- | --- |
| Azure aboneliği |Azure Key Vault oluşturmak için bir Azure aboneliğine ihtiyacınız vardır: [ücretsiz deneme Için kaydolun](https://azure.microsoft.com/pricing/free-trial/) |
| HSM korumalı anahtarları desteklemek için Azure Key Vault Premium hizmet katmanı |Azure Key Vault yönelik hizmet katmanları ve özellikleri hakkında daha fazla bilgi için [Azure Key Vault fiyatlandırma](https://azure.microsoft.com/pricing/details/key-vault/) Web sitesine bakın. |
| nCipher nShield HSM 'leri, smartcards ve Destek yazılımı |NCipher donanım güvenlik modülüne erişiminizin olması ve nCipher nShield HSM 'lerin temel operasyonel bilgisine sahip olmanız gerekir. Uyumlu modellerin listesi için bkz. [NCipher nShield Hardware Security Module](https://go.ncipher.com/rs/104-QOX-775/images/nCipher_nShield_Family_Brochure.pdf?_ga=2.106120835.1607422418.1590478092-577009923.1587131206) veya yoksa bir HSM satın alma. |
| Aşağıdaki donanım ve yazılımlar:<ol><li>En az sürüm 11,50 olan Windows 7 ve nCipher nShield yazılımının en düşük Windows işletim sistemine sahip çevrimdışı bir x64 iş istasyonu.<br/><br/>Bu iş istasyonu Windows 7 çalıştırıyorsa, [Microsoft .NET Framework 4.5 sürümünü yüklemeniz](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe) gerekir.</li><li>Internet 'e bağlı ve Windows 7 ' nin en düşük Windows işletim sistemine sahip ve **En düşük sürüm 1.1.0** [Azure PowerShell](/powershell/azure/) yüklü bir iş istasyonu.</li><li>En az 16 MB boş alana sahip bir USB sürücü veya başka bir taşınabilir depolama cihazı.</li></ol> |Güvenlik nedenleriyle ilk iş istasyonunun bir ağa bağlı olmaması önerilir. Ancak, bu öneri programlı bir şekilde zorlanmaz.<br/><br/>Aşağıdaki yönergelerde, bu iş istasyonu, bağlantısı kesilen iş istasyonu olarak adlandırılır.</p></blockquote><br/>Ayrıca, kiracı anahtarınız bir üretim ağı için ise, araç takımını indirmek için ikinci ve ayrı bir iş istasyonu kullanmanızı ve kiracı anahtarını yüklemenizi öneririz. Ancak test amacıyla, birincisi ile aynı iş istasyonunu kullanabilirsiniz.<br/><br/>Aşağıdaki yönergelerde, bu ikinci iş istasyonu Internet 'e bağlı iş istasyonu olarak adlandırılır.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Anahtarınızı Azure Key Vault HSM 'ye oluşturun ve aktarın

Anahtarınızı oluşturmak ve bir Azure Key Vault HSM 'ye aktarmak için aşağıdaki beş adımı kullanacaksınız:

* [1. Adım: Internet 'e bağlı iş istasyonunuzu hazırlama](#step-1-prepare-your-internet-connected-workstation)
* [2. Adım: bağlantısı kesilen iş istasyonunuzu hazırlama](#step-2-prepare-your-disconnected-workstation)
* [3. Adım: anahtarınızı oluşturma](#step-3-generate-your-key)
* [Adım 4: Kiracı anahtarınızı aktarım için hazırlama](#step-4-prepare-your-key-for-transfer)
* [5. Adım: Anahtarınızı Azure Anahtar Kasası’na aktarma](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>1. Adım: Internet 'e bağlı iş istasyonunuzu hazırlama

Bu ilk adım için, Internet 'e bağlı iş istasyonunuzda aşağıdaki yordamları uygulayın.

### <a name="step-11-install-azure-powershell"></a>Adım 1,1: Azure PowerShell yüklemesi

Internet 'e bağlı iş istasyonunda, Azure Key Vault yönetmek için cmdlet 'leri içeren Azure PowerShell modülünü indirip yükleyin. Yükleme yönergeleri için bkz. [Azure PowerShell yükleme ve yapılandırma](/powershell/azure/).

### <a name="step-12-get-your-azure-subscription-id"></a>Adım 1,2: Azure abonelik KIMLIĞINIZI alın

Bir Azure PowerShell oturumu başlatın ve aşağıdaki komutu kullanarak Azure hesabınızda oturum açın:

```Powershell
   Connect-AzAccount
```
Açılır tarayıcı penceresinde Azure hesabı kullanıcı adınızı ve parolanızı girin. Sonra [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) komutunu kullanın:

```powershell
   Get-AzSubscription
```
Çıktıdan Azure Key Vault için kullanacağınız aboneliğin KIMLIĞINI bulun. Daha sonra bu abonelik KIMLIĞINE ihtiyacınız olacak.

Azure PowerShell penceresini kapatmayın.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Adım 1,3: Azure Key Vault için BYOK araç takımını Indirin

Microsoft Indirme Merkezi ' ne gidin ve coğrafi bölgeniz veya Azure örneğiniz için [Azure Key Vault BYOK araç takımını indirin](https://www.microsoft.com/download/details.aspx?id=45345) . İndirilecek paket adını ve karşılık gelen SHA-256 paket karmasını belirlemek için aşağıdaki bilgileri kullanın:

---
**Birleşik Devletler:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**'Ya**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Noktası**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0BTU 562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**Latin Amerika:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**Japonya:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Kore:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDAV05344ED136F

---
**Güney Afrika:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**BAE**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Avustralya:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure Kamu:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**ABD kamu DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Kanada:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Almanya:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**Almanya Genel:**

KeyVault-BYOK-Tools-Germany-Public.zip

54534936D0A0C99C8117DB724C04A5E50FD204CFCBD75C78972B785865364A29

---
**Hindistan:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Fransa:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Birleşik Krallık:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**İsviçre:**

KeyVault-BYOK-Tools-Switzerland.zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


İndirilen BYOK araç takımının bütünlüğünü doğrulamak için, Azure PowerShell oturumunuzda, [Get-FileHash](/powershell/module/microsoft.powershell.utility/get-filehash) cmdlet 'ini kullanın.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Araç takımı şunları içerir:

* **BYok-kek-pkg-** ile başlayan bir ada sahip bir anahtar değişim anahtarı (kek) paketi.
* **BYok-SecurityWorld-pkg-** ile başlayan bir ada sahip bir güvenlik dünyası paketi.
* Verifykeypackage.py adlı bir Python betiği **.**
* **KeyTransferRemote.exe** ve Ilişkili dll 'ler adlı bir komut satırı yürütülebilir dosyası.
* vcredist_x64.exe adlı Visual C++ yeniden dağıtılabilir paket **.**

Paketi bir USB sürücüye veya başka bir taşınabilir depolama cihazına kopyalayın.

## <a name="step-2-prepare-your-disconnected-workstation"></a>2. Adım: bağlantısı kesilen iş istasyonunuzu hazırlama

Bu ikinci adımda, bir ağa (Internet veya iç ağınız) bağlı olmayan iş istasyonunda aşağıdaki yordamları uygulayın.

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>2,1. Adım: bağlantısı kesilen iş istasyonunu nCipher nShield HSM ile hazırlama

NCipher destek yazılımını bir Windows bilgisayara yükleyip bu bilgisayara bir nCipher nShield HSM ekleyin.

NCipher araçlarının yolunuzda (**% nfast_home% \ bin**) olduğundan emin olun. Örneğin, aşağıdakini yazın:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Daha fazla bilgi için, nShield HSM 'ye dahil edilen kullanıcı kılavuzuna bakın.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Adım 2,2: bağlantısı kesilen iş istasyonuna BYOK araç takımını yükler

USB sürücü veya başka bir taşınabilir depolama cihazından BYOK araç takımı paketini kopyalayın ve ardından aşağıdakileri yapın:

1. İndirilen paketteki dosyaları herhangi bir klasöre ayıklayın.
2. Bu klasörden vcredist_x64.exe dosyasını çalıştırın.
3. Visual Studio 2013 için Visual C++ çalışma zamanı bileşenlerini yüklemek için yönergeleri izleyin.

## <a name="step-3-generate-your-key"></a>3. Adım: anahtarınızı oluşturma

Bu üçüncü adımda, bağlantısı kesilen iş istasyonunda aşağıdaki yordamları uygulayın. Bu adımı gerçekleştirmek için HSM 'nizin başlatma modunda olması gerekir. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Adım 3,1: HSM modunu ' ı ' olarak değiştirme

Modu değiştirmek için nCipher nShield Edge kullanıyorsanız: 1. Gerekli modu vurgulamak için mod düğmesini kullanın. 2. Birkaç saniye içinde, CTRL tuşuna basın ve birkaç saniye boyunca temizle düğmesini basılı tutun. Mod değişirse, yeni modun ışığı yanıp sönmeye devam eder ve aydınlatılır. Durum ışığı, birkaç saniye boyunca düzenli olarak yanıp sönebilir ve cihaz hazırsa düzenli olarak yanıp sönmeyebilir. Aksi takdirde, cihaz geçerli modda kalır, uygun mod da aydınlatılır.

### <a name="step-32-create-a-security-world"></a>Adım 3,2: güvenlik dünyası oluşturma

Bir komut istemi başlatın ve nCipher New-World programını çalıştırın.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Bu program, C:\ProgramData\nCipher\Key Management Data\local klasörüne karşılık gelen %NFAST_KMDATA%\local\world klasöründe bir **Güvenlik Dünyası** dosyası oluşturur. Çekirdek için farklı değerler kullanabilirsiniz, ancak örneğimizde her biri için üç boş kart ve PIN girmeniz istenir. Ardından, herhangi iki kart güvenlik dünyasına tam erişim sağlar. Bu kartlar, yeni güvenlik dünyası için **Yönetici Kart Seti** haline gelir.

> [!NOTE]
> HSM 'niz daha yeni şifresi üzerinde anlaşılamadı Suite DLf3072s256mRijndael 'yi desteklemiyorsa,--cipher-Suite = DLf3072s256mRijndael ile--Cipher-Suite = DLf1024s160mRijndael ile değiştirebilirsiniz
> 
> NCipher yazılım sürümü 12,50 ile birlikte gelen new-world.exe oluşturulan güvenlik dünyası, bu BYOK yordamıyla uyumlu değildir. Kullanılabilecek iki seçenek vardır:
> 1) Yeni bir güvenlik dünyası oluşturmak için nCipher yazılım sürümünün 12.40.2 'e indirgenmesini sağlar.
> 2) NCipher desteği ile iletişim kurun ve bu BYOK yordamıyla uyumlu new-world.exe 12.40.2 sürümünü kullanmanıza olanak sağlayan 12,50 yazılım sürümü için bir düzeltme sağlamak üzere bunları isteyin.

Ardından şunları yapın:

* Dünya dosyasının yedeğini alın. Dünya dosyasını, Yönetici Kartlarını ve bunların PIN’lerini güvenceye alın ve koruyun ve bir karta tek bir kişiden fazlasının erişemediğinden emin olun.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Adım 3,3: HSM modunu ' O ' olarak değiştirme

Modu değiştirmek için nCipher nShield Edge kullanıyorsanız: 1. Gerekli modu vurgulamak için mod düğmesini kullanın. 2. Birkaç saniye içinde, CTRL tuşuna basın ve birkaç saniye boyunca temizle düğmesini basılı tutun. Mod değişirse, yeni modun ışığı yanıp sönmeye devam eder ve aydınlatılır. Durum ışığı, birkaç saniye boyunca düzenli olarak yanıp sönebilir ve cihaz hazırsa düzenli olarak yanıp sönmeyebilir. Aksi takdirde, cihaz geçerli modda kalır, uygun mod da aydınlatılır.

### <a name="step-34-validate-the-downloaded-package"></a>Adım 3,4: indirilen paketi doğrulama

Bu adım isteğe bağlıdır ancak aşağıdakileri doğrulayabilmeniz adına önerilir:

* Araç takımı 'nda bulunan anahtar değişim anahtarı, orijinal bir nCipher nShield HSM 'sinden oluşturulmuştur.
* Araç takımı 'nda bulunan güvenlik dünyasının karması, orijinal bir nCipher nShield HSM ' de oluşturulmuştur.
* Anahtar Değişim Anahtarı dışarı aktarılamaz.

> [!NOTE]
> İndirilen paketi doğrulamak için, HSM 'nin bağlı olması, açık olması ve üzerinde bir güvenlik dünyası olması gerekir (örneğin, yeni oluşturduğunuz bir bağlantı).

İndirilen paketi doğrulamak için:

1. Coğrafi bölgenize veya Azure örneğine bağlı olarak aşağıdakilerden birini yazarak verifykeypackage.py betiğini çalıştırın:

   * Kuzey Amerika için:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
      ```
   * Avrupa için:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
      ```
   * Asya için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
        ```
   * Latin Amerika için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
        ```
   * Japonya için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
        ```
   * Kore için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
        ```
   * Güney Afrika için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
        ```
   * UAE için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
        ```
   * Avustralya için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
        ```
   * Azure 'un ABD devlet örneğini kullanan [Azure Kamu](https://azure.microsoft.com/features/gov/)için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
        ```
   * ABD kamu DOD için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
        ```
   * Kanada için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
        ```
   * Almanya için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * Almanya genel için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * Hindistan için:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
      ```
   * Fransa için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
        ```
   * Birleşik Krallık için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
        ```
   * Isviçre için:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1
        ```

     > [!TIP]
     > NCipher nShield yazılımı% NFAST_HOME% \ python\bin konumunda Python içeriyor
     >
     >
2. Başarılı doğrulamayı belirten şunları görmediğinizi onaylayın: **Sonuç: başarılı**

Bu betik, imzalayan zincirini nShield kök anahtarına kadar doğrular. Bu kök anahtarın karması, komut dosyasında tümleşiktir ve değeri **59178a47 de508c3f 291277ee 184f46c4 f1d9c639** olmalıdır. Ayrıca, [nCipher Web sitesini](https://www.ncipher.com)ziyaret ederek bu değeri ayrı olarak da doğrulayabilirsiniz.

Şimdi yeni bir anahtar oluşturmaya hazırsınız.

### <a name="step-35-create-a-new-key"></a>Adım 3,5: yeni bir anahtar oluşturma

NCipher nShield **GenerateKey** programını kullanarak bir anahtar oluşturun.

Anahtarı oluşturmak için aşağıdaki komutu çalıştırın:

```azurepowershell
generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=
```

Bu komutu çalıştırdığınızda, aşağıdaki yönergeleri kullanın:

* *protect* parametresi, gösterildiği gibi **module** değerine ayarlanmalıdır. Bu, modül korumalı bir anahtar oluşturur. BYOK araç takımı, OCS korumalı anahtarları desteklemez.
* **ident** ve **plainname** için *contosokey* değerini bir dize değeriyle değiştirin. Yönetim üst kafalarını en aza indirmek ve hata riskini azaltmak için, her ikisi için de aynı değeri kullanmanızı öneririz. Ida **değeri** yalnızca rakamlar, tireler ve küçük harf karakterler içermelidir.
* Bu örnekte pubexp (varsayılan) boş bırakılmıştır, ancak belirli bir değer belirtebilirsiniz. Daha fazla bilgi için [nCipher belgelerine bakın.](https://www.entrust.com/-/media/documentation/brochures/entrust-nshield-general-purpose-hsms-br-a4.pdf)

Bu komut,% NFAST_KMDATA% \ yerel klasörünüzde, **key_simple_** **başlayan ve ardından** komutta belirtilen bir ad olan bir ada sahip bir simgeleştirilmiş anahtar dosyası oluşturur. Örneğin: **key_simple_contosokey**. Bu dosya şifreli bir anahtar içerir.

Bu Simgeleştirilmiş Anahtar Dosyasını güvenli bir yere yedekleyin.

> [!IMPORTANT]
> Anahtarınızı daha sonra Azure Key Vault 'e aktardığınızda, Microsoft bu anahtarı sizin için geri aktaramıyor, böylelikle anahtar ve güvenlik dünyasını güvenle yedeklemeniz çok önemli hale gelir. Anahtarınızı yedeklemeye yönelik rehberlik ve en iyi uygulamalar için [nCipher](https://www.ncipher.com/about-us/contact-us) ile iletişim kurun.
>


Artık anahtarınızı Azure Key Vault aktarmaya hazırsınız.

## <a name="step-4-prepare-your-key-for-transfer"></a>Adım 4: Kiracı anahtarınızı aktarım için hazırlama

Bu dördüncü adımda, bağlantısı kesilen iş istasyonunda aşağıdaki yordamları uygulayın.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Adım 4,1: daha düşük izinlerle anahtarınızın bir kopyasını oluşturun

Yeni bir komut istemi açın ve geçerli dizini, BYOK ZIP dosyasının sıkıştırkunununkkın bulunduğu konum olarak değiştirin. Anahtarınızla ilgili izinleri azaltmak için, bir komut isteminden, coğrafi bölgenize veya Azure örneğine bağlı olarak aşağıdakilerden birini çalıştırın:

* Kuzey Amerika için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-
   ```
* Avrupa için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
   ```
* Asya için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
   ```
* Latin Amerika için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
   ```
* Japonya için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
   ```
* Kore için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
   ```
* Güney Afrika için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
   ```
* UAE için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
   ```
* Avustralya için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
   ```
* Azure 'un ABD devlet örneğini kullanan [Azure Kamu](https://azure.microsoft.com/features/gov/)için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
   ```
* ABD kamu DOD için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
   ```
* Kanada için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
   ```
* Almanya için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* Almanya genel için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* Hindistan için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
   ```
* Fransa için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
   ```
* Birleşik Krallık için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
   ```
* Isviçre için:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1
   ```

Bu komutu çalıştırdığınızda, *contosokey* değerini **Adım 3,5: anahtar oluşturma adımından yeni bir anahtar oluşturma** bölümünde belirttiğiniz değerle değiştirin. [](#step-3-generate-your-key)

Güvenlik dünyası yönetici kartlarınızı eklemek isteyip istemediğiniz sorulur.

Komut tamamlandığında **Result: Success** ve anahtarınızın, daha düşük izinlerle birlikte key_xferacId_ adlı dosyada olduğunu görürsünüz \<contosokey> .

NCipher nShield yardımcı programlarını kullanarak aşağıdaki komutları kullanarak ACL 'LERI inceler:

* aclprint.py:

   ```cmd
   "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
   ```
* kmfile-dump.exe:

   ```cmd
   "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
   ```
  Bu komutları çalıştırdığınızda, contosokey değerini **adım 3,5: anahtar oluşturma adımından yeni bir anahtar oluşturma** bölümünde belirttiğiniz değerle değiştirin. [](#step-3-generate-your-key)

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Adım 4,2: Microsoft 'un anahtar değişim anahtarını kullanarak anahtarınızı şifreleyin

Coğrafi bölgenize veya Azure örneğine bağlı olarak aşağıdaki komutlardan birini çalıştırın:

* Kuzey Amerika için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Avrupa için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Asya için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Latin Amerika için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Japonya için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Kore için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Güney Afrika için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* UAE için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Avustralya için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Azure 'un ABD devlet örneğini kullanan [Azure Kamu](https://azure.microsoft.com/features/gov/)için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* ABD kamu DOD için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Kanada için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Almanya için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Almanya genel için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Hindistan için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Fransa için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Birleşik Krallık için:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Isviçre için:

  ```azurepowershell
  KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
  ```


Bu komutu çalıştırdığınızda, aşağıdaki yönergeleri kullanın:

* *Contosokey* değerini, 3,5. adımdaki anahtarı oluşturmak için kullandığınız tanımlayıcıyla değiştirin. Adım: [anahtarınızı](#step-3-generate-your-key) oluşturma adımından **Yeni bir anahtar oluşturma** .
* *SubscriptionID* değerini, anahtar kasanızı içeren Azure aboneliğinin kimliğiyle değiştirin. Bu değeri daha önce elde edersiniz **: adım 1,2:** [Internet 'e bağlı Iş istasyonunuzu hazırlama](#step-1-prepare-your-internet-connected-workstation) ADıMıNDAN Azure abonelik Kimliğinizi alın.
* *ContosoFirstHSMKey* değerini çıkış dosyası adınız için kullanılan bir etiketle değiştirin.

Bu başarıyla tamamlandığında, **Sonuç: başarılı** ' i görüntüler ve geçerli klasörde şu ada sahip yeni bir dosya vardır: KeyTransferPackage-*ContosoFirstHSMkey*. bYok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Adım 4,3: anahtar aktarım paketinizi Internet 'e bağlı iş istasyonuna kopyalama

Önceki adımdan (KeyTransferPackage-ContosoFirstHSMkey. bYok) Internet 'e bağlı iş istasyonunuza çıkış dosyasını kopyalamak için bir USB sürücü veya başka bir taşınabilir depolama alanı kullanın.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Adım 5: Anahtarınızı Azure Anahtar Kasası’na aktarma

Bu son adımda, Internet 'e bağlı iş istasyonunda, bağlantısı kesilen iş istasyonundan Azure Key Vault HSM 'ye kopyaladığınız anahtar aktarım paketini karşıya yüklemek için [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet 'ini kullanın:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Karşıya yükleme başarılı olursa, yeni eklediğiniz anahtarın özelliklerinin görüntülendiğini görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar

Artık Anahtar Kasanızda HSM korumalı bu anahtarı kullanabilirsiniz. Daha fazla bilgi için bu fiyat ve özellik [karşılaştırması](https://azure.microsoft.com/pricing/details/key-vault/)bölümüne bakın.