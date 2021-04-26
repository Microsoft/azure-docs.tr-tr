---
title: Azure Sentinel 'de gelişmiş çok aşamalı saldırı algılama
description: Uyarı fatıg 'yi azaltmak ve gelişmiş çok aşamalı saldırı algılamasına dayalı eylem yapılabilir olaylar oluşturmak için Azure Sentinel 'de Fusion teknolojisini kullanın.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 23e116eba6393f834b3368901d4440e668b16fca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724294"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Azure Sentinel 'de gelişmiş çok aşamalı saldırı algılama

> [!IMPORTANT]
> Bazı Fusion algılamalar (aşağıda belirtildiği gibi bkz.) Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Azure Sentinel, makine öğrenimine göre Fusion teknolojisini kullanarak çok aşamalı saldırıları otomatik olarak algılayabilir ve bu arada, sonlandırma zincirinin çeşitli aşamalarında gözlemlenen anormal davranışların ve şüpheli etkinliklerin birleşimlerini tanımlayarak otomatik olarak kullanılabilir. Bu bulmalar temelinde, Azure Sentinel, aksi takdirde yakalamak zor olan olaylar oluşturur. Bu olaylar iki veya daha fazla uyarıyı veya etkinliği kapsar. Tasarım yaparak, bu olaylar düşük hacimse, yüksek uygunlukta ve yüksek öneme sahiptir.

Ortamınız için özelleştirilmiş olan bu algılama teknolojisi yalnızca hatalı pozitif oranları azaltmaz, sınırlı veya eksik bilgilere sahip saldırıları da algılayabilir.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Gelişmiş çok aşamalı saldırı algılamasını yapılandırma

Bu algılama, Azure Sentinel 'de varsayılan olarak etkinleştirilmiştir. Durumu denetlemek veya birden çok uyarıyı temel alan olaylar oluşturmak için alternatif bir çözüm kullandığınız olayda devre dışı bırakmak için aşağıdaki yönergeleri kullanın:

1. Önceden yapmadıysanız, [Azure portal](https://portal.azure.com)da oturum açın

1. **Azure Sentinel**  >  **yapılandırma**  >  **Analizi** ' ne gidin

1. **Etkin kurallar**' ı seçin ve ardından **Fusion** kural türü Için listeyi filtreleyerek **ad** sütununda **Gelişmiş çok aşamalı saldırı algılamayı** bulun. Bu algılamanın etkin veya devre dışı olduğunu doğrulamak için **durum** sütununu kontrol edin.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{alternatif-metin}":::

1. Durumu değiştirmek için bu girişi seçin ve **Gelişmiş çok aşamalı saldırı algılama** dikey penceresinde **Düzenle**' yi seçin.

1. **Kural oluşturma Sihirbazı** dikey penceresinde durum değişikliği sizin için otomatik olarak seçilir, bu nedenle **İleri**' yi seçin ve ardından **Kaydet**' i seçin. 

 **Fusion** kural türü değiştirilemeyen tek bir kural içerdiğinden, kural şablonları bu kural türü için geçerli değildir.

> [!NOTE]
> Azure Sentinel, makine öğrenimi sistemlerini eğitmek için şu anda 30 günlük geçmiş veri kullanmaktadır. Bu veriler, makine öğrenimi ardışık düzeninde geçerken Microsoft 'un anahtarları kullanılarak her zaman şifrelenir. Ancak, Azure Sentinel çalışma alanınızda CMK 'yı etkinleştirdiyseniz eğitim verileri, [müşteri tarafından yönetilen anahtarlar (CMK)](customer-managed-keys.md) kullanılarak şifrelenmez. Fusion 'un devre dışı bırakılması için **Azure Sentinel** \> **yapılandırma** \> **Analizi \> etkin kurallar \> Gelişmiş çok aşamalı saldırı algılama** ' ya gidin ve **durum** sütununda **devre dışı bırak** ' ı seçin.

## <a name="attack-detection-scenarios"></a>Saldırı algılama senaryoları

Aşağıdaki bölümde, Azure Sentinel 'in Fusion teknolojisini kullanarak aradığı, tehdit sınıflandırmasına göre gruplanan bağıntı senaryolarının türleri listelenmektedir.

Yukarıda belirtildiği gibi, Fusion gelişmiş çok aşamalı saldırıları algılamak için çeşitli ürünlerden birden çok güvenlik uyarısı içerdiğinden, başarılı Fusion algılamaları, **günlüklerdeki** **güvenlik uyarıları** tablosunda **Uyarı** olarak değil, Azure Sentinel **olayları** sayfasında **Fusion olayları** olarak sunulur.

Bu Fusion destekli saldırı algılama senaryolarına olanak tanımak için, listelenen tüm veri kaynakları ilişkili Azure Sentinel veri bağlayıcıları kullanılarak alınmalıdır.

> [!NOTE]
> Bu senaryolardan bazıları **önizlemededir**. Bu şekilde belirtilecektir.

## <a name="compute-resource-abuse"></a>İşlem kaynağı kötüye kullanımı

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>Şüpheli Azure Active Directory oturum açma sonrasında birden çok VM oluşturma etkinliği
Bu senaryo şu anda **Önizleme** aşamasındadır.

**Mitre ATT&CK tactika:** İlk erişim, etki 

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), kaynak ele geçirme (T1496)

**Veri Bağlayıcısı kaynakları:** Microsoft Cloud App Security, Azure Active Directory Kimlik Koruması

**Açıklama:** Bu türün Fusion olayları, bir Azure AD hesabında şüpheli oturum açma sonrasında tek bir oturumda anormal sayıda sanal makine oluşturulduğunu gösterir. Bu tür bir uyarı, Fusion olay açıklamasında belirtilen hesabın güvenliğinin aşıldığını ve şifre araştırma işlemlerini çalıştırma gibi yetkisiz amaçlar için yeni VM 'Ler oluşturmak üzere kullanılacağını yüksek ölçüde güvenle olduğunu gösterir. Birden çok VM oluşturma etkinliği uyarısına sahip şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:

- **Birden çok VM oluşturma etkinliğine olağan bir konuma karşı önde gelen bir konum**

