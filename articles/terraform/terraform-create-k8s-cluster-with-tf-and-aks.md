---
title: Öğretici-Terkform kullanarak Azure Kubernetes Service (AKS) ile bir Kubernetes kümesi oluşturma
description: Azure Kubernetes Service ve Terraform ile Kubernetes Kümesi oluşturmayı gösteren öğretici
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 792c075cfb40eb4904a30b63e9902a59ceda9bc1
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159306"
---
# <a name="tutorial-create-a-kubernetes-cluster-with-azure-kubernetes-service-using-terraform"></a>Öğretici: Terrayform kullanarak Azure Kubernetes hizmeti ile bir Kubernetes kümesi oluşturma

[Azure Kubernetes hizmeti (AKS)](/azure/aks/) , barındırılan Kubernetes ortamınızı yönetir. AKS, kapsayıcı düzenleme uzmanlığı olmadan Kapsayıcılı uygulamaları dağıtmanıza ve yönetmenize olanak tanır. AKS Ayrıca, uygulamanızı çevrimdışı duruma getirmeden birçok yaygın bakım işlemi yapmanızı sağlar. Bu işlemler, kaynakları isteğe bağlı olarak sağlamayı, yükseltmeyi ve ölçeklendirmeyi içerir.

Bu öğreticide, aşağıdaki görevleri nasıl gerçekleştireceğinizi öğreneceksiniz:

> [!div class="checklist"]
> * HCL (HashiCorp Language) ile Kubernetes kümesi tanımlama
> * Terraform ve AKS ile Kubernetes kümesi oluşturma
> * kubectl aracıyla bir Kubernetes kümesinin kullanılabilirlik durumunu test etme

## <a name="prerequisites"></a>Önkoşullar

- **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.

- **Terraform'u yapılandırma**: [Terraform'u yükleme ve Azure erişimini yapılandırma](/azure/virtual-machines/linux/terraform-install-configure) makalesindeki yönergeleri izleyin

- **Azure hizmet sorumlusu**: **Azure CLI ile Azure hizmet sorumlusu oluşturma** makalesinin [Hizmet sorumlusunu oluşturma](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) bölümündeki yönergeleri izleyin. appId, displayName, password ve tenant değerlerini not edin.

## <a name="create-the-directory-structure"></a>Dizin yapısını oluşturma

İlk adım, bu alıştırmadaki Terraform yapılandırma dosyalarınızı barındıracak olan dizini oluşturmaktır.

