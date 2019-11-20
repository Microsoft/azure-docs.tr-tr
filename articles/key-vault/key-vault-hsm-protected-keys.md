---
title: Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma-Azure Key Vault | Microsoft Docs
description: Azure Key Vault ile kullanmak üzere kendi HSM korumalı anahtarlarınızı planlayıp, oluşturmanıza ve aktarmaya yardımcı olması için bu makaleyi kullanın. BYOK olarak da bilinir veya kendi anahtarınızı getir.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 4ebb31a839a645bcb1312405ee0222f39dbbcd1e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261278"
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ek güvence için, Azure Key Vault kullandığınızda, donanım güvenlik modüllerinde (HSM 'ler), hiçbir zaman HSM sınırını bırakmamış anahtarlar içeri aktarabilir veya oluşturabilirsiniz. Bu senaryo, genellikle *kendi anahtarını getir*veya bYok olarak adlandırılır. HSM'ler, FIPS 140-2 Düzey 2 doğrulanmasına sahiptir. Azure Key Vault anahtarlarınızı korumak için HSM 'lerin nCipher nShield ailesini kullanır.

Bu konudaki bilgileri kullanarak, Azure Key Vault ile kullanmak üzere kendi HSM korumalı anahtarlarınızı planlayıp, oluşturmanıza ve daha sonra aktarmanızı sağlamanıza yardımcı olması için bu konudaki bilgileri kullanın.

Bu işlev, Azure Çin için kullanılamaz.

> [!NOTE]
> Azure Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault nedir?](key-vault-overview.md)  
> HSM korumalı anahtarlar için Anahtar Kasası oluşturmayı içeren bir başlangıç öğreticisi için bkz. [Azure Key Vault nedir?](key-vault-overview.md).

HSM korumalı bir anahtarı oluşturma ve Internet üzerinden aktarma hakkında daha fazla bilgi:

* Anahtar, saldırı yüzeyini azaltan çevrimdışı bir iş istasyonundan oluşturulur.
* Anahtar bir anahtar değişim anahtarı (KEK) ile şifrelenir ve bu, Azure Key Vault HSM 'lere aktarılıncaya kadar şifrelenmeden kalır. Yalnızca anahtarınızın şifrelenmiş sürümü özgün iş istasyonunu bırakır.
* Araç takımı, kiracı anahtarınızda anahtarınızı Azure Key Vault güvenlik dünyasına bağlayan özellikleri ayarlar. Bu nedenle Azure Key Vault HSM 'leri anahtarınızı aldıktan ve şifresini çözdüğünde, yalnızca bu HSM 'ler tarafından kullanılabilir. Anahtarınız verilemiyor. Bu bağlama nCipher HSMs tarafından zorlanır.
* Anahtarınızı şifrelemek için kullanılan anahtar değişim anahtarı (KEK) Azure Key Vault HSM 'ler içinde oluşturulur ve dışarı aktarılabilir değildir. HSM 'ler, KEK 'in HSM dışında net bir sürümü olmaması için zorlar. Ayrıca, araç takımı, KEK dışa aktarılabilir ve nCipher tarafından üretilmiş orijinal bir HSM içinde oluşturulan nCipher 'dan kanıtlama içerir.
* Araç takımı, Azure Key Vault güvenlik dünyasının nCipher tarafından üretilmiş orijinal bir HSM üzerinde de oluşturulduğunu nCipher 'dan kanıtlama içerir. Bu kanıtlama, Microsoft 'un orijinal donanım kullandığını kanıtlar.
* Microsoft, her coğrafi bölgedeki ayrı KEKs ve ayrı güvenlik dünyalarını kullanır. Bu ayrım, anahtarınızın yalnızca sizin şifrelediğiniz bölgedeki veri merkezlerinde kullanılabilmesini sağlar. Örneğin, Avrupa Müşterideki bir anahtar Kuzey Amerika veya Asya 'daki veri merkezlerinde kullanılamaz.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>NCipher HSMs ve Microsoft hizmetleri hakkında daha fazla bilgi