- **Bilinmeyen bir konumdan birden çok VM oluşturma etkinliğine olan oturum açma olayı**

- **Virüslü bir cihazdan birden çok VM oluşturma etkinliğine olan oturum açma etkinliği**

- **Anonim bir IP adresinden birden çok VM oluşturma etkinliğine kadar bir oturum açma olayı**

- **Sızdırılan kimlik bilgilerine sahip ve birden çok VM oluşturma etkinliğine sahip olan kullanıcının oturum açma olayı**

## <a name="credential-harvesting-new-threat-classification"></a>Kimlik bilgisi zor (yeni tehdit sınıflandırması)

### <a name="malicious-credential-theft-tool-execution-following-suspicious-sign-in"></a>Şüpheli oturum açma sonrasında kötü amaçlı kimlik bilgisi hırsızlığı Aracı yürütme

**Mitre ATT&CK tactika:** İlk erişim, kimlik bilgisi erişimi

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), işletim sistemi kimlik bilgileri dökümü (T1003)

**Veri Bağlayıcısı kaynakları:** Azure Active Directory Kimlik Koruması, uç nokta için Microsoft Defender

**Açıklama:** Bu türün Fusion olayları, bilinen bir kimlik bilgisi hırsızlığı aracının şüpheli bir Azure AD oturum açma işlemi sonrasında yürütüldüğünü belirtir. Bu, uyarı açıklamasında belirtilen kullanıcı hesabının güvenliğinin aşıldığını ve anahtarlar, düz metin parolaları ve/veya sistemden parola karmaları gibi bir aracı, **Mpikatz** gibi bir aracı başarıyla kullanmış olabileceğini belirten, yüksek güvenilirlikli bir gösterge sağlar. En zor kimlik bilgileri, bir saldırganın hassas verilere erişmesine, ayrıcalıklara ve/veya geçici olarak ağ üzerinden taşınmasına izin verebilir. Kötü amaçlı kimlik bilgileri hırsızlığı aracı uyarısıyla şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:

- **Kötü amaçlı kimlik bilgileri hırsızlığı aracı yürütmesi için önde gelen konumlara imkansız seyahat**

- **Bilmediğiniz bir konumdan kötü amaçlı kimlik bilgisi hırsızlığı aracının yürütülmesine kadar bir oturum açma etkinliği**

- **Virüslü bir cihazdan kötü amaçlı kimlik bilgisi hırsızlığı aracının yürütülmesine kadar bir oturum açma olayı**

- **Anonim bir IP adresinden lider, kötü amaçlı kimlik bilgisi hırsızlığı aracı yürütmeye yönelik oturum açma etkinliği**

- **Sızdırılan kimlik bilgilerine sahip kullanıcı tarafından kötü amaçlı kimlik bilgileri hırsızlığı aracı yürütmesi ile oturum açma etkinliği**

### <a name="suspected-credential-theft-activity-following-suspicious-sign-in"></a>Şüpheli oturum açma sonrasında şüphelenilen kimlik bilgisi hırsızlığı etkinliği

**Mitre ATT&CK tactika:** İlk erişim, kimlik bilgisi erişimi

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), parola depolarındaki kimlik bilgileri (T1555), işletim sistemi kimlik bilgileri dökümü (T1003)

**Veri Bağlayıcısı kaynakları:** Azure Active Directory Kimlik Koruması, uç nokta için Microsoft Defender

**Açıklama:** Bu türün Fusion olayları, şüpheli bir Azure AD oturum açma sonrasında kimlik bilgisi hırsızlığı deseniyle ilişkili etkinliğin oluştuğunu belirtir. Bu, uyarı açıklamasında belirtilen kullanıcı hesabının güvenliğinin aşıldığını ve anahtarlar, düz metin parolaları, parola karmaları vb. gibi kimlik bilgilerini çalmak için kullanıldığını yüksek güvenilirlikli bir gösterge sağlar. Çalınmış kimlik bilgileri, bir saldırganın hassas verilere erişmesine, ayrıcalıkların ilerletebilir ve/veya geçici olarak ağ üzerinden taşınmasına izin verebilir. Kimlik bilgisi hırsızlığı etkinlik uyarısına sahip şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:

- **Şüpheli kimlik bilgisi hırsızlığı etkinliğine karşı önde gelen konumlara imkansız seyahat**

- **Bilinmeyen bir konumdan gelen oturum açma olayı, şüpheli kimlik bilgisi hırsızlığı etkinliğine**

- **Virüslü bir cihazdan gelen oturum açma olayı, şüphelenilen kimlik bilgisi hırsızlığı etkinliğine**

- **Anonim IP adresinden gelen oturum açma olayı, şüpheli kimlik bilgisi hırsızlığı etkinliğine**

- **Sızdırılan kimlik bilgilerine sahip kullanıcıdan gelen oturum açma olayı, şüpheli kimlik bilgisi hırsızlığı etkinliğine**

## <a name="crypto-mining-new-threat-classification"></a>Şifre araştırma (yeni tehdit sınıflandırması)

### <a name="crypto-mining-activity-following-suspicious-sign-in"></a>Şüpheli oturum açma sonrasında şifre araştırma etkinliği

**Mitre ATT&CK tactika:** İlk erişim, kimlik bilgisi erişimi

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), kaynak ele geçirme (T1496)

**Veri Bağlayıcısı kaynakları:** Azure Active Directory Kimlik Koruması, Azure Defender (Azure Güvenlik Merkezi)

**Açıklama:** Bu türün Fusion olayları, bir Azure AD hesabında şüpheli bir oturum açma işlemiyle ilişkili şifre araştırma etkinliğini gösterir. Bu, uyarı açıklamasında belirtilen kullanıcı hesabının güvenliğinin aşıldığını ve ortamınızdaki kaynakları şifreleme-para birimine eklemek için kullanılan yüksek güvenilirlikli bir gösterge sağlar. Bu, bilgi işlem gücü ve/veya daha yüksek bir beklenen bulut kullanım reçetelerine neden olabilir. Şifre araştırma etkinlik uyarısıyla şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:  

