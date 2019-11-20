---
title: PowerShell kullanarak her zaman açık kullanılabilirlik grubunu bir Azure VM üzerinde yapılandırma | Microsoft Docs
description: Bu öğretici, klasik dağıtım modeliyle oluşturulan kaynakları kullanır. Azure 'da her zaman açık kullanılabilirlik grubu oluşturmak için PowerShell 'i kullanırsınız.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: 89f731062ce46969c73f745d62b289b3b3483d8c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100366"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>PowerShell ile her zaman açık kullanılabilirlik grubunu bir Azure VM üzerinde yapılandırma
> [!div class="op_single_selector"]
> * [Klasik 'SINI](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klasik PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Başlamadan önce, artık bu görevi Azure Resource Manager modelinde tamamlayacağınızı düşünün. Yeni dağıtımlar için Azure Resource Manager modeli önerilir. Bkz. [Azure sanal makineler 'de Always on kullanılabilirlik grupları SQL Server](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> En yeni dağıtımların Kaynak Yöneticisi modelini kullanmasını öneririz. Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../../../azure-resource-manager/resource-manager-deployment-model.md). Bu makale klasik dağıtım modelini incelemektedir.

Azure sanal makineleri (VM 'Ler), veritabanı yöneticilerinin yüksek kullanılabilirliğe sahip bir SQL Server sisteminin maliyetini düşürmesine yardımcı olabilir. Bu öğreticide, Azure ortamında her zaman uçtan uca SQL Server kullanarak bir kullanılabilirlik grubunun nasıl uygulanacağı gösterilmektedir. Öğreticinin sonunda, her zaman Azure 'daki SQL Server çözümü aşağıdaki öğelerden oluşur:

* Ön uç ve arka uç alt ağı dahil olmak üzere birden çok alt ağ içeren bir sanal ağ.
* Active Directory etki alanı olan bir etki alanı denetleyicisi.
* Arka uç alt ağına dağıtılan ve Active Directory etki alanına katılmış iki SQL Server VM.
* Düğüm çoğunluğu çekirdek modeliyle üç düğümlü bir Windows Yük devretme kümesi.
* Bir kullanılabilirlik veritabanının iki zaman uyumlu-kayıt çoğaltması olan bir kullanılabilirlik grubu.

Bu senaryo, maliyet verimliliği veya diğer faktörlere yönelik olarak değil, Azure 'da kolaylık sağlaması için iyi bir seçimdir. Örneğin, bir iki düğümlü bir kullanılabilirlik grubu için, iki düğümlü bir yük devretme kümesinde çekirdek dosya paylaşma tanığı olarak etki alanı denetleyicisini kullanarak, Azure 'daki işlem saatlerine kaydedilecek VM sayısını en aza indirmenize olanak sağlayabilirsiniz. Bu yöntem, yukarıdaki yapılandırmadan bir VM sayısını azaltır.

Bu öğretici, her adımın ayrıntılarında elaborating olmadan yukarıda açıklanan çözümü kurmak için gereken adımları göstermeye yöneliktir. Bu nedenle, GUI yapılandırma adımları sağlamak yerine, her adımla hızlı bir şekilde yararlanmak için PowerShell komut dosyalarını kullanır. Bu öğreticide aşağıdakiler varsayılmaktadır:

* Sanal makine aboneliğine sahip bir Azure hesabınız zaten var.
* [Azure PowerShell cmdlet 'lerini](/powershell/azure/overview)yüklediniz.
* Şirket içi çözümler için her zaman açık kullanılabilirlik gruplarının katı olarak anlaşılmış olursunuz. Daha fazla bilgi için bkz. [Always on kullanılabilirlik grupları (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Azure aboneliğinize bağlanın ve sanal ağı oluşturun
1. Yerel bilgisayarınızdaki bir PowerShell penceresinde, Azure modülünü içeri aktarın, yayımlama ayarları dosyasını makinenize indirin ve indirilen yayımlama ayarlarını içeri aktararak PowerShell oturumunuzu Azure aboneliğinize bağlayın.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    **Get-Azuikinci olarak SettingsFile** komutu, Azure ile bir yönetim sertifikası oluşturur ve makinenize indirir. Bir tarayıcı otomatik olarak açılır ve Azure aboneliğiniz için Microsoft hesabı kimlik bilgilerini girmeniz istenir. İndirilen **. publishsettings** dosyası, Azure aboneliğinizi yönetmek için ihtiyacınız olan tüm bilgileri içerir. Bu dosyayı yerel bir dizine kaydettikten sonra **Import-Azuikinci dosya SettingsFile** komutunu kullanarak içeri aktarın.

   > [!NOTE]
   > . Publishsettings dosyası, Azure aboneliklerinizi ve hizmetlerinizi yönetmek için kullanılan kimlik bilgilerinizi (Kodlanmamış) içerir. Bu dosya için en iyi güvenlik uygulaması, kaynak dizinlerinizin dışında (örneğin, Kütüphanaries\belgeler klasöründe) geçici olarak depolanması ve içeri aktarma tamamlandıktan sonra onu silmektir. . Publishsettings dosyasına erişim sağlayan kötü niyetli bir Kullanıcı, Azure hizmetlerinizi düzenleyebilir, oluşturabilir ve silebilir.

2. Bulut BT altyapınızı oluşturmak için kullanacağınız bir dizi değişken tanımlayın.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Komutlarınızın daha sonra başarılı olacağını sağlamak için aşağıdakilere dikkat edin:

   * **$StorageAccountName** ve **$DcServiceName** değişkenleri, Internet 'te sırasıyla bulut depolama hesabınızı ve bulut sunucunuzu tanımlamak için kullanıldıklarından benzersiz olmalıdır.
   * **$AffinityGroupName** ve **$virtualNetworkName** değişkenleri için belirttiğiniz adlar, daha sonra kullanacağınız sanal ağ yapılandırma belgesinde yapılandırılır.
   * **$sqlImageName** , SQL Server 2012 Service Pack 1 Enterprise EDITION içeren VM görüntüsünün güncelleştirilmiş adını belirtir.
   * Kolaylık sağlaması için **contoso! 000** , tüm öğreticinin tamamında kullanılan parolayla aynıdır.

3. Benzeşim grubu oluşturun.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Bir yapılandırma dosyasını içeri aktararak bir sanal ağ oluşturun.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Yapılandırma dosyası aşağıdaki XML belgesini içerir. Kısaca, **Contosoag**adlı benzeşim grubunda **ContosoNET** adlı bir sanal ağı belirtir. **10.10.0.0/16** adres alanına sahiptir ve sırasıyla ön alt ağ ve arka alt ağ olan **10.10.1.0/24** ve **10.10.2.0/24**olmak üzere iki alt ağa sahip olur. Ön alt ağ, Microsoft SharePoint gibi istemci uygulamaları yerleştirebileceğiniz yerdir. Arka alt ağ, SQL Server VM 'Leri yerleştireceğiniz yerdir. Daha önce **$affinityGroupName** ve **$virtualNetworkName** değişkenlerini değiştirirseniz, aşağıdaki ilgili adları da değiştirmeniz gerekir.

        <NetworkConfiguration xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. Oluşturduğunuz benzeşim grubuyla ilişkili bir depolama hesabı oluşturun ve bunu aboneliğinizde geçerli depolama hesabı olarak ayarlayın.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Yeni bulut hizmeti ve kullanılabilirlik kümesinde etki alanı denetleyicisi sunucusunu oluşturun.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Bu kullanıma yönelik bu komutlar aşağıdaki işlemleri yapar:

   * **New-AzureVMConfig** bir VM yapılandırması oluşturur.
   * **Add-AzureProvisioningConfig** , tek başına bir Windows Server 'ın yapılandırma parametrelerini sağlar.
   * **Add-AzureDataDisk** , önbelleğe alma seçeneği None olarak ayarlanmış şekilde, Active Directory verilerini depolamak için kullanacağınız veri diskini ekler.
   * **New-AzureVM** yeni bir bulut hizmeti oluşturur ve yeni bulut hizmetinde yenı Azure VM oluşturur.

7. Yeni VM 'nin tam olarak sağlanması için bekleyin ve uzak masaüstü dosyasını çalışma dizininize indirin. Yeni Azure VM 'nin sağlanması uzun zaman aldığı için, `while` döngü kullanıma hazırlanana kadar yeni VM 'yi yoklamaya devam eder.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

Etki alanı denetleyicisi sunucusu artık başarıyla sağlandı. Ardından, bu etki alanı denetleyicisi sunucusunda Active Directory etki alanını yapılandırırsınız. PowerShell penceresini yerel bilgisayarınızda açık bırakın. İki SQL Server VM 'yi oluşturmak için bunu daha sonra yeniden kullanacaksınız.

## <a name="configure-the-domain-controller"></a>Etki alanı denetleyicisini yapılandırma
1. Uzak Masaüstü dosyasını başlatarak etki alanı denetleyicisi sunucusuna bağlanın. Yeni VM oluştururken belirttiğiniz makine yöneticisinin Kullanıcı adı AzureAdmin ve Password **contoso! 000**kullanın.
2. Yönetici modunda bir PowerShell penceresi açın.
3. Aşağıdaki **dcpromo 'yu çalıştırın.**  **Corp.contoso.com** etki ALANıNı ayarlamak için exe komutu, d sürücüsündeki veri dizinleriyle birlikte.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Komut bittikten sonra, sanal makine otomatik olarak yeniden başlatılır.

4. Uzak Masaüstü dosyasını başlatarak etki alanı denetleyicisi sunucusuna yeniden bağlanın. Bu kez, **Corp\administrator**olarak oturum açın.
5. Yönetici modunda bir PowerShell penceresi açın ve aşağıdaki komutu kullanarak Active Directory PowerShell modülünü içeri aktarın:

        Import-Module ActiveDirectory

6. Etki alanına üç Kullanıcı eklemek için aşağıdaki komutları çalıştırın.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **Corp\ınstall** , SQL Server hizmet örnekleri, yük devretme kümesi ve kullanılabilirlik grubuyla ilgili her şeyi yapılandırmak için kullanılır. **Corp\sqlsvc1** ve **Corp\sqlsvc2** iki SQL Server sanal makinesi için SQL Server hizmet hesapları olarak kullanılır.
7. Ardından, **Corp\ınstall** 'e etki alanında bilgisayar nesneleri oluşturma izinleri vermek için aşağıdaki komutları çalıştırın.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Yukarıda belirtilen GUID, bilgisayar nesne türünün GUID 'sidir. **Corp\ınstall** hesabının, yük devretme kümesi Için etkin doğrudan nesneleri oluşturmak üzere **tüm özellikleri oku** ve **bilgisayar nesneleri oluşturma** iznine sahip olması gerekir. **Tüm özellikleri oku** izni zaten corp\ınstall için varsayılan olarak verilmiştir, bu nedenle açıkça vermeniz gerekmez. Yük devretme kümesini oluşturmak için gereken izinler hakkında daha fazla bilgi için bkz [. yük devretme kümesi adım adım Kılavuzu: Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx)hesaplarını yapılandırma.

    Active Directory ve kullanıcı nesnelerini yapılandırmayı tamamladığınıza göre, iki SQL Server sanal makine oluşturacak ve bu etki alanına katabileceksiniz.

## <a name="create-the-sql-server-vms"></a>SQL Server VM 'Leri oluşturma
1. Yerel bilgisayarınızda açık olan PowerShell penceresini kullanmaya devam edin. Aşağıdaki ek değişkenleri tanımlayın:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    **10.10.0.4** IP adresi genellikle Azure sanal ağınızın **10.10.0.0/16** alt ağında oluşturduğunuz ilk VM 'ye atanır. **Ipconfig komutunu**çalıştırarak etki alanı denetleyicisi sunucunuzun adresi olduğunu doğrulamanız gerekir.
2. **Contosoquorum**adlı yük devretme KÜMESINDEKI ilk VM 'yi oluşturmak için aşağıdaki komut ile kullanıma yönelik komutları çalıştırın:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Yukarıdaki komutla ilgili olarak aşağıdakileri göz önünde bulundurularak:

   * **New-AzureVMConfig** , istenen kullanılabilirlik kümesi adına sahıp bir VM yapılandırması oluşturur. Sonraki VM 'Ler aynı Kullanılabilirlik kümesi adıyla oluşturulur, böylece aynı Kullanılabilirlik kümesine birleştirilir.
   * **Add-AzureProvisioningConfig** , VM 'yi oluşturduğunuz Active Directory etki alanına birleştirir.
   * **Set-Azuyeniden gönderme ağ** , VM 'yi arka alt ağa koyar.
   * **New-AzureVM** yeni bir bulut hizmeti oluşturur ve yeni bulut hizmetinde yenı Azure VM oluşturur. **Dnssettings** parametresi, yeni bulut hizmetindeki sunucular için DNS sunucusunun **10.10.0.4**IP adresine sahip olduğunu belirtir. Bu, etki alanı denetleyici sunucusunun IP adresidir. Bu parametre, bulut hizmetindeki yeni VM 'Lerin Active Directory etki alanına başarıyla katılmasına olanak tanımak için gereklidir. Bu parametre olmadan VM 'nizin IPv4 ayarlarını, VM sağlandıktan sonra birincil DNS sunucusu olarak kullanacak şekilde el ile ayarlamanız gerekir ve ardından VM 'yi Active Directory etki alanına katın.
3. **ContosoSQL1** ve **ContosoSQL2**adlı SQL Server VM 'leri oluşturmak için aşağıdaki komut ile kullanıma yönelik komutları çalıştırın.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Yukarıdaki komutlarla ilgili olarak aşağıdakilere göz önünde bulundurularak:

   * **New-AzureVMConfig** , etki alanı denetleyici sunucusuyla aynı Kullanılabilirlik kümesi adını kullanır ve sanal makine galerisinde SQL Server 2012 Service Pack 1 Enterprise Edition görüntüsünü kullanır. Ayrıca, işletim sistemi diskini salt okuma-önbelleğe alma (yazma önbelleği yok) olarak ayarlar. Veritabanı dosyalarını, VM 'ye eklediğiniz ayrı bir veri diskine geçirmenize ve bunu okuma veya yazma önbelleği olmadan yapılandırmanıza önerilir. Ancak, bir sonraki en iyi şey, işletim sistemi diskinde okuma önbelleğini kaldıramayacağınız için işletim sistemi diskinde yazma önbelleğini kaldırmalıdır.
   * **Add-AzureProvisioningConfig** , VM 'yi oluşturduğunuz Active Directory etki alanına birleştirir.
   * **Set-Azuyeniden gönderme ağ** , VM 'yi arka alt ağa koyar.
   * **Add-AzureEndpoint** , Istemci uygulamaların Internet 'teki bu SQL Server Hizmetleri örneklerine erişebilmeleri için erişim uç noktaları ekler. ContosoSQL1 ve ContosoSQL2 için farklı bağlantı noktaları verilir.
   * **New-AzureVM** , contosoquorum ile aynı bulut hizmetindeki yeni SQL Server VM oluşturur. Aynı Kullanılabilirlik kümesinde olmasını istiyorsanız VM 'Leri aynı bulut hizmetine yerleştirmeniz gerekir.
4. Her VM 'nin, uzak masaüstü dosyasını çalışma dizininize indirmesi için her bir sanal makinenin tam olarak sağlanması ve her VM için bekleyin. `for` Döngü üç yeni VM 'de döngü yapar ve komutları her biri için üst düzey küme ayraçları içinde yürütür.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    SQL Server VM 'Ler artık sağlanmakta ve çalışıyor, ancak varsayılan seçeneklerle SQL Server birlikte yüklenirler.

## <a name="initialize-the-failover-cluster-vms"></a>Yük devretme kümesi VM 'lerini başlatma
Bu bölümde, yük devretme kümesinde kullanacağınız üç sunucuyu ve SQL Server yüklemesini değiştirmeniz gerekir. Bu avantajlar şunlardır:

* Tüm sunucular: **Yük Devretme Kümelemesi** özelliğini yüklemeniz gerekir.
* Tüm sunucular: Makine **Yöneticisi**olarak **corp\ınstall** eklemeniz gerekir.
* Yalnızca ContosoSQL1 ve ContosoSQL2: Varsayılan veritabanına **Corp\ınstall** öğesini **sysadmin** rolü olarak eklemeniz gerekir.
* Yalnızca ContosoSQL1 ve ContosoSQL2: **NT AUTHORITY\SYSTEM** ' i aşağıdaki izinlerle bir oturum açma olarak eklemeniz gerekir:

  * Tüm kullanılabilirlik gruplarını Değiştir
  * SQL 'e Bağlan
  * Sunucu durumunu görüntüle
* Yalnızca ContosoSQL1 ve ContosoSQL2: **TCP** protokolü SQL Server VM zaten etkin. Ancak, SQL Server uzaktan erişim için güvenlik duvarını yine de açmanız gerekir.

Şimdi başlamaya hazırsınız. **Contosoquorum**'dan başlayarak, aşağıdaki adımları izleyin:

1. Uzak Masaüstü dosyalarını başlatarak **Contosoquorum** 'a bağlanın. VM 'Leri oluştururken belirttiğiniz makine yöneticisinin Kullanıcı adı **Azureadmin** ve Password **contoso! 000**kullanın.
2. Bilgisayarların **corp.contoso.com**'e başarıyla katıldığını doğrulayın.
3. Devam etmeden önce SQL Server yüklemesinin otomatik başlatma görevlerinin çalışmasını tamamlamasını bekleyin.
4. Yönetici modunda bir PowerShell penceresi açın.
5. Windows Yük Devretme Kümelemesi özelliğini yükler.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. **Corp\ınstall** ' i yerel yönetici olarak ekleyin.

        net localgroup administrators "CORP\Install" /Add
7. ContosoQuorum oturumunu kapatın. Bu sunucu ile şimdi tamamladınız.

        logoff.exe

Sonra, Initialize **ContosoSQL1** ve **ContosoSQL2**. SQL Server VM 'Ler için aynı olan aşağıdaki adımları izleyin.

1. Uzak Masaüstü dosyalarını başlatarak iki SQL Server VM 'ye bağlanın. VM 'Leri oluştururken belirttiğiniz makine yöneticisinin Kullanıcı adı **Azureadmin** ve Password **contoso! 000**kullanın.
2. Bilgisayarların **corp.contoso.com**'e başarıyla katıldığını doğrulayın.
3. Devam etmeden önce SQL Server yüklemesinin otomatik başlatma görevlerinin çalışmasını tamamlamasını bekleyin.
4. Yönetici modunda bir PowerShell penceresi açın.
5. Windows Yük Devretme Kümelemesi özelliğini yükler.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. **Corp\ınstall** ' i yerel yönetici olarak ekleyin.

        net localgroup administrators "CORP\Install" /Add
7. SQL Server PowerShell sağlayıcısını içeri aktarın.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Varsayılan SQL Server örneği için bir sysadmin rolü olarak **Corp\ınstall** ekleyin.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Yukarıda açıklanan üç izinle **NT AUTHORITY\SYSTEM** 'ı oturum açma olarak ekleyin.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. SQL Server uzaktan erişim için güvenlik duvarını açın.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Her iki sanal makine oturumunu kapatın.

         logoff.exe

Son olarak, kullanılabilirlik grubunu yapılandırmaya hazırsınız demektir. **ContosoSQL1**üzerindeki tüm işleri gerçekleştirmek Için SQL Server PowerShell sağlayıcısını kullanacaksınız.

## <a name="configure-the-availability-group"></a>Kullanılabilirlik grubunu yapılandırma
1. Uzak Masaüstü dosyalarını başlatarak **ContosoSQL1** 'e yeniden bağlanın. Makine hesabını kullanarak oturum açmak yerine, **Corp\ınstall**kullanarak oturum açın.
2. Yönetici modunda bir PowerShell penceresi açın.
3. Aşağıdaki değişkenleri tanımlayın:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. SQL Server PowerShell sağlayıcısını içeri aktarın.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. ContosoSQL1 için SQL Server hizmet hesabını Corp\sqlsvc1olarak değiştirin.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. ContosoSQL2 için SQL Server hizmet hesabını Corp\sqlsvc2olarak değiştirin.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. [Azure VM 'Deki her zaman açık kullanılabilirlik grupları Için yük devretme kümesi oluşturmak Için](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) **CreateAzureFailoverCluster. ps1** öğesini yerel çalışma dizinine indirin. İşlevsel yük devretme kümesi oluşturmanıza yardımcı olması için bu betiği kullanacaksınız. Windows Yük Devretme Kümelemesi 'nin Azure ağıyla nasıl etkileşime girdiği hakkında önemli bilgiler için bkz. [Azure sanal makinelerinde SQL Server Için yüksek kullanılabilirlik ve olağanüstü durum kurtarma](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Çalışma dizininizi değiştirin ve indirilen betiği kullanarak yük devretme kümesini oluşturun.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. **ContosoSQL1** ve **ContosoSQL2**' deki varsayılan SQL Server örnekleri için Always on kullanılabilirlik grupları 'nı etkinleştirin.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. Bir yedekleme dizini oluşturun ve SQL Server hizmet hesapları için izin verin. Bu dizini, ikincil çoğaltmada kullanılabilirlik veritabanını hazırlamak için kullanacaksınız.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. **ContosoSQL1** üzerinde **MyDB1**adlı bir veritabanı oluşturun, hem tam yedekleme hem de bir günlük YEDEKLEMESI alın ve **WITH norecovery** seçeneğiyle **ContosoSQL2** 'e geri yükleyin.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. SQL Server VM 'lerde kullanılabilirlik grubu uç noktalarını oluşturun ve uç noktalarında uygun izinleri ayarlayın.

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. Kullanılabilirlik çoğaltmaları oluşturun.

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. Son olarak, kullanılabilirlik grubunu oluşturun ve ikincil çoğaltmayı kullanılabilirlik grubuna katın.

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>Sonraki adımlar
Azure 'da bir kullanılabilirlik grubu oluşturarak SQL Server her zaman açık bir şekilde başarıyla uyguladık. Bu kullanılabilirlik grubuna yönelik bir dinleyici yapılandırmak için bkz. [Azure 'Da Always on kullanılabilirlik grupları için BIR ıLB dinleyicisi yapılandırma](../classic/ps-sql-int-listener.md).

Azure 'da SQL Server kullanma hakkında diğer bilgiler için bkz. [Azure sanal makinelerinde SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