Entrust Datacard bir şirket olan nCipher güvenliği, iş açısından kritik bilgi ve uygulamalarına güven, bütünlük ve denetim sunarak dünya lideri kuruluşların güçlendirin. nCipher 'in şifreleme çözümleri, bulut, IoT, blok zinciri, Dijital Ödemeler ile güvenli bir şekilde gelişen teknolojiden yararlanarak, küresel kuruluşların kendi hassas veriler, ağ iletişimleri ve kurumsal altyapı. nCipher, iş açısından kritik uygulamalar için güven sunar, verilerin bütünlüğünü sağlar ve müşterileri tamamen tüm denetim altına (bugün, yarın, her zaman) yerleştirirler.

Microsoft, HSM 'lerin durumunu iyileştirmek için nCipher güvenliği ile işbirliği içeriyor. Bu geliştirmeler, anahtarlarınızla ilgili bir denetim elde etmek zorunda kalmadan barındırılan hizmetlerin genel avantajlarından yararlanmanızı sağlar. Özellikle, bu geliştirmeler Microsoft 'un HSMs 'yi yönetmesine olanak tanır. Bulut hizmeti olarak Azure Key Vault, kuruluşunuzun kullanım artışlarını karşılamak için kısa bildirimde ölçeği artırır. Aynı zamanda, anahtarınız Microsoft 'un HSMs içinde korunur: Anahtarı oluşturup Microsoft 'un HSMs 'ye aktaracağından, anahtar yaşam döngüsü üzerinde denetimi koruyabilirsiniz.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Azure Key Vault için kendi anahtarını getir (BYOK) uygulama

