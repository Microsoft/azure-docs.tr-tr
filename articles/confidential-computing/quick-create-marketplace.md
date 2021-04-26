---
title: Hızlı başlangıç-Market ile Azure gizli bilgi işlem sanal makinesi oluşturma
description: Market ile nasıl hızlı bir şekilde gizli bilgi işlem sanal makinesi oluşturulacağını öğrenerek dağıtımlarınızla çalışmaya başlayın.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: be935dbd7e4559bcad8c5cf78622a5c63810f54c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812399"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-marketplace"></a>Hızlı başlangıç: market 'te Azure gizli bilgi Işlem VM 'si dağıtma

Intel SGX ile desteklenen bir sanal makine (VM) oluşturmak için Azure Marketi 'ni kullanarak Azure gizli bilgi işlem ile çalışmaya başlayın. Daha sonra, geliştirme ortamınızı ayarlamak için açık şifreleme yazılım geliştirme seti 'ni (SDK) yükleyeceksiniz. 

Bu öğretici, bir gizli bilgi işlem sanal makinesi dağıtmaya hızlı bir başlangıç yapmak istiyorsanız önerilir. VM 'Ler özel donanımda çalıştırılır ve belirli yapılandırma girişlerinin istendiği gibi çalışmasını gerektirir. Bu hızlı başlangıçta açıklanan Market teklifi, Kullanıcı girişini kısıtlayarak dağıtımı kolaylaştırır.

Daha özel yapılandırmayla gizli bir işlem sanal makinesi dağıtmaya ilgileniyorsanız, [Azure Portal gizli işlem sanal makine dağıtım adımlarını](quick-create-portal.md)izleyin.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) .

> [!NOTE]
> Ücretsiz deneme hesaplarının bu öğreticide kullanılan sanal makinelere erişimi yoktur. Lütfen Kullandıkça Öde aboneliğine yükseltin.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. En üstte, arama çubuğuna **Azure gizli bilgi işlem** yazma yazın.

1. **Market** bölümünde **Azure gizli bilgi Işlem (sanal makine)** seçeneğini belirleyin. 

    ![Market seçin](media/quick-create-marketplace/portal-search-marketplace.png)    

1. Azure gizli işlem dağıtım giriş sayfasında **Oluştur**' u seçin.
 

## <a name="configure-your-virtual-machine"></a>Sanal makinenizi yapılandırma

1. **Temel bilgiler** sekmesinde, **aboneliğinizi** ve **kaynak grubunuzu** seçin. Bu şablondan bir sanal makine dağıtmak için kaynak grubunuzun boş olması gerekir.

