---
title: Öğretici-Azure Kubernetes hizmetinde bir Application Gateway ingcontroller oluşturma
description: Azure Kubernetes hizmeti ile bir Kubernetes kümesinin giriş denetleyicisi olarak Application Gateway ile nasıl oluşturulacağını gösteren öğretici
ms.topic: tutorial
ms.date: 11/13/2019
ms.openlocfilehash: 6d07fc6becf76453de792c69b25aea49c39775ae
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159101"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Öğretici: Azure Kubernetes hizmetinde bir Application Gateway ingcontroller oluşturma

[Azure Kubernetes hizmeti (AKS)](/azure/aks/) , barındırılan Kubernetes ortamınızı yönetir. AKS, kapsayıcı düzenleme uzmanlığı olmadan Kapsayıcılı uygulamaları dağıtmayı ve yönetmeyi hızlı ve kolay hale getirir. AKS Ayrıca, işletimsel ve bakım görevleri için uygulamaları çevrimdışına alma yükünü ortadan kaldırır. AKS 'i kullanarak, kaynakları sağlama, yükseltme ve ölçeklendirme dahil olmak üzere bu görevler isteğe bağlı olarak gerçekleştirilebilir.

Bir giriş denetleyicisi, Kubernetes Hizmetleri için çeşitli özellikler sağlar. Bu özellikler ters proxy, yapılandırılabilir trafik yönlendirme ve TLS sonlandırmasını içerir. Kubernetes giriş kaynakları, bağımsız bir Kubernetes hizmeti için giriş kurallarını yapılandırmak üzere kullanılır. Giriş denetleyicisi ve giriş kurallarını kullanarak tek bir IP adresi, trafiği bir Kubernetes kümesinde birden çok hizmete yönlendirebilir. Bu işlevlerin tümü Azure [Application Gateway](/azure/Application-Gateway/)tarafından sağlanır ve Azure 'Da Kubernetes için Ideal bir giriş denetleyicisi haline gelir. 

Bu öğreticide, aşağıdaki görevleri nasıl gerçekleştireceğinizi öğreneceksiniz:

> [!div class="checklist"]
> * Bir [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) kümesi, Application Gateway giriş denetleyicisi olarak, aks 'i kullanarak oluşturun.
> * Bir Kubernetes kümesi tanımlamak için HCL (HashiCorp Language) kullanın.
> * Application Gateway kaynak oluşturmak için Terrayform kullanın.
> * Bir Kubernetes kümesi oluşturmak için Terkform ve AKS ' i kullanın.
> * Kubectl aracını kullanarak bir Kubernetes kümesinin kullanılabilirliğini test edin.

## <a name="prerequisites"></a>Önkoşullar

- **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.

- **Terraform'u yapılandırma**: [Terraform'u yükleme ve Azure erişimini yapılandırma](/azure/virtual-machines/linux/terraform-install-configure) makalesindeki yönergeleri izleyin

- **Azure Kaynak grubu**: demo için kullanabileceğiniz bir Azure Kaynak grubunuz yoksa, [bir Azure Kaynak grubu oluşturun](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups). Kaynak grubu adı ve konumunu, bu değerler tanıtımda kullanıldığı için bir yere göz atın.

- **Azure hizmet sorumlusu**: **Azure CLI ile Azure hizmet sorumlusu oluşturma** makalesinin [Hizmet sorumlusunu oluşturma](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) bölümündeki yönergeleri izleyin. AppID, displayName ve Password değerlerini göz önünde atın.

- **Hizmet sorumlusu nesne kimliğini edinin**: Cloud Shell şu komutu çalıştırın: `az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>Dizin yapısını oluşturma

İlk adım, bu alıştırmadaki Terraform yapılandırma dosyalarınızı barındıracak olan dizini oluşturmaktır.

1. [Azure portala](https://portal.azure.com) gidin.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)'i açın.

1. `clouddrive` dizinine geçin.

    ```bash
    cd clouddrive
    ```

1. `terraform-aks-k8s` adlı bir dizin oluşturun.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Dizinleri yeni dizinle değiştirin:

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>Azure sağlayıcısını tanımlama

Azure sağlayıcısını tanımlayan Terraform yapılandırma dosyasını yapılandırın.

1. Cloud Shell'de `main.tf` adlı bir dosya oluşturun.

    ```bash
    code main.tf
    ```

1. Aşağıdaki kodu düzenleyiciye yapıştırın:

    ```hcl
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Dosyayı kaydedin ( **&lt;ctrl > S**) ve düzenleyiciden çıkın ( **&lt;CTRL > Q**).

