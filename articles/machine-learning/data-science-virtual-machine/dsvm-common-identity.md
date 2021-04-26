---
title: Ortak bir kimlik ayarlama
titleSuffix: Azure Data Science Virtual Machine
description: Birden çok veri bilimi sanal makinesi genelinde kullanılabilecek ortak Kullanıcı hesapları oluşturmayı öğrenin. Veri Bilimi Sanal Makinesi kullanıcıların kimliğini doğrulamak için Azure Active Directory veya şirket içi Active Directory kullanabilirsiniz.
keywords: derin öğrenme, AI, veri bilimi araçları, veri bilimi sanal makinesi, Jeo-uzamsal analiz, ekip veri bilimi işlemi
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 43ef0f55f0b0f3ff9008f0ddf240ad0ea4f37df6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519737"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Veri Bilimi Sanal Makinesi ortak bir kimlik ayarlama

Bir Veri Bilimi Sanal Makinesi (DSVM) dahil Microsoft Azure bir sanal makinede (VM), VM sağlanırken yerel kullanıcı hesapları oluşturursunuz. Ardından kullanıcılar bu kimlik bilgilerini kullanarak VM 'de kimlik doğrular. Kullanıcılarınızın erişmesi gereken birden çok VM 'niz varsa, kimlik bilgilerini yönetmek çok fazla sayıda olabilir. Mükemmel bir çözüm, standart tabanlı bir kimlik sağlayıcısı aracılığıyla ortak Kullanıcı hesapları ve yönetimi dağıtmaktır. Bu yaklaşımda, birden çok DSVMs dahil olmak üzere Azure 'daki birden fazla kaynağa erişmek için tek bir kimlik bilgileri kümesi kullanabilirsiniz.

Active Directory, popüler bir kimlik sağlayıcıdır ve Azure 'da hem bulut hizmeti hem de şirket içi dizin olarak desteklenir. Bir Azure sanal makine ölçek kümesindeki tek başına DSVM veya bir DSVMs kümesi üzerinde kullanıcıların kimliğini doğrulamak için Azure Active Directory (Azure AD) veya şirket içi Active Directory kullanabilirsiniz. Bunu, DSVM örneklerini bir Active Directory etki alanına ekleyerek yapabilirsiniz.

Zaten Active Directory varsa, bunu ortak kimlik sağlayıcınız olarak kullanabilirsiniz. Active Directory yoksa, Azure 'da [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) (Azure AD DS) aracılığıyla yönetilen bir Active Directory örneğini çalıştırabilirsiniz.

[Azure AD](../../active-directory/index.yml) belgeleri, Azure AD 'niz varsa şirket içi dizininize bağlama hakkında rehberlik dahil olmak üzere ayrıntılı [Yönetim yönergeleri](../../active-directory/hybrid/whatis-hybrid-identity.md)sağlar.

Bu makalede Azure AD DS kullanarak Azure 'da tam olarak yönetilen bir Active Directory etki alanı hizmetinin nasıl ayarlanacağı açıklanır. Daha sonra DSVMs 'nizi yönetilen Active Directory etki alanına ekleyebilirsiniz. Bu yaklaşım, kullanıcıların bir DSVMs havuzuna (ve diğer Azure kaynaklarına) ortak bir kullanıcı hesabı ve kimlik bilgileriyle erişmelerini sağlar.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Azure 'da tam olarak yönetilen Active Directory etki alanı ayarlama

Azure AD DS, Azure üzerinde tam olarak yönetilen bir hizmet sağlayarak kimliklerinizi yönetmeyi kolaylaştırır. Bu Active Directory etki alanında kullanıcıları ve grupları yönetirsiniz. Dizininizde Azure 'da barındırılan Active Directory etki alanı ve Kullanıcı hesapları ayarlamak için şu adımları izleyin:

