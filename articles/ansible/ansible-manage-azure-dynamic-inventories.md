---
title: Öğretici-Azure kaynaklarınızın dinamik envanterini, anormal kullanarak yapılandırma
description: Azure dinamik envanterlerinizi yönetmek için nasıl kullanılacağını öğrenin
keywords: anerişilebilir, Azure, DevOps, Bash, cloudshell, Dynamic Inventory
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: dea6b47e5e263fdd2e048a0435efbebfa90fe69e
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156166"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Öğretici: Azure kaynaklarınızın dinamik envanterini, anormal kullanarak yapılandırma

Anormal, çeşitli kaynaklardan (Azure gibi bulut kaynakları dahil) *dinamik bir stoğa*envanter bilgilerini çekmek için kullanılabilir. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * İki test sanal makinesi yapılandırın. 
> * Sanal makinelerden birini etiketleme
> * Etiketli sanal makinelere NGINX 'i yükler
> * Yapılandırılmış Azure kaynaklarını içeren bir dinamik envanter yapılandırma

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Test VM 'Leri oluşturma

1. [Azure portalında](https://go.microsoft.com/fwlink/p/?LinkID=525040) oturum açın.

1. [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)'i açın.

1. Bu öğreticide sanal makineleri barındıracak bir Azure Kaynak grubu oluşturun.

    > [!IMPORTANT]  
    > Bu adımda oluşturduğunuz Azure Kaynak grubu, tamamen küçük büyük harfe sahip bir ada sahip olmalıdır. Aksi takdirde, dinamik envanteri oluşturma işlemi başarısız olur.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Aşağıdaki tekniklerden birini kullanarak Azure 'da iki Linux sanal makinesi oluşturun:

    - **Anormal PlayBook** -makale, [Azure 'da bir temel sanal makine oluşturun](/azure/virtual-machines/linux/ansible-create-vm) ve anormal bir PlayBook 'tan sanal makine oluşturmayı gösterir. Sanal makinelerden birini veya her ikisini tanımlamak için bir PlayBook kullanırsanız, SSH bağlantısının parola yerine kullanıldığından emin olun.

    - **Azure CLI** -iki sanal makineyi oluşturmak için Cloud Shell aşağıdaki komutlardan her birini verin:

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-vm"></a>Bir VM’yi etiketleme

Azure kaynaklarınızı Kullanıcı tanımlı kategorilere göre [düzenlemek için etiketleri kullanabilirsiniz](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) . 

### <a name="using-ansible-version--28"></a>< 2,8 sürümünü kullanma
Anahtar `nginx``ansible-inventory-test-vm1` sanal makineyi etiketlemek için aşağıdaki [az Resource Tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) komutunu girin:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Anormal sürümü kullanma > = 2,8
Anahtar `Ansible=nginx``ansible-inventory-test-vm1` sanal makineyi etiketlemek için aşağıdaki [az Resource Tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) komutunu girin:

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Dinamik sayım oluşturma

Sanal makinelerinizi tanımladıktan (ve etiketledikten) sonra, dinamik envanteri oluşturmak zaman alabilir.

### <a name="using-ansible-version--28"></a>< 2,8 sürümünü kullanma

Anerişilebilir, Azure kaynaklarınızın dinamik envanterini üreten [azure_rm. Kopyala](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) adlı bir Python betiği sağlar. Aşağıdaki adımlar, iki test Azure sanal makinelerinize bağlanmak için `azure_rm.py` betiğini kullanma konusunda size yol gösterir:

1. `azure_rm.py` betiğini almak için GNU `wget` komutunu kullanın:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. `azure_rm.py` betiğinin erişim izinlerini değiştirmek için `chmod` komutunu kullanın. Aşağıdaki komut, belirtilen dosyanın (`azure_rm.py`) yürütülmesine (çalıştırmaya) izin vermek için `+x` parametresini kullanır:

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Kaynak grubunuza bağlanmak için [anerişilebilir komutunu](https://docs.ansible.com/ansible/2.4/ansible.html) kullanın: 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Bağlandıktan sonra, aşağıdaki çıktıya benzer sonuçlar görürsünüz:

    ```Output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="ansible-version--28"></a>Anerişilebilir sürüm > = 2,8

Anormal 2,8 ile başlayarak, anormal bir [Azure dinamik envanter eklentisi](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py)sağlar. Aşağıdaki adımlar, eklentiyi kullanma işleminde size yol gösterir:

1. Envanter eklentisi bir yapılandırma dosyası gerektirir. Yapılandırma dosyası `azure_rm` bitmelidir ve `yml` veya `yaml`uzantılı bir uzantıya sahip olmalıdır. Bu öğretici örneği için aşağıdaki PlayBook 'u `myazure_rm.yml`olarak kaydedin:

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. Kaynak grubundaki VM 'Lere ping atamak için aşağıdaki komutu çalıştırın:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Yukarıdaki komutu çalıştırırken şu hatayı alabilirsiniz:

    ```Output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    "Ana bilgisayar anahtarı doğrulama" hatası alırsanız, Anlabilen yapılandırma dosyasına aşağıdaki satırı ekleyin. Anormal yapılandırma dosyası `/etc/ansible/ansible.cfg` veya `~/.ansible.cfg`konumunda bulunur.

    ```bash
    host_key_checking = False
    ```

1. PlayBook 'u çalıştırdığınızda aşağıdaki çıktıya benzer sonuçlar görürsünüz:
  
    ```Output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>VM etiketini etkinleştir

### <a name="if-youre-using-ansible--28"></a>< 2,8 kullanıyorsanız,

- Bir etiket ayarladıktan sonra bu etiketi "etkinleştirmeniz" gerekir. Etiketi etkinleştirmenin bir yolu, etiketi bir ortam değişkenine dışarı aktararak `export` komutu aracılığıyla `AZURE_TAGS`.

    ```azurecli-interactive
    export AZURE_TAGS=nginx
    ```
    
- Şu komutu çalıştırın:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    Artık yalnızca bir sanal makine görürsünüz (etiketi, `AZURE_TAGS` ortam değişkenine eşlenen değerle eşleşen bir değer):

    ```Output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Daha erişilebilir > = 2,8 ' i kullanıyorsanız

- Aşağıdaki çıktıyı almak için komutu `ansible-inventory -i myazure_rm.yml --graph` çalıştırın:

    ```Output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- Ayrıca, NGINX VM bağlantısını test etmek için aşağıdaki komutu çalıştırabilirsiniz:
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>Etiketli VM 'de NGINX 'i ayarlama

Etiketlerin amacı, sanal makinelerinizin alt grupları ile hızlı ve kolay bir şekilde çalışma olanağı sağlamaktır. Örneğin, yalnızca bir `nginx`etiketi atadığınız sanal makinelerde NGINX 'i yüklemek istediğinizi varsayalım. Aşağıdaki adımlar, ne kadar kolay bir şekilde gerçekleştirileceğini göstermektedir:

1. `nginx.yml`adlı bir dosya oluşturun:

   ```azurecli-interactive
   code nginx.yml
   ```

1. Aşağıdaki örnek kodu düzenleyiciye yapıştırın:

    ```yml
        ---
        - name: Install and start Nginx on an Azure virtual machine
          hosts: all
          become: yes
          tasks:
          - name: install nginx
            apt: pkg=nginx state=installed
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. Dosyayı kaydedin ve düzenleyiciden çıkın.

1. `ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

   - Anerişilebilir < 2,8:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Anerişilebilir > = 2,8:

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. PlayBook çalıştırıldıktan sonra aşağıdaki sonuçlara benzer bir çıktı görürsünüz:

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] 

    TASK [Gathering Facts] 
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] 
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] 
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP 
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>NGINX yüklemesini test etme

Bu bölümde, NGINX 'in sanal makinenizde yüklü olduğunu test eden bir teknik gösterilmektedir.

1. `ansible-inventory-test-vm1` sanal makinenin IP adresini almak için [az VM List-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) komutunu kullanın. Döndürülen değer (sanal makinenin IP adresi), sanal makineye bağlanmak için SSH komutuna parametre olarak kullanılır.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. `ansible-inventory-test-vm1` sanal makinesine bağlıyken NGINX [-v](https://nginx.org/en/docs/switches.html) komutunu çalıştırarak NGINX 'nin yüklü olup olmadığını saptayın.

    ```azurecli-interactive
    nginx -v
    ```

1. `nginx -v` komutunu çalıştırdığınızda, NGINX 'in yüklü olduğunu belirten NGINX sürümü (ikinci satır) görürsünüz.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. SSH oturumunun bağlantısını kesmek için `<Ctrl>D` klavye birleşimine tıklayın.

1. `ansible-inventory-test-vm2` sanal makinesi için önceki adımların yapılması, NGINX 'in nereden alınacağını belirten bir bilgi iletisi verir (Bu noktada bu noktada yüklü olmadığını gösterir):

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Hızlı başlangıç: Azure 'da Linux sanal makinelerini kullanarak yapılandırma](/azure/virtual-machines/linux/ansible-create-vm)