## <a name="define-input-variables"></a>Giriş değişkenlerini tanımlama

Bu dağıtım için gerekli tüm değişkenleri listeleyen Terrayform yapılandırma dosyasını oluşturun.

1. Cloud Shell'de `variables.tf` adlı bir dosya oluşturun.

    ```bash
    code variables.tf
    ```

1. Aşağıdaki kodu düzenleyiciye yapıştırın:
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group."
    }

    variable "location" {
      description = "Location of the cluster."
    }

    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }

    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }

    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }

    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }

    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }

    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }

    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }

    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }

    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }

    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }

    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
      default     = 40
    }

    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }

    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }

    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }

    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }

    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }

    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }

    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }

    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }

    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }

    variable "tags" {
      type = "map"

      default = {
        source = "terraform"
      }
    }
    ```

1. Dosyayı kaydedin ( **&lt;ctrl > S**) ve düzenleyiciden çıkın ( **&lt;CTRL > Q**).

## <a name="define-the-resources"></a>Kaynakları tanımlama 
Tüm kaynakları oluşturan Terrayform yapılandırma dosyası oluşturun. 

1. Cloud Shell'de `resources.tf` adlı bir dosya oluşturun.

    ```bash
    code resources.tf
    ```

1. Hesaplanan değişkenlerin yeniden kullanılması için bir Yereller bloğu oluşturmak üzere aşağıdaki kod bloğunu yapıştırın:

    ```hcl
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```

1. Aşağıdaki kod bloğunu, kaynak grubu için bir veri kaynağı oluşturmak üzere yapıştırın, Yeni Kullanıcı kimliği:

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. Temel ağ kaynakları oluşturmak için aşağıdaki kod bloğunu yapıştırın:

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = var.virtual_network_name
      location            = data.azurerm_resource_group.rg.location
      resource_group_name = data.azurerm_resource_group.rg.name
      address_space       = [var.virtual_network_address_prefix]

      subnet {
        name           = var.aks_subnet_name
        address_prefix = var.aks_subnet_address_prefix
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = var.app_gateway_subnet_address_prefix
      }

      tags = var.tags
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = var.aks_subnet_name
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = data.azurerm_resource_group.rg.location
      resource_group_name          = data.azurerm_resource_group.rg.name
      allocation_method            = "Static"
      sku                          = "Standard"

      tags = var.tags
    }
    ```

1. Application Gateway kaynağı oluşturmak için aşağıdaki kod bloğunu yapıştırın:

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = var.app_gateway_name
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      sku {
        name     = var.app_gateway_sku
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = data.azurerm_subnet.appgwsubnet.id
      }

      frontend_port {
        name = local.frontend_port_name
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = local.frontend_ip_configuration_name
        public_ip_address_id = azurerm_public_ip.test.id
      }

      backend_address_pool {
        name = local.backend_address_pool_name
      }

      backend_http_settings {
        name                  = local.http_setting_name
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = local.listener_name
        frontend_ip_configuration_name = local.frontend_ip_configuration_name
        frontend_port_name             = local.frontend_port_name
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = local.request_routing_rule_name
        rule_type                  = "Basic"
        http_listener_name         = local.listener_name
        backend_address_pool_name  = local.backend_address_pool_name
        backend_http_settings_name = local.http_setting_name
      }

      tags = var.tags

      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

1. Rol atamaları oluşturmak için aşağıdaki kod bloğunu yapıştırın:

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = data.azurerm_subnet.kubesubnet.id
      role_definition_name = "Network Contributor"
      principal_id         = var.aks_service_principal_object_id 

      depends_on = ["azurerm_virtual_network.test"]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = azurerm_user_assigned_identity.testIdentity.id
      role_definition_name = "Managed Identity Operator"
      principal_id         = var.aks_service_principal_object_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = azurerm_application_gateway.network.id
      role_definition_name = "Contributor"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = data.azurerm_resource_group.rg.id
      role_definition_name = "Reader"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

