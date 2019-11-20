---
title: Öğretici-anlanabilir kullanarak MySQL için Azure veritabanı 'nda veritabanlarını yapılandırma
description: MySQL sunucusu için Azure Veritabanı oluşturmak ve yapılandırmak için Ansible'ı kullanmayı öğrenin
keywords: ansible, azure, devops, bash, playbook, mysql, veritabanı
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d8c8fd361f37a9cb961012807fe40b905c10c047
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156534"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Öğretici: anlanabilir kullanarak MySQL için Azure veritabanı 'nda veritabanlarını yapılandırma

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[MySQL Için Azure veritabanı](/azure/mysql/overview) , MySQL Community sürümünü temel alan ilişkisel bir veritabanı hizmetidir. MySQL için Azure veritabanı, Web uygulamalarınızda MySQL veritabanlarını yönetmenizi sağlar.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * MySql sunucusu oluşturma
> * MySql veritabanı oluşturma
> * Bir dış uygulamanın sunucunuza bağlanabilmesi için bir fileduvar kuralı yapılandırın
> * Azure Cloud Shell 'den MySql sunucunuza bağlanma
> * Kullanılabilir MySQL sunucularınızı sorgulama
> * Bağlı sunuculardaki tüm veritabanlarını listeleme

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Bu bölümdeki PlayBook kodu bir Azure Kaynak grubu oluşturur. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.  

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

* `myResourceGroup` adlı bir kaynak grubu oluşturulur.
* Kaynak grubu `eastus` konumunda oluşturulur:

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>MySQL sunucusu ve veritabanı oluşturma

Bu bölümdeki PlayBook kodu bir MySQL sunucusu ve MySQL için Azure veritabanı örneği oluşturur. Yeni MySQL sunucusu, bir sanal çekirdeğe sahip ve `mysqlserveransible`adlı bir genel 5 temel amaç sunucusudur. Veritabanı örneği `mysqldbansible`olarak adlandırılmıştır.

Fiyatlandırma katmanları hakkında daha fazla bilgi için bkz. [MySQL Için Azure veritabanı fiyatlandırma katmanları](/azure/mysql/concepts-pricing-tiers). 

Aşağıdaki playbook'u `mysql_create.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

PlayBook 'u çalıştırmadan önce aşağıdaki notlara bakın:

* `vars` bölümünde, `mysqlserver_name` değeri benzersiz olmalıdır.
* `vars` bölümünde `<server_admin_password>` bir parolayla değiştirin.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Güvenlik duvarı kuralını yapılandırma

Sunucu düzeyinde bir güvenlik duvarı kuralı, bir dış uygulamanın Azure MySQL hizmeti güvenlik duvarı üzerinden sunucunuza bağlanmasına izin verir. Dış uygulamalara örnek olarak `mysql` komut satırı aracı ve MySQL çalışma ekranı verilebilir.

Bu bölümdeki PlayBook kodu, herhangi bir dış IP adresinden bağlantılara izin veren `extenalaccess` adlı bir güvenlik duvarı kuralı oluşturur. 

Aşağıdaki playbook'u `mysql_firewall.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

PlayBook 'u çalıştırmadan önce aşağıdaki notlara bakın:

* VARS bölümünde `startIpAddress` ve `endIpAddress`' yı değiştirin. Bağlanacağınız aralığa karşılık gelen IP adresi aralığını kullanın.
* MySQL için Azure Veritabanı bağlantıları 3306 bağlantı noktası üzerinden iletişim kurar. Kurumsal ağ içinden bağlanmaya çalışıyorsanız, 3306 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu örnekte, BT departmanınız 3306 numaralı bağlantı noktasını açmadığı sürece sunucunuza bağlanamazsınız.
* PlayBook, REST API doğrudan kullanımına izin veren `azure_rm_resource` modülünü kullanır.

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Sunucuya bağlanma

Bu bölümde, daha önce oluşturduğunuz sunucuya bağlanmak için Azure Cloud Shell 'i kullanırsınız.

1. Aşağıdaki kodda, **deneyin** düğmesini seçin:

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. İstemde, sunucu durumunu sorgulamak için aşağıdaki komutu girin:

    ```sql
    mysql> status
    ```
    
    Her şey iyi olursa aşağıdaki sonuçlara benzer bir çıktı görürsünüz:
    
    ```
    demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65233
    Server version: 5.6.39.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper
    
    Connection id:          65233
    Current database:
    Current user:           mysqladmin@13.76.42.93
    SSL:                    Cipher in use is AES256-SHA
    Current pager:          stdout
    Using outfile:          ''
    Using delimiter:        ;
    Server version:         5.6.39.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 36 min 21 sec
    
    Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
    --------------
    ```
    
## <a name="query-mysql-servers"></a>MySQL sunucularını sorgula

Bu bölümdeki PlayBook kodu, `myResourceGroup` MySQL sunucularını sorgular ve bulunan sunuculardaki veritabanlarını listeler.

Aşağıdaki playbook'u `mysql_query.yml` olarak kaydedin:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

`ansible-playbook` komutunu kullanarak PlayBook 'u çalıştırın:

```bash
ansible-playbook mysql_query.yml
```

PlayBook çalıştırıldıktan sonra aşağıdaki sonuçlara benzer bir çıktı görürsünüz:

```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

MySQL veritabanı için aşağıdaki çıktıyı da görürsünüz:

```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, bu makalede oluşturulan kaynakları silin. 

Aşağıdaki playbook'u `cleanup.yml` olarak kaydedin:

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