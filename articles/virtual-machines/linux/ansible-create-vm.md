---
title: Hızlı başlangıç-Azure 'da Linux sanal makinelerini kullanarak yapılandırma
description: Bu hızlı başlangıçta, Azure 'da bir Linux sanal makinesini nasıl oluşturacağınızı öğrenin.
keywords: ansible, azure, devops, sanal makine
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 325b581910bc343f57a2da00ab3ed6e447c1e9e3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037086"
---
# <a name="quickstart-configure-linux-virtual-machines-in-azure-using-ansible"></a>Hızlı başlangıç: Azure 'da Linux sanal makinelerini kullanarak yapılandırma

Ansible, bildirim temelli bir dil kullanarak Ansible *playbook'ları* aracılığıyla Azure kaynağı oluşturma, yapılandırma ve dağıtım işlemlerini otomatikleştirmenizi sağlar. Bu makalede, Linux sanal makinelerini yapılandırmaya yönelik örnek bir PlayBook sunulmaktadır. [Ansible playbook'un tamamı](#complete-sample-ansible-playbook), bu makalenin sonunda listelenmiştir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Ansible için kaynaklarınızın dağıtıldığı bir kaynak grubu gerekir. Aşağıdaki örnek Ansible playbook bölümünde `myResourceGroup` konumunda `eastus` adlı bir kaynak grubu oluşturulur:

```yaml
- name: Create resource group
  azure_rm_resourcegroup:
    name: myResourceGroup
    location: eastus
```

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

Azure sanal makinesi oluştururken bir [sanal ağ](/azure/virtual-network/virtual-networks-overview) oluşturmanız ya da mevcut bir sanal ağı kullanmanız gerekir. Sanal ağda sanal makinelerinize nasıl erişilmesini istediğinize de karar vermeniz gerekir. Aşağıdaki örnek Ansible playbook bölümünde `myVnet` adres alanında `10.0.0.0/16` adlı bir sanal ağ oluşturulur:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Sanal ağa dağıtılan tüm Azure kaynakları, sanal ağ içindeki bir [alt ağa](/azure/virtual-network/virtual-network-manage-subnet) dağıtılır. 

Aşağıdaki örnek Ansible playbook bölümünde `mySubnet` sanal ağında `myVnet` adlı bir alt ağ oluşturulur:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma





[Genel IP adresleri](/azure/virtual-network/virtual-network-ip-addresses-overview-arm), İnternet kaynaklarının Azure kaynakları ile gelen iletişimi kurmasına izin verir. Genel IP adresleri, Azure kaynaklarının genel kullanıma yönelik Azure hizmetleriyle iletişim kurmasına olanak tanır. Her iki senaryoda da erişilmekte olan kaynağa atanan bir IP adresi. Adres, atamayı kaldırana kadar kaynağa ayrılmıştır. Bir kaynağa genel bir IP adresi atanmamışsa, kaynak yine de Internet ile iletişim kurabilir. Bağlantı, Azure tarafından, kullanılabilir bir IP adresi dinamik olarak atanarak yapılır. Dinamik olarak atanan adres kaynağa ayrılmamış.

Aşağıdaki örnek Ansible playbook bölümünde `myPublicIP` adlı bir genel IP adresi oluşturulur:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

[Ağ güvenlik grupları](/azure/virtual-network/security-overview) , bir sanal ağdaki Azure kaynakları arasında ağ trafiğini filtreleyin. Güvenlik kuralları, Azure kaynaklarından gelen ve giden trafiği yöneten tanımlanmıştır. Azure kaynakları ve ağ güvenlik grupları hakkında daha fazla bilgi için bkz. [Azure hizmetleri Için sanal ağ tümleştirmesi](/azure/virtual-network/virtual-network-for-azure-services)

Aşağıdaki PlayBook, `myNetworkSecurityGroup`adlı bir ağ güvenlik grubu oluşturur. Ağ güvenlik grubu, TCP bağlantı noktası 22 ' de SSH trafiğine izin veren bir kural içerir.

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: Tcp
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```

## <a name="create-a-virtual-network-interface-card"></a>Sanal ağ arabirimi kartı oluşturma

Sanal ağ arabirimi kartı, sanal makinenizi belirli bir sanal ağa, genel IP adresine ve ağ güvenlik grubuna bağlar. 

Örnek bir PlayBook bölümünde bulunan aşağıdaki bölümde, oluşturduğunuz sanal ağ kaynaklarına bağlı `myNIC` adlı bir sanal ağ arabirim kartı oluşturulur:

```yaml
- name: Create virtual network interface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Son adım, bu makalenin önceki bölümlerinde oluşturduğunuz tüm kaynakları kullanan bir sanal makine oluşturmaktır. 

Bu bölümde gösterilen örnek Ansible playbook bölümü, `myVM` adlı bir sanal makine oluşturur ve `myNIC` adlı bir sanal ağ arabirimi kartını ekler. &lt;your-key-data> yer tutucusunun yerine kendi ortak anahtar verilerinizi yazın.

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys:
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>Örnek Ansible playbook'unun tamamı

Bu bölümde makale boyunca oluşturduğunuz örnek Ansible playbook'unun tamamı listelenmektedir. 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network interface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys:
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>Örnek Ansible playbook'unu çalıştırma

Bu bölümde bu makalede gösterilen Ansible playbook'unun çalıştırılma adımları gösterilmektedir.

1. [Azure portalında](https://go.microsoft.com/fwlink/p/?LinkID=525040) oturum açın.

1. [Cloud Shell](/azure/cloud-shell/overview)'i açın.

1. `azure_create_complete_vm.yml` adlı bir dosya (playbook'unuzu içerecek) oluşturun ve aşağıda gösterilen şekilde VI düzenleyicisinde açın:

   ```azurecli-interactive
   vi azure_create_complete_vm.yml
   ```

1. **I** anahtarını seçerek ekleme moduna geçin.

1. [Örnek Ansible playbook'unun tamamını](#complete-sample-ansible-playbook) düzenleyiciye yapıştırın.

1. **Esc** tuşuna basarak ekleme modundan çıkın.

1. Dosyayı kaydedin ve aşağıdaki komutu girerek VI düzenleyicisini kapatın:

    ```bash
    :wq
    ```

1. Örnek Ansible playbook'unu çalıştırın.

   ```bash
   ansible-playbook azure_create_complete_vm.yml
   ```

1. Çıktı aşağıdakine benzer olacak ve bir sanal makine oluşturulacaktır:

   ```bash
   PLAY [Create Azure VM] ****************************************************

   TASK [Gathering Facts] ****************************************************
   ok: [localhost]

   TASK [Create resource group] *********************************************
   changed: [localhost]

   TASK [Create virtual network] *********************************************
   changed: [localhost]

   TASK [Add subnet] *********************************************************
   changed: [localhost]

   TASK [Create public IP address] *******************************************
   changed: [localhost]

   TASK [Dump public IP for VM which will be created] ********************************************************************
   ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
   }

   TASK [Create Network Security Group that allows SSH] **********************
   changed: [localhost]

   TASK [Create virtual network interface card] *******************************
   changed: [localhost]

   TASK [Create VM] **********************************************************
   changed: [localhost]

   PLAY RECAP ****************************************************************
   localhost                  : ok=8    changed=7    unreachable=0    failed=0
   ```

1. Linux VM'nize erişmek için SSH komutu kullanılır. &lt;ip-address> yer tutucusunun yerine önceki adımda belirlediğiniz IP adresini yazın.

    ```bash
    ssh azureuser@<ip-address>
    ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Hızlı başlangıç: Azure 'da bir Linux sanal makinesini kullanarak yönetme](./ansible-manage-linux-vm.md)
