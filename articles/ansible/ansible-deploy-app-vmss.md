---
title: Öğretici-Azure 'daki sanal makine ölçek kümelerine uygulama dağıtma (Anlabilen)
description: Azure sanal makine ölçek kümelerini yapılandırmak ve ölçek kümesinde uygulama dağıtmak için nasıl kullanılacağını öğrenin
keywords: ansible, azure, devops, bash, playbook, sanal makine, sanal makine ölçek kümesi, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 7c54790a3d988341caa65bdf0ce33c240f0580fb
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156399"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Öğretici: Azure 'daki sanal makine ölçek kümelerine uygulama dağıtma (Anlabilen)

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Bir Azure VM grubu için konak bilgilerini alma
> * Örnek uygulamayı kopyalama ve derleme
> * Bir ölçek kümesine JRE 'yi (Java Runtime Environment) yükler
> * Java uygulamasını bir ölçek kümesine dağıtma

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **Git** - Bu öğreticide Java örneği indirmek için [git](https://git-scm.com) kullanılmaktadır.
- **Java SE Development Kit (JDK)** - Örnek Java projesini derlemek için [JDK](https://aka.ms/azure-jdks) kullanılır.
- **Apache maven** - [Apache Maven](https://maven.apache.org/download.cgi) , örnek Java projesini oluşturmak için kullanılır.

## <a name="get-host-information"></a>Ana bilgisayar bilgilerini alma

Bu bölümdeki PlayBook kodu, bir sanal makine grubu için konak bilgilerini alır. Kod, belirtilen bir kaynak grubundaki genel IP adreslerini ve yük dengeleyiciyi alır ve envanterde `scalesethosts` adlı bir konak grubu oluşturur.

Aşağıdaki örnek playbook'u `get-hosts-tasks.yml` olarak kaydedin:

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Uygulamaları dağıtım için hazırlama

Bu bölümdeki PlayBook kodu, bir Java örnek projesini GitHub 'dan kopyalamak için `git` kullanır ve projeyi oluşturur. 

Aşağıdaki playbook'u `app.yml` olarak kaydedin:

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

Örnek Ansible playbook'u aşağıdaki komut ile çalıştırın:

  ```bash
  ansible-playbook app.yml
  ```

PlayBook çalıştırıldıktan sonra aşağıdaki sonuçlara benzer bir çıktı görürsünüz:

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>Uygulamayı bir ölçek kümesine dağıtma

Bu bölümdeki PlayBook kodu şu şekilde kullanılır:

* JRE 'yi `saclesethosts` adlı bir konak grubuna yükler
* Java uygulamasını `saclesethosts` adlı bir konak grubuna dağıtma

Örnek PlayBook 'u almanın iki yolu vardır:

* [PlayBook 'U indirin](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) ve `vmss-setup-deploy.yml`kaydedin.
* `vmss-setup-deploy.yml` adlı yeni bir dosya oluşturun ve aşağıdaki içerikleri içine kopyalayın:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

PlayBook 'u çalıştırmadan önce aşağıdaki notlara bakın:

* `vars` bölümünde, `{{ admin_password }}` yer tutucusunu kendi parolanızla değiştirin.
* SSH bağlantı türünü parolalarla birlikte kullanmak için sshpass programını yüklemelisiniz:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS

    ```bash
    yum install sshpass
    ```

* Bazı ortamlarda, anahtar yerine SSH parolası kullanmayla ilgili bir hata görebilirsiniz. Bu hatayı alırsanız, `/etc/ansible/ansible.cfg` veya `~/.ansible.cfg`' ye aşağıdaki satırı ekleyerek ana bilgisayar anahtarı denetimini devre dışı bırakabilirsiniz:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Playbook'u aşağıdaki komut ile çalıştırın:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Anerişilebilir-PlayBook komutunu çalıştırmanın çıktısı, örnek Java uygulamasının ölçek kümesinin konak grubuna yüklendiğini gösterir:

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>Sonuçları doğrulama

Ölçek kümesi için yük dengeleyicinin URL 'sine giderek çalışmalarınızın sonuçlarını doğrulayın:

![Azure 'da bir ölçek kümesinde çalışan Java uygulaması.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: Azure 'da sanal makine ölçek kümelerini anormal kullanarak otomatik ölçeklendirme](./ansible-auto-scale-vmss.md)