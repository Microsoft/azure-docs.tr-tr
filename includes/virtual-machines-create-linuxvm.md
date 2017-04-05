
1. [Azure CLI 1.0’dan Azure’a bağlanma](../articles/xplat-cli-connect.md) içinde listelenen adımları kullanarak Azure aboneliğinizde oturum açın.

2. Klasik dağıtım modunda olduğunuzdan emin olmak için aşağıdakileri yapın:

    ```azurecli
    azure config mode asm
    ```

3. Kullanılabilir görüntülerden yüklemek istediğiniz Linux görüntüsünü bulmak için aşağıdakileri yapın:

   ```azurecli   
    azure vm image list | grep "Linux"
    ```
   
    Bir Windows komut istemi penceresinde grep yerine **find** komutunu kullanın.
   
4. Önceki listeden Linux görüntüsü ile VM oluşturmak için `azure vm create` komutunu kullanın. Bu adımda bir bulut hizmeti ve depolama hesabı oluşturulur. Bu VM’yi bir `-c` seçeneği ile var olan bir bulut hizmetine de bağlamanız gerekir. `-e` seçeneği ile Linux sanal makinesinde oturum açmak için bir SSH uç noktası oluşturun. Aşağıdaki örnekte, `West US` konumunda `Ubuntu-14_04_4-LTS` görüntüsü kullanılarak `myVM` adlı bir VM oluşturulur ve `ops` kullanıcı adı eklenir:
   
    ```azurecli
    azure vm create myVM \
        b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB \
        -g ops -p P@ssw0rd! -z "Small" -e -l "West US"
    ```

    Çıktı aşağıdaki örneğe benzer:

    ```azurecli
    info:    Executing command vm create
    + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB
    + Looking up cloud service
    info:    cloud service myVM not found.
    + Creating cloud service
    + Retrieving storage accounts
    + Creating VM
    info:    vm create command OK
    ```
   
   > [!NOTE]
   > Bir Linux sanal makinesi için `vm create` içinde `-e` seçeneğini belirtmeniz gerekir. Sanal makine oluşturulduktan sonra SSH etkinleştirilemez. SSH hakkında daha ayrıntılı bilgi için bkz. [Azure’da Linux ile SSH kullanma](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

5. `azure vm show` komutunu kullanarak VM’nin özniteliklerini doğrulayabilirsiniz. Aşağıdaki örnekte `myVM` adlı VM’nin bilgileri listelenmiştir:

    ```azurecli   
    azure vm show myVM
    ```

6. VM’nizi aşağıdaki gibi `azure vm start` komutuyla başlatın:

    ```azurecli
    azure vm start myVM
    ```

## <a name="next-steps"></a>Sonraki adımlar
Tüm bu Azure CLI 1.0 sanal makine komutlarıyla ilgili ayrıntılar için [Azure CLI 1.0’ı Klasik dağıtım API’si ile kullanma](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) makalesini okuyun.

