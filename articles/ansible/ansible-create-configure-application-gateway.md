---
title: Öğretici-Azure Application Gateway ile Web trafiğini yönetme
description: Web trafiğini yönetmek üzere bir Azure Application Gateway oluşturup yapılandırmak için Ansible kullanmayı öğrenin
keywords: anerişilebilir, Azure, DevOps, Bash, PlayBook, Application Gateway, yük dengeleyici, Web trafiği
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 07f75e39b8c6f592ecd4c48697527493b1109bb9
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156619"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Öğretici: Azure Application Gateway kullanarak Web trafiğini yönetme

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Application Gateway](/azure/application-gateway/overview), web uygulamalarınıza trafiği yönetmenizi sağlayan bir web trafiği yük dengeleyicisidir. Kaynak IP adresi ve bağlantı noktasına bağlı olarak, geleneksel yük dengeleyiciler trafiği bir hedef IP adresine ve bağlantı noktasına yönlendirir. Application Gateway, trafiğin URL 'ye göre yönlendirilebileceği daha ayrıntılı bir denetim sağlar. Örneğin, `images` URL 'nin yolu ise trafik, görüntüler için yapılandırılmış belirli bir sunucu kümesine (havuz olarak bilinir) yönlendirildiğini tanımlayabilirsiniz.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Ağ ayarlama
> * HHPD görüntüleriyle iki Azure kapsayıcı örneği oluşturma
> * Sunucu havuzundaki Azure kapsayıcı örnekleriyle bir uygulama ağ geçidi oluşturma

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Bu bölümdeki PlayBook kodu bir Azure Kaynak grubu oluşturur. Kaynak grubu, Azure kaynaklarının yapılandırıldığı mantıksal bir kapsayıcıdır.  

Aşağıdaki playbook'u `rg.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

PlayBook 'u çalıştırmadan önce aşağıdaki notlara bakın:

- Kaynak grubu adı `myResourceGroup`. Bu değer öğretici genelinde kullanılır.
- Kaynak grubu `eastus` konumunda oluşturulur.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma

Bu bölümdeki PlayBook kodu, uygulama ağ geçidinin diğer kaynaklarla iletişim kurmasını sağlamak için bir sanal ağ oluşturur.

Aşağıdaki playbook'u `vnet_create.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

PlayBook 'u çalıştırmadan önce aşağıdaki notlara bakın:

* `vars` bölümü, ağ kaynaklarını oluşturmak için kullanılan değerleri içerir. 
* Bu değerleri, belirli ortamınız için değiştirmeniz gerekir.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Sunucu oluşturma

Bu bölümdeki PlayBook kodu, uygulama ağ geçidi için Web sunucusu olarak kullanılacak HTTPD görüntüleriyle iki Azure Container örneği oluşturur.  

Aşağıdaki playbook'u `aci_create.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Uygulama ağ geçidi oluşturma

Bu bölümdeki PlayBook kodu, `myAppGateway`adlı bir uygulama ağ geçidi oluşturur.  

Aşağıdaki playbook'u `appgw_create.yml` olarak kaydedin:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

PlayBook 'u çalıştırmadan önce aşağıdaki notlara bakın:

* `appGatewayIP` `gateway_ip_configurations` bloğunda tanımlanmıştır. Ağ geçidinin IP yapılandırması için alt ağ başvurusu gerekir.
* `appGatewayBackendPool` `backend_address_pools` bloğunda tanımlanmıştır. Bir uygulama ağ geçidi en az bir arka uç adres havuzuna sahip olmalıdır.
* `appGatewayBackendHttpSettings` `backend_http_settings_collection` bloğunda tanımlanmıştır. Bağlantı noktası 80 ve bir HTTP protokolünün iletişim için kullanıldığını belirtir.
* `appGatewayHttpListener` `backend_http_settings_collection` bloğunda tanımlanmıştır. appGatewayBackendPool ile ilişkilendirilmiş varsayılan dinleyicidir.
* `appGatewayFrontendIP` `frontend_ip_configurations` bloğunda tanımlanmıştır. appGatewayHttpListener’a myAGPublicIPAddress’i atar.
* `rule1` `request_routing_rules` bloğunda tanımlanmıştır. appGatewayHttpListener ile ilişkilendirilmiş varsayılan yönlendirme kuralıdır.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook appgw_create.yml
```

Uygulama ağ geçidinin oluşturulması birkaç dakika sürebilir.

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

1. [Kaynak grubu oluştur](#create-a-resource-group) bölümünde bir konum belirtirsiniz. Değerini aklınızda yapın.

1. [Ağ kaynakları oluştur](#create-network-resources) bölümünde, etki alanını belirtirsiniz. Değerini aklınızda yapın.

1. Aşağıdaki kalıbı konum ve etki alanı ile değiştirerek test URL 'SI için: `http://<domain>.<location>.cloudapp.azure.com`.

1. Test URL 'sine gidin.

1. Aşağıdaki sayfayı görürseniz, uygulama ağ geçidi beklendiği gibi çalışıyor demektir.

    ![Çalışan uygulama ağ geçidinin başarılı testi](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu makalede oluşturulan kaynakları silin. 

Aşağıdaki kodu `cleanup.yml`olarak kaydedin:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure üzerinde Ansible](/azure/ansible/)