1. Aşağıdaki değerleri yazın veya seçin:

   * **Bölge**: sizin Için doğru Azure bölgesini seçin.

        > [!NOTE]
        > Gizli işlem sanal makineleri yalnızca belirli bölgelerde bulunan özel donanımlar üzerinde çalışır. DCsv2-Series VM 'Ler için kullanılabilir olan en son bölgeler için bkz. [kullanılabilir bölgeler](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).
    
    * **Görüntü seçin**: herhangi bir görüntü seçin. Bu öğreticiyi tamamladığınızda, Ubuntu 18,04 (Gen 2) seçeneğini belirleyin. Aksi takdirde, aşağıdaki uygun adımlara yönlendirilirsiniz. 

    * **Sanal makine adı**, yeni VM 'niz için bir ad girin.

    * **Kimlik doğrulama türü**: bir Linux sanal makinesi oluşturuyorsanız **SSH ortak anahtarını** seçin. 

         > [!NOTE]
         > Kimlik doğrulaması için SSH ortak anahtarı veya Parola kullanabilirsiniz. SSH daha güvenlidir. SSH anahtarı oluşturma talimatları için bkz. [Azure'daki Linux VM için Linux ve Mac üzerinde SSH anahtarı oluşturma](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Kullanıcı adı**: VM için yönetici adını girin.

    * **SSH ortak anahtarı**: varsa, RSA ortak anahtarınızı girin.
    
    * **Parola**: uygunsa, kimlik doğrulaması için parolanızı girin.
 
1. Ekranınızın altındaki **Sonraki: sanal makine ayarları** düğmesini seçin.

    > [!IMPORTANT]
    > Sayfanın güncelleştirilmesini bekleyin. "Gizli bilgi Işlem DCsv2 serisi VM 'Lerin sınırlı sayıda bölgede kullanılabilir olduğunu belirten bir ileti *görmemelisiniz* . Bu ileti devam ederse, önceki sayfaya dönün ve kullanılabilir bir DCsv2-Series bölgesi seçin.

1. **Değişiklik boyutu** için, boyut seçicisinde gizli işlem özelliklerine sahıp bir VM seçin. 

    > [!TIP]
    > Boyutları **DC1s_v2**, **DC2s_v2**, **DC4s_V2** ve **DC8_v2** görmeniz gerekir. Bunlar şu anda gizli bilgi işlem desteği olan tek sanal makine boyutlarıdır. [Daha fazla bilgi edinin](virtual-machine-solutions.md).

1. **Işletim sistemi disk türü** için bir disk türü seçin.

1. **Sanal ağ** için yeni bir tane oluşturun veya var olan bir kaynaktan seçim yapın.

1. **Alt ağ** için yeni bir tane oluşturun veya var olan bir kaynaktan seçim yapın.

1. **Ortak gelen bağlantı noktalarını Seç** için **SSH (Linux)/RDP (Windows)** öğesini seçin. Bu hızlı başlangıçta, sanal makineye bağlanmak ve açık şifreleme SDK yapılandırmasını gerçekleştirmek için bu adım gereklidir. 

1. **Önyükleme tanılaması** için, bu hızlı başlangıç için devre dışı bırakın. 

1. **Gözden geçir ve oluştur**’u seçin.

1. **Gözden geçir + oluştur** bölmesinde **Oluştur**' u seçin.

> [!NOTE]
> Bir sonraki bölüme ilerleyin ve bir Linux VM dağıttıysanız Bu öğreticiye devam edin. Bir Windows VM dağıttıysanız, Windows sanal makinenize bağlanmak ve ardından [OE SDK 'Yı Windows 'a yüklemek](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md) [için aşağıdaki adımları izleyin](../virtual-machines/windows/connect-logon.md) .


## <a name="connect-to-the-linux-vm"></a> Linux VM'ye bağlanma

BASH kabuğu kullanıyorsanız **ssh** komutuyla Azure VM'ye bağlanabilirsiniz. Aşağıdaki komutta yer alan VM kullanıcı adı ve IP adresini Linux VM bilgileriyle değiştirin.

```bash
ssh azureadmin@40.55.55.555
```

Sanal makinenizin genel bakış bölümü altında, Azure portal VM 'nizin genel IP adresini bulabilirsiniz.

![Azure portalında IP adresi](media/quick-create-portal/public-ip-virtual-machine.png)

Windows üzerinde çalıştırıyorsanız ve BASH kabuğu yoksa, PuTTY gibi bir SSH istemcisi yükleyebilirsiniz.

1. [PuTTY'yi indirin ve yükleyin](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. PuTTY'yi çalıştırın.

1. PuTTY yapılandırma ekranına VM'nizin genel IP adresini girin.

1. **Aç** ' ı seçin ve istemlerin Kullanıcı adınızı ve parolanızı girin.

Linux VM'lerinize bağlanma hakkında daha fazla bilgi için bkz. [Portal kullanarak Azure’da bir Linux VM oluşturma](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Sunucunun ana bilgisayar anahtarı hakkında kayıt defterinde önbelleğe alınmamış bir PuTTY güvenlik uyarısı görürseniz, aşağıdaki seçeneklerden birini belirleyin. Bu konağa güveniyorsanız, anahtarı PuTTy 'in önbelleğine eklemek ve bağlanmaya devam etmek için **Evet** ' i seçin. Yalnızca bir kez bağlanmak istiyorsanız, anahtarı önbelleğe eklemeden, **Hayır**' ı seçin. Bu konağa güvenmiyorsanız bağlantıyı bırakmak için **iptal** ' i seçin.

## <a name="install-the-open-enclave-sdk-oe-sdk"></a>Açık Enclave SDK 'sını yükleme (OE SDK) <a id="Install"></a>

Bir Ubuntu 18,04 LTS Gen 2 görüntüsünü çalıştıran DCsv2-Series sanal makinesine [OE SDK 'yı](https://github.com/openenclave/openenclave) yüklemek için adım adım yönergeleri izleyin. 

Sanal makineniz Ubuntu 18,04 LTS Gen 2 üzerinde çalışıyorsa [ubuntu 18,04 yükleme yönergelerini](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)izlemeniz gerekir. 

#### <a name="1-configure-the-intel-and-microsoft-apt-repositories"></a>1. Intel ve Microsoft APT depolarını yapılandırma

```bash
echo 'deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main' | sudo tee /etc/apt/sources.list.d/intel-sgx.list
wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo apt-key add -

echo "deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-7 main" | sudo tee /etc/apt/sources.list.d/llvm-toolchain-bionic-7.list
wget -qO - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -

echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | sudo tee /etc/apt/sources.list.d/msprod.list
wget -qO - https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```

#### <a name="2-install-the-intel-sgx-dcap-driver"></a>2. Intel SGX DCAP sürücüsünü yükler
Ubuntu 'ın bazı sürümlerinde Intel SGX sürücüsü zaten yüklü olabilir. Aşağıdaki komutu kullanarak denetleyin: 

```bash
dmesg | grep -i sgx
[  106.775199] sgx: intel_sgx: Intel SGX DCAP Driver {version}
``` 
Çıkış boşsa, sürücüyü yükler: 

```bash
sudo apt update
sudo apt -y install dkms
wget https://download.01.org/intel-sgx/sgx-dcap/1.7/linux/distro/ubuntu18.04-server/sgx_linux_x64_driver_1.35.bin -O sgx_linux_x64_driver.bin
chmod +x sgx_linux_x64_driver.bin
sudo ./sgx_linux_x64_driver.bin
```

> [!WARNING]
> Lütfen [Intel 'ın SGX sitesindeki](https://01.org/intel-software-guard-extensions/downloads)en son ıNTEL SGX Dcap sürücüsünü kullanın.

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. Intel ve Open Enclade paketlerini ve bağımlılıklarını yükleme

```bash
sudo apt -y install clang-8 libssl-dev gdb libsgx-enclave-common libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
```

> [!NOTE] 
> Bu adım, Azure 'da uzaktan kanıtlama gerçekleştirmek için gerekli olan [az-Dcap-Client](https://github.com/microsoft/azure-dcap-client) paketini de yüklüyor.

#### <a name="4-verify-the-open-enclave-sdk-install"></a>4. **Açık KUŞNAVE SDK yüklemesini doğrulayın**

Yüklü SDK 'Yı doğrulamak ve kullanmak için bkz. GitHub 'da [Open Enclave SDK 'Sını kullanma](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/Linux_using_oe_sdk.md) .

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, sanal makineyi ve tüm ilişkili kaynakları silebilirsiniz. 

Sanal makine için kaynak grubunu seçin ve **Sil**' i seçin. Kaynakları silmeyi tamamlayacak kaynak grubunun adını onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir gizli bilgi işlem sanal makinesi dağıttığınız ve açık şifreleme ve SDK 'Yı yüklediniz. Azure 'daki sanal makinelerin gizli bilgi işlem hakkında daha fazla bilgi için bkz. [sanal makinelerdeki çözümler](virtual-machine-solutions.md). 

GitHub 'daki açık şifreleme SDK örneklerine devam ederek nasıl gizli bilgi işlem uygulamaları derleyebilirsiniz öğrenin. 

> [!div class="nextstepaction"]
> [Açık şifreleme SDK örnekleri oluşturma](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
