---
title: Bir komut dosyası ile Azure geçişi gereci ayarlama
description: Bir komut dosyası ile Azure geçişi gereci ayarlamayı öğrenin
ms.topic: how-to
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: ff05a01ad8173923ff614657d0231f743f38ba1c
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714767"
---
# <a name="set-up-an-appliance-with-a-script"></a>Komut dosyası ile gereç ayarlama

VMware ve Hyper-V ' d e sunucularda değerlendirme/geçiş için bir [Azure geçiş](./migrate-appliance-architecture.md) gereci oluşturmak üzere bu makaleyi izleyin. Bir gereç oluşturmak için bir komut dosyası çalıştırın ve Azure 'a bağlanabildiğini doğrulayın. 

Bir betiği kullanarak VMware ve Hyper-V üzerinde sunucular için gereci dağıtabilir veya Azure portal yüklediğiniz bir şablonu kullanabilirsiniz. İndirilen şablonu kullanarak bir gereç oluştursanız komut dosyası kullanmak faydalıdır.

- Bir şablon kullanmak için [VMware](./tutorial-discover-vmware.md) veya [Hyper-V](./tutorial-discover-hyper-v.md)öğreticilerini izleyin.
- Fiziksel sunucular için bir gereç ayarlamak üzere yalnızca bir komut dosyası kullanabilirsiniz. [Bu makaleyi](how-to-set-up-appliance-physical.md)izleyin.
- Azure Kamu bulutunda bir gereç ayarlamak için [Bu makaleyi](deploy-appliance-script-government.md)izleyin.

## <a name="prerequisites"></a>Önkoşullar

Betik, mevcut bir sunucuda Azure geçişi gereci ayarlar.

- Gereç görevi görecek sunucu, aşağıdaki donanım ve işletim sistemi gereksinimlerini karşılamalıdır:

Senaryo | Gereksinimler
--- | ---
VMware | Windows Server 2016, 32 GB bellek, sekiz vCPU, yaklaşık 80 GB disk depolaması
Hyper-V | 16 GB bellek, 8 GB disk depolaması 80 etrafında sekiz vCPU ile Windows Server 2016

- Sunucuda Ayrıca bir dış sanal anahtar gerekir. Statik veya dinamik bir IP adresi gerektirir. 
- Gereci dağıtmadan önce, [Hyper-V üzerinde](migrate-appliance.md#appliance---hyper-v) [VMware 'deki sunucular](migrate-appliance.md#appliance---vmware)için ayrıntılı gereç gereksinimlerini gözden geçirin.
- Betiği mevcut bir Azure geçiş gereci üzerinde çalıştırmayın.

## <a name="set-up-the-appliance-for-vmware"></a>VMware için gereci ayarlama

VMware için gereci ayarlamak için, AzureMigrateInstaller-Server-Public.zip adlı daraltılmış dosyayı portaldan veya [buradan](https://go.microsoft.com/fwlink/?linkid=2140334)indirin ve içeriği ayıklayın. Gereç Web uygulamasını başlatmak için PowerShell betiğini çalıştırın. Gereci ayarlayın ve ilk kez yapılandırın. Ardından, gereci proje ile kaydedersiniz.

### <a name="verify-file-security"></a>Dosya güvenliğini doğrula

Dağıtmadan önce daraltılmış dosyanın güvenli olduğunu denetleyin.

1. Dosyayı indirdiğiniz sunucuda, bir yönetici komut penceresi açın.
2. Daraltılmış dosyanın karmasını oluşturmak için aşağıdaki komutu çalıştırın
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Örnek: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Azure genel bulutu için en son gereç sürümünü ve betiğini doğrulayın:

    **Algoritma** | **İndir** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [En son sürüm](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72

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

1. Sıkıştırılmış dosyayı, Gereç barındıracak sunucuda bir klasöre ayıklayın. Betiği mevcut bir Azure geçişi gereci üzerinde çalıştırmayın emin olun.
2. Yönetici (yükseltilmiş) ayrıcalıklarla sunucuda PowerShell 'i başlatın.
3. PowerShell dizinini, indirilen sıkıştırılmış dosyadan ayıklanan içerikleri içeren klasör olarak değiştirin.
4. Komut dosyası **AzureMigrateInstaller.ps1** aşağıdaki gibi çalıştırın:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
  
5. Betik başarıyla çalıştıktan sonra gereci ayarlayabilmeniz için gereç Web uygulamasını başlatır. Herhangi bir sorunla karşılaşırsanız, C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log konumundaki betik günlüklerini gözden geçirin.

### <a name="verify-access"></a>Erişimi doğrulama

Gerecin [genel](migrate-appliance.md#public-cloud-urls) bulut Için Azure URL 'lerine bağlanabildiğinizden emin olun.

## <a name="set-up-the-appliance-for-hyper-v"></a>Hyper-V için gereci ayarlama

Hyper-V için gereci ayarlamak için portaldan veya [buradan](https://go.microsoft.com/fwlink/?linkid=2105112)AzureMigrateInstaller-Server-Public.zip adlı daraltılmış dosyayı indirin ve içeriği ayıklayın. Gereç Web uygulamasını başlatmak için PowerShell betiğini çalıştırın. Gereci ayarlayın ve ilk kez yapılandırın. Ardından, gereci proje ile kaydedersiniz.


### <a name="verify-file-security"></a>Dosya güvenliğini doğrula

Dağıtmadan önce daraltılmış dosyanın güvenli olduğunu denetleyin.

1. Dosyayı indirdiğiniz sunucuda, bir yönetici komut penceresi açın.
2. Daraltılmış dosyanın karmasını oluşturmak için aşağıdaki komutu çalıştırın
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Örnek: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Azure genel bulutu için en son gereç sürümünü ve betiğini doğrulayın:

    **Senaryo** | **İndir** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [En son sürüm](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

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

1. Sıkıştırılmış dosyayı, Gereç barındıracak sunucuda bir klasöre ayıklayın. Betiği mevcut bir Azure geçişi gereci üzerinde çalıştırmayın emin olun.
2. Yönetici (yükseltilmiş) ayrıcalıklarla sunucuda PowerShell 'i başlatın.
3. PowerShell dizinini, indirilen sıkıştırılmış dosyadan ayıklanan içerikleri içeren klasör olarak değiştirin.
4. Komut dosyası **AzureMigrateInstaller.ps1** aşağıdaki gibi çalıştırın:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Betik başarıyla çalıştıktan sonra gereci ayarlayabilmeniz için gereç Web uygulamasını başlatır. Herhangi bir sorunla karşılaşırsanız, C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log konumundaki betik günlüklerini gözden geçirin.

### <a name="verify-access"></a>Erişimi doğrulama

Gerecin [genel](migrate-appliance.md#public-cloud-urls) bulut Için Azure URL 'lerine bağlanabildiğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Gereci dağıttıktan sonra, ilk kez yapılandırmanız ve projeye kaydetmeniz gerekir.

- [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)için gereç ayarlayın.
- [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)için gereci ayarlayın.