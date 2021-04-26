---
title: Visual Studio 'yu kullanarak bir rol için Uzak Masaüstü 'Nü etkinleştirme (Azure Cloud Services klasik)
description: Azure bulut hizmeti uygulamanızı uzak masaüstü bağlantılarına izin verecek şekilde yapılandırma
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ad95cefbdf839c28b0979b051e217a1dfec76eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743245"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-visual-studio"></a>Visual Studio 'Yu kullanarak Azure Cloud Services (klasik) bir rol için Uzak Masaüstü Bağlantısı etkinleştirme

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Azure portalı](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Uzak Masaüstü, Azure 'da çalışan bir rolün masaüstüne erişmenizi sağlar. Çalışırken uygulamanızdaki sorunları gidermek ve tanılamak için Uzak Masaüstü bağlantısı kullanabilirsiniz.

Visual Studio 'nun bulut hizmetleri için sağladığı Yayımlama Sihirbazı, yayımlama işlemi sırasında, sağladığınız kimlik bilgilerini kullanarak uzak masaüstü 'Nü etkinleştirme seçeneği içerir. Bu seçeneğin kullanılması, Visual Studio 2017 sürüm 15,4 ve önceki sürümleri kullanılırken uygundur.

Ancak, Visual Studio 2017 sürüm 15,5 ve sonrası ile, yalnızca tek bir geliştirici olarak çalışmıyorsanız, Yayımlama Sihirbazı aracılığıyla uzak masaüstü 'Nü etkinleştirmenizi öneririz. Projenin başka geliştiriciler tarafından açılabileceği herhangi bir durum için Azure portal, PowerShell aracılığıyla veya sürekli bir dağıtım iş akışındaki bir yayın işlem hattından uzak masaüstü 'Nü etkinleştirin. Bu öneri, Visual Studio 'Nun, bu makalede açıklandığı gibi, bulut hizmeti sanal makinesinde uzak masaüstü ile iletişim kurduğu bir değişiklikten kaynaklanıyor.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Visual Studio 2017 sürüm 15,4 ve önceki sürümleri aracılığıyla uzak masaüstü 'Nü yapılandırma

Visual Studio 2017 sürüm 15,4 ve önceki sürümlerini kullanırken, Yayımla sihirbazında **uzak masaüstünü tüm roller Için etkinleştir** seçeneğini kullanabilirsiniz. Sihirbazı yine de Visual Studio 2017 sürüm 15,5 ve sonraki sürümleriyle kullanmaya devam edebilirsiniz, ancak uzak masaüstü seçeneğini kullanmayın.

1. Visual Studio 'da, Çözüm Gezgini ' de bulut hizmeti projenize sağ tıklayıp **Yayımla**' yı seçerek Yayımlama Sihirbazı ' nı başlatın.

2. Gerekirse Azure aboneliğinizde oturum açın ve **İleri ' yi** seçin.

3. **Ayarlar** sayfasında, **tüm roller Için uzak masaüstünü etkinleştir**' i seçin ve ardından **ayarlar...** bağlantısını seçerek **Uzak Masaüstü yapılandırması** iletişim kutusunu açın.

4. İletişim kutusunun alt kısmındaki **diğer seçenekler**' i seçin. Bu komut, Uzak Masaüstü aracılığıyla bağlanırken kimlik bilgileri şifrelemek için bir sertifika oluşturduğunuz veya seçtiğiniz bir açılan listeyi görüntüler.

   > [!Note]
   > Uzak Masaüstü bağlantısı için gereken sertifikalar, diğer Azure işlemleri için kullandığınız sertifikalardan farklıdır. Uzaktan erişim sertifikası özel bir anahtara sahip olmalıdır.

5. Listeden bir sertifika seçin veya **&lt; Oluştur... &gt;** seçeneğini belirleyin. Yeni bir sertifika oluşturuyorsanız, istendiğinde yeni sertifika için bir kolay ad sağlayın ve **Tamam**' ı seçin. Yeni sertifika açılan liste kutusunda görünür.

6. Bir Kullanıcı adı ve parola belirtin. Mevcut bir hesabı kullanamazsınız. Yeni hesap için Kullanıcı adı olarak "Yönetici" kullanmayın.

7. Hesabın sona ereceği ve sonrasında Uzak Masaüstü bağlantılarının engelleneceğini belirten bir tarih seçin.

8. Gerekli tüm bilgileri sağladıktan sonra **Tamam**' ı seçin. Visual Studio, `.cscfg` `.csdef` Seçilen sertifika kullanılarak şifrelenmiş parola dahil olmak üzere, uzak masaüstü ayarlarını projenizin ve dosyalarınıza ekler.

9. **İleri** düğmesini kullanarak kalan adımları tamamladıktan sonra bulut hizmetinizi yayımlamaya hazırsanız **Yayımla** ' yı seçin. Yayımlamaya hazırsanız **iptal** ' i seçin ve değişiklikleri kaydetmek isteyip Istemediğiniz sorulduğunda **Evet** yanıtını verin. Bulut hizmetinizi daha sonra bu ayarlarla yayımlayabilirsiniz.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Visual Studio 2017 sürüm 15,5 ve üstünü kullanırken uzak masaüstü 'Nü yapılandırma

Visual Studio 2017 sürüm 15,5 ve sonrasında, bir bulut hizmeti projesiyle Yayımlama Sihirbazı 'nı kullanmaya devam edebilirsiniz. Yalnızca tek bir geliştirici olarak çalışıyorsanız, **tüm roller Için uzak masaüstünü etkinleştir** seçeneğini de kullanabilirsiniz.

Bir ekibin parçası olarak çalışıyorsanız, [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) veya [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)'i kullanarak Azure bulut hizmetinde uzak masaüstü 'nü etkinleştirmeniz gerekir.

Bu öneri, Visual Studio 2017 sürüm 15,5 ' deki ve daha sonra bulut hizmeti VM ile iletişim kuran bir değişiklikten kaynaklanır. Yayımla Sihirbazı aracılığıyla uzak masaüstü 'Nü etkinleştirirken, Visual Studio 'nun önceki sürümleri, "RDP eklentisi" olarak adlandırılan sanal makine ile iletişim kurar. Visual Studio 2017 sürüm 15,5 ve üzeri, daha güvenli ve daha esnek olan "RDP uzantısını" kullanmak yerine iletişim kurar. Bu değişiklik Ayrıca, uzak masaüstünü etkinleştirmek için Azure portal ve PowerShell yöntemlerinin de RDP uzantısını kullanmasını sağlayacak şekilde de hizalanır.

Visual Studio RDP uzantısıyla iletişim kurduğunda, TLS üzerinden düz metin parolası iletir. Ancak, projenin yapılandırma dosyaları yalnızca şifrelenmiş bir parolayı depolar ve bu, yalnızca ilk olarak şifrelemek için kullanılan yerel sertifikayla şifresi çözülür.

Bulut hizmeti projesini her seferinde aynı geliştirme bilgisayarından dağıtırsanız, bu yerel sertifika kullanılabilir. Bu durumda, Yayımla sihirbazında **uzak masaüstünü tüm roller Için etkinleştir** seçeneğini kullanmaya devam edebilirsiniz.

Siz veya başka geliştiriciler bulut hizmeti projesini farklı bilgisayarlardan dağıtmak istiyorsanız, bu diğer bilgisayarlar parolanın şifresini çözmek için gerekli sertifikaya sahip olmayacaktır. Sonuç olarak, aşağıdaki hata iletisini görürsünüz:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Bulut hizmetini dağıttığınız her seferinde parolayı değiştirebilirsiniz, ancak bu eylem, uzak masaüstü kullanması gereken herkese karşı uygun hale gelir.

Projeyi bir takımla paylaşıyorsanız, bu seçeneği en iyi şekilde Yayımla sihirbazında kaldırmak ve bunun yerine uzak masaüstünü doğrudan [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) aracılığıyla veya [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)kullanarak etkinleştirmek için kullanabilirsiniz.

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Visual Studio 2017 sürüm 15,5 ve üzeri bir yapı sunucusundan dağıtma

Derleme aracısında Visual Studio 2017 sürüm 15,5 veya sonraki bir sürümün yüklü olduğu bir yapı sunucusundan (örneğin, Azure DevOps Services) bir bulut hizmeti projesi dağıtabilirsiniz. Bu düzenleme ile, dağıtım, şifreleme sertifikasının kullanılabildiği aynı bilgisayardan gerçekleşir.

Azure DevOps Services ' den RDP uzantısını kullanmak için, yapı ardışık düzenine aşağıdaki ayrıntıları ekleyin:

1. `/p:ForceRDPExtensionOverPlugin=true`Dağıtımın RDP eklentisi yerıne RDP uzantısıyla çalıştığından emin olmak Için MSBuild bağımsız değişkenlerine dahil edin. Örnek:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Derleme adımlarınızı tamamladıktan sonra, **Azure Cloud Service dağıtım** adımını ekleyin ve özelliklerini ayarlayın.

1. Dağıtım adımından sonra, bir **Azure PowerShell** adımı ekleyin, **görünen ad** özelliğini "Azure dağıtımı: RDP uzantısını etkinleştir" (veya başka bir uygun ad) olarak ayarlayın ve uygun Azure aboneliğinizi seçin.

1. **Komut dosyası türünü** "inline" olarak ayarlayın ve aşağıdaki kodu **satır içi betik** alanına yapıştırın. (Ayrıca `.ps1` bu betiği kullanarak projenizde bir dosya oluşturabilir, **komut dosyası türünü** "betik dosyası yolu" olarak ayarlayabilir ve **betik yolunu** dosyayı işaret etmek üzere ayarlayabilirsiniz.)

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Uzak Masaüstü kullanarak bir Azure rolüne bağlanma

Bulut hizmetinizi Azure 'da yayımladıktan ve uzak masaüstü 'nü etkinleştirdikten sonra, Cloud Service VM 'de oturum açmak için Visual Studio Sunucu Gezgini kullanabilirsiniz:

1. Sunucu Gezgini ' de, **Azure** düğümünü genişletin ve ardından bir bulut hizmeti ve rollerinin biri için düğümü genişleterek örnek listesini görüntüleyin.

2. Bir örnek düğümüne sağ tıklayın ve **Uzak Masaüstü kullanarak bağlan**' ı seçin.

3. Daha önce oluşturduğunuz Kullanıcı adını ve parolayı girin. Artık uzak oturumunuzda oturum açtınız.

## <a name="additional-resources"></a>Ek kaynaklar

[Cloud Services’ı Yapılandırma](cloud-services-how-to-configure-portal.md)