- **Şifre araştırma etkinliğine yönelik olarak önde gelen konumlara imkansız seyahat**

- **Bilinmeyen bir konumdan şifre araştırma etkinliğine kadar bir oturum açma etkinliği**

- **Virüslü bir cihazdan şifre araştırma etkinliğine kadar bir oturum açma etkinliği**

- **Anonim bir IP adresinden şifre araştırma etkinliğine kadar bir oturum açma etkinliği**

- **Sızdırılan kimlik bilgilerine sahip kullanıcı tarafından şifre araştırma etkinliğine yönelik oturum açma etkinliği**

## <a name="data-exfiltration"></a>Veri sızdırma

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>Office 365 posta kutusu şüpheli bir Azure AD oturum açma sonrasında

**Mitre ATT&CK tactika:** İlk erişim, exfiltration, koleksiyon

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), e-posta koleksiyonu (T1114), otomatik exfiltration (T1020)

**Veri Bağlayıcısı kaynakları:** Microsoft Cloud App Security, Azure Active Directory Kimlik Koruması

**Açıklama:** Bu türün Fusion olayları, bir Azure AD hesabında şüpheli oturum açma işlemi sonrasında bir kullanıcının gelen kutusunda şüpheli bir gelen kutusu iletme kuralının ayarlandığını gösterir. Bu gösterge, Kullanıcı hesabının (Fusion olay açıklamasında belirtilen) tehlikeye girdiğinin ve doğru Kullanıcı bilgisi olmadan bir posta kutusu iletme kuralı etkinleştirerek kuruluşunuzun ağından verileri almak için kullanılan yüksek güvenilirlik sağlar. Office 365 posta kutusu nfiltration uyarısı ile şüpheli Azure AD oturum açma uyarılarının permütasyon 'leri şunlardır:

- **Office 365 posta kutusu için önde gelen bir konuma yönelik imkansız seyahat**

- **Bilmediğiniz bir konumdan Office 365 posta kutusu ile iletişim kurmak için oturum açma etkinliği**

- **Virüslü bir cihazdan Office 365 posta kutusu için lider olarak oturum açma etkinliği**

- **Anonim bir IP adresinden Office 365 posta kutusu için önde gelen oturum açma olayı**

- **Sızdırılan kimlik bilgilerine sahip kullanıcıdan Office 365 posta kutusu için oturum açma etkinliği**

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>Şüpheli Azure AD oturum açma sonrasında toplu dosya indirme

**Mitre ATT&CK tactika:** İlk erişim, exfiltration

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078)

**Veri Bağlayıcısı kaynakları:** Microsoft Cloud App Security, Azure Active Directory Kimlik Koruması

**Açıklama:** Bu türün Fusion olayları, bir Azure AD hesabında şüpheli oturum açma sonrasında bir kullanıcı tarafından anormal sayıda dosya indirilip indirilmediğini belirtir. Bu gösterge, Fusion olay açıklamasında belirtilen hesabın tehlikeye girdiğinin ve kuruluşunuzun ağından verileri almak için kullanılan yüksek güvenirlik sağlar. Toplu dosya indirme uyarısıyla şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:  

- **Toplu dosya indirme için normal bir konuma yönelik imkansız seyahat**

- **Bilinmeyen bir konumdan yığın dosya indirme için bir oturum açma olayı**

- **Virüslü bir cihazdan toplu dosya indirme için lider olan oturum açma olayı**

- **Anonim bir IP 'nin lideri olan oturum açma olayı, toplu dosya indirme**

- **Sızdırılan kimlik bilgilerine sahip kullanıcı tarafından yığın dosyası indirme için oturum açma etkinliği**

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>Aşağıdaki şüpheli Azure AD oturum açma bölümünde toplu dosya paylaşımı

**Mitre ATT&CK tactika:** İlk erişim, exfiltration

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), Web hizmeti üzerinden Exfiltratıon (T1567)

**Veri Bağlayıcısı kaynakları:** Microsoft Cloud App Security, Azure Active Directory Kimlik Koruması

**Açıklama:** Bu türün Fusion olayları, bir Azure AD hesabında şüpheli oturum açma sonrasında, belirli bir eşiğin üzerindeki bir dosya sayısının diğer kişilerle paylaşıldığını gösterir. Bu gösterge, Fusion olay açıklamasında belirtilen hesabın güvenliğinin aşıldığına ve belgeler, elektronik tablolar, vb. gibi dosyaları paylaşarak kuruluşunuzun ağından verileri, kötü amaçlı kullanıcılara karşı paylaşmak için kullanılan yüksek güvenilirlik sağlar. Toplu dosya paylaşım uyarısıyla şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:  

- **Toplu dosya paylaşımına önde gelen bir konum için imkansız seyahat**

- **Bilinmeyen bir konumdan yığın dosya paylaşımına önde gelen oturum açma olayı**

- **Virüslü bir cihazdan toplu dosya paylaşımına lider olarak oturum açma etkinliği**

- **Anonim bir IP adresinden yığın dosya paylaşımına önde gelen oturum açma olayı**

- **Sızdırılan kimlik bilgilerine sahip kullanıcıdan yığın dosya paylaşımına lider olarak oturum açma etkinliği**

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Şüpheli gelen kutusu düzenleme kuralları aşağıdaki şüpheli Azure AD oturum açma kurallarını ayarlar
Bu senaryo, bu listedeki iki tehdit sınıflandırmalarına aittir: **veri ayıklanma** ve yan yana **taşıma**. Netlik açısından, her iki bölümde de görünür.

Bu senaryo şu anda **Önizleme** aşamasındadır.

**Mitre ATT&CK tactika:** İlk erişim, yan yana hareket, Exfilsyon

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), Iç Spear kimlik avı (T1534)

**Veri Bağlayıcısı kaynakları:** Microsoft Cloud App Security, Azure Active Directory Kimlik Koruması

