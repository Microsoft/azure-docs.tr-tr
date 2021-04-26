---
title: Yazılım envanterini, bağımlılıklarını ve SQL Server örneklerini ve veritabanlarını bulmaya yönelik sunucu kimlik bilgilerini sağlayın
description: Gereç Yapılandırma Yöneticisi 'nde sunucu kimlik bilgilerini nasıl sağlayacağınızı öğrenin
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: e72d509f71704bbf8608543df5e819a9b8783935
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562078"
---
# <a name="provide-server-credentials-to-discover-software-inventory-dependencies-and-sql-server-instances-and-databases"></a>Yazılım envanterini, bağımlılıklarını ve SQL Server örneklerini ve veritabanlarını bulmaya yönelik sunucu kimlik bilgilerini sağlayın

Yazılım envanterini (yüklü uygulamaları bulma), aracısız bağımlılık analizini ve SQL Server örnekleri ve veritabanlarını bulma işlemini gerçekleştirmek için gereç Yapılandırma Yöneticisi 'nde birden çok sunucu kimlik bilgisi eklemeyi öğrenmek için bu makaleyi izleyin.

> [!Note]
> VMware ortamınızda çalışan SQL Server örnekleri ve veritabanlarının keşfi ve değerlendirmesi artık önizlemededir. Bu özelliği denemek için [**bu bağlantıyı**](https://aka.ms/AzureMigrate/SQL) kullanarak **Doğu Avustralya** bölgesinde bir proje oluşturun. Zaten Doğu Avustralya bölgesinde bir projeniz varsa ve bu özelliği denemek istiyorsanız, lütfen portalda bu [**önkoşulları**](how-to-discover-sql-existing-project.md) tamamladığınızdan emin olun.

Azure [geçişi](migrate-appliance.md) gereci, VMware ortamında çalışan şirket içi sunucuları bulmak ve Azure 'a sunucu yapılandırma ve performans meta verileri göndermek Için Azure geçişi: bulma ve değerlendirme tarafından kullanılan hafif bir gereç. Gereç Ayrıca yazılım envanteri, aracısız bağımlılık Analizi ve SQL Server örneklerinin ve veritabanlarının keşfedilmesine olanak sağlamak için de kullanılabilir.

Bu özellikleri kullanmak istiyorsanız, aşağıdaki adımları izleyerek sunucu kimlik bilgileri sağlayabilirsiniz. Gereç, bulma özelliklerini gerçekleştirmek için kimlik bilgilerini sunucularla otomatik olarak eşlemeye çalışır.

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>VMware ortamında çalışan sunucular için kimlik bilgileri ekleme

### <a name="types-of-server-credentials-supported"></a>Desteklenen sunucu kimlik bilgileri türleri

Gereç Yapılandırma Yöneticisi 'nde etki alanı, etki alanı olmayan (Windows veya Linux) veya SQL Server kimlik doğrulama kimlik bilgileri olabilen birden çok sunucu kimlik bilgilerini ekleyebilirsiniz.

Desteklenen sunucu kimlik bilgileri türleri aşağıdaki tabloda listelenmiştir:

Kimlik bilgileri türü | Description
--- | ---
**Etki alanı kimlik bilgileri** | **Kimlik bilgileri ekle** kalıcı seçeneğinde açılan listeden seçeneği belirleyerek **etki alanı kimlik bilgilerini** ekleyebilirsiniz. <br/><br/> Etki alanı kimlik bilgilerini sağlamak için, FQDN biçiminde (örneğin, prod.corp.contoso.com) sağlanması gereken **etki alanı adını** belirtmeniz gerekir. <br/><br/> Ayrıca kimlik bilgileri, Kullanıcı adı ve parola için kolay bir ad belirtmeniz gerekir. <br/><br/> Eklenen etki alanı kimlik bilgileri, etki alanının Active Directory karşı özgünlük için otomatik olarak doğrulanacak. Bu, Gereç etki alanı kimlik bilgilerini bulunan sunucularla eşlemeyi denediğinde hesap kilitlenmelerini önlemektir. <br/><br/> Gereç, doğrulanamadı hatasıyla başarısız olan etki alanı kimlik bilgilerini eşlemeye çalışmaz. Yazılım envanterine devam etmek için en az bir etki alanı kimlik bilgisinin veya etki alanı olmayan en az bir kimlik bilgisinin olması gerekir.<br/><br/>Windows Server 'larda otomatik olarak eşlenen etki alanı kimlik bilgileri, yazılım envanteri gerçekleştirmek için kullanılır ve ayrıca, SQL Server örnekleri ve veritabanlarını _(SQL sunucularınızda Windows kimlik doğrulama modu yapılandırdıysanız)_ keşfetmesi için de kullanılabilir.<br/> SQL Server 'Lar üzerinde desteklenen kimlik doğrulama modu türleri hakkında [daha fazla bilgi edinin](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) .
**Etki alanı dışı kimlik bilgileri (Windows/Linux)** | **Kimlik bilgileri ekle** kalıcı ' nın açılan listesinden gerekli seçeneği belirleyerek **Windows (etki alanı olmayan)** veya **Linux (etki alanı olmayan)** ekleyebilirsiniz. <br/><br/> Kimlik bilgileri, Kullanıcı adı ve parola için kolay bir ad belirtmeniz gerekir.
**Kimlik doğrulama kimlik bilgilerini SQL Server** | Kimlik doğrulama kimlik bilgilerini **Ekle** ' ye tıklayarak **SQL Server kimlik doğrulaması** bilgilerini ekleyebilirsiniz. <br/><br/> Kimlik bilgileri, Kullanıcı adı ve parola için kolay bir ad belirtmeniz gerekir. <br/><br/> SQL sunucularınızda SQL Server kimlik doğrulama modu yapılandırdıysanız VMware ortamınızda çalıştırılan SQL Server örnekleri ve veritabanlarını öğrenmek için bu kimlik bilgilerini ekleyebilirsiniz.<br/> SQL Server 'Lar üzerinde desteklenen kimlik doğrulama modu türleri hakkında [daha fazla bilgi edinin](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) .<br/><br/> Gerecin, SQL Server örnekleri ve veritabanlarını öğrenmek için SQL Server kimlik doğrulama kimlik bilgilerini kullanmadan önce sunucularda yüklü olan SQL 'i bulabilmesi üzere yazılım envanterini tamamlayabilmesi için, en az bir başarıyla doğrulanan etki alanı kimlik bilgisi veya en az bir Windows (etki alanı olmayan) kimlik bilgisi sağlamanız gerekir.

Yazılım envanterini gerçekleştirmek için Windows/Linux kimlik bilgilerinde gerekli izinleri denetleyin, aracısız bağımlılık analizini yapın ve SQL Server örnekleri ve veritabanlarını bulun.

### <a name="required-permissions"></a>Gerekli izinler

Aşağıdaki tabloda, ilgili özellikleri gerçekleştirmek için gereçte sunulan sunucu kimlik bilgileri için gereken izinler listelenmiştir:

Özellik | Windows kimlik bilgilerinizi sağlamanız gerekmez | Linux kimlik bilgileri
---| ---| ---
**Yazılım envanteri** | Konuk Kullanıcı hesabı | Normal/normal kullanıcı hesabı (sudo olmayan erişim izinleri)
**SQL Server örnekleri ve veritabanlarının keşfi** | Sysadmin sunucu rolünün üyesi olan kullanıcı hesabı. | _Şu anda desteklenmiyor_
**Aracısız bağımlılık analizi** | Yönetici izinlerine sahip etki alanı veya etki alanı olmayan (yerel) hesap | Kök kullanıcı hesabı veya <br/> /bin/netstat ve/bin/ls dosyalarında şu izinlere sahip bir hesap: CAP_DAC_READ_SEARCH ve CAP_SYS_PTRACE.<br/><br/> Aşağıdaki komutları kullanarak bu özellikleri ayarlayın: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>Kimlik bilgilerini sağlamak için önerilen uygulamalar

- [Gerekli izinlere](add-server-credentials.md#required-permissions)sahip bir ayrılmış etki alanı kullanıcı hesabı oluşturmanız önerilir, bu, yazılım envanteri, aracısız bağımlılık analizi ve SQL Server örneklerinin ve veritabanlarının istenen sunucularda keşfedilmesi kapsamına alınır.
- Yazılım envanterini başlatmak için en az bir başarıyla doğrulanan etki alanı kimlik bilgisi veya etki alanı olmayan en az bir kimlik bilgisi sağlamanız önerilir.
- SQL Server örnekleri ve veritabanlarını öğrenmek için, SQL sunucularınızda Windows kimlik doğrulama modunu yapılandırdıysanız etki alanı kimlik bilgileri sağlayabilirsiniz.
- Ayrıca, SQL sunucularınızda SQL Server kimlik doğrulama modunu yapılandırdıysanız SQL Server kimlik doğrulama kimlik bilgileri sağlayabilirsiniz, ancak gerecin yazılım envanterini tamamlayabilmesi için en az bir başarıyla doğrulanan etki alanı kimlik bilgisi veya en az bir Windows (etki alanı olmayan) kimlik bilgisi sağlamanız önerilir.

## <a name="credentials-handling-on-appliance"></a>Gereç üzerinde kimlik bilgileri işleme

- Gereç Yapılandırma Yöneticisi 'nde belirtilen tüm kimlik bilgileri, Gereç sunucusunda yerel olarak depolanır ve Azure 'a gönderilmez.
- Gereç sunucusunda depolanan kimlik bilgileri, veri koruma API 'SI (DPAPI) kullanılarak şifrelenir.
- Kimlik bilgilerini ekledikten sonra, Gereç ilgili sunucularda bulma işlemini gerçekleştirmek için kimlik bilgilerini otomatik olarak eşlemeye çalışır.
- Gereç, kimlik bilgilerinin gerekli bulma verilerini alabilmesi için bir sunucuda otomatik olarak eşlenen kimlik bilgilerini kullanır. Kimlik bilgileri çalışmayı durdurduktan sonra, Gereç yeniden eklenen kimlik bilgileri listesinden eşlemeyi dener ve sunucuda devam eden bulma işlemine devam eder.
- Eklenen etki alanı kimlik bilgileri, etki alanının Active Directory karşı özgünlük için otomatik olarak doğrulanacak. Bu, Gereç etki alanı kimlik bilgilerini bulunan sunucularla eşlemeyi denediğinde hesap kilitlenmelerini önlemektir. Gereç, doğrulanamadı hatasıyla başarısız olan etki alanı kimlik bilgilerini eşlemeye çalışmaz.
- Gereç, bir etki alanı veya etki alanı olmayan kimlik bilgilerini bir sunucuyla eşleştiremez, projenizdeki sunucuda "kimlik bilgileri kullanılamıyor" durumunu görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar

[VMware ortamınızda çalışan sunucuları bulmaya](tutorial-discover-vmware.md) yönelik öğreticileri gözden geçirin