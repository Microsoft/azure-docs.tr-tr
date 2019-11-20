---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 1a18812c6447266d34f2c8aa1ccff8984f68570a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008816"
---
Oluşturup uygun ölçekte tutarlı bir şekilde Azure sanal makineleri (VM'ler) yönetmek için Otomasyon biçimi genellikle istenildiği gibi. Birçok araca ve tam Azure altyapı dağıtımı ve Yönetimi yaşam döngüsünü otomatikleştirmenize olanak tanıyan çözümler vardır. Bu makalede, Azure'da kullanabileceğiniz altyapı Otomasyonu araçlardan bazıları tanıtılmaktadır. Bu araçlar genellikle aşağıdaki yaklaşımlardan birini sığacak:

- Sanal Makine yapılandırmasını otomatikleştirin
    - Araçlarda [Ansible](#ansible), [Chef](#chef), ve [Puppet](#puppet).
    - VM özelleştirme belirli araçlarda [cloud-init](#cloud-init) Linux VM'ler için [PowerShell Desired State Configuration (DSC)](#powershell-dsc)ve [Azure özel betik uzantısı](#azure-custom-script-extension) tüm Azure Vm'leri.
 
- Altyapı yönetimini otomatikleştirme
    - Araçlarda [Packer](#packer) özel VM otomatikleştirmek için görüntüyü oluşturur, ve [Terraform](#terraform) altyapı yapı işlemi otomatik hale getirmek için.
    - [Azure Otomasyonu](#azure-automation) Azure ve şirket içi altyapınız genelinde eylemleri gerçekleştirebilirsiniz.

- Uygulama dağıtımı ve teslim otomatikleştirin
    - Örnekler [Azure DevOps Hizmetleri](#azure-devops-services) ve [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) yapılandırma yönetimi, VM oluşturma ve uygulama dağıtımı için bir Otomasyon altyapısı. Ansible, kimlik doğrulaması ve hedef makineleri yönetmek için genellikle SSH anahtarlarıyla bir aracısız modelini kullanır. Belirli görevleri gerçekleştirmek kullanılabilir olan Ansible modül sayısı ile playbook'ları yapılandırma görevleri tanımlanır. Daha fazla bilgi için [Ansible nasıl çalıştığını](https://www.ansible.com/how-ansible-works).

Şunları nasıl yapacağınızı öğrenin:

- [Yükleme ve Azure ile kullanmak için Linux üzerinde Ansible yapılandırma](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Bir Linux sanal makinesi oluşturma](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Bir Linux sanal makinesini yönetin](../articles/virtual-machines/linux/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) olduğundan, altyapınızın nasıl yapılandırılacağını tanımlamak yardımcı olan bir Otomasyon platformudur dağıtıldığı ve yönetildiği. Uygulama yaşam döngüsü Otomasyon altyapısı yerine, Chef, Habitat ek bileşenler dahil ve güvenlik ve ilke gereksinimlerine uyum Chef yardımcı olan InSpec otomatikleştirin. Chef istemciler, bir veya daha fazla merkezi Chef, yapılandırmaları yönetin ve sunucuları ile hedef makinelerde yüklenir. Daha fazla bilgi için [bir genel bakış Chef](https://docs.chef.io/chef_overview.html).

Şunları nasıl yapacağınızı öğrenin:

- [Chef dağıtma da Azure Market'te otomatikleştirmek](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Windows üzerinde Chef yüklemek ve Azure sanal makinelerini oluşturma](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) uygulama teslimat ve dağıtım sürecinin işleyen bir kurumsal kullanıma hazır Otomasyon platformudur. Aracılar Puppet Azure altyapısının istenen yapılandırmasını tanımlamak bildirimleri çalıştırmak için ana ve Vm'leri izin vermek için hedef makinede yüklü. Puppet bir Gelişmiş devops iş akışı için Jenkins ve GitHub gibi diğer çözümlerle tümleştirilebilir. Daha fazla bilgi için [Puppet nasıl çalıştığını](https://puppet.com/products/how-puppet-works).

Şunları nasıl yapacağınızı öğrenin:

- [Azure Market'ten Puppet dağıtma](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io), Linux VM’sini ilk kez önyüklendiğinde özelleştirmeyi sağlayan, sık kullanılan bir yaklaşımdır. cloud-init’i paket yükleme, dosyalara yazma ve kullanıcılar ile güvenliği yapılandırma işlemleri için kullanabilirsiniz. Cloud-init önyükleme işlemi sırasında çağrıldığı için ek adımlar veya yapılandırmanıza uygulayabileceğiniz gerekli aracı yoktur.  Düzgün bir şekilde biçimlendirme hakkında daha fazla bilgi için `#cloud-config` dosyaları görmek [cloud-init belgeler sitesinde](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` dosyaları, base64 ile kodlanmış metin dosyalarıdır.

Cloud-init, dağıtımlar arasında da çalışır. Örneğin, bir paket yüklemek için **apt-get install** veya **yum install** kullanmazsınız. Bunun yerine, yüklenecek paketlerin listesini tanımlayabilirsiniz. Cloud-init, seçtiğiniz dağıtım için yerel paket yönetim aracını otomatik olarak kullanır.

Etkin olarak desteklenen Linux distro ortaklarımızla birlikte kullanılabilir cloud-init etkinleştirilmiş görüntüleri Azure Market'te sahip olmak için çalışıyoruz. Bu görüntüleri cloud-init dağıtımlarınızı yapın ve yapılandırmaları VM'ler ve sanal makine ölçek kümeleri ile sorunsuz bir şekilde çalışır. Azure'da cloud-init hakkında daha ayrıntılı bilgi edinin:

- [Azure'da Linux sanal makineleri için cloud-init desteğine](../articles/virtual-machines/linux/using-cloud-init.md)
- [Bir öğreticide cloud-init kullanarak otomatik VM yapılandırması deneyin](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) hedef makineler tanımlamak için bir yönetim platformu. DSC de kullanılabilir aracılığıyla Linux'ta [Open Management Infrastructure (OMI) sunucusu](https://collaboration.opengroup.org/omi/).

DSC yapılandırmaları ne bir makineye yükleyin ve ana bilgisayar yapılandırma tanımlar. İstenen Eylemler gönderilen yapılandırmalarına göre işler her hedef düğümde yerel Configuration Manager'ı (LCM) altyapısı çalıştırır. Bir çekme sunucusu DSC yapılandırmaları ve ilişkili kaynakları depolamak için merkezi bir konakta çalışan bir web hizmetidir. Çekme sunucusu, uyumluluk ve gerekli yapılandırmaları sağlamak için her bir hedef ana bilgisayarda LCM altyapısı ile iletişim kurar.

Şunları nasıl yapacağınızı öğrenin:

- [Temel bir DSC yapılandırması oluşturma](https://msdn.microsoft.com/powershell/dsc/quickstarts/website-quickstart).
- [DSC çekme sunucusunu yapılandırma](/powershell/scripting/dsc/pull-server/pullserver).
- [Linux için DSC kullanma](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Azure özel betik uzantısı
Azure özel betik uzantısı için [Linux](../articles/virtual-machines/linux/extensions-customscript.md) veya [Windows](../articles/virtual-machines/windows/extensions-customscript.md) indirir ve Azure VM'ler üzerinde betikler yürütür. VM oluşturma veya VM sonra istediğiniz zaman kullanımda olduğunda, uzantıyı kullanabilirsiniz. 

Betikler Azure depolama veya GitHub deposu gibi genel bir konumdan indirilebilir. Özel betik uzantısı ile komut dosyaları, kaynak VM üzerinde çalışan herhangi bir dilde yazabilirsiniz. Bu komut dosyaları, uygulamaları yüklemek veya VM olarak istenen yapılandırmak için kullanılabilir. Güvenli kimlik bilgileri için korumalı bir yapılandırmada parolalar gibi hassas bilgileri depolanabilir. Bu kimlik bilgileri, yalnızca sanal makine içinde şifresi çözülür.

Şunları nasıl yapacağınızı öğrenin:

- [Azure CLI ile Linux VM oluşturma ve özel betik uzantısı kullanın](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Azure PowerShell ile bir Windows VM oluşturma ve özel betik uzantısı kullanın](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) Azure'da özel bir VM görüntüsü oluşturduğunuzda yapı işlemini otomatikleştirir. Packer, işletim sistemini tanımlamak ve VM için kendi özel gereksinimlerinize göre özelleştirme yapılandırma sonrası komut dosyalarını çalıştırmak için kullanın. Yapılandırıldıktan sonra VM'ye yönetilen Disk görüntüsü dahil edilir. Packer kaynak VM, ağ ve depolama kaynakları oluşturma, yapılandırma betiklerini çalıştırın ve ardından sanal makine görüntüsü oluşturma işlemini otomatikleştirir.

Şunları nasıl yapacağınızı öğrenin:

- [Azure'da bir Linux VM görüntüsü oluşturmak için Packer kullanın](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Azure'da Windows VM görüntüsü oluşturmak için Packer kullanın](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) tanımlayın ve tüm Azure altyapısının bir tek şablon biçim diliyle - HashiCorp yapılandırma dili (HCL) oluşturmanıza imkan tanıyan bir Otomasyon aracı verilebilir. Terraform ile ağ, depolama ve uygulama çözümü için VM kaynakları oluşturma işlemini otomatik hale getiren şablonları tanımlayın. Mevcut Terraform şablonlarınızı tutarlılığı sağlamak ve altyapı dağıtımı için bir Azure Resource Manager şablonu dönüştürmek zorunda kalmadan basitleştirmek için Azure ile diğer platformlar için kullanabilirsiniz.

Şunları nasıl yapacağınızı öğrenin:

- [Terraform'u yükleme ve Azure ile yapılandırma](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Terraform ile Azure altyapısının oluşturma](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Otomasyonu
[Azure Otomasyonu](https://azure.microsoft.com/services/automation/) runbook'ları, hedef VM üzerinde görevleri bir dizi işlemek için kullanır. Azure Otomasyonu, varolan Vm'leri yönetmek yerine bir altyapı oluşturmak için kullanılır. Azure Otomasyonu, hem Linux ve Windows sanal makineleri, hem de karma runbook çalışanı ile şirket içi sanal veya fiziksel makineler üzerinde çalıştırabilir. Runbook'ları, GitHub gibi kaynak denetim deposu depolanabilir. Bu runbook, daha sonra el ile ya da tanımlanmış bir zamanlamaya göre çalıştırabilir.

Azure Otomasyonu, Vm'leri belirli bir dizi nasıl yapılandırılmalıdır tanımları oluşturmanıza olanak sağlayan bir Desired State Configuration ' nı (DSC) hizmet de sunar. DSC, ardından gerekli yapılandırma uygulanır ve VM tutarlı kalmasını sağlar. Azure Automation DSC, hem Windows hem de Linux makinelerinde çalışır.

Şunları nasıl yapacağınızı öğrenin:

- [PowerShell runbook'u oluşturma](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Karma Runbook çalışanı şirket içi kaynakları yönetmek için kullanma](../articles/automation/automation-hybrid-runbook-worker.md).
- [Azure Otomasyonu DSC kullanma](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps hizmetleriyle](https://www.visualstudio.com/team-services/) paylaşımı ve kod, otomatik derlemeler kullanın ve bir sürekli tümleştirme ve geliştirme (CI/CD) işlem hattı oluşturma izleme yardımcı olacak araçları paketidir. Azure DevOps hizmetleriyle, kullanımı kolaylaştırmak için Visual Studio ve diğer düzenleyiciler ile tümleştirilir. Azure DevOps hizmetleriyle oluşturabilir ve Azure Vm'leri yapılandırabilir ve ardından kod bu bilgisayarlara dağıtın.

Aşağıdakiler hakkında daha fazla bilgi edinin:

- [Azure DevOps Hizmetleri](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) , dağıtmak ve test uygulamaları ve kod teslimi için otomatik işlem hatları oluşturma yardımcı olan bir sürekli tümleştirme sunucusudur. Eklenti temel Jenkins platformu genişletmek için yüzlerce vardır ve diğer birçok ürün ve Web kancaları aracılığıyla çözümler ile de tümleştirebilirsiniz. El ile Jenkins Azure VM üzerinde yüklemek, Jenkins'den Docker kapsayıcısı içinde çalıştırın veya önceden oluşturulmuş Azure Market görüntüsü kullanın.

Şunları nasıl yapacağınızı öğrenin:

- [Jenkins, GitHub ve Docker ile azure'da bir Linux sanal makinesi üzerinde geliştirme altyapısı oluşturma](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Sonraki adımlar
Azure'da altyapı Otomasyon araçları kullanmak için birçok farklı seçenek vardır. Sahip olduğunuz bir ortamı ve gereksinimlerinize en uygun çözümü kullanma özgürlüğünü tanıyabilir. Azure'da yerleşik araçlar bazılarını deneyin başlamak için nasıl özelleştirmesini otomatik hale getirmek bkz. bir [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) veya [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) VM.
