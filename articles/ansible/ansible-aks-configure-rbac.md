---
title: Öğretici-Azure Kubernetes Service 'te (AKS) rol tabanlı erişim denetimi (RBAC) rollerini kullanarak yapılandırma
description: Azure Kubernetes Service (AKS) kümesinde RBAC 'yi yapılandırmak için nasıl kullanılacağını öğrenin
keywords: anyalabilen, Azure, DevOps, Bash, cloudshell, PlayBook, aks, Container, aks, Kubernetes, Azure Active Directory, RBAC
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1be123eb06bd2679169478daf27a7148d2a8b055
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156863"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Öğretici: Azure Kubernetes Service 'te (AKS) rol tabanlı erişim denetimi (RBAC) rollerini kullanarak yapılandırma

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS, Kullanıcı kimlik doğrulaması için [Azure Active Directory (ad)](/azure/active-directory/) kullanacak şekilde yapılandırılabilir. Yapılandırıldıktan sonra, AKS kümesinde oturum açmak için Azure AD kimlik doğrulama belirtecinizi kullanırsınız. RBAC, kullanıcının kimliğini veya dizin grubu üyeliğini temel alabilir.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Azure AD özellikli bir AKS kümesi oluşturma
> * Kümede RBAC rolü yapılandırma

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **RedHat OpenShift kitaplığı** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>AKS kimlik doğrulaması için Azure AD 'yi yapılandırma

Azure AD 'yi AKS kimlik doğrulaması için yapılandırırken iki Azure AD uygulaması yapılandırılır. Bu işlemin bir Azure kiracı yöneticisi tarafından tamamlanması gerekir. Daha fazla bilgi için bkz. [AKS ile Azure Active Directory tümleştirme](/azure/aks/aad-integration#create-the-server-application). 

Azure kiracı yöneticisi 'nden aşağıdaki değerleri alın:

- Sunucu uygulaması gizli dizisi
- Sunucu uygulama KIMLIĞI
- İstemci uygulama KIMLIĞI 
- Kiracı Kimliği

Bu değerler, örnek PlayBook 'u çalıştırmak için gereklidir.  

## <a name="create-an-aks-cluster"></a>AKS kümesi oluşturma

Bu bölümde, [Azure AD uygulaması](#configure-azure-ad-for-aks-authentication)Ile BIR aks oluşturacaksınız.

Örnek PlayBook ile çalışırken göz önünde bulundurmanız gereken bazı önemli notlar şunlardır:

- PlayBook, `~/.ssh/id_rsa.pub``ssh_key` yükler. Bunu değiştirirseniz, "SSH-RSA" (tırnak işaretleri olmadan) ile başlayan tek satır biçimini kullanın.
- `client_id` ve `client_secret` değerleri, varsayılan kimlik bilgisi dosyası olan `~/.azure/credentials`'dan yüklenir. Bu değerleri hizmet sorumlusu olarak ayarlayabilir veya bu değerleri ortam değişkenlerinden yükleyebilirsiniz:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

Aşağıdaki playbook'u `aks-create.yml` olarak kaydedin:

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aksversion_facts:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>Azure AD nesne KIMLIĞINI al

RBAC bağlama oluşturmak için önce Azure AD nesne KIMLIĞINI almanız gerekir. 

1. [Azure portalında](https://go.microsoft.com/fwlink/p/?LinkID=525040) oturum açın.

1. Sayfanın üst kısmındaki Ara alanında `Azure Active Directory`girin. 

1. Tıklatın `Enter`.

1. **Yönet** menüsünde **Kullanıcılar**' ı seçin.

1. Ad alanında, hesabınızı arayın.

1. **Ad** sütununda hesabınıza yönelik bağlantıyı seçin.

1. **Kimlik** bölümünde, **nesne kimliğini**kopyalayın.

    ![Azure AD nesne KIMLIĞINI kopyalayın.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>RBAC bağlaması oluşturma

Bu bölümde, AKS 'de bir rol bağlama veya küme rolü bağlama oluşturursunuz. 

Aşağıdaki playbook'u `kube-role.yml` olarak kaydedin:

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

`&lt;your-aad-account>` yer tutucusunu Azure AD kiracı [nesne kimliğiniz](#get-the-azure-ad-object-id)ile değiştirin.

Aşağıdaki PlayBook 'u kaydedin-yeni rolünüzü `aks-kube-deploy.yml`olarak dağıtır:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Örnek PlayBook 'u çalıştırma

Bu bölümde, bu makalede oluşturulan görevleri çağıran tüm örnek PlayBook listelenmektedir. 

Aşağıdaki playbook'u `aks-rbac.yml` olarak kaydedin:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

`vars` bölümünde aşağıdaki yer tutucuları Azure AD bilgileriniz ile değiştirin:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

`ansible-playbook` komutunu kullanarak tüm PlayBook 'ları çalıştırın:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Sonuçları doğrulama

Bu bölümde, bu makalede oluşturulan düğümleri kubectl listesini kullanırsınız.

Terminal isteminde aşağıdaki komutu girin:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

Komut sizi bir kimlik doğrulama sayfasına yönlendirdirecektir. Azure hesabınızla oturum açın.

Kubectl kimlik doğrulamasından sonra düğümleri aşağıdaki sonuçlara benzer şekilde listeler:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu makalede oluşturulan kaynakları silin. 

Aşağıdaki kodu `cleanup.yml`olarak kaydedin:

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure üzerinde Ansible](/azure/ansible/)
