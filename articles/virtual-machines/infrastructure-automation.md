---
title: Altyapı Otomasyonu araçlarını kullanma
description: Azure 'da sanal makineler oluşturmak ve yönetmek için anormal, Chef, Pupevcil hayvan, Terrampaform ve Packer gibi altyapı Otomasyonu araçlarını nasıl kullanacağınızı öğrenin.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/17/2020
ms.author: cynthn
ms.openlocfilehash: 7a702fbf405011a9df3e7e77936a9a37f0fdfad7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730647"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Azure 'daki sanal makinelerle altyapı Otomasyonu araçlarını kullanma

Azure sanal makinelerini (VM 'Ler) bir ölçekte tutarlı bir şekilde oluşturmak ve yönetmek için, bazı Otomasyon formları genellikle istenir. Azure altyapı dağıtımı ve yönetim yaşam döngüsünün tamamını otomatikleştirmenize imkan tanıyan birçok araç ve çözüm vardır. Bu makalede, Azure 'da kullanabileceğiniz bazı altyapı Otomasyonu araçları tanıtılmaktadır. Bu araçlar, genellikle aşağıdaki yaklaşımlardan birine uyum sağlar:

- VM yapılandırmasını otomatikleştirme
    - Araçlar, [anormal](#ansible), [Chef](#chef), [pupevcil hayvan](#puppet)ve [Azure Resource Manager şablonu](#azure-resource-manager-template)içerir.
    - VM özelleştirmesine özgü araçlar, Linux sanal makineleri için [Cloud-init](#cloud-init) , [PowerShell İstenen Durum Yapılandırması (DSC)](#powershell-dsc)ve tüm Azure VM 'Leri Için [Azure Özel Betik uzantısı](#azure-custom-script-extension) 'nı içerir.

- Altyapı yönetimini otomatikleştirin
    - Araçlar, özel VM görüntü yapılarını otomatikleştirmek için [Packer](#packer) ve altyapı oluşturma işlemini otomatikleştirmek Için [terrayform](#terraform) içerir.
    - [Azure Otomasyonu](#azure-automation) , Azure ve şirket içi altyapınız üzerinde eylemler gerçekleştirebilir.

- Uygulama dağıtımını ve teslimini otomatikleştirin
    - [Azure DevOps Services](#azure-devops-services) ve [Jenkins](#jenkins)örnekleri sayılabilir.

## <a name="ansible"></a>Ansible
[Anormal](https://www.ansible.com/) , yapılandırma YÖNETIMI, VM oluşturma veya uygulama dağıtımı için bir Otomasyon altyapısıdır. Anormal, hedef makinelerin kimliğini doğrulamak ve yönetmek için genellikle SSH anahtarlarıyla bir aracı daha az modeli kullanır. Yapılandırma görevleri, belirli görevleri yürütmek için kullanılabilen çok sayıda modülle PlayBook 'lar içinde tanımlanır. Daha fazla bilgi için bkz. [anormal çalışma](https://www.ansible.com/how-ansible-works).

Şunları nasıl yapacağınızı öğrenin:

- [Azure ile kullanmak Için Linux üzerinde erişilebilir ve yapılandırın](/azure/developer/ansible/install-on-linux-vm).
- [Bir Linux sanal makinesi oluşturun](/azure/developer/ansible/vm-configure).
- [Linux sanal makinesini yönetin](/azure/developer/ansible/vm-manage).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) , altyapınızın nasıl yapılandırıldığını, dağıtıldığını ve yönetildiğini tanımlamaya yardımcı olan bir Otomasyon platformudur. Altyapı yerine uygulama yaşam döngüsü otomasyonu için Chef Habitat ve güvenlik ve ilke gereksinimleriyle uyumluluğu otomatik hale getirmeye yardımcı olan Chef InSpec dahil olmak üzere ek bileşenler. Chef Istemcileri, konfigürasyonları depolayan ve yöneten bir veya daha fazla merkezi Chef sunucusuyla hedef makinelere yüklenir. Daha fazla bilgi için bkz. [Chef 'e genel bakış](https://docs.chef.io/chef_overview.html).

Şunları nasıl yapacağınızı öğrenin:

- [Azure Marketi 'Nden Chef otomatikleştirmesini dağıtın](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Windows üzerinde Chef 'ı yükleyip Azure VM 'leri oluşturun](/azure/developer/chef/windows-vm-configure).


## <a name="puppet"></a>Puppet
[Pupevcil hayvan](https://www.puppet.com) , uygulama teslimi ve dağıtım sürecini işleyen kurumsal özellikli bir Otomasyon platformudur. Aracılar, Pupevcil hayvan yöneticisinin Azure altyapısının ve VM 'lerin istenen yapılandırmasını tanımlayan bildirimler çalıştırmasına izin vermek için hedef makinelere yüklenir. Pupevcil hayvan, gelişmiş bir DevOps iş akışı için Jenkins ve GitHub gibi diğer çözümlerle tümleştirilebilir. Daha fazla bilgi için bkz. [Pupevcil hayvan nasıl çalıştığını öğrenin](https://puppet.com/products/how-puppet-works).

Şunları nasıl yapacağınızı öğrenin:

- [Pupevcil hayvan dağıtın](https://puppet.com/docs/puppet/5.5/install_windows.html).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io), Linux VM’sini ilk kez önyüklendiğinde özelleştirmeyi sağlayan, sık kullanılan bir yaklaşımdır. cloud-init’i paket yükleme, dosyalara yazma ve kullanıcılar ile güvenliği yapılandırma işlemleri için kullanabilirsiniz. İlk önyükleme işlemi sırasında Cloud-init çağrıldığından, yapılandırmanızı uygulamak için başka bir adım veya gerekli aracı yoktur.  Dosyalarınızı düzgün şekilde biçimlendirme hakkında daha fazla bilgi için `#cloud-config` bkz. [Cloud-init belgeleri sitesi](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` dosyalar, Base64 olarak kodlanmış metin dosyalarıdır.

Cloud-init, dağıtımlar arasında da çalışır. Örneğin, bir paket yüklemek için **apt-get install** veya **yum install** kullanmazsınız. Bunun yerine, yüklenecek paketlerin listesini tanımlayabilirsiniz. Cloud-init, seçtiğiniz dağıtım için yerel paket yönetim aracını otomatik olarak kullanır.

Azure Marketi 'nde Cloud-init özellikli görüntülerin kullanılabilmesini sağlamak için, onaylanan Linux olmayan iş ortaklarıyla etkin bir şekilde çalışıyoruz. Bu görüntüler, Cloud-init dağıtımlarınızın ve yapılandırmalarının VM 'Ler ve sanal makine ölçek kümeleri ile sorunsuz bir şekilde çalışmasını kolaylaştırır.
Azure 'da Cloud-init hakkında daha fazla bilgi edinin:

- [Azure 'da Linux sanal makineleri için Cloud-init desteği](./linux/using-cloud-init.md)
- [Cloud-init kullanarak OTOMATIK VM yapılandırması ile ilgili bir öğretici deneyin](./linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell Istenen durum yapılandırması (DSC)](/powershell/scripting/dsc/overview/overview) , hedef makinelerin yapılandırmasını tanımlayan bir yönetim platformudur. DSC, [Open Management Infrastructure (OMı) sunucusu](https://collaboration.opengroup.org/omi/)üzerinden Linux üzerinde de kullanılabilir.

DSC yapılandırması, bir makineye nelerin yükleneceğini ve konağın nasıl yapılandırılacağını tanımlar. Yerel bir Configuration Manager (LCM) altyapısı, gönderilen yapılandırmalara bağlı olarak istenen eylemleri işleyen her bir hedef düğümde çalışır. Çekme sunucusu, DSC yapılandırmalarının ve ilişkili kaynakların depolanması için merkezi bir konakta çalışan bir Web hizmetidir. Çekme sunucusu, uyumluluk için gerekli yapılandırmaların ve raporun sağlanması için her bir hedef konaktaki LCM altyapısıyla iletişim kurar.

Şunları nasıl yapacağınızı öğrenin:

- [Temel BIR DSC yapılandırması oluşturun](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [DSC çekme sunucusu yapılandırın](/powershell/scripting/dsc/pull-server/pullserver).
- [Linux IÇIN DSC kullanın](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Azure Özel Betik Uzantısı
[Linux](./extensions/custom-script-linux.md) veya [Windows](./extensions/custom-script-windows.md) Için Azure Özel Betik uzantısı, Azure VM 'lerinde betikleri indirir ve yürütür. Bir VM oluşturduğunuzda veya VM kullanımda olduktan sonra istediğiniz zaman uzantıyı kullanabilirsiniz.

Betikler, Azure Storage 'dan veya GitHub deposu gibi genel bir konumdan indirilebilir. Özel Betik uzantısıyla, betikleri kaynak VM üzerinde çalışan herhangi bir dilde yazabilirsiniz. Bu betikler, uygulamaları yüklemek veya VM 'yi istendiği şekilde yapılandırmak için kullanılabilir. Kimlik bilgilerinin güvenliğini sağlamak için, parolalar gibi hassas bilgiler korumalı bir yapılandırmada depolanabilir. Bu kimlik bilgilerinin yalnızca VM 'nin içinde şifresi çözülür.

Şunları nasıl yapacağınızı öğrenin:

- [Azure CLI ile bir LINUX VM oluşturun ve özel Betik uzantısı 'nı kullanın](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx?toc=%2fcli%2fazure%2ftoc.json).
- [Azure PowerShell ile bir Windows sanal makinesi oluşturun ve özel betik uzantısını kullanın](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis).


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) , Azure 'da özel bir VM görüntüsü oluşturduğunuzda derleme işlemini otomatikleştirir. İşletim sistemini tanımlamak ve belirli gereksinimleriniz için VM 'yi özelleştiren yapılandırma sonrası betikleri çalıştırmak için Packer 'ı kullanırsınız. Yapılandırıldıktan sonra, VM daha sonra yönetilen disk görüntüsü olarak yakalanır. Packer, kaynak VM, ağ ve depolama kaynakları oluşturmak, yapılandırma betikleri çalıştırmak ve sonra VM görüntüsünü oluşturmak için işlemi otomatikleştirir.

Şunları nasıl yapacağınızı öğrenin:

- [Azure 'da bir LINUX VM görüntüsü oluşturmak Için Packer 'ı kullanın](./linux/build-image-with-packer.md).
- [Azure 'da bir WINDOWS VM görüntüsü oluşturmak Için Packer 'ı kullanın](./windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terkform](https://www.terraform.io) , tek bir şablon biçimi ile Azure altyapısının tamamını tanımlamanıza ve oluşturmanıza olanak tanıyan bir otomasyon aracıdır. Bu, diyez ICORP yapılandırma DILI (HCL). Terrayform sayesinde, belirli bir uygulama çözümü için ağ, depolama ve VM kaynakları oluşturma sürecini otomatikleştiren şablonlar tanımlarsınız. Bir Azure Resource Manager şablonuna dönüştürmeye gerek olmadan altyapı dağıtımını kolaylaştırmak ve basitleştirmek için mevcut Teraform şablonlarınızı Azure ile diğer platformlar için kullanabilirsiniz.

Şunları nasıl yapacağınızı öğrenin:

- [Azure Ile Terrayform 'U yükleyip yapılandırın](/azure/developer/terraform/getting-started-cloud-shell).
- [Terrayform ile bir Azure altyapısı oluşturun](/azure/developer/terraform/create-linux-virtual-machine-with-infrastructure).


## <a name="azure-automation"></a>Azure Otomasyonu
[Azure Otomasyonu](https://azure.microsoft.com/services/automation/) , hedeflediğiniz VM 'lerde bir dizi görevi işlemek için Runbook 'ları kullanır. Azure Otomasyonu, bir altyapı oluşturmak yerine var olan VM 'Leri yönetmek için kullanılır. Azure Otomasyonu hem Linux hem de Windows VM 'lerde, karma Runbook Worker ile şirket içi sanal veya fiziksel makinelerde çalıştırılabilir. Runbook 'lar GitHub gibi bir kaynak denetim deposunda depolanabilir. Bu runbook 'lar daha sonra el ile veya tanımlı bir zamanlamaya göre çalıştırılabilir.

Azure Otomasyonu, belirli bir VM kümesinin yapılandırılması için tanımlar oluşturmanıza olanak tanıyan Istenen durum yapılandırması (DSC) hizmetini de sağlar. DSC daha sonra gerekli yapılandırmanın uygulanmasını ve VM 'nin tutarlı kalmasını sağlar. Azure Automation DSC hem Windows hem de Linux makinelerde çalışır.

Şunları nasıl yapacağınızı öğrenin:

- [PowerShell runbook 'U oluşturun](../automation/learn/automation-tutorial-runbook-textual-powershell.md).
- [Şirket içi kaynakları yönetmek Için karma Runbook Worker 'ı kullanın](../automation/automation-hybrid-runbook-worker.md).
- [Azure Automation DSC kullanın](../automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps Services](https://www.visualstudio.com/team-services/) , kodu paylaşmanıza ve izlemenize, otomatik derlemeler kullanmanıza ve eksiksiz bir sürekli tümleştirme ve GELIŞTIRME (CI/CD) işlem hattı oluşturmanıza yardımcı olan bir araç paketidir. Azure DevOps Services kullanımı kolaylaştırmak için Visual Studio ve diğer düzenleyicilerle tümleştirilir. Azure DevOps Services Ayrıca, Azure VM 'Leri oluşturup yapılandırabilir ve ardından bunlara kod dağıtabilir.

Aşağıdakiler hakkında daha fazla bilgi edinin:

- [Azure DevOps Services](/azure/devops/user-guide/index).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://jenkins.io) , uygulamaları dağıtmaya ve test etmeye ve kod teslimi için otomatik işlem hatları oluşturmaya yardımcı olan bir sürekli tümleştirme sunucusudur. Çekirdek Jenkins platformunu genişletmek için yüzlerce eklenti vardır ve Web kancaları aracılığıyla birçok başka ürün ve çözüm ile de tümleştirebilirsiniz. Jenkins 'i bir Azure VM 'ye el ile yükleyebilir, bir Docker kapsayıcısının içinden Jenkins 'i çalıştırabilir veya önceden oluşturulmuş bir Azure Market görüntüsü kullanabilirsiniz.

Şunları nasıl yapacağınızı öğrenin:

- [Azure 'Da Jenkins, GitHub ve Docker ile bir Linux sanal makinesi üzerinde bir geliştirme altyapısı oluşturun](/azure/developer/jenkins/pipeline-with-github-and-docker).


## <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu
[Azure Resource Manager](../azure-resource-manager/templates/overview.md) , Azure dağıtım ve yönetim hizmetidir. Azure aboneliğinizde kaynak oluşturmanıza, güncelleştirmenize ve silmenizi sağlayan bir yönetim katmanı sağlar. Dağıtım sonrasında kaynaklarınızın güvenliğini sağlamak ve onları düzenlemek için erişim denetimleri, kilitler ve etiketler gibi yönetim özelliklerini kullanabilirsiniz.

Şunları nasıl yapacağınızı öğrenin:

- [Kaynak Yöneticisi şablonu kullanarak spot VM 'Leri dağıtın](./linux/spot-template.md).
- [Kaynak Yöneticisi şablonundan bir Windows sanal makinesi oluşturun](./windows/ps-template.md).
- [BIR VM için şablonu indirin](/previous-versions/azure/virtual-machines/windows/download-template).
- [Azure görüntü Oluşturucu şablonu oluşturun](./linux/image-builder-json.md).

## <a name="next-steps"></a>Sonraki adımlar
Azure 'da altyapı Otomasyonu araçlarını kullanmanın birçok farklı seçeneği vardır. Gereksinimlerinize ve ortamınıza en uygun çözümü kullanma özgürlüğüne sahipsiniz. Kullanmaya başlamak ve Azure 'da yerleşik araçları denemek için bkz. bir [Linux](./linux/tutorial-automate-vm-deployment.md) veya [Windows](./windows/tutorial-automate-vm-deployment.md) sanal makinesi özelleştirmesini otomatikleştirme.