**Açıklama:** Bu türün Fusion olayları, bir Azure AD hesabında şüpheli oturum açma sonrasında bir kullanıcının gelen kutusunda anormal gelen kutusu kurallarının ayarlandığını gösterir. Bu, Fusion olay açıklamasında belirtilen hesabın güvenliğinin aşıldığını ve kullanıcının e-posta gelen kutusu kurallarını kötü amaçlı amaçlarla işlemek için kullanıldığını yüksek güvenilirlikli bir gösterge sağlar. Bu, bir saldırgan tarafından kuruluşun ağından verileri düzenlemeye yönelik bir girişim olabilir. Alternatif olarak, saldırgan ek kullanıcı ve/veya ayrıcalıklı hesaplara erişim elde ederek kuruluş içinden kimlik avı e-postaları oluşturmaya çalışıyor (dış kaynaklardan e-postada hedeflenen kimlik avı algılama mekanizmalarını atlayarak). Şüpheli gelen kutusu düzenleme kuralları uyarısıyla şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:  

- **Şüpheli gelen kutusu işleme kuralına olağan bir konum için imkansız seyahat**

- **Bilmediğiniz bir konumdan şüpheli gelen kutusu işleme kuralına kadar olan oturum açma olayı**

- **Virüslü bir cihazdan şüpheli gelen kutusu işleme kuralına kadar bir oturum açma olayı**

- **Anonim bir IP adresinden şüpheli gelen kutusu işleme kuralına bir oturum açma olayı**

- **Sızdırılan kimlik bilgileri ile şüpheli gelen kutusu işleme kuralına sahip kullanıcıdan oturum açma olayı**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>Şüpheli Azure AD oturum açma sonrasında birden çok Power BI raporu paylaşma etkinliği 
Bu senaryo şu anda **Önizleme** aşamasındadır.

**Mitre ATT&CK tactika:** İlk erişim, exfiltration 

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), Web hizmeti üzerinden Exfiltratıon (T1567)

**Veri Bağlayıcısı kaynakları:** Microsoft Cloud App Security, Azure Active Directory Kimlik Koruması

**Açıklama:** Bu türün Fusion olayları, bir Azure AD hesabında şüpheli bir oturum açma sonrasında tek bir oturumda, anormal sayıda Power BI raporun paylaşıldığını gösterir. Bu gösterge, Füzyon olay açıklamasında belirtilen hesabın güvenliğinin aşıldığına ve yetkisiz kullanıcılarla Power BI raporlarının paylaşılarak kuruluşunuzun ağından veri almak için kullanılan yüksek güvenilirlik sağlar. Birden çok Power BI rapor paylaşım etkinliği ile şüpheli Azure AD oturum açma uyarılarının permütasyon 'i şunlardır:  

- **Birden çok Power BI rapor paylaşma etkinliğine yönelik olarak önde gelen bir konuma yönelik imkansız seyahat**

- **Bilinmeyen bir konumdan birden çok Power BI rapor paylaşma etkinliğine yönelik oturum açma etkinliği**

- **Virüslü bir cihazdan birden çok Power BI rapor paylaşma etkinliğine olan oturum açma etkinliği**

- **Anonim bir IP adresinden, birden çok Power BI rapor paylaşma etkinliğine yönelik oturum açma olayı**

- **Sızdırılan kimlik bilgilerine sahip kullanıcı tarafından birden çok Power BI rapor paylaşım etkinliğine yönelik oturum açma etkinliği**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>Şüpheli Power BI rapor paylaşımı şüpheli Azure AD oturum açma
Bu senaryo şu anda **Önizleme** aşamasındadır.

**Mitre ATT&CK tactika:** İlk erişim, exfiltration 

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), Web hizmeti üzerinden Exfiltratıon (T1567)

**Veri Bağlayıcısı kaynakları:** Microsoft Cloud App Security, Azure Active Directory Kimlik Koruması

**Açıklama:** Bu türün Fusion olayları, bir Azure AD hesabında şüpheli bir oturum açma sonrasında şüpheli Power BI rapor paylaşma etkinliğinin oluştuğunu belirtir. Power BI raporu, doğal dil işleme kullanılarak tanımlanan gizli bilgiler içerdiğinden ve bir dış e-posta adresiyle paylaşıldığından, Web 'de yayımlanmakta veya dışarıdan abone olunan bir e-posta adresine bir anlık görüntü olarak sunulırken, paylaşım etkinliği şüpheli olarak tanımlandı. Bu uyarı, Füzyon olay açıklamasında belirtilen hesabın güvenliğinin aşıldığına ve yetkisiz kullanıcılar tarafından kötü amaçlı amaçlarla Power BI raporları paylaşarak, önemli verileri kuruluşunuza göre düzenlemek için kullanılan yüksek güvenilirliğe sahip olduğunu gösterir. Şüpheli Power BI rapor paylaşımıyla şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:  

- **Şüpheli Power BI Rapor paylaşımına önde gelen bir konuma yönelik imkansız seyahat**

- **Bilinmeyen bir konumdan bir oturum açma olayı, şüpheli Power BI Rapor paylaşımına önde gelen**

- **Virüslü bir cihazdan gelen oturum açma etkinliği, şüpheli Power BI Rapor paylaşımına**

- **Anonim IP adresinden önde gelen oturum açma olayı, şüpheli Power BI rapor paylaşımı**

- **Sızdırılan kimlik bilgilerine sahip kullanıcıdan oturum açma olayı, şüpheli Power BI rapor paylaşımı**

## <a name="data-destruction"></a>Veri yok etme

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>Şüpheli Azure AD oturum açma sonrasında toplu dosya silme

**Mitre ATT&CK tactika:** İlk erişim, etki

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), veri yok etme (T1485)

**Veri Bağlayıcısı kaynakları:** Microsoft Cloud App Security, Azure Active Directory Kimlik Koruması

**Açıklama:** Bu türün Fusion olayları, bir Azure AD hesabında şüpheli bir oturum açma sonrasında anormal sayıda benzersiz dosyanın silindiğini gösterir. Bu, Fusion olay açıklamasında belirtilen hesabın tehlikede olduğunu ve kötü amaçlı olarak verileri bozmak için kullanıldığını belirten bir gösterge sağlar. Toplu dosya silme uyarısıyla şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:  

- **Toplu dosya silmeye önde gelen bir konum için imkansız seyahat**