1. Kubernetes kümesi oluşturmak için aşağıdaki kod bloğunu yapıştırın:

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = var.aks_name
      location   = data.azurerm_resource_group.rg.location
      dns_prefix = var.aks_dns_prefix

      resource_group_name = data.azurerm_resource_group.rg.name

      linux_profile {
        admin_username = var.vm_user_name

        ssh_key {
          key_data = file(var.public_ssh_key_path)
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      agent_pool_profile {
        name            = "agentpool"
        count           = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
        os_type         = "Linux"
        os_disk_size_gb = var.aks_agent_os_disk_size
        vnet_subnet_id  = data.azurerm_subnet.kubesubnet.id
      }

      service_principal {
        client_id     = var.aks_service_principal_app_id
        client_secret = var.aks_service_principal_client_secret
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = var.aks_dns_service_ip
        docker_bridge_cidr = var.aks_docker_bridge_cidr
        service_cidr       = var.aks_service_cidr
      }

      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = var.tags
    }

    ```

1. Dosyayı kaydedin ( **&lt;ctrl > S**) ve düzenleyiciden çıkın ( **&lt;CTRL > Q**).

Bu bölümde sunulan kod, kümenin adını, konumunu ve resource_group_name ayarlar. `dns_prefix` değeri-kümeye erişmek için kullanılan tam etki alanı adının (FQDN) bir parçasını oluşturur.

`linux_profile` kaydı, SSH kullanarak çalışan düğümlerinde oturum açmayı etkinleştiren ayarları yapılandırmanıza olanak tanır.

AKS ile yalnızca çalışan düğümleri için ödeme yaparsınız. `agent_pool_profile` kaydı bu çalışan düğümlerinin ayrıntılarını yapılandırır. `agent_pool_profile record` oluşturulacak çalışan düğümlerinin sayısını ve çalışan düğümlerinin türünü içerir. Daha sonra kümede ölçeği büyütme veya küçültme yapmanız gerekiyorsa, bu kayıttaki `count` değerini değiştirirsiniz.

## <a name="create-a-terraform-output-file"></a>Terraform çıkış dosyası oluşturma

[Terrayform çıkışları](https://www.terraform.io/docs/configuration/outputs.html) , terrampaform planı uygularken kullanıcıya vurgulanmış değerleri tanımlamanızı sağlar ve `terraform output` komutu kullanılarak sorgulanabilir. Bu bölümde [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) ile kümeye erişmenizi sağlayan bir çıkış dosyası oluşturacaksınız.

1. Cloud Shell'de `output.tf` adlı bir dosya oluşturun.

    ```bash
    code output.tf
    ```

1. Aşağıdaki kodu düzenleyiciye yapıştırın:

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }

    output "identity_resource_id" {
        value = azurerm_user_assigned_identity.testIdentity.id
    }

    output "identity_client_id" {
        value = azurerm_user_assigned_identity.testIdentity.client_id
    }
    ```

1. Dosyayı kaydedin ( **&lt;ctrl > S**) ve düzenleyiciden çıkın ( **&lt;CTRL > Q**).

## <a name="configure-azure-storage-to-store-terraform-state"></a>Azure Storage 'ı Terrayform durumunu depolayacak şekilde yapılandırma

Terraform, durumu `terraform.tfstate` dosyasıyla yerel olarak izler. Bu model tek kişilik bir ortamda iyi çalışır. Ancak, daha pratik bir çoklu kişi ortamında, [Azure depolama](/azure/storage/)'yı kullanarak sunucuda durumu izlemeniz gerekir. Bu bölümde, gerekli depolama hesabı bilgilerini almayı ve bir depolama kapsayıcısı oluşturmayı öğreneceksiniz. Terrayform durum bilgileri bu kapsayıcıda depolanır.

1. Azure portal **Azure hizmetleri**altında **depolama hesapları**' nı seçin. ( **Depolama hesapları** seçeneği ana sayfada görünmüyorsa, **diğer hizmetler** ' i seçin ve ardından bulun ve seçin.)

1. **Depolama hesapları** sayfasında, Terrayform 'un durumu depolamak için gereken depolama hesabının adını seçin. Örneğin Cloud Shell'i ilk açtığınızda oluşturulmuş olan depolama hesabını kullanabilirsiniz.  Cloud Shell tarafından oluşturulan depolama hesabı genellikle `cs` ile başlar ve sonrasında rastgele sayı ve harf dizesi bulunur. 

    Daha sonra ihtiyacınız olduğu gibi, seçtiğiniz depolama hesabını bir yere göz atın.

