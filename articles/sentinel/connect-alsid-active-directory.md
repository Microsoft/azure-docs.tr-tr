---
title: Active Directory için alsıd 'nizi Azure Sentinel 'e bağlama | Microsoft Docs
description: Active Directory bağlayıcısının alsıd 'sini Azure Sentinel 'e çekmek için nasıl kullanacağınızı öğrenin. Çalışma kitaplarında alsıd verilerini görüntüleyin, uyarı oluşturun ve araştırmayı geliştirebilirsiniz.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 654ecb65068e4321b85594d96e8ca7a7f73cde7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99566922"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>Active Directory (AD) için alsıd 'nizi Azure Sentinel 'e bağlama

> [!IMPORTANT]
> Active Directory bağlayıcısının alsıd 'si Şu anda **önizlemededir**. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Bu makalede, AD çözümünüz için alsıd 'nizi Azure Sentinel 'e nasıl bağlayabileceğiniz açıklanır. Active Directory Data Connector için alsıd, Azure Sentinel ile AD günlüklerine yönelik alsıd 'nizi kolayca bağlamanıza olanak tanır; böylece, çalışma kitaplarında verileri görüntüleyebilir, özel uyarılar oluşturmak için sorgulayabilir ve araştırmayı geliştirebilirsiniz. AD ve Azure Sentinel için alsıd arasındaki tümleştirme, Log Analytics aracısının yüklü olduğu Syslog sunucusunu kullanır. Ayrıca, kusto işlevine dayalı özel olarak oluşturulmuş bir günlük ayrıştırıcısı kullanır.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel çalışma alanında yazma izninizin olması gerekir.

- AD çözümünüz için alsıd 'niz, günlükleri Syslog aracılığıyla dışarı aktarmak üzere yapılandırılmalıdır.

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>Syslog Aracısı aracılığıyla AD günlükleri için alsıd 'yi Azure Sentinel 'e gönder

Syslog Aracısı aracılığıyla syslog iletilerini Azure Sentinel çalışma alanınıza iletmek için AD için alsıd 'i yapılandırın.

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. **Veri bağlayıcıları** Galerisi ' nden, **Active Directory için alsıd (Önizleme)** bağlayıcısını seçin ve ardından **bağlayıcı sayfasını açın**.

1. **Active Directory Bağlayıcısı Için alsıd 'si** sayfasında yer alan yönergeleri izleyin:

    1. Syslog sunucusunu yapılandırma

        1. Henüz bir tane yoksa, günlüklerin gönderileceği AD için alsıd için bir Linux Syslog sunucusu oluşturun. Azure Sentinel, **rsyslog** ve **Syslog-ng** Daemon 'ları destekler. 

        1. Syslog sunucunuzu, farklı bir dosyadaki AD günlükleri için alsıd çıktısı olacak şekilde yapılandırın.

    1. AD için alsıd 'yi, günlükleri Syslog sunucunuza gönderecek şekilde yapılandırın

        1. AD Portal **Için alsıd** 'Unuzda *sistem*, *yapılandırma* ve sonra *Syslog*' a gidin. Buradan, syslog sunucunuza doğru yeni bir Syslog uyarısı oluşturabilirsiniz. Uzak sunucu için, Linux aracısını yüklediğiniz Linux makinenin IP adresini kullanın.

        1. Günlüklerin sunucunuzda ayrı bir dosyada doğru bir şekilde toplandığından emin olun (bunu yapmak için, AD için alsıd 'de *Syslog* uyarı yapılandırmasındaki **yapılandırmayı sına** düğmesini kullanabilirsiniz).

    1. Linux için Log Analytics aracısını yükleyip ekleme

        - Azure Linux VM veya Azure olmayan bir Linux makinesi (fiziksel veya sanal) seçin. Ekrandaki bağlantıları ve yönergeleri izleyin. Daha fazla ayrıntı için bkz. [Linux makinenizi veya gereci yapılandırma](connect-syslog.md#configure-your-linux-machine-or-appliance) .

    1. Log Analytics Aracısı tarafından toplanacak günlükleri yapılandırın

        - Çalışma alanı Gelişmiş ayarları yapılandırmasındaki tesisler ve önem derecelerine seçin.

            1. Bağlayıcı sayfasında **çalışma alanınızın Gelişmiş ayarları yapılandırma >bağlantısını açın** .

            1. **Gelişmiş ayarlar** ekranında, **veriler** ' i ve ardından **özel Günlükler**' i seçin.

            1. **Aşağıdaki yapılandırmayı Linux makinelerime** girin onay kutusunu Işaretleyin ve **Ekle**' ye tıklayın.

            1. Syslog sunucusunu çalıştıran Linux makinesinden AD Syslog dosyası için örnek bir alsıd yüklemek üzere **Dosya Seç** ' e tıklayın ve **İleri**' ye tıklayın.

            1. **Ayarlanan kayıt sınırlayıcısı** ' nin **yeni satır** olarak ayarlandığını denetleyip **İleri**' ye tıklayın.

            1. **Linux** ' u seçin ve Syslog dosyasına dosya yolunu girin, ardından İleri ' ye tıklayın **+** . 

            1. Ad alanına, _CL sonekinden önce *Alsıdforadlog* yazın ve **bitti**' ye tıklayın.
    
Günlüklerinizin Log Analytics görünmesi 20 dakikaya kadar sürebilir.

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler *AlsidForADLog_CL* tablosundaki **özel Günlükler** altında **Günlükler**' de görünür.

Bu veri Bağlayıcısı, beklenen şekilde çalışması için bir kusto Işlevini temel alan bir ayrıştırıcıya bağımlıdır. Sorgularda ve çalışma kitaplarında kullanmak üzere **afad_parser** kusto işlevini ayarlamak için aşağıdaki adımları kullanın.

1. Azure Sentinel gezinti menüsünde **Günlükler**' i seçin.

1. Aşağıdaki sorguyu kopyalayın ve sorgu penceresine yapıştırın.
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. **Kaydet** açılan düğmesine tıklayın ve **Kaydet**' e tıklayın. **Kaydet** panelinde,

    1. **Ad**' ın altına **afad_parser** girin.

    1. **Farklı kaydet** altında **işlev**' ı seçin.

    1. **Işlev diğer adı** altında **afad_parser** girin.

    1. **Kategori** altında **işlevler** girin.

    1. **Kaydet**’e tıklayın.

    İşlev uygulamalarının etkinleştirilmesi genellikle 10 ila 15 dakika sürer.

Artık, `afad_parser` sorgu penceresinin en üst satırına gırerek ad verileri Için alsıd sorgulaması yapmaya hazırsınız demektir.

Daha fazla sorgu örneği için bağlayıcı sayfasındaki **sonraki adımlar** sekmesine bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, AD için alsıd 'yi Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .
