---
title: Azure CLı ve Python aracılığıyla Azure Stack Edge Pro cihaz GPU 'unuzda sanal makineler dağıtma
description: Azure CLı ve Python kullanarak bir Azure Stack Edge Pro GPU cihazında sanal makinelerin (VM 'Ler) nasıl oluşturulacağını ve yönetileceğini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: 14d9c762702648d25efcbcc7ac85824659e20f05
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304078"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-using-azure-cli-and-python"></a>Azure CLı ve Python kullanarak Azure Stack Edge Pro GPU cihazınızda sanal makineler dağıtma

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]

Bu öğreticide, Azure komut satırı arabirimi (CLı) ve Python kullanarak Azure Stack Edge Pro cihazınızda bir VM oluşturma ve yönetme açıklanmaktadır.

## <a name="vm-deployment-workflow"></a>VM dağıtımı iş akışı

Dağıtım iş akışı aşağıdaki diyagramda gösterilmiştir.

![VM dağıtımı iş akışı](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

Dağıtım iş akışının üst düzey özeti aşağıdaki gibidir:

1. Azure Resource Manager Bağlan
2. Kaynak grubu oluşturma
3. Depolama hesabı oluşturma
4. Ana bilgisayar dosyasına blob URI Ekle
5. Sertifikaları yükleme
6. VHD’yi karşıya yükleme
7. VHD 'den yönetilen diskler oluşturma
8. Görüntü yönetilen diskinden bir VM görüntüsü oluşturma
9. Önceden oluşturulmuş kaynaklarla VM oluşturma
10. Sanal ağ oluşturma
11. VNet alt ağ KIMLIĞINI kullanarak bir VNıC oluşturma

İş akışı diyagramının ayrıntılı açıklaması için, bkz. [Azure PowerShell kullanarak Azure Stack Edge Pro cihazınızda VM dağıtma](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). Azure Resource Manager bağlanma hakkında daha fazla bilgi için bkz. [Azure PowerShell kullanarak Azure Resource Manager bağlanma](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="prerequisites"></a>Önkoşullar

Azure CLı ve Python kullanarak Azure Stack Edge Pro cihazınızda bir VM oluşturmaya ve yönetmeye başlamadan önce, aşağıdaki adımlarda listelenen önkoşulları tamamladığınızdan emin olmanız gerekir:

1. Azure Stack Edge Pro cihazınızdaki ağ ayarlarını [Adım 1: yapılandırma Azure Stack Edge Pro cihazı](azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device)bölümünde açıklandığı gibi tamamladınız.

2. İşlem için bir ağ arabirimi etkinleştirildi. Bu ağ arabirimi IP 'si, VM dağıtımı için bir sanal anahtar oluşturmak üzere kullanılır. Aşağıdaki adımlar süreç boyunca size yol gösterir:

    1. **İşlem** sayfasına gidin. Sanal anahtar oluşturmak için kullanacağınız ağ arabirimini seçin.

        > [!IMPORTANT] 
        > İşlem için yalnızca bir bağlantı noktası yapılandırabilirsiniz.

    2. Ağ arabiriminde işlem etkinleştirin. Azure Stack Edge Pro, bu ağ arabirimine karşılık gelen bir sanal anahtar oluşturur ve yönetir.

    <!--If you decide to use another network interface for compute, make sure that you:

    - Delete all the VMs that you have deployed using Azure Resource Manager.

    - Delete all virtual network interfaces and the virtual network associated with this network interface.

    - You can now enable another network interface for compute.-->

3. Azure Stack Edge Pro cihazınızda ve istemcinizin güvenilir deposunda tüm sertifikaları oluşturdunuz ve yüklediniz. [2. Adım: sertifika oluşturma ve yüklemede](azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)açıklanan yordamı izleyin.

4. Azure Stack Edge Pro cihazınız için Base-64 kodlamalı bir *. cer* sertifikası (pek biçimi) oluşturdunuz. Bu sertifika, cihaza imza zinciri olarak zaten yüklenmiş ve istemcinizdeki güvenilir kök depoda yüklü. Bu sertifika, Python 'un bu istemcide çalışması için *ped* biçiminde de gereklidir.

    Komutunu kullanarak bu sertifikayı `pem` biçime dönüştürün `certutil` . Bu komutu, sertifikanızı içeren dizinde çalıştırmalısınız.

    ```powershell
    certutil.exe <SourceCertificateName.cer> <DestinationCertificateName.pem>
    ```
    Örnek komut kullanımı aşağıda gösterilmiştir:

    ```powershell
    PS C:\Certificates> certutil.exe -encode aze-root.cer aze-root.pem
    Input Length = 2150
    Output Length = 3014
    CertUtil: -encode command completed successfully.
    PS C:\Certificates>
    ```    
    Bunu `pem` daha sonra Python deposuna de ekleyeceksiniz.

5. Cihaz IP 'sini, cihazın yerel Web Kullanıcı arabiriminde **ağ** sayfanızda atamış olursunuz. Bu IP 'yi şu şekilde ekleyin:

    - İstemci üzerindeki konak dosyası veya,
    - DNS sunucusu yapılandırması
    
    > [!IMPORTANT]
    > Uç nokta adı çözümlemesi için DNS sunucusu yapılandırmasını değiştirmenizi öneririz.

    1. **Not defteri 'ni** yönetici olarak başlatın (dosyayı kaydetmek için yönetici ayrıcalıkları gereklidir) ve ardından konumunda bulunan **Hosts** dosyasını açın `C:\Windows\System32\Drivers\etc` .
    
        ![Windows Gezgini ana bilgisayarları dosyası](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)
    
    2. Aşağıdaki girdileri, cihazınız için uygun değerlerle değiştirerek **ana bilgisayar** dosyanıza ekleyin:
    
        ```
        <Device IP> login.<appliance name>.<DNS domain>
        <Device IP> management.<appliance name>.<DNS domain>
        <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
    3. Başvuru için aşağıdaki görüntüyü kullanın. **Hosts** dosyasını kaydedin.

        ![Not defteri 'nde Hosts dosyası](media/azure-stack-edge-gpu-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

6. Bu yordamda kullanılan [Python betiğini indirin](https://aka.ms/ase-vm-python) .

## <a name="step-1-set-up-azure-clipython-on-the-client"></a>1. Adım: istemcide Azure CLı/Python ayarlama

### <a name="verify-profile-and-install-azure-cli"></a>Profili doğrulama ve Azure CLı 'yı yüklemeyi

<!--1. Verify the API profile of the client and identify which version of the modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908&preserve-view=true#azure-resource-manager-api-profiles).-->

1. Azure CLı 'yi istemcinizi ' ne yüklersiniz. Bu örnekte, Azure CLı 2.0.80 yüklendi. Azure CLı sürümünü doğrulamak için `az --version` komutunu çalıştırın.

    Yukarıdaki komutun örnek çıktısı aşağıda verilmiştir:

    ```output
    PS C:\windows\system32> az --version
    azure-cli                         2.0.80
    
    command-modules-nspkg              2.0.3
    core                              2.0.80
    nspkg                              3.0.4
    telemetry                          1.0.4
    Extensions:
    azure-cli-iot-ext                  0.7.1
    
    Python location 'C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe'
    Extensions directory 'C:\.azure\cliextensions'
    
    Python (Windows) 3.6.6 (v3.6.6:4cf1f54eb7, Jun 27 2018, 02:47:15) [MSC v.1900 32 bit (Intel)]
    
    Legal docs and information: aka.ms/AzureCliLegal
    
    Your CLI is up-to-date.
    
    Please let us know how we are doing: https://aka.ms/clihats
    PS C:\windows\system32>
    ```

    Azure CLı yoksa, [Windows 'Da Azure CLI](/cli/azure/install-azure-cli-windows)Indirin ve yükleyin. Windows komut istemi 'ni veya Windows PowerShell 'i kullanarak Azure CLı 'yı çalıştırabilirsiniz.

2. CLı 'nın Python konumunu bir yere unutmayın. Azure CLı için güvenilen kök sertifika deposunun konumunu öğrenmek için Python konumuna ihtiyacınız vardır.

3. Bu makalede kullanılan örnek betiği çalıştırmak için, aşağıdaki Python kitaplık sürümlerine ihtiyacınız olacaktır:

    ```powershell
    azure-common==1.1.23
    azure-mgmt-resource==2.1.0
    azure-mgmt-network==2.7.0
    azure-mgmt-compute==5.0.0
    azure-mgmt-storage==1.5.0
    azure-storage-blob==1.2.0rc1
    haikunator
    msrestazure==0.6.2
    ```
    Sürümleri yüklemek için şu komutu çalıştırın:

    ```powershell
    .\python.exe -m pip install haikunator
    ```

    Aşağıdaki örnek çıktıda Haıkunator yüklemesi gösterilmektedir:

    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install haikunator

    Collecting haikunator
      Downloading https://files.pythonhosted.org/packages/43/fa/130968f1a1bb1461c287b9ff35c630460801783243acda2cbf3a4c5964a5/haikunator-2.1.0-py2.py3-none-any.whl
    
    Installing collected packages: haikunator
    Successfully installed haikunator-2.1.0
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> 
    ```

    Aşağıdaki örnek çıktıda, için PIP yüklemesi gösterilmektedir `msrestazure` : 
    
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install msrestazure==0.6.2
    Requirement already satisfied: msrestazure==0.6.2 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (0.6.2)
    Requirement already satisfied: msrest<2.0.0,>=0.6.0 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from msrestazure==0.6.2) (0.6.10)
    === CUT ===========================  CUT ==================================
    Requirement already satisfied: cffi!=1.11.3,>=1.8 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (1.13.2)
    Requirement already satisfied: pycparser in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cffi!=1.11.3,>=1.8->cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (2.18)
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

### <a name="trust-the-azure-stack-edge-pro-ca-root-certificate"></a>Azure Stack Edge Pro CA kök sertifikasına güvenin

1. Makinenizde sertifika konumunu bulun. Konum, yüklediğiniz yere bağlı olarak farklılık gösterebilir `az cli` . Windows PowerShell 'i yönetici olarak çalıştırın. `az cli`Yüklü Python: yoluna geçiş yapın `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe` .

    Sertifika konumunu almak için aşağıdaki komutu yazın:

    ```powershell
    .\python -c "import certifi; print(certifi.where())"
    ```
    
    Cmdlet 'i aşağıda görüldüğü gibi sertifika konumunu döndürür:  
        
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python -c "import certifi; print(certifi.where())"
    C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```
      
    Bu konumu daha sonra kullanacağınız gibi bir yere unutmayın. `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`

2. Azure Stack Edge Pro CA kök sertifikasına, var olan Python sertifikasına ekleyerek güvenin. PEK sertifikasını daha önce kaydettiğiniz yerin yolunu sağlarsınız.

    ```powershell
    $pemFile = "<Path to the pem format certificate>"
    ```
    Örnek bir yol "C:\vm-scripts\rootteam3device.exe" olacaktır.
    
    Ardından, aşağıdaki komut dizisini Windows PowerShell 'e yazın:

    ```powershell
    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
        $root.Import($pemFile)
        
    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry= [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")
    
    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText
    
    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry
    
    Write-Host "Python Cert store was updated to allow the Azure Stack Edge Pro CA root certificate"
    ```
    
### <a name="connect-to-azure-stack-edge-pro"></a>Azure Stack Edge Pro 'ya bağlanma

1. Komutu çalıştırarak Azure Stack Edge Pro ortamınızı kaydettirin `az cloud register` .

    Bazı senaryolarda, doğrudan giden internet bağlantısı, SSL yakalaşmayı zorlayan bir ara sunucu veya güvenlik duvarı aracılığıyla yönlendirilir. Bu durumlarda, az Cloud Register komutu \" buluttan uç noktalar alınamıyor gibi bir hata vererek başarısız olabilir. \" Bu hatayı geçici olarak çözmek için, Windows PowerShell 'de aşağıdaki ortam değişkenlerini ayarlayın:

    ```powershell
    $ENV:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION = 1 
    $ENV:ADAL_PYTHON_SSL_NO_VERIFY = 1
    ```

2. Azure Resource Manager uç noktası için ortam değişkenlerini, kaynakların oluşturulduğu konumu ve kaynak VHD 'nin bulunduğu yolu ayarlayın. Kaynakların konumu tüm Azure Stack Edge Pro cihazlarında düzeltilir ve olarak ayarlanır `dbelocal` . Ayrıca adres öneklerini ve özel IP adresini belirtmeniz gerekir. Aşağıdaki ortam değişkenlerinin tümü, için sabit değer olarak kodlanmalıdır; hariç değerleriniz temel alınarak değerlerdir `AZURE_RESOURCE_LOCATION` `"dbelocal"` .

    ```powershell
    $ENV:ARM_ENDPOINT = "https://management.team3device.teatraining1.com"
    $ENV:AZURE_RESOURCE_LOCATION = "dbelocal"
    $ENV:VHD_FILE_PATH = "C:\Downloads\Ubuntu1604\Ubuntu13.vhd"
    $ENV:ADDRESS_PREFIXES = "5.5.0.0/16"
    $ENV:PRIVATE_IP_ADDRESS = "5.5.174.126"
    ```

3. Ortamınızı kaydedin. Az Cloud Register çalıştırırken şu parametreleri kullanın:

    | Değer | Açıklama | Örnek |
    | --- | --- | --- |
    | Ortam adı | Bağlanmaya çalıştığınız ortamın adı | Bir ad sağlayın, örneğin `aze-environ` |
    | Kaynak Yöneticisi uç noktası | Bu URL `https://Management.<appliancename><dnsdomain>` . <br> Bu URL 'yi almak için cihazınızın yerel Web Kullanıcı arabiriminde **cihazlar** sayfasına gidin. |Örneğin, `https://management.team3device.teatraining1.com`.  |
    
    ```powershell
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.<appliance name>.<DNS domain>"
    ```
    Aşağıda yukarıdaki komutun örnek kullanımı gösterilmektedir:
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud register -n az-new-env --endpoint-resource-manager "https://management.team3device.teatraining1.com"
    ```
    
    
4. Aşağıdaki komutları kullanarak etkin ortamı ayarlayın:

    ```powershell
    az cloud set -n <EnvironmentName>
    ```
    Aşağıda yukarıdaki komutun örnek kullanımı gösterilmektedir:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud set -n az-new-env
    Switched active cloud to 'az-new-env'.
    Use 'az login' to log in to this cloud.
    Use 'az account set' to set the active subscription.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

4. Komutunu kullanarak Azure Stack Edge Pro ortamınızda oturum açın `az login` . Azure Stack Edge Pro ortamında Kullanıcı ya da [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md)olarak oturum açabilirsiniz.

   *Kullanıcı* olarak oturum açmak için şu adımları izleyin:

   Kullanıcı adını ve parolayı doğrudan komut içinden belirtebilir `az login` veya bir tarayıcı kullanarak kimlik doğrulaması yapabilirsiniz. Hesabınız Multi-Factor Authentication etkinleştirildiyse, ikincisini yapmanız gerekir.

   Aşağıdakiler, öğesinin örnek kullanımını göstermektedir `az login` :
    
    ```powershell
    PS C:\Certificates> az login -u EdgeARMuser
    ```
   Login komutunu kullandıktan sonra sizden parola istenir. Azure Resource Manager parolayı girin.

   Aşağıda, parola alındıktan sonra başarılı bir oturum açma için örnek çıktı gösterilmektedir:  
   
   ```output
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az login -u EdgeARMuser
   Password:
   [
        {
            "cloudName": "az-new-env",
            "id": "A4257FDE-B946-4E01-ADE7-674760B8D1A3",
            "isDefault": true,
            "name": "Default Provider Subscription",
            "state": "Enabled",
            "tenantId": "c0257de7-538f-415c-993a-1b87a031879d",
            "user": {
                "name": "EdgeArmUser@localhost",
                "type": "user"
            }
        }
   ]
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
   ```
   `id` `tenantId` Bu değerler, sırasıyla Azure Resource Manager abonelik kimliğiniz ve Azure Resource Manager kiracı Kimliğiniz için karşılık gelen ve değerlerini bir yere iade edin ve sonraki adımda kullanılacaktır.
       
   Aşağıdaki ortam değişkenlerinin *hizmet sorumlusu* olarak çalışacak şekilde ayarlanması gerekir:

   ```
   $ENV:ARM_TENANT_ID = "c0257de7-538f-415c-993a-1b87a031879d"
   $ENV:ARM_CLIENT_ID = "cbd868c5-7207-431f-8d16-1cb144b50971"
   $ENV:ARM_CLIENT_SECRET - "<Your Azure Resource Manager password>"
   $ENV:ARM_SUBSCRIPTION_ID = "A4257FDE-B946-4E01-ADE7-674760B8D1A3"
   ```

   Azure Resource Manager Istemci KIMLIĞINIZ sabit kodlanmış. Azure Resource Manager kiracı KIMLIĞINIZ ve Azure Resource Manager abonelik KIMLIĞINIZ, `az login` daha önce çalıştırdığınız komutun çıktısında bulunur. Azure Resource Manager Istemci parolası, ayarladığınız Azure Resource Manager paroladır.

   Daha fazla bilgi için bkz. [Azure Resource Manager Password](/azure/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password).

5. Profili 2019-03-01-karma sürüm olarak değiştirin. Profil sürümünü değiştirmek için aşağıdaki komutu çalıştırın:

    ```powershell
    az cloud update --profile 2019-03-01-hybrid
    ```

    Aşağıdakiler, öğesinin örnek kullanımını göstermektedir `az cloud update` :

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud update --profile 2019-03-01-hybrid
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

## <a name="step-2-create-a-vm"></a>2. Adım: VM oluşturma

Bir sanal makine oluşturmak için bir Python betiği sunulmaktadır. Kullanıcı olarak oturum açmış olmanıza veya hizmet sorumlusu olarak ayarlanmış olmasına bağlı olarak, komut dosyası girişi uygun şekilde alır ve bir VM oluşturur.

1. Python betiğini Python 'un yüklü olduğu dizinden çalıştırın.

    `.\python.exe example_dbe_arguments_name_https.py cli`

2. Betik çalıştırıldığında, VHD 'nin karşıya yüklenmesi 20-30 dakika sürer. Karşıya yükleme işleminin ilerlemesini görüntülemek için Azure Depolama Gezgini veya AzCopy kullanabilirsiniz.

    Komut dosyasının başarılı bir şekilde çalışmasının örnek bir çıkışı aşağıda verilmiştir. Betik, bir kaynak grubundaki tüm kaynakları oluşturur, bu kaynakları bir VM oluşturmak için kullanır ve son olarak, oluşturduğu tüm kaynaklar dahil olmak üzere kaynak grubunu siler.

    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe example_dbe_arguments_name_https.py cli
    
    Create Resource Group
    Create a storage account
    Uploading to Azure Stack Storage as blob:
            ubuntu13.vhd
    
    Listing blobs...
            ubuntu13.vhd
    
    VM image resource id:
                /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/azure-sample-group-virtual-machines118/providers/Microsoft.Compute/images/UbuntuImage
    
    Create Vnet
    Create Subnet
    Create NIC
    Creating Linux Virtual Machine
    Tag Virtual Machine
    Create (empty) managed Data Disk
    Get Virtual Machine by Name
    Attach Data Disk
    Detach Data Disk
    Deallocating the VM (to prepare for a disk resize)
    Update OS disk size
    Start VM
    Restart VM
    Stop VM
    
    List VMs in subscription
            VM: VmName118
    
    List VMs in resource group
            VM: VmName118
    
    Delete VM
    All example operations completed successfully!
    
    Delete Resource Group
    Deleted: azure-sample-group-virtual-machines118
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ``` 


## <a name="next-steps"></a>Sonraki adımlar

[Linux sanal makineleri için ortak az CLı komutları](../virtual-machines/linux/cli-manage.md)