1. [Azure portala](https://portal.azure.com) gidin.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)'i açın. Önceden bir ortam seçmediyseniz **Bash** ortamını seçin.

    ![Cloud Shell istemi](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. `clouddrive` dizinine geçin.

    ```bash
    cd clouddrive
    ```

1. `terraform-aks-k8s` adlı bir dizin oluşturun.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Dizinleri yeni dizinle değiştirin:

    ```bash
    cd terraform-aks-k8s
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
        version = "~>1.5"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Dosyayı kaydedin ( **&lt;ctrl > S**) ve düzenleyiciden çıkın ( **&lt;CTRL > Q**).

## <a name="define-a-kubernetes-cluster"></a>Kubernetes kümesi tanımlama

Kubernetes kümesinin kaynaklarını tanımlayan Terraform yapılandırma dosyasını oluşturun.

1. Cloud Shell'de `k8s.tf` adlı bir dosya oluşturun.

    ```bash
    code k8s.tf
    ```

1. Aşağıdaki kodu düzenleyiciye yapıştırın:

    ```hcl
    resource "azurerm_resource_group" "k8s" {
        name     = var.resource_group_name
        location = var.location
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = var.log_analytics_workspace_location
        resource_group_name = azurerm_resource_group.k8s.name
        sku                 = var.log_analytics_workspace_sku
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = azurerm_log_analytics_workspace.test.location
        resource_group_name   = azurerm_resource_group.k8s.name
        workspace_resource_id = azurerm_log_analytics_workspace.test.id
        workspace_name        = azurerm_log_analytics_workspace.test.name

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = var.cluster_name
        location            = azurerm_resource_group.k8s.location
        resource_group_name = azurerm_resource_group.k8s.name
        dns_prefix          = var.dns_prefix

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = file(var.ssh_public_key)
            }
        }

        agent_pool_profile {
            name            = "agentpool"
            count           = var.agent_count
            vm_size         = "Standard_DS1_v2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = var.client_id
            client_secret = var.client_secret
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = azurerm_log_analytics_workspace.test.id
            }
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    Yukarıdaki kod, kümenin, konumun ve kaynak grubu adının adını ayarlar. Tam etki alanı adı (FQDN) için önek de ayarlanır. FQDN, kümeye erişmek için kullanılır.

    `linux_profile` kaydı, SSH kullanarak çalışan düğümlerinde oturum açmayı etkinleştiren ayarları yapılandırmanıza olanak tanır.

    AKS ile yalnızca çalışan düğümleri için ödeme yaparsınız. `agent_pool_profile` kaydı bu çalışan düğümlerinin ayrıntılarını yapılandırır. `agent_pool_profile record` oluşturulacak çalışan düğümlerinin sayısını ve çalışan düğümlerinin türünü içerir. Daha sonra kümede ölçeği büyütme veya küçültme yapmanız gerekiyorsa, bu kayıttaki `count` değerini değiştirirsiniz.

1. Dosyayı kaydedin ( **&lt;ctrl > S**) ve düzenleyiciden çıkın ( **&lt;CTRL > Q**).

## <a name="declare-the-variables"></a>Değişkenleri tanımlama

1. Cloud Shell'de `variables.tf` adlı bir dosya oluşturun.

    ```bash
    code variables.tf
    ```

1. Aşağıdaki kodu düzenleyiciye yapıştırın:

    ```hcl
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. Dosyayı kaydedin ( **&lt;ctrl > S**) ve düzenleyiciden çıkın ( **&lt;CTRL > Q**).

## <a name="create-a-terraform-output-file"></a>Terraform çıkış dosyası oluşturma

[Terraform çıkışları](https://www.terraform.io/docs/configuration/outputs.html), Terraform bir plan uyguladığında kullanıcı için vurgulanabilecek değerleri tanımlamanızı sağlar. Bu değerler `terraform output` komutuyla sorgulanabilir. Bu bölümde [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) ile kümeye erişmenizi sağlayan bir çıkış dosyası oluşturacaksınız.

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
    ```

1. Dosyayı kaydedin ( **&lt;ctrl > S**) ve düzenleyiciden çıkın ( **&lt;CTRL > Q**).

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Terraform durumunu depolamak için Azure depolama alanı ayarlama

Terraform, durumu `terraform.tfstate` dosyasıyla yerel olarak izler. Bu model tek kişilik bir ortamda iyi çalışır. Çok kişili bir ortamda, durumu izlemek için [Azure depolama](/azure/storage/) kullanılır.

Bu bölümde, aşağıdaki görevlerin nasıl yapılacağını göreceksiniz:
- Depolama hesabı bilgilerini alma (hesap adı ve hesap anahtarı)
- Terrayform durum bilgilerinin depolanacağı bir depolama kapsayıcısı oluşturun.

1. Azure portalda sol taraftaki menüden **Tüm hizmetler**'i seçin.

1. **Depolama hesapları**’nı seçin.

1. **Depolama hesapları** sekmesinde Terraform durum bilgilerinin depolanacağı depolama hesabının adını seçin. Örneğin Cloud Shell'i ilk açtığınızda oluşturulmuş olan depolama hesabını kullanabilirsiniz.  Cloud Shell tarafından oluşturulan depolama hesabı genellikle `cs` ile başlar ve sonrasında rastgele sayı ve harf dizesi bulunur. Seçtiğiniz depolama hesabını bir yere göz atın. Bu değer daha sonra gereklidir.

1. Depolama hesabı sekmesinde **Erişim anahtarları**'nı seçin.

    ![Depolama hesabı adı](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. **key1** **anahtar** değerini not edin. (Anahtarın sağ tarafındaki simgeyi seçtiğinizde değer panoya kopyalanır.)

    ![Depolama hesabı erişim anahtarları](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. Cloud Shell, Azure depolama hesabınızda bir kapsayıcı oluşturun. Yer tutucuları ortamınız için uygun değerlerle değiştirin.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Kubernetes kümesi oluşturma

Bu bölümde `terraform init` komutunu kullanarak önceki bölümlerde oluşturduğunuz yapılandırma dosyalarında tanımlanan kaynakları oluşturmayı öğreneceksiniz.

1. Cloud Shell ' de Terrayform ' u başlatın. Yer tutucuları ortamınız için uygun değerlerle değiştirin.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    `terraform init` komutu, arka uç ve sağlayıcı eklentisinin başlatılma başarısını görüntüler:

    !["terraform init" komutunun sonuçları](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Hizmet sorumlusu kimlik bilgilerini dışarı aktarma Yer tutucuları hizmet sorumlınızdan uygun değerlerle değiştirin.

    ```bash
    export TF_VAR_client_id=<service-principal-appid>
    export TF_VAR_client_secret=<service-principal-password>
    ```

1. `terraform plan` komutunu çalıştırarak altyapı öğelerini tanımlayan Terraform planını oluşturun. 

    ```bash
    terraform plan -out out.plan
    ```

    `terraform plan` komutu, `terraform apply` komutunu çalıştırdığınızda oluşturularak kaynakları gösterir:

    !["terraform plan" komutunun sonuçları](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Kubernetes kümesini oluşturma planını uygulamak için `terraform apply` komutunu çalıştırın. Bir Kubernetes kümesi oluşturma işlemi birkaç dakika sürebilir ve Cloud Shell oturum zaman aşımına uğramaz. Cloud Shell oturum zaman aşımına uğrarsa öğreticiyi tamamlamanızı sağlamak için "Cloud Shell zaman aşımından kurtarma" bölümündeki adımları izleyebilirsiniz.

    ```bash
    terraform apply out.plan
    ```

    `terraform apply` komutu, yapılandırma dosyalarınızda tanımlı kaynakların oluşturulmasının sonuçlarını gösterir:

    !["terraform apply" komutunun sonuçları](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Azure portal yeni Kubernetes kümeniz için oluşturulan kaynakları görmek için, sol taraftaki menüden **tüm kaynaklar** ' ı seçin.

    ![Azure portal tüm kaynaklar](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Zaman aşımına uğrayan Cloud Shell oturumunu kurtarma

Cloud Shell oturumu zaman aşımına uğrarsa, kurtarmak için aşağıdaki adımları gerçekleştirebilirsiniz:

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

    ![kubectl aracı, Kubernetes kümenizin durumunu doğrulamanızı sağlar](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Sistem durumunu ve günlükleri izleme

AKS kümesi oluşturulduğunda hem küme düğümleri hem de pod'lar için sistem durumu ölçümlerini yakalamak için izleme özellikleri etkinleştirilmiştir. Bu sistem durumu ölçümleri Azure portaldan kullanılabilir. Kapsayıcı sistem durumu izleme hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmet durumunu izleme](/azure/azure-monitor/insights/container-insights-overview).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Azure 'da Terrayform kullanma hakkında daha fazla bilgi edinin](/azure/terraform)