1. Depolama hesabı sayfasında **Erişim anahtarları**'nı seçin.

    ![Depolama hesabı adı](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. **key1** **anahtar** değerini not edin. (Anahtarın sağ tarafındaki simgeyi seçtiğinizde değer panoya kopyalanır.)

    ![Depolama hesabı erişim anahtarları](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. Cloud Shell, Azure depolama hesabınızda bir kapsayıcı oluşturun. Yer tutucuları, Azure depolama hesabınız için uygun değerlerle değiştirin.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Kubernetes kümesi oluşturma
Bu bölümde `terraform init` komutunu kullanarak önceki bölümlerde oluşturduğunuz yapılandırma dosyalarında tanımlanan kaynakları oluşturmayı öğreneceksiniz.

1. Cloud Shell ' de Terrayform ' u başlatın. Yer tutucuları, Azure depolama hesabınız için uygun değerlerle değiştirin.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    `terraform init` komutu, arka uç ve sağlayıcı eklentisinin başlatılma başarısını görüntüler:

    !["terraform init" komutunun sonuçları](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Cloud Shell, `terraform.tfvars`adlı bir dosya oluşturun:

    ```bash
    code terraform.tfvars
    ```

1. Daha önce oluşturulan aşağıdaki değişkenleri düzenleyiciye yapıştırın. Ortamınızın konum değerini almak için `az account list-locations`kullanın.

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. Dosyayı kaydedin ( **&lt;ctrl > S**) ve düzenleyiciden çıkın ( **&lt;CTRL > Q**).

1. `terraform plan` komutunu çalıştırarak altyapı öğelerini tanımlayan Terraform planını oluşturun. 

    ```bash
    terraform plan -out out.plan
    ```

    `terraform plan` komutu, `terraform apply` komutunu çalıştırdığınızda oluşturulan kaynakları görüntüler:

    !["terraform plan" komutunun sonuçları](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Kubernetes kümesini oluşturma planını uygulamak için `terraform apply` komutunu çalıştırın. Bir Kubernetes kümesi oluşturma işlemi birkaç dakika sürebilir ve Cloud Shell oturum zaman aşımına uğramaz. Cloud Shell oturum zaman aşımına uğrarsa öğreticiyi tamamlamanızı sağlamak için "Cloud Shell zaman aşımından kurtarma" bölümündeki adımları izleyebilirsiniz.

    ```bash
    terraform apply out.plan
    ```

    `terraform apply` komutu, yapılandırma dosyalarınızda tanımlı kaynakların oluşturulmasının sonuçlarını gösterir:

    !["terraform apply" komutunun sonuçları](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. Azure portal, seçili kaynak grubundaki yeni Kubernetes kümeniz için oluşturulan kaynakları görmek için sol menüdeki **kaynak grupları** ' nı seçin.

    ![Cloud Shell istemi](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Zaman aşımına uğrayan Cloud Shell oturumunu kurtarma

Cloud Shell oturumu zaman aşımına uğrarsa, kurtarmak için aşağıdaki adımları kullanabilirsiniz:

1. Cloud Shell oturumu başlatın.

1. Terraform yapılandırma dosyalarınızı içeren dizine geçin.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Şu komutu çalıştırın:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
  
## <a name="test-the-kubernetes-cluster"></a>Kubernetes kümesini test etme
Yeni oluşturulan kümeyi doğrulamak için Kubernetes araçlarını kullanabilirsiniz.

1. Terraform durumundaki Kubernetes yapılandırmasını alın ve kubectl tarafından okunabilecek bir dosyaya kaydedin.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. kubectl aracının doğru yapılandırmayı alabilmesi için bir ortam değişkeni ayarlayın.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Kümenin durumunu doğrulayın.

    ```bash
    kubectl get nodes
    ```

    Çalışan düğümlerinin ayrıntılarını görmeniz ve bu düğümlerin durumunun aşağıdaki görüntüde olduğu gibi **Ready** (Hazır) olması gerekir:

    ![kubectl aracı, Kubernetes kümenizin durumunu doğrulamanızı sağlar](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)

## <a name="install-azure-ad-pod-identity"></a>Azure AD Pod kimliğini yükler

Azure Active Directory Pod kimliği, [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)belirteç tabanlı erişim sağlar.

[Azure AD Pod Identity](https://github.com/Azure/aad-pod-identity) , Kubernetes kümenize aşağıdaki bileşenleri ekler:

  - Kubernetes [CRD 'ler](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity`, `AzureIdentityBinding`
  - [Yönetilen kimlik denetleyicisi (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) bileşeni
  - [Düğüm yönetilen kimliği (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) bileşeni

RBAC **etkinse**, KÜMENIZE Azure AD Pod kimliğini yüklemek için şu komutu çalıştırın:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

RBAC **devre dışıysa**, KÜMENIZE Azure AD Pod kimliğini yüklemek için şu komutu çalıştırın:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>Held 'yi yükler

Bu bölümdeki kod, [Heln](/azure/aks/kubernetes-helm) -Kubernetes paket yöneticisini kullanır-`application-gateway-kubernetes-ingress` paketini yüklemek için:

1. RBAC **etkinse**, Held 'yi yüklemek ve yapılandırmak için aşağıdaki komut kümesini çalıştırın:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. RBAC **devre dışıysa**, Held 'yi yüklemek ve yapılandırmak için aşağıdaki komutu çalıştırın:

    ```bash
    helm init
    ```

1. AGIC Held deposunu ekleyin:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>Giriş denetleyicisi Held grafiğini yükler

1. AGIC 'yi yapılandırmak için `helm-config.yaml` indirin:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. `helm-config.yaml` düzenleyin ve `appgw` ve `armAuth` bölümleri için uygun değerleri girin.

    ```bash
    code helm-config.yaml
    ```

    Değerler aşağıdaki şekilde açıklanmıştır:

    - `verbosityLevel`: AGIC günlük altyapısının ayrıntı düzeyini ayarlar. Olası değerler için [günlük düzeylerini](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) görüntüleyin.
    - `appgw.subscriptionId`: uygulama ağ geçidi için Azure abonelik KIMLIĞI. Örnek: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: uygulama ağ geçidinin oluşturulduğu Azure Kaynak grubunun adı. 
    - `appgw.name`: Application Gateway adı. Örnek: `applicationgateway1`.
    - `appgw.shared`: Bu Boole bayrağı `false`için varsayılan olarak ayarlanmalıdır. `true`, [paylaşılan bir uygulama ağ geçidine](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)ihtiyacınız olacak şekilde ayarlayın.
    - `kubernetes.watchNamespace`: AGIC 'in izlemeniz gereken ad alanını belirtin. Ad alanı, tek bir dize değeri veya ad alanlarının virgülle ayrılmış bir listesi olabilir. Bu değişkenin açıklama olarak bırakılması veya boş ya da boş dize olarak ayarlanması, giriş denetleyicisindeki tüm erişilebilir ad alanlarını gözlemleyerek.
    - `armAuth.type`: `aadPodIdentity` veya `servicePrincipal`değeri.
    - `armAuth.identityResourceID`: yönetilen kimliğin kaynak KIMLIĞI.
    - `armAuth.identityClientId`: kimliğin Istemci KIMLIĞI.
    - `armAuth.secretJSON`: yalnızca hizmet sorumlusu gizli türü seçildiğinde gereklidir (`armAuth.type` `servicePrincipal`olarak ayarlandığında).

    Önemli notlar:
    - `identityResourceID` değeri teraform betiğiyle oluşturulur ve şu şekilde çalıştırılarak bulunabilir: `echo "$(terraform output identity_client_id)"`.
    - `identityClientID` değeri teraform betiğiyle oluşturulur ve şu şekilde çalıştırılarak bulunabilir: `echo "$(terraform output identity_resource_id)"`.
    - `<resource-group>` değeri, uygulama ağ geçidinizin kaynak grubudur.
    - `<identity-name>` değeri oluşturulan kimliğin adıdır.
    - Belirli bir aboneliğin tüm kimlikleri: `az identity list`kullanılarak listelenebilir.

1. Application Gateway giriş denetleyicisi paketini yükler:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Örnek uygulama yükler

Uygulama ağ geçidi, AKS ve AGIC yüklendikten sonra, [Azure Cloud Shell](https://shell.azure.com/)aracılığıyla örnek bir uygulama yükleyebilirsiniz:

1. YAML dosyasını indirmek için kıvrımlı komutunu kullanın:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. YAML dosyasını uygulayın:

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu makalede oluşturulan kaynakları silin.  

Yer tutucusunu uygun değerle değiştirin. Belirtilen kaynak grubundaki tüm kaynaklar silinecek.

```bash
az group delete -n <resource-group>
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Application Gateway giriş denetleyicisi](https://azure.github.io/application-gateway-kubernetes-ingress/)