1. Azure portal, kullanıcıyı Active Directory ekleyin: 

   1. [Azure Active Directory Yönetim merkezinde](https://aad.portal.azure.com) , dizin için genel yönetici olan bir hesap kullanarak oturum açın.
    
   1. **Azure Active Directory**'yi ve ardından **Kullanıcılar ve gruplar**'ı seçin.
    
   1. **Kullanıcılar ve gruplar**' da, **tüm kullanıcılar**' ı seçin ve ardından **Yeni Kullanıcı**' yı seçin.
   
        **Kullanıcı** bölmesi açılır:
      
        !["Kullanıcı" bölmesi](./media/add-user.png)
    
   1. **Ad** ve **Kullanıcı adı** gibi kullanıcı ayrıntılarını girin. Kullanıcı adının etki alanı adı kısmı, ilk varsayılan etki alanı adı "[etki alanı adı]. onmicrosoft. com" veya "contoso.com" gibi doğrulanmış, Federasyon dışı bir [özel etki alanı adı](../../active-directory/fundamentals/add-custom-domain.md) olmalıdır.
    
   1. Bu işlem tamamlandıktan sonra kullanıcıyla paylaşabilmek için oluşturulan kullanıcı parolasını kopyalayın veya bir yere not edin.
    
   1. İsteğe bağlı olarak kullanıcının **Profil**, **Gruplar** veya **Dizin rolü** bilgilerini doldurun. 
    
   1. **Kullanıcı** altında **Oluştur**' u seçin.
    
   1. Oluşturulan parolayı, oturum açabilmeniz için yeni kullanıcıya güvenli bir şekilde dağıtın.

1. Azure AD DS örneği oluşturun. Azure portal ("bir örnek oluşturma ve temel ayarları yapılandırma" bölümünde)  [Azure Active Directory Domain Services etkinleştir](../../active-directory-domain-services/tutorial-create-instance.md) bölümündeki yönergeleri izleyin. Azure AD DS parolasının eşitlenmesi için Active Directory mevcut kullanıcı parolalarının güncelleştirilmesi önemlidir. Ayrıca, bu bölümdeki "bir Azure AD DS örneği oluşturmak için Azure portal temel bilgileri penceresindeki alanları doldurun" bölümünde açıklandığı gibi, Azure AD DS DNS eklemek de önemlidir.

1. Yukarıdaki adımın "sanal ağ oluşturma ve yapılandırma" bölümünde oluşturulan sanal ağda ayrı bir DSVM alt ağı oluşturun.
1. DSVM alt ağında bir veya daha fazla DSVM örneği oluşturun.
1. Active Directory 'e DSVM eklemek için [yönergeleri](../../active-directory-domain-services/join-ubuntu-linux-vm.md) izleyin. 
1. Çalışma alanınızın herhangi bir makineye bağlanmasını sağlamak için ev veya Not defteri dizininizi barındırmak üzere bir Azure dosya paylaşımının bağlama. (Sıkı dosya düzeyi izinlere ihtiyacınız varsa, bir veya daha fazla VM üzerinde çalışan ağ dosya sistemi [NFS] gerekir.)

   1. [Azure dosya paylaşma oluşturun](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Bu paylaşımın Linux DSVM üzerine takın. Azure portal depolama hesabınızda Azure dosya paylaşımının **bağlantısını** seçtiğinizde, LINUX dsvm 'de bash kabuğu 'nda çalıştırılacak komut görüntülenir. Komut şöyle görünür:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Örneğin, Azure dosya paylaşımınızı/Data/Workspace dizinine bağladığınızı varsayalım. Şimdi paylaşımdaki her bir kullanıcı için dizinler oluşturun:/Data/Workspace/User1,/Data/Workspace/User2 vb. `notebooks`Her kullanıcının çalışma alanında bir dizin oluşturun. 
1. İçinde için sembolik bağlantılar `notebooks` oluşturun `$HOME/userx/notebooks/remote` .   

Artık Azure 'da barındırılan Active Directory örneğindeki kullanıcılara sahipsiniz. Kullanıcılar, Active Directory kimlik bilgilerini kullanarak Azure AD DS 'a katılmış herhangi bir DSVM 'de (SSH veya jupi) oturum açabilirler. Kullanıcı çalışma alanı bir Azure dosya paylaşımında olduğu için, kullanıcıların, jupi 'yi kullanırken herhangi bir DSVM 'deki not defterlerine ve diğer çalışmasına erişimi vardır.

Otomatik ölçeklendirme için, bir sanal makine ölçek kümesi kullanarak, etki alanına bu şekilde katılmış ve paylaşılan disk takılmış bir VM havuzu oluşturabilirsiniz. Kullanıcılar, sanal makine ölçek kümesindeki kullanılabilir herhangi bir makinede oturum açabilir ve onun Not defterlerinin kaydedildiği paylaşılan diske erişebilir. 

## <a name="next-steps"></a>Sonraki adımlar

* [Bulut kaynaklarına erişmek için kimlik bilgilerini güvenli bir şekilde depolayın](dsvm-secure-access-keys.md)