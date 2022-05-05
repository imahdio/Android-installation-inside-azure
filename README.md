based [this article](https://mahsa-hanifi.medium.com/running-android-inside-azure-68977c687ff5), I'm going to install android x86 os on an azure virtual machine. there are 2 methods I have tested them out:

# 1- Azure CLI and PowerShell

<table>
    <tr><th>steps</th><th>details</th><th>related images</th>
    </tr>
    <tr><td rowspan=3>1-Prerequisites</td><td>1.1-Download and install Azure CLI on my windows OS</td><td><img src="https://user-images.githubusercontent.com/64577273/166830855-65ff691c-d7b2-4e51-8997-525f408a5398.png"></td>
    </tr>
    <tr><td>1.2-Upgrading existing Windows PowerShell <a href="https://github.com/PowerShell/PowerShell/releases/tag/v7.2.3">through this link</a></td><td><img src="https://user-images.githubusercontent.com/64577273/166832781-31d2726a-15e2-4a22-84a8-0df6fbfcb75e.png"></td>
    </tr>
    <tr><td>1.3-install Azure PowerShell with 3 following cmdlets<br><br><ul><li><code>Install-Module -Name PowerShellGet -Force</code></li><li><code>Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force</code></li><li><code>Install-Module -Name AzureRM -AllowClobber</code></li></ul></td><td><img src="https://user-images.githubusercontent.com/64577273/166837597-d9a4914c-5939-441e-973c-6dca1fca86a6.png"></td>
    </tr>
    <tr><td rowspan=3>2-Preparing the Disk</td><td>2.1-download desired Android x86 releases from <a href="https://www.android-x86.org/download">official website</a></td><td><img src="https://user-images.githubusercontent.com/64577273/166838259-fdedfd53-b76c-4268-9454-7e5dbd163507.png"></td>
    </tr>
    <td>2.2-installing virtualbox from <a href="https://www.virtualbox.org/wiki/Downloads">official website</a></td><td><img src="https://user-images.githubusercontent.com/64577273/166837449-42bb0193-a07f-406f-a683-cc77a0a66d41.png"></td>
    </tr>
    <tr><td>2.3-converting downloaded iso to vhd with assist of virtual box through this cmd command<br><br><code>VBoxManage convertfromraw [file.iso] [file.VMDK] </code></td><td><img src="https://user-images.githubusercontent.com/64577273/166838880-d69b34f5-ddcf-49e7-b880-ccd954026e59.png"></td>
    </tr>
    <tr><td rowspan=6>3-creating virtual machine on azure with this cmdlet</td><td>3.1-Login to Azure<br><br><code>az login</code></td><td><img src="https://user-images.githubusercontent.com/64577273/166868531-521259ed-e9c6-4265-a7d2-2dcdae6081d9.png"></td>
    </tr>
    <tr><td>3.2-Create Resource Group<br><br><ul><li><code>$rgName="my-android-resource-group"</code></li><li><code>$locationCode="australiaeast"</code></li><li><code>az group create --name $rgName --location $locationCode</code></li></ul></td><td><img src="https://user-images.githubusercontent.com/64577273/166869289-4dcbef05-c4c2-441d-a2ae-c830b4becb60.png"></td>
    </tr>
    <tr><td>3.3-create Virtual Network<br><br><ul><li><code>$vNetworkName="${rgName}-vn"</code></li><li><code>$subnetName="${vNetworkName}-sub"</code></li><li><code>az network vnet create --name $vNetworkName --resource-group $rgName --subnet-name $subnetName</code></li></ul></td><td><img src="https://user-images.githubusercontent.com/64577273/166869807-bf1642c8-cdfe-463c-8059-bb9ad6ec3f7f.png"></td>
    </tr>
    <tr><td>3.4-create Storage Account<br><br><ul><li><code>$appname="myandroid4"</code></li><li><code>$storageAccountName="${appname}sa"</code></li><li><code>az storage account create --name $storageAccountName --resource-group $rgName --location $locationCode --sku Standard_LRS --kind StorageV2 --encryption-services blob</code></li></ul></td><td><img src="https://user-images.githubusercontent.com/64577273/166871671-cbbf39f5-b39a-4998-b077-ea7d17295b4f.png"></td>
    </tr>
    <tr><td>3.5-create the container<br><br><ul><li><code>$keyValue=az storage account keys list --account-name $storageAccountName --resource-group $rgName --query [0].value --output JSON</code></li><li><code>$containerName="${appname}c"</code></li><li><code>az storage container create --name $containerName --account-key $keyValue --account-name $storageAccountName</code></li><li><code>az storage blob upload -f C:\Users\imahdio\Downloads\android-x86-9.0-r2.vhd -c $containerName -n android-x86-9.0-r2 --account-name $storageAccountName --account-key $keyValue --auth-mode key --overwrite</code></li></td><td><img src="https://user-images.githubusercontent.com/64577273/166876960-27744a7c-f9ef-4dd9-8823-06dc4748e09f.png"></td>
    </tr>
    <tr><td>3.6-apply creating new vm successfully<ul><li><code>$virtualMachineName="${rgName}-vm"</code></li><li><code>$size="Standard_B1s"</code></li><li><code>az vm create --public-ip-sku Standard --resource-group $rgName --name $virtualMachineName --location $locationCode --image UbuntuLTS --storage-account $storageAccountName --use-unmanaged-disk --vnet-name $vNetworkName --subnet $subnetName --size $size --no-wait --admin-username 'imahdio' --admin-password 'samplepass'</code></li></ul></td><td><img src="https://user-images.githubusercontent.com/64577273/166887826-f543ef0a-e4da-4b83-a350-5f33ebaeaf56.png"><br><img src="https://user-images.githubusercontent.com/64577273/166888080-a5ec9a9b-ae4b-4d94-b8b6-1527bc351c96.png"></td>
    </tr>
    <tr><td>4-set up the Network section</td><td>Add inbound security rule 5555</td><td><img src="https://user-images.githubusercontent.com/64577273/166890947-6a71f2fe-3165-4450-a00f-b51787a900fd.png"></td>
    </tr>
    <tr><td>5-Connect to the Android VM using bastion</td><td>successfully connection result but without any android environment(!!!)</td><td><img src="https://user-images.githubusercontent.com/64577273/166897575-ab69cc3d-ca0b-4a3c-9142-4e7a78d15817.png"></td>
    </tr>
    <tr><td rowspan=4>6-Connect to the Android VM using adb communications</td><td>6.1-Install Latest Version of Java based <a href="https://www.maketecheasier.com/install-android-sdk-in-windows">this article</a></td><td><img src="https://user-images.githubusercontent.com/64577273/166892946-f92fa311-d0be-4acc-b293-ab7d46d65c79.png"></td>
    </tr>
    <tr><td>6.2-add path to environment</td><td><img src="https://user-images.githubusercontent.com/64577273/166894557-5b5d761b-65b0-49ff-88f3-2a8809f26181.png"></td>
    </tr>
    <tr><td>6.3-Install sdkmanager and SDK platform-Tools</td><td></td>
    </tr>
    <tr><td>6.4-failed connection result</td><td><img src="https://user-images.githubusercontent.com/64577273/166897296-6856edd6-18fe-4377-9deb-b82fd5ba995a.png"></td>
    </tr>
</table>