- **Bilmediğiniz bir konumdan toplu dosya silmeye kadar olan oturum açma etkinliği**

- **Virüslü bir cihazdan toplu dosya silmeye kadar lider olan oturum açma olayı**

- **Anonim bir IP adresinden toplu dosya silmeye kadar bir oturum açma olayı**

- **Sızdırılan kimlik bilgilerine sahip kullanıcı tarafından yığın dosyası silinmeye birlikte oturum açma etkinliği**

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>Şüpheli Azure AD oturum açma sonrasında şüpheli e-posta silme etkinliği
Bu senaryo şu anda **Önizleme** aşamasındadır.

**Mitre ATT&CK tactika:** İlk erişim, etki 

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), veri yok etme (T1485)

**Veri Bağlayıcısı kaynakları:** Microsoft Cloud App Security, Azure Active Directory Kimlik Koruması

**Açıklama:** Bu türün Fusion olayları, bir Azure AD hesabında şüpheli bir oturum açma sonrasında tek bir oturumda anormal sayıda e-posta silindiğini gösterir. Bu, Fusion olay açıklamasında belirtilen hesabın tehlikede olduğunu ve kuruluşun güvenliğini sağlama ya da istenmeyen posta ile ilgili e-posta etkinliğini gizleme gibi kötü amaçlı amaçlarla veri yok etmek için kullanılmış olduğunu belirten bir gösterge sağlar. Şüpheli e-posta silme etkinliği uyarısına sahip şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:   

- **Olağan olmayan bir konuma, şüpheli e-posta silme etkinliğine imkansız seyahat**

- **Bilmediğiniz bir konumdan gelen ve şüpheli e-posta silme etkinliğine yönelik oturum açma etkinliği**

- **Virüslü bir cihazdan şüpheli e-posta silme etkinliğine yönelik oturum açma etkinliği**

- **Anonim IP adresinden gelen, şüpheli e-posta silme etkinliğine ait oturum açma olayı**

- **Sızdırılan kimlik bilgilerine sahip kullanıcıdan gelen oturum açma olayı, şüpheli e-posta silme etkinliğine**

## <a name="denial-of-service"></a>Hizmet reddi

### <a name="multiple-vm-delete-activities-following-suspicious-azure-ad-sign-in"></a>Şüpheli Azure AD oturum açma sonrasında birden çok VM silme etkinliği
Bu senaryo şu anda **Önizleme** aşamasındadır.

**Mitre ATT&CK tactika:** İlk erişim, etki

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), uç nokta hizmet reddi (T1499)

**Veri Bağlayıcısı kaynakları:** Microsoft Cloud App Security, Azure Active Directory Kimlik Koruması

**Açıklama:** Bu türün Fusion olayları, bir Azure AD hesabında şüpheli bir oturum açma sonrasında tek bir oturumda anormal sayıda sanal makine silinmekte olduğunu gösterir. Bu gösterge, Fusion olay açıklamasında belirtilen hesabın güvenliğinin aşıldığını ve kuruluşun bulut ortamını kesintiye uğratan veya yok etmeye çalışmak üzere kullanıldığının yüksek güvenilirliğe sahip olmanızı sağlar. Birden çok VM silme etkinliği ile şüpheli Azure AD oturum açma uyarılarının permütasyonları:  

- **Birden çok VM silme etkinliğine yönelik genel bir konuma imkansız seyahat**

- **Bilinmeyen bir konumdan birden çok VM silme etkinliğine yönelik oturum açma olayı**

- **Virüslü bir cihazdan birden çok VM silme etkinliğine olan oturum açma etkinliği**

- **Anonim IP adresinden birden çok VM silme etkinliğine yönelik oturum açma olayı**

- **Sızdırılan kimlik bilgilerine sahip kullanıcı tarafından birden çok VM silme etkinliği ile oturum açma etkinliği**

## <a name="lateral-movement"></a>Yana hareket

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>Office 365 kimliğe bürünme şüpheli Azure AD oturum açma

**Mitre ATT&CK tactika:** İlk erişim, yan yana hareket

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), Iç Spear kimlik avı (T1534)

**Veri Bağlayıcısı kaynakları:** Microsoft Cloud App Security, Azure Active Directory Kimlik Koruması

**Açıklama:** Bu türün Fusion olayları, bir Azure AD hesabından gelen şüpheli bir oturum açma işleminden sonra anormal sayıda kimliğe bürünme eylemi oluştuğunu gösterir. Bazı yazılımda, kullanıcıların diğer kullanıcıları taklit etmesine izin veren seçenekler vardır. Örneğin, e-posta hizmetleri kullanıcıların diğer kullanıcılara kendi adına e-posta göndermesini yetkilendirmelerini sağlar. Bu uyarı, Fusion olay açıklamasında belirtilen hesabın tehlikede olduğunu ve kötü amaçlı yazılım dağıtımı veya yan yana hareket için kimlik avı e-postaları gönderme gibi kötü amaçlı olarak kimliğe bürünme etkinliklerini yürütmek için kullanıldığını gösterir. Office 365 kimliğe bürünme uyarısıyla şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:  

- **Office 365 kimliğe bürünme için önde gelen bir konuma imkansız seyahat**

- **Bilmediğiniz bir konumdan Office 365 kimliğe bürünmeye önde gelen oturum açma olayı**

- **Virüslü bir cihazdan Office 365 kimliğe bürünmeye önde gelen oturum açma etkinliği**

- **Anonim bir IP adresinden Office 365 kimliğe bürünmeye önde gelen oturum açma olayı**

- **Sızdırılan kimlik bilgilerine sahip kullanıcıdan Office 365 kimliğe bürünme ile oturum açma etkinliği**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Şüpheli gelen kutusu düzenleme kuralları aşağıdaki şüpheli Azure AD oturum açma kurallarını ayarlar
Bu senaryo, bu listedeki iki tehdit sınıflandırmalarına aittir: **yan yana hareket** ve **veri taşalımı**. Netlik açısından, her iki bölümde de görünür.

Bu senaryo şu anda **Önizleme** aşamasındadır.

