---
title: Azure Kamu 'da Azure geçiş gereci ayarlama
description: Azure Kamu 'da Azure geçiş gereci ayarlamayı öğrenin
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: b0bc2b69a4a1ec31cfa560d51920378fe1ab52b8
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714803"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Azure Kamu 'da gereç ayarlama 

Bir Azure Kamu bulutundaki VMware ortamındaki sunucular, Hyper-V ve fiziksel sunuculardaki sunucular için bir [Azure geçiş](./migrate-appliance-architecture.md) gereci dağıtmak üzere bu makaleyi izleyin. Gereci oluşturmak için bir komut dosyası çalıştırın ve Azure 'a bağlanabildiğini doğrulayın. Genel bulutta bir gereç kurmak istiyorsanız [Bu makaleyi](deploy-appliance-script.md)izleyin.


> [!NOTE]
> Bir cihazı kullanarak (VMware ortamındaki sunucular ve Hyper-V ortamındaki sunucular için) bir gereç dağıtma seçeneği, Azure Kamu 'da desteklenmez.


## <a name="prerequisites"></a>Önkoşullar

Betik, Azure geçişi gerecini mevcut bir fiziksel sunucuda veya sanallaştırılmış bir sunucuda ayarlar.

- Gereç işlevi görecek sunucu Windows Server 2016 ' i, 32 GB bellek, 5. disk depolaması ve bir dış sanal anahtar ile birlikte sekiz V80 CPU ile çalıştırıyor olmalıdır. Statik veya dinamik bir IP adresi gerektirir. 
- Gereci dağıtmadan önce, VMware, [Hyper-V](migrate-appliance.md#appliance---hyper-v)ve [fiziksel sunuculardaki](migrate-appliance.md#appliance---physical) [sunucular](migrate-appliance.md#appliance---vmware)için ayrıntılı gereç gereksinimlerini gözden geçirin.
- Betiği mevcut bir Azure geçiş gereci üzerinde çalıştırmayın.

## <a name="set-up-the-appliance-for-vmware"></a>VMware için gereci ayarlama

VMware için gereci ayarlamak üzere Azure portal daraltılmış bir dosya indirir ve içeriği ayıklayabilir. Gereç Web uygulamasını başlatmak için PowerShell betiğini çalıştırın. Gereci ayarlayın ve ilk kez yapılandırın. Ardından, gereci projeye kaydedersiniz.

### <a name="download-the-script"></a>Betiği indir

1. **Geçiş hedefleri**  >  **Windows, Linux ve SQL Server**  >  **Azure geçişi: bulma ve değerlendirme** için **bul**' a tıklayın.
2. **Sunucu bul**'da  >  **sunucularınız sanallaştırılmış mı?**, **VMware vSphere hiper yöneticiyle Evet '** i seçin.
3. Sıkıştırılmış dosyayı indirmek için **İndir**' e tıklayın.

### <a name="verify-file-security"></a>Dosya güvenliğini doğrula

Dağıtmadan önce daraltılmış dosyanın güvenli olduğunu denetleyin.

1. Dosyayı indirdiğiniz sunucuda, bir yönetici komut penceresi açın.
2. Daraltılmış dosyanın karmasını oluşturmak için aşağıdaki komutu çalıştırın
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Örnek: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. En son gereç sürümünü ve karma değerini doğrulayın:

    **Algoritma** | **İndir** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [En son sürüm](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


### <a name="run-the-script"></a>Betiği çalıştırın

Komut dosyası şu şekildedir:

- Aracıları ve bir Web uygulamasını kurar.
- Windows etkinleştirme hizmeti, IIS ve PowerShell ıSE dahil Windows rollerini kurar.
- Bir IIS yeniden yazılabilir modülünü indirir ve yükler. [Daha fazla bilgi edinin](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure geçişi için kalıcı ayarlarla bir kayıt defteri anahtarını (HKLM) güncelleştirir.
- Günlük ve yapılandırma dosyalarını şu şekilde oluşturur:
    - **Yapılandırma dosyaları**:%ProgramData%\Microsoft Azure\Config
    - **Günlük dosyaları**:%ProgramData%\Microsoft Azure\Logs

Betiği çalıştırmak için:

1. Sıkıştırılmış dosyayı, Gereç barındıracak sunucuda bir klasöre ayıklayın. Betiği, mevcut bir Azure geçiş gereci ile bir sunucuda çalıştırmayın.
2. Yönetici (yükseltilmiş) ayrıcalıklarla sunucuda PowerShell 'i başlatın.
3. PowerShell dizinini, indirilen sıkıştırılmış dosyadan ayıklanan içerikleri içeren klasör olarak değiştirin.
4. Komut dosyası **AzureMigrateInstaller.ps1** aşağıdaki gibi çalıştırın:
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. Betik başarıyla çalıştıktan sonra gereci ayarlayabilmeniz için gereç Web uygulamasını başlatır. Herhangi bir sorunla karşılaşırsanız, C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log konumundaki betik günlüklerini gözden geçirin.

### <a name="verify-access"></a>Erişimi doğrulama

Gerecin [kamu bulutları](migrate-appliance.md#government-cloud-urls)Için Azure URL 'lerine bağlanabildiğinizden emin olun.


## <a name="set-up-the-appliance-for-hyper-v"></a>Hyper-V için gereci ayarlama

Hyper-V için gereci ayarlamak üzere Azure portal daraltılmış bir dosya indirir ve içeriği ayıklayabilir. Gereç Web uygulamasını başlatmak için PowerShell betiğini çalıştırın. Gereci ayarlayın ve ilk kez yapılandırın. Ardından, gereci projeye kaydedersiniz.

### <a name="download-the-script"></a>Betiği indir

1.  **Geçiş hedefleri**  >  **Windows, Linux ve SQL Server**  >  **Azure geçişi: bulma ve değerlendirme** için **bul**' a tıklayın.
2.  Sunucuları **bul**' da  >  **sunucularınız sanallaştırılır mi?**, **Hyper-V ile Evet '** i seçin.
3.  Sıkıştırılmış dosyayı indirmek için **İndir**' e tıklayın. 


### <a name="verify-file-security"></a>Dosya güvenliğini doğrula

Dağıtmadan önce daraltılmış dosyanın güvenli olduğunu denetleyin.

1. Dosyayı indirdiğiniz sunucuda, bir yönetici komut penceresi açın.
2. Daraltılmış dosyanın karmasını oluşturmak için aşağıdaki komutu çalıştırın
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Örnek: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. En son gereç sürümünü ve karma değerini doğrulayın:

    **Senaryo** | **İndir** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [En son sürüm](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

### <a name="run-the-script"></a>Betiği çalıştırın

Komut dosyası şu şekildedir:

- Aracıları ve bir Web uygulamasını kurar.
- Windows etkinleştirme hizmeti, IIS ve PowerShell ıSE dahil Windows rollerini kurar.
- Bir IIS yeniden yazılabilir modülünü indirir ve yükler. [Daha fazla bilgi edinin](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure geçişi için kalıcı ayarlarla bir kayıt defteri anahtarını (HKLM) güncelleştirir.
- Günlük ve yapılandırma dosyalarını şu şekilde oluşturur:
    - **Yapılandırma dosyaları**:%ProgramData%\Microsoft Azure\Config
    - **Günlük dosyaları**:%ProgramData%\Microsoft Azure\Logs

Betiği çalıştırmak için:

1. Sıkıştırılmış dosyayı, Gereç barındıracak sunucuda bir klasöre ayıklayın. Betiği, var olan bir Azure geçiş gereci çalıştıran bir sunucuda çalıştırmayın emin olun.
2. Yönetici (yükseltilmiş) ayrıcalıklarla sunucuda PowerShell 'i başlatın.
3. PowerShell dizinini, indirilen sıkıştırılmış dosyadan ayıklanan içerikleri içeren klasör olarak değiştirin.
4. Komut dosyası **AzureMigrateInstaller.ps1** aşağıdaki gibi çalıştırın: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. Betik başarıyla çalıştıktan sonra gereci ayarlayabilmeniz için gereç Web uygulamasını başlatır. Herhangi bir sorunla karşılaşırsanız, C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log konumundaki betik günlüklerini gözden geçirin.

### <a name="verify-access"></a>Erişimi doğrulama

Gerecin [kamu bulutları](migrate-appliance.md#government-cloud-urls)Için Azure URL 'lerine bağlanabildiğinizden emin olun.


## <a name="set-up-the-appliance-for-physical-servers"></a>Fiziksel sunucular için gereci ayarlama

VMware için gereci ayarlamak üzere Azure portal daraltılmış bir dosya indirir ve içeriği ayıklayabilir. Gereç Web uygulamasını başlatmak için PowerShell betiğini çalıştırın. Gereci ayarlayın ve ilk kez yapılandırın. Ardından, gereci projeye kaydedersiniz.

### <a name="download-the-script"></a>Betiği indir

1. **Geçiş hedefleri**  >  **Windows, Linux ve SQL Server**  >  **Azure geçişi: bulma ve değerlendirme** için **bul**' a tıklayın.
2. Sunucuları **bul**' da  >  **sunucularınız sanallaştırılır mi?**, **sanallaştırılmış/diğer** seçeneğini belirleyin.
3. Sıkıştırılmış dosyayı indirmek için **İndir**' e tıklayın.

### <a name="verify-file-security"></a>Dosya güvenliğini doğrula

Dağıtmadan önce daraltılmış dosyanın güvenli olduğunu denetleyin.

1. Dosyayı indirdiğiniz sunucularda, bir yönetici komut penceresi açın.
2. Daraltılmış dosyanın karmasını oluşturmak için aşağıdaki komutu çalıştırın
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Örnek: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. En son gereç sürümünü ve karma değerini doğrulayın:

    **Senaryo** | _ 'Yi **İndir** | _ *Karma değeri**
    --- | --- | ---
    Fiziksel (85 MB) | [En son sürüm](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

### <a name="run-the-script"></a>Betiği çalıştırın

Komut dosyası şu şekildedir:

- Aracıları ve bir Web uygulamasını kurar.
- Windows etkinleştirme hizmeti, IIS ve PowerShell ıSE dahil Windows rollerini kurar.
- Bir IIS yeniden yazılabilir modülünü indirir ve yükler. [Daha fazla bilgi edinin](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure geçişi için kalıcı ayarlarla bir kayıt defteri anahtarını (HKLM) güncelleştirir.
- Günlük ve yapılandırma dosyalarını şu şekilde oluşturur:
    - **Yapılandırma dosyaları**:%ProgramData%\Microsoft Azure\Config
    - **Günlük dosyaları**:%ProgramData%\Microsoft Azure\Logs

Betiği çalıştırmak için:

1. Sıkıştırılmış dosyayı, Gereç barındıracak sunucuda bir klasöre ayıklayın. Betiği, var olan bir Azure geçiş gereci çalıştıran bir sunucuda çalıştırmayın emin olun.
2. Yönetici (yükseltilmiş) ayrıcalıklarla sunucuda PowerShell 'i başlatın.
3. PowerShell dizinini, indirilen sıkıştırılmış dosyadan ayıklanan içerikleri içeren klasör olarak değiştirin.
4. Komut dosyası **AzureMigrateInstaller.ps1** aşağıdaki gibi çalıştırın:

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. Betik başarıyla çalıştıktan sonra gereci ayarlayabilmeniz için gereç Web uygulamasını başlatır. Herhangi bir sorunla karşılaşırsanız, C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log konumundaki betik günlüklerini gözden geçirin.

### <a name="verify-access"></a>Erişimi doğrulama

Gerecin [kamu bulutları](migrate-appliance.md#government-cloud-urls)Için Azure URL 'lerine bağlanabildiğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Gereci dağıttıktan sonra, ilk kez yapılandırmanız ve projeye kaydetmeniz gerekir.

- [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)için gereç ayarlayın.
- [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)için gereci ayarlayın.
- [Fiziksel sunucular](how-to-set-up-appliance-physical.md)için gereç ayarlayın.