Kendi HSM korumalı anahtarınızı oluşturup Azure Key Vault (kendi anahtarını getir (BYOK) senaryosunu aktarırsanız, aşağıdaki bilgileri ve yordamları kullanın.

## <a name="prerequisites-for-byok"></a>BYOK için Önkoşullar

Azure Key Vault için kendi anahtarınızı getir (BYOK) önkoşulları listesi için aşağıdaki tabloya bakın.

| Gereksinim | Daha fazla bilgi |
| --- | --- |
| Azure aboneliği |Azure Key Vault oluşturmak için bir Azure aboneliğine sahip olmanız gerekir: [Ücretsiz deneme için kaydolun](https://azure.microsoft.com/pricing/free-trial/) |
| HSM korumalı anahtarları desteklemek için Azure Key Vault Premium hizmet katmanı |Azure Key Vault yönelik hizmet katmanları ve özellikleri hakkında daha fazla bilgi için [Azure Key Vault fiyatlandırma](https://azure.microsoft.com/pricing/details/key-vault/) Web sitesine bakın. |
| nCipher nShield HSM 'leri, smartcards ve Destek yazılımı |NCipher donanım güvenlik modülüne erişiminizin olması ve nCipher nShield HSM 'lerin temel operasyonel bilgisine sahip olmanız gerekir. Uyumlu modellerin listesi için bkz. [NCipher nShield Hardware Security Module](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) veya yoksa bir HSM satın alma. |
| Aşağıdaki donanım ve yazılımlar:<ol><li>En az sürüm 11,50 olan Windows 7 ve nCipher nShield yazılımının en düşük Windows işletim sistemine sahip çevrimdışı bir x64 iş istasyonu.<br/><br/>Bu iş istasyonu Windows 7 çalıştırıyorsa [Microsoft .NET Framework 4,5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe)' ü yüklemelisiniz.</li><li>Internet 'e bağlı ve Windows 7 ' nin en düşük Windows işletim sistemine sahip ve **En düşük sürüm 1.1.0** [Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) yüklü bir iş istasyonu.</li><li>En az 16 MB boş alana sahip bir USB sürücü veya başka bir taşınabilir depolama cihazı.</li></ol> |Güvenlik nedenleriyle, ilk iş istasyonunun bir ağa bağlı olmaması önerilir. Ancak, bu öneri programlı bir şekilde zorlanmaz.<br/><br/>Aşağıdaki yönergelerde, bu iş istasyonu, bağlantısı kesilen iş istasyonu olarak adlandırılır.</p></blockquote><br/>Ayrıca, kiracı anahtarınız bir üretim ağı için ise, araç takımını indirmek için ikinci ve ayrı bir iş istasyonu kullanmanızı ve kiracı anahtarını yüklemenizi öneririz. Ancak test amacıyla, ilki ile aynı iş istasyonunu kullanabilirsiniz.<br/><br/>Aşağıdaki yönergelerde, bu ikinci iş istasyonu Internet 'e bağlı iş istasyonu olarak adlandırılır.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Anahtarınızı Azure Key Vault HSM 'ye oluşturun ve aktarın

Anahtarınızı oluşturmak ve bir Azure Key Vault HSM 'ye aktarmak için aşağıdaki beş adımı kullanacaksınız:

* [1. Adım: Internet 'e bağlı iş istasyonunuzu hazırlama](#step-1-prepare-your-internet-connected-workstation)
* [2. Adım: Bağlantısı kesilen iş istasyonunuzu hazırlama](#step-2-prepare-your-disconnected-workstation)
* [3. Adım: Anahtarınızı oluşturma](#step-3-generate-your-key)
* [4. Adım: Anahtarınızı aktarım için hazırlama](#step-4-prepare-your-key-for-transfer)
* [5. Adım: Anahtarınızı Azure Key Vault aktarın](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>1\. adım: Internet 'e bağlı iş istasyonunuzu hazırlama

Bu ilk adım için, Internet 'e bağlı iş istasyonunuzda aşağıdaki yordamları uygulayın.

### <a name="step-11-install-azure-powershell"></a>Adım 1,1: Azure PowerShell'i yükleyin

Internet 'e bağlı iş istasyonunda, Azure Key Vault yönetmek için cmdlet 'leri içeren Azure PowerShell modülünü indirip yükleyin. Yükleme yönergeleri için bkz. [Azure PowerShell yükleme ve yapılandırma](/powershell/azure/overview).

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

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Adım 1,3: Azure Key Vault için BYOK araç takımını indirin

Microsoft Indirme Merkezi ' ne gidin ve coğrafi bölgeniz veya Azure örneğiniz için [Azure Key Vault BYOK araç takımını indirin](https://www.microsoft.com/download/details.aspx?id=45345) . İndirilecek paket adını ve karşılık gelen SHA-256 paket karmasını belirlemek için aşağıdaki bilgileri kullanın:

---
**Amerika Birleşik Devletleri:**

Keykasası-BYOK-Tools-Birleşik Devletler. zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**'Ya**

KeyVault-BYOK-Tools-Europe. zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Noktası**

KeyVault-BYOK-Tools-AsiaPacific. zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**Latin Amerika:**

KeyVault-BYOK-Tools-LatinAmerica. zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**Japonya:**

KeyVault-BYOK-Tools-Japan. zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Kore:**

KeyVault-BYOK-Tools-Korea. zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**Güney Afrika:**

KeyVault-BYOK-Tools-SouthAfrica. zip

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**BAE**

KeyVault-BYOK-Tools-UAE. zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Avustralya:**

KeyVault-BYOK-Tools-Australia. zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure Kamu:** ](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud. zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**ABD kamu DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD. zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Kanada:**

KeyVault-BYOK-Tools-Canada. zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Almanya:**

KeyVault-BYOK-Tools-Germany. zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**Almanya Genel:**

KeyVault-BYOK-Tools-Germany-Public. zip

54534936D0A0C99C8117DB724C04A5E50FD204CFCBD75C78972B785865364A29

---
**Hindistan:**

KeyVault-BYOK-Tools-India. zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Fransa:**

KeyVault-BYOK-Tools-France. zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Birleşik Krallık:**

KeyVault-BYOK-Tools-UnitedKingdom. zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**İsviçre:**

KeyVault-BYOK-Tools-Switzerland. zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


İndirilen BYOK araç takımının bütünlüğünü doğrulamak için, Azure PowerShell oturumunuzda, [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) cmdlet 'ini kullanın.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Araç takımı şunları içerir:

* **BYok-kek-pkg-** ile başlayan bir ada sahip bir anahtar değişim anahtarı (kek) paketi.
* **BYok-SecurityWorld-pkg-** ile başlayan bir ada sahip bir güvenlik dünyası paketi.
* Verifykeypackage.py adlı bir Python betiği **.**
* **Keytransferremote. exe** ve Ilişkili dll 'ler adlı komut satırı yürütülebilir dosyası.
* C++ **Vcredist_x64. exe** adlı bir Visual yeniden dağıtılabilir paket.

Paketi bir USB sürücüye veya başka bir taşınabilir depolamaya kopyalayın.

## <a name="step-2-prepare-your-disconnected-workstation"></a>2\. adım: Bağlantısı kesilen iş istasyonunuzu hazırlama

Bu ikinci adımda, bir ağa (Internet veya iç ağınız) bağlı olmayan iş istasyonunda aşağıdaki yordamları uygulayın.

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Adım 2,1: NCipher nShield HSM ile bağlantısı kesilen iş istasyonunu hazırlama

NCipher destek yazılımını bir Windows bilgisayara yükleyip bu bilgisayara bir nCipher nShield HSM ekleyin.

NCipher araçlarının yolunuzda olduğundan emin olun ( **%nfast_home%\Bin**). Örneğin, aşağıdakileri yazın:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Daha fazla bilgi için, nShield HSM 'ye dahil edilen kullanıcı kılavuzuna bakın.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Adım 2,2: Bağlantısı kesilen iş istasyonuna BYOK araç takımını yükler

USB sürücüsünden veya diğer taşınabilir depolamadan BYOK araç kümesi paketini kopyalayın ve ardından aşağıdakileri yapın:

1. İndirilen paketten dosyaları herhangi bir klasöre ayıklayın.
2. Bu klasörden, vcredist_x64. exe ' yi çalıştırın.
3. Visual Studio 2013 için Visual C++ Runtime bileşenlerini yüklemek için yönergeleri izleyin.

## <a name="step-3-generate-your-key"></a>3\. adım: Anahtarınızı oluşturma

Bu üçüncü adımda, bağlantısı kesilen iş istasyonunda aşağıdaki yordamları uygulayın. Bu adımı gerçekleştirmek için HSM 'nizin başlatma modunda olması gerekir. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Adım 3,1: HSM modunu ' ı ' olarak değiştir

Modunu değiştirmek için nCipher nShield Edge kullanıyorsanız: 1. Gerekli modu vurgulamak için mod düğmesini kullanın. 2. Birkaç saniye içinde, CTRL tuşuna basın ve birkaç saniye boyunca temizle düğmesini basılı tutun. Mod değişirse, yeni modun ışığı yanıp sönmeye devam eder ve aydınlatılır. Durum ışığı, birkaç saniye boyunca düzenli olarak yanıp sönebilir ve cihaz hazırsa düzenli olarak yanıp sönmeyebilir. Aksi takdirde, cihaz geçerli modda kalır, uygun mod da aydınlatılır.

### <a name="step-32-create-a-security-world"></a>Adım 3,2: Güvenlik dünyası oluşturma

Bir komut istemi başlatın ve nCipher New-World programını çalıştırın.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Bu program,%NFAST_KMDATA%\local\world adresinde C:\ProgramData\nCipher\Key Management Data\local klasörüne karşılık gelen bir **güvenlik dünyası** dosyası oluşturur. Çekirdek için farklı değerler kullanabilirsiniz, ancak örneğimizde her biri için üç boş kart ve PIN girmeniz istenir. Ardından, herhangi iki kart güvenlik dünyasına tam erişim sağlar. Bu kartlar, yeni güvenlik dünyası için **yönetici kart kümesi** olur.

> [!NOTE]
> HSM 'niz daha yeni şifresi üzerinde anlaşılamadı Suite DLf3072s256mRijndael 'yi desteklemiyorsa,--cipher-Suite = DLf3072s256mRijndael ile--Cipher-Suite = DLf1024s160mRijndael ile değiştirebilirsiniz
> 
> NCipher yazılım sürümü 12,50 ile birlikte gelen New-World. exe ile oluşturulan güvenlik dünyası, bu BYOK yordamıyla uyumlu değildir. Kullanılabilecek iki seçenek vardır:
> 1) Yeni bir güvenlik dünyası oluşturmak için nCipher yazılım sürümünün 12.40.2 'e indirgenmesini sağlar.
> 2) NCipher desteği ile iletişim kurun ve bu BYOK yordamıyla uyumlu New-World. exe ' nin 12.40.2 sürümünü kullanmanıza olanak sağlayan 12,50 yazılım sürümü için bir düzeltme sağlamak üzere bunları isteyin.

Ardından şunları yapın:

* Dünya dosyasını yedekleyin. Dünya dosyasını, yönetici kartlarını ve bunların PIN 'lerini güvenli hale getirin ve koruyun ve tek bir kişinin birden fazla karta erişimi olmadığından emin olun.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Adım 3,3: HSM modunu ' O ' olarak değiştir

Modunu değiştirmek için nCipher nShield Edge kullanıyorsanız: 1. Gerekli modu vurgulamak için mod düğmesini kullanın. 2. Birkaç saniye içinde, CTRL tuşuna basın ve birkaç saniye boyunca temizle düğmesini basılı tutun. Mod değişirse, yeni modun ışığı yanıp sönmeye devam eder ve aydınlatılır. Durum ışığı, birkaç saniye boyunca düzenli olarak yanıp sönebilir ve cihaz hazırsa düzenli olarak yanıp sönmeyebilir. Aksi takdirde, cihaz geçerli modda kalır, uygun mod da aydınlatılır.

### <a name="step-34-validate-the-downloaded-package"></a>Adım 3,4: İndirilen paketi doğrula

Bu adım isteğe bağlıdır, ancak şunları doğrulayabilmeniz önerilir:

* Araç takımı 'nda bulunan anahtar değişim anahtarı, orijinal bir nCipher nShield HSM 'sinden oluşturulmuştur.
* Araç takımı 'nda bulunan güvenlik dünyasının karması, orijinal bir nCipher nShield HSM ' de oluşturulmuştur.
* Anahtar değişim anahtarı dışa aktarılabilir değil.

> [!NOTE]
> İndirilen paketi doğrulamak için, HSM 'nin bağlı olması, açık olması ve üzerinde bir güvenlik dünyası olması gerekir (örneğin, yeni oluşturduğunuz bir bağlantı).

İndirilen paketi doğrulamak için:

1. Coğrafi bölgenize veya Azure örneğine bağlı olarak aşağıdakilerden birini yazarak verifykeypackage.py betiğini çalıştırın:

   * Kuzey Amerika için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Avrupa için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Asya için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Latin Amerika için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Japonya için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Kore için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Güney Afrika için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
   * UAE için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * Avustralya için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Azure 'un ABD devlet örneğini kullanan [Azure Kamu](https://azure.microsoft.com/features/gov/)için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * ABD kamu DOD için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Kanada için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Almanya için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Almanya genel için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Hindistan için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Fransa için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Birleşik Krallık için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
   * Isviçre için:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1

     > [!TIP]
     > NCipher nShield yazılımı,%NFAST_HOME%\python\bin adresinde Python içerir
     >
     >
2. Başarılı doğrulamayı belirten aşağıdakileri görmediğinizi onaylayın: **Kaynaklanan BAŞARILI**

Bu betik, imzalayan zincirini nShield kök anahtarına kadar doğrular. Bu kök anahtarın karması betiğe katıştırılır ve değeri **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**olmalıdır. Ayrıca, [nCipher Web sitesini](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation)ziyaret ederek bu değeri ayrı olarak da doğrulayabilirsiniz.

Şimdi yeni bir anahtar oluşturmaya hazırsınız.

### <a name="step-35-create-a-new-key"></a>Adım 3,5: Yeni anahtar oluştur

NCipher nShield **GenerateKey** programını kullanarak bir anahtar oluşturun.

Anahtarı oluşturmak için aşağıdaki komutu çalıştırın:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Bu komutu çalıştırdığınızda, şu yönergeleri kullanın:

* *Koruma* parametresi, gösterildiği gibi, değer **modülüne**ayarlanmalıdır. Bu, modül korumalı bir anahtar oluşturur. BYOK araç takımı, OCS korumalı anahtarları desteklemez.
* Ida **ve** **plainname** için *contosokey* değerini herhangi bir dize değeriyle değiştirin. Yönetim üst kafalarını en aza indirmek ve hata riskini azaltmak için, her ikisi için de aynı değeri kullanmanızı öneririz. Ida **değeri** yalnızca rakamlar, tireler ve küçük harf karakterler içermelidir.
* Bu örnekte pubexp boş bırakılır (varsayılan), ancak belirli değerler belirtebilirsiniz. Daha fazla bilgi için [nCipher belgelerine bakın.](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based)

Bu komut, başlayıp klasörünüzde, **key_simple_** ile **başlayan ve ardından** komutta belirtilen IBir adı olan bir simgeleştirilmiş anahtar dosyası oluşturur. Örneğin: **key_simple_contosokey**. Bu dosya şifreli bir anahtar içeriyor.

Bu simgeleştirilmiş anahtar dosyasını güvenli bir konumda yedekleyin.

> [!IMPORTANT]
> Anahtarınızı daha sonra Azure Key Vault 'e aktardığınızda, Microsoft bu anahtarı sizin için geri aktaramıyor, böylelikle anahtar ve güvenlik dünyasını güvenle yedeklemeniz çok önemli hale gelir. Anahtarınızı yedeklemeye yönelik rehberlik ve en iyi uygulamalar için [nCipher](https://www.ncipher.com/about-us/contact-us) ile iletişim kurun.
>


Artık anahtarınızı Azure Key Vault aktarmaya hazırsınız.

## <a name="step-4-prepare-your-key-for-transfer"></a>4\. Adım: Anahtarınızı aktarım için hazırlama

Bu dördüncü adımda, bağlantısı kesilen iş istasyonunda aşağıdaki yordamları uygulayın.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Adım 4,1: Daha düşük izinlerle anahtarınızın bir kopyasını oluşturun

Yeni bir komut istemi açın ve geçerli dizini, BYOK ZIP dosyasının sıkıştırkunununkkın bulunduğu konum olarak değiştirin. Anahtarınızla ilgili izinleri azaltmak için, bir komut isteminden, coğrafi bölgenize veya Azure örneğine bağlı olarak aşağıdakilerden birini çalıştırın:

* Kuzey Amerika için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Avrupa için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Asya için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Latin Amerika için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Japonya için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Kore için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Güney Afrika için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
* UAE için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* Avustralya için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Azure 'un ABD devlet örneğini kullanan [Azure Kamu](https://azure.microsoft.com/features/gov/)için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* ABD kamu DOD için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Kanada için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Almanya için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Almanya genel için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Hindistan için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Fransa için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Birleşik Krallık için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
* Isviçre için:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1

Bu komutu çalıştırdığınızda, *contosokey* değerini 3,5 **adımında belirttiğiniz değerle değiştirin:**  Anahtar oluşturma [adımınızdan](#step-3-generate-your-key) yeni bir anahtar oluşturun.

Güvenlik dünyası yönetici kartlarınızı eklemek isteyip istemediğiniz sorulur.

Komut tamamlandığında şu sonucu görürsünüz **: Daha** düşük izinlerle anahtarınızın başarısı ve kopyası, key_xferacId_\<contosokey > adlı dosyada bulunur.

NCipher nShield yardımcı programlarını kullanarak aşağıdaki komutları kullanarak ACL 'LERI inceler:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump. exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Bu komutları çalıştırdığınızda, contosokey değerini 3,5 **adımında belirttiğiniz değerle değiştirin:**  Anahtar oluşturma [adımınızdan](#step-3-generate-your-key) yeni bir anahtar oluşturun.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Adım 4,2: Microsoft 'un anahtar değişim anahtarını kullanarak anahtarınızı şifreleyin

Coğrafi bölgenize veya Azure örneğine bağlı olarak aşağıdaki komutlardan birini çalıştırın:

* Kuzey Amerika için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Avrupa için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Asya için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Latin Amerika için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Japonya için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Kore için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Güney Afrika için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* UAE için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Avustralya için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Azure 'un ABD devlet örneğini kullanan [Azure Kamu](https://azure.microsoft.com/features/gov/)için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* ABD kamu DOD için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Kanada için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Almanya için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Almanya genel için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Hindistan için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Fransa için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Birleşik Krallık için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Isviçre için:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Bu komutu çalıştırdığınızda, şu yönergeleri kullanın:

* *Contosokey* değerini, 3,5 **adımında anahtarı oluşturmak için kullandığınız tanımlayıcıyla değiştirin:**  Anahtar oluşturma [adımınızdan](#step-3-generate-your-key) yeni bir anahtar oluşturun.
* *SubscriptionID* değerini, anahtar kasanızı içeren Azure aboneliğinin kimliğiyle değiştirin. Bu değeri daha önce **aldığınız adım 1,2:**  [Internet 'e bağlı iş istasyonunuzu hazırlama](#step-1-prepare-your-internet-connected-workstation) adımından Azure abonelik Kimliğinizi alın.
* *ContosoFirstHSMKey* değerini çıkış dosyası adınız için kullanılan bir etiketle değiştirin.

Bu başarıyla tamamlandığında, şu sonucu görüntüler **: Başarılı** ve geçerli klasörde aşağıdaki ada sahip yeni bir dosya vardır: KeyTransferPackage-*ContosoFirstHSMkey*. bYok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Adım 4,3: Anahtar aktarım paketinizi Internet 'e bağlı iş istasyonuna kopyalayın

Önceki adımdan (KeyTransferPackage-ContosoFirstHSMkey. bYok) Internet 'e bağlı iş istasyonunuza çıkış dosyasını kopyalamak için bir USB sürücü veya başka bir taşınabilir depolama alanı kullanın.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>5\. Adım: Anahtarınızı Azure Key Vault aktarın

Bu son adımda, Internet 'e bağlı iş istasyonunda, bağlantısı kesilen iş istasyonundan Azure Key Vault HSM 'ye kopyaladığınız anahtar aktarım paketini karşıya yüklemek için [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet 'ini kullanın:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Karşıya yükleme başarılı olursa, yeni eklediğiniz anahtarın özelliklerinin görüntülendiğini görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar

Artık Anahtar Kasanızda HSM korumalı bu anahtarı kullanabilirsiniz. Daha fazla bilgi için bu fiyat ve özellik [karşılaştırması](https://azure.microsoft.com/pricing/details/key-vault/)bölümüne bakın.