**Mitre ATT&CK tactika:** İlk erişim, yan yana hareket, Exfilsyon

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), Iç Spear kimlik avı (T1534), otomatik exfiltration (T1020)

**Veri Bağlayıcısı kaynakları:** Microsoft Cloud App Security, Azure Active Directory Kimlik Koruması

**Açıklama:** Bu türün Fusion olayları, bir Azure AD hesabında şüpheli oturum açma sonrasında bir kullanıcının gelen kutusunda anormal gelen kutusu kurallarının ayarlandığını gösterir. Bu gösterge, Fusion olay açıklamasında belirtilen hesabın güvenliğinin aşıldığını ve kullanıcının e-posta gelen kutusu kurallarını kötü amaçlı amaçlarla işlemek için kullanıldığını yüksek güvenilirliğe olanak sağlar. Bu, bir saldırgan tarafından kuruluşun ağından verileri düzenlemeye yönelik bir girişim olabilir. Alternatif olarak, saldırgan ek kullanıcı ve/veya ayrıcalıklı hesaplara erişim elde ederek kuruluş içinden kimlik avı e-postaları oluşturmaya çalışıyor (dış kaynaklardan e-postada hedeflenen kimlik avı algılama mekanizmalarını atlayarak). Şüpheli gelen kutusu düzenleme kuralları uyarısıyla şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:

- **Şüpheli gelen kutusu işleme kuralına olağan bir konum için imkansız seyahat**

- **Bilmediğiniz bir konumdan şüpheli gelen kutusu işleme kuralına kadar olan oturum açma olayı**

- **Virüslü bir cihazdan şüpheli gelen kutusu işleme kuralına kadar bir oturum açma olayı**

- **Anonim bir IP adresinden şüpheli gelen kutusu işleme kuralına bir oturum açma olayı**

- **Sızdırılan kimlik bilgileri ile şüpheli gelen kutusu işleme kuralına sahip kullanıcıdan oturum açma olayı**

## <a name="malicious-administrative-activity"></a>Kötü amaçlı yönetim etkinliği

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>Şüpheli Azure AD oturum açma sonrasında şüpheli bulut uygulaması yönetim etkinliği

**Mitre ATT&CK tactika:** İlk erişim, kalıcılık, savunma Evasion, yan yana taşıma, koleksiyon, exfiltration ve etki

**Mitre ATT&CK teknikleri:** yok

**Veri Bağlayıcısı kaynakları:** Microsoft Cloud App Security, Azure Active Directory Kimlik Koruması

**Açıklama:** Bu türün Fusion olayları, aynı hesaptan şüpheli bir Azure AD oturum açma işleminden sonra tek bir oturumda anormal sayıda yönetim etkinliği gerçekleştirildiğini gösterir. Bu, Fusion olay açıklamasında belirtilen hesabın tehlikede olduğunu ve kötü amaçlı olarak herhangi bir sayıda yetkisiz yönetim eylemi yapmak için kullanıldığını belirten bir gösterge sağlar. Bu Ayrıca, yönetici ayrıcalıklarına sahip bir hesabın tehlikede olduğunu gösterir. Şüpheli bulut uygulaması yönetim etkinliği uyarısına sahip şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:  

- **Şüpheli bulut uygulaması yönetim etkinliğine karşı önde gelen bir konum için imkansız seyahat**

- **Bilinmeyen bir konumdan şüpheli bulut uygulaması yönetim etkinliğine yönelik oturum açma olayı**

- **Virüslü bir cihazdan şüpheli bulut uygulaması yönetim etkinliğine yönelik oturum açma etkinliği**

- **Anonim bir IP adresinden, şüpheli bulut uygulaması yönetim etkinliğine yönelik oturum açma olayı**

- **Sızdırılan kimlik bilgilerine sahip kullanıcıdan gelen oturum açma olayı, şüpheli bulut uygulaması yönetim etkinliğine**

## <a name="malicious-execution-with-legitimate-process"></a>Yasal işlemle kötü amaçlı yürütme

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell şüpheli bir ağ bağlantısı yaptı ve bu, Palo Alto Networks güvenlik duvarı tarafından işaretlenen anormal trafik tarafından izlenebilir.
Bu senaryo şu anda **Önizleme** aşamasındadır.

**Mitre ATT&CK tactika:** Yürütme

**Mitre ATT&CK teknikleri:** Komut ve betik yorumlayıcısı (T1059)

**Veri Bağlayıcısı kaynakları:** Uç nokta için Microsoft Defender (eski adıyla Microsoft Defender Gelişmiş tehdit koruması veya MDADTP), Palo Alto Networks 

**Açıklama:** Bu türün Fusion olayları, bir PowerShell komutu aracılığıyla giden bir bağlantı isteğinin yapıldığını ve bu, Palo Alto Networks güvenlik duvarı tarafından anormal gelen etkinliğinin algılandığını gösterir. Bu, bir saldırganın ağınıza erişim kazandığı ve kötü amaçlı işlemler gerçekleştirmeye çalıştığı hakkında bir gösterge sağlar. Bu kalıbı izleyen PowerShell tarafından yapılan bağlantı girişimleri, kötü amaçlı yazılım komutu ve denetim etkinliği, ek kötü amaçlı yazılımların İndirilme istekleri veya bir saldırgan uzaktan etkileşimli erişim sağlayan bir bildirim olabilir. Tüm "Land kapalı" saldırılarıyla birlikte bu etkinlik, PowerShell 'in meşru bir kullanımı olabilir. Bununla birlikte, şüpheli gelen güvenlik duvarı etkinliğinin ardından gelen PowerShell komut yürütmesi, PowerShell 'in kötü amaçlı olarak kullanıldığı güveni artırır ve daha fazla araştırılması gerekir. Palo Alto günlüklerinde, Azure Sentinel [tehdit günlüklerine](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)odaklanır ve tehditlere izin verildiğinde trafik şüpheli olarak değerlendirilir (şüpheli veriler, dosyalar, floods, paketler, taramalar, casus yazılım, URL 'ler, virüsler, güvenlik açıkları, yavalar, yavalar, yavalar). Ayrıca, ek uyarı ayrıntıları için Fusion olay açıklamasında listelenen [tehdit/Içerik türüne](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) karşılık gelen Palo Alto tehdit günlüğüne başvurun.

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Şüpheli uzak WMI yürütme, Palo Alto Networks güvenlik duvarı tarafından işaretlenen anormal trafik tarafından izlenir
Bu senaryo şu anda **Önizleme** aşamasındadır.

**Mitre ATT&CK tactika:** Yürütme, bulma

**Mitre ATT&CK teknikleri:** Windows Yönetim Araçları (T1047)

**Veri Bağlayıcısı kaynakları:** Uç nokta (eskiden MDADTP) için Microsoft Defender, Palo Alto Networks 

**Açıklama:** Bu türün Fusion olayları, Windows Yönetim Arabirimi (WMI) komutlarının bir sistemde uzaktan yürütüldüğünü ve bu, Palo Alto Networks güvenlik duvarı tarafından şüpheli gelen etkinliğin algılandığını gösterir. Bu, bir saldırganın ağınıza erişim kazandığını ve geçici olarak taşımaya, ayrıcalıkların ilerleme ve/veya kötü amaçlı yükleri yürütmesine olanak sağladığını belirten bir bildirim sağlar. Tüm "Land kapalı" saldırılarıyla birlikte bu etkinlik, WMI 'nin meşru bir kullanımı olabilir. Ancak, şüpheli gelen güvenlik duvarı etkinliğinin ardından uzak WMI komut yürütmesi, WMI 'nın kötü amaçlı olarak kullanıldığı ve daha fazla Araştırılması gereken güveni artırır. Palo Alto günlüklerinde, Azure Sentinel [tehdit günlüklerine](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)odaklanır ve tehditlere izin verildiğinde trafik şüpheli olarak değerlendirilir (şüpheli veriler, dosyalar, floods, paketler, taramalar, casus yazılım, URL 'ler, virüsler, güvenlik açıkları, yavalar, yavalar, yavalar). Ayrıca, ek uyarı ayrıntıları için Fusion olay açıklamasında listelenen [tehdit/Içerik türüne](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) karşılık gelen Palo Alto tehdit günlüğüne başvurun.

### <a name="suspicious-powershell-command-line-following-suspicious-sign-in"></a>Şüpheli oturum açma sonrasında şüpheli PowerShell komut satırı

**Mitre ATT&CK tactika:** İlk erişim, yürütme

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), komut ve komut dosyası yorumlayıcısı (T1059)

**Veri Bağlayıcısı kaynakları:** Azure Active Directory Kimlik Koruması, uç nokta için Microsoft Defender (eski adıyla MDADTP)

**Açıklama:** Bu türün Fusion olayları, bir kullanıcının bir Azure AD hesabında şüpheli bir oturum açma sonrasında potansiyel olabilecek kötü amaçlı PowerShell komutlarını yürütüldüğünü belirtir. Bu, uyarı açıklamasında belirtilen hesabın güvenliğinin aşıldığını ve diğer kötü amaçlı eylemlerin alındığını, yüksek güvenilirlikli bir gösterge sağlar. Saldırganlar, virüs tarayıcıları gibi disk tabanlı güvenlik mekanizmalarına engel olmak için genellikle PowerShell 'den, diskteki yapıtlardan çıkmadan kötü amaçlı yükleri yürütmek için yararlanır. Şüpheli PowerShell komut uyarısıyla şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:

- **Şüpheli PowerShell komut satırına önde gelen konumlara imkansız seyahat**

- **Bilmediğiniz bir konumdan, şüpheli PowerShell komut satırına lider olarak oturum açma olayı**

- **Virüslü bir cihazdan şüpheli PowerShell komut satırına lider olarak oturum açma olayı**

- **Anonim bir IP adresinden, şüpheli PowerShell komut satırına ilk olarak oturum açma olayı**

- **Sızdırılan kimlik bilgilerine sahip kullanıcıdan gelen oturum açma olayı, şüpheli PowerShell komut satırı ile**

## <a name="malware-c2-or-download"></a>Kötü amaçlı yazılım C2 veya indirme

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Anonim olarak çalışan hizmeti olan ağ isteği, Palo Alto Networks güvenlik duvarı tarafından işaretlenen anormal trafik tarafından izlenir.
Bu senaryo şu anda **Önizleme** aşamasındadır.

**Mitre ATT&CK tactika:** Komut ve denetim

**Mitre ATT&CK teknikleri:** Şifreli kanal (T1573), proxy (T1090)

**Veri Bağlayıcısı kaynakları:** Uç nokta (eskiden MDADTP) için Microsoft Defender, Palo Alto Networks 

**Açıklama:** Bu türün Fusion olayları, Tor 'ın anonimleştirme hizmetine giden bir bağlantı isteğinin yapıldığını ve bu, Palo Alto Networks güvenlik duvarı tarafından anormal gelen etkinliğinin algılandığını gösterir. Bu, bir saldırganın ağınıza erişim kazandığını ve eylemlerini ve amacını gizleme denediğinin bir göstergesidir. Bu kalıbı izleyen TOR ağı bağlantıları, kötü amaçlı yazılım komutu ve denetim etkinliği, ek kötü amaçlı yazılımların indirileceği veya bir saldırganın uzaktan etkileşimli erişim kurma istekleri hakkında bir gösterge olabilir. Palo Alto günlüklerinde, Azure Sentinel [tehdit günlüklerine](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)odaklanır ve tehditlere izin verildiğinde trafik şüpheli olarak değerlendirilir (şüpheli veriler, dosyalar, floods, paketler, taramalar, casus yazılım, URL 'ler, virüsler, güvenlik açıkları, yavalar, yavalar, yavalar). Ayrıca, ek uyarı ayrıntıları için Fusion olay açıklamasında listelenen [tehdit/Içerik türüne](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) karşılık gelen Palo Alto tehdit günlüğüne başvurun.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>, Palo Alto Networks güvenlik duvarı tarafından işaretlenen anormal trafik tarafından izlenen, yetkisiz erişim girişimleri geçmişi ile IP 'ye giden bağlantı
Bu senaryo şu anda **Önizleme** aşamasındadır.

**Mitre ATT&CK tactika:** Komut ve denetim

**Mitre ATT&CK teknikleri:** Uygulanamaz

**Veri Bağlayıcısı kaynakları:** Uç nokta (eskiden MDADTP) için Microsoft Defender, Palo Alto Networks 

**Açıklama:** Bu türün Fusion olayları, bir IP adresine giden bağlantının yetkisiz erişim girişimleri geçmişine sahip bir bağlantı olduğunu ve bu şekilde Palo Alto Networks güvenlik duvarı tarafından anormal etkinliğin algılandığını gösterir. Bu, bir saldırganın ağınıza erişim kazandığını belirten bir bildirim sağlar. Bu kalıbı izleyen bağlantı girişimleri, kötü amaçlı yazılım komutu ve denetim etkinliği, ek kötü amaçlı yazılımın İndirilme istekleri veya bir saldırganın uzaktan etkileşimli erişim kurma istekleri olabilir. Palo Alto günlüklerinde, Azure Sentinel [tehdit günlüklerine](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)odaklanır ve tehditlere izin verildiğinde trafik şüpheli olarak değerlendirilir (şüpheli veriler, dosyalar, floods, paketler, taramalar, casus yazılım, URL 'ler, virüsler, güvenlik açıkları, yavalar, yavalar, yavalar). Ayrıca, ek uyarı ayrıntıları için Fusion olay açıklamasında listelenen [tehdit/Içerik türüne](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) karşılık gelen Palo Alto tehdit günlüğüne başvurun.

## <a name="ransomware"></a>Fidye yazılımı

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>Şüpheli Azure AD oturum açma sonrasında fidye yürütme

**Mitre ATT&CK tactika:** İlk erişim, etki

**Mitre ATT&CK teknikleri:** Geçerli hesap (T1078), etki için şifrelenmiş veriler (T1486)

**Veri Bağlayıcısı kaynakları:** Microsoft Cloud App Security, Azure Active Directory Kimlik Koruması

**Açıklama:** Bu türün Fusion olayları, bir Azure AD hesabında şüpheli oturum açma sonrasında bir fidye saldırı saldırısı olduğunu gösteren anormal Kullanıcı davranışının algılandığını gösterir. Bu gösterge, Fusion olay açıklamasında belirtilen hesabın güvenliğinin aşıldığını ve veri sahibini almak veya veri sahibinin verilerine erişimini reddetmek amacıyla verileri şifrelemek için kullanılan yüksek güvenirlik sağlar. Fidye yazılımı yürütme uyarısıyla şüpheli Azure AD oturum açma uyarılarının permütasyonları şunlardır:  

- **Bulut uygulamasındaki fidye 'ya normal bir konuma karşı önde gelen bir konum**

- **Tanınmayan bir konumdan bulut uygulamasındaki fidye 'ya lider olarak oturum açma etkinliği**

- **Etkilenen bir cihazdan, bulut uygulamasındaki fidye 'ya lider olarak oturum açma etkinliği**

- **Anonim bir IP adresinden, bulut uygulamasındaki fidye 'ya lider olarak oturum açma olayı**

- **Bir kullanıcı tarafından, sızdırılan kimlik bilgilerine sahip, bulut uygulamasında fidye 'ya yönelik oturum açma olayı**

## <a name="remote-exploitation"></a>Uzaktan yararlanma

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Palo Alto Networks güvenlik duvarı tarafından bayrak eklenmiş anormal trafik tarafından izlenen saldırı çerçevesinin şüpheli kullanımı
Bu senaryo şu anda **Önizleme** aşamasındadır.

**Mitre ATT&CK tactika:** İlk erişim, yürütme, yan yana taşıma, ayrıcalık yükseltme

**Mitre ATT&CK teknikleri:** Public-Facing uygulama (T1190), Istemci yürütme (T1203) ile yararlanma, uzak hizmetlerden yararlanma (T1210), ayrıcalık yükseltme (T1068) için yararlanılması

**Veri Bağlayıcısı kaynakları:** Uç nokta (eskiden MDADTP) için Microsoft Defender, Palo Alto Networks 

**Açıklama:** Bu türün Fusion olayları, standart olmayan protokollerin kullanımları olduğunu, Metasploit gibi saldırı çerçevelerinin kullanımına benzer olduğunu ve bu nedenle Palo Alto Networks güvenlik duvarı tarafından şüpheli gelen etkinlik algılandığını gösterir. Bu, bir saldırganın ağ kaynaklarınıza erişim kazanmak için bir hizmetin yararlandığını veya bir saldırganın zaten erişim kazandığını ve daha sonra da bu ayrıcalıkları daha sonra ve/veya ileri bir düzeye taşımak için kullanılabilir sistemlerden/hizmetlerden daha fazla yararlanmaya çalışıyor olduğunu gösteren bir başlangıç göstergesi olabilir. Palo Alto günlüklerinde, Azure Sentinel [tehdit günlüklerine](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)odaklanır ve tehditlere izin verildiğinde trafik şüpheli olarak değerlendirilir (şüpheli veriler, dosyalar, floods, paketler, taramalar, casus yazılım, URL 'ler, virüsler, güvenlik açıkları, yavalar, yavalar, yavalar). Ayrıca, ek uyarı ayrıntıları için Fusion olay açıklamasında listelenen [tehdit/Içerik türüne](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) karşılık gelen Palo Alto tehdit günlüğüne başvurun.

## <a name="next-steps"></a>Sonraki adımlar

Gelişmiş çok aşamalı saldırı algılama hakkında daha fazla bilgi edindiniz, verilerinizi ve olası tehditleri nasıl algılayacağınızı öğrenmek için aşağıdaki hızlı başlangıç ile ilgileniyor olabilirsiniz: [Azure Sentinel ile çalışmaya](quickstart-get-visibility.md)başlayın.

Sizin için oluşturulan olayları araştırmaya hazırsanız aşağıdaki öğreticiye bakın: [Azure Sentinel ile olayları araştırın](tutorial-investigate-cases.md).

