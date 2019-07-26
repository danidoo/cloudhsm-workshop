## AWS CloudHSM - Part II - Managing Keys
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

</details>

### 1. Creando llaves en el HSM
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />
En este modulo "you will be using key_mgmt_util software provided in cloudHSM Client package to create symmmetric keys, RSA Key pair and ECC key pair."

- __1.1.__ Vuelva a la pestaña o ventana del navegador del [AWS Systems Manager](https://console.aws.amazon.com/ssm/home) en la terminal de la instancia
- __1.1.__ Conectese al 
```
sudo systemctl start cloudhsm-client
```

- __1.1.__ Conectese al 
```
./key_mgmt_util
```

- __1.1.__ 
```
loginHSM -u CU -s user1 -p defaultPassword
```

- __1.1.__ 
```
genSymKey -t 31 -s 32 -l aes256
```

- __1.1.__ 
```
genRSAKeyPair -m 2048 -e 65541 -l rsa_test
```

- __1.1.__ 
```
genECCKeyPair -i 12 -l ecc12
```

- __1.1.__ 
```
exit
```

### 2. Importando llaves al HSM
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />
En este modulo "you will be using key_mgmt_util software provided in cloudHSM Client package to import symmetric keys, export symmetric keys and wrap keys."

- __2.1.__ Vuelva a la pestaña o ventana del navegador del [AWS Systems Manager](https://console.aws.amazon.com/ssm/home) en la terminal de la instancia
- __2.1.__ The first command uses OpenSSL to generate a random 256-bit AES symmetric key. It saves the key in the aes256.key file.
```
openssl rand -out aes256-forImport.key 32
```

- __2.1.__ Conectese al 
```
./key_mgmt_util
```

- __1.1.__ 
```
loginHSM -u CU -s user1 -p defaultPassword
```

- __1.1.__ Generate a wrapping key on the HSM and take note of the key handle returned.You will reuire it in the below exercises.
```
genSymKey -t 31 -s 32 -l wrappinhKey
```

- __1.1.__ Use the imSymKey to import the AES key from the aes256.key file into the HSMs.
```
imSymKey -f aes256.key -w <wrapping key handle> -t 31 -l imported
```

- __1.1.__ Generate a 3DES key and note the key handle returned. This key will be exported later
```
genSymKey -t 21 -s 24 -l 3DES_export
```

- __1.1.__ This command exports a Triple DES (3DES) symmetric key (key handle -k). It uses an existing AES key (key handle -w) in the HSM as the wrapping key. Then it writes the plaintext of the 3DES key to the 3DES.key file.
```
exSymKey -k <handle for 3DES key> -w <wrapping key handle> -out 3DES.key
```

- __1.1.__ 
```
openssl enc -d -aes-256-cbc -in plain.txt -out encr.txt -pass file:./3DES.key
```

- __1.1.__ 
```

```

- __1.1.__ 
```

```

- __1.1.__ 
```

```

- __1.1.__ 
```

```

- __1.1.__ 
```

```


### 2. Cree un cluster de CloudHSM
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __2.1.__ En una nueva pestaña o ventana del navegador, abra la consola de AWS en el servicio [AWS CloudHSM](https://console.aws.amazon.com/cloudhsm/home) en la región de N. Virginia
- __2.2.__ Haga click en **Create cluster**, y usando los Ids del VPC y las subnets, elija los parametros adecuados
- __2.3.__ Deje la opción **Create new cluster** seleccionada y haga click en **Next: Review** y después en **Create cluster**
- __2.4.__ La creación del cluster puede llevar hasta 10 minutos. Siga con la proxima etapa mientras el proceso sigue.

### 3. Cree un bucket S3 para almacenamiento de objetos
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __3.1.__ En una nueva pestaña o ventana del navegador, abra la consola de AWS en el servicio [Amazon S3](https://console.aws.amazon.com/s3/home)
- __3.2.__ Haga click en **Create bucket**
- __3.2.__ En **Bucket name** llene con el nombre **crypto-&lt;su_userid&gt;** (los buckets tinen que tener nombres únicos globalmente)
- __3.2.__ Elija la región **US East (N. Virginia)**
- __3.2.__ Haga click en **Create**

### 4. Cree una politica IAM de acceso al bucket S3 creado anteriormente
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __4.1.__ En una nueva pestaña o ventana del navegador, abra la consola de AWS, en el servicio [Amazon IAM](https://console.aws.amazon.com/iam/home)
- __4.2.__ Haga click en **Policies** en el menu lateral y después en **Create policy**
- __4.3.__ Haga click en **Choose a service**, filtre por **S3** haga click en **S3**
- __4.4.__ Seleccione **All S3 actions (s3:*)**
- __4.5.__ Haga click **Resources** y despues en **Add ARN** del item **bucket**
- __4.6.__ En **Bucket name** llene con el nombre del bucket creado anteriormente (**crypto-&lt;su_userid&gt;**) y haga click en **Add**
- __4.7.__ Haga click en **Add ARN** del item **object**
- __4.8.__ En **Bucket name** llene con el nombre del bucket creado anteriormente (**crypto-&lt;su_userid&gt;**)
- __4.9.__ En **Object name** seleccione **Any** y haga click en **Add**
- __4.10.__ Haga click en **Review policy**
- __4.11.__ En **Name** llene con el nombre **S3CryptoBucket** y después en **Create policy**

### 5. Cree un role IAM para instancias EC2 gestionadas por el SSM Systems Manager
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __5.1.__ Aún en la consola del servicio [Amazon IAM](https://console.aws.amazon.com/iam/home)
- __5.2.__ Haga click en **Roles** en el menu lateral y después en **Create role**
- __5.3.__ Haga click en **EC2** y después en **Next: Permissions**
- __5.4.__ Filtre las politicas por **ssm** y elija la politica **AmazonEC2RoleforSSM**
- __5.5.__ Filtre de nuevo por **cloudhsm** y elija la politica **AWSCloudHSMFullAccess**
- __5.6.__ Filtre de nuevo por **s3** y elija la politica creada en la etapa anterior **S3CryptoBucket**
- __5.7.__ Haga click en **Next: Tags** y después en **Next: Review**
- __5.8.__ En **Role name** llene con el nombre **EC2-SSM+S3Crypto** y haga click en **Create role**

### 6. Cree un CloudHSM en el cluster
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __6.1.__ Vuelva a la pestaña del servicio AWS CloudHSM
- __6.2.__ Cuando el estado del cluster figure **Uninitialized**, haga click en **Initialize**
- __6.3.__ Cree un nuvo CloudHSM en el cluster, seleccionando la subnet en la AZ **us-east-1a** y haciendo click en **Create** (Si recibes el mensaje "CreateHsm request failed", elija la siguiente AZ - b, c...)
- __6.4.__ El processo de creación de un nuevo CloudHSM en el cluster puede llevar cerca de 5 minutos. Siga a la proxima etapa mientras el proceso sigue.

### 7. Cree una instancia EC2 para gestión del CloudHSM
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __7.1.__ Abra la consola AWS el servicio [Amazon EC2](https://console.aws.amazon.com/vpc/home).
- __7.2.__ Haga click en **Running Instances** y después en **Launch Instance**.
- __7.3.__ Seleccióne la versión de Sistema Operativo **Amazon Linux 2 AMI (64-bit x86)** haciendo click en **Select** al lado de esta versión de AMI.
- __7.4.__ En el tipo de instancia, dele seleccionado **t2.micro** y haga click en **Next: Configure Instance Details**
- __7.5.__ En **Network** elija la VPC con nombre **cloudhsm-vpc**
- __7.6.__ En **Subnet** elija la subnet **private-subnet-us-east-1b** (o preferencialmente la de la región donde fue creado el CloudHSM)
- __7.7.__ En **IAM role** elija el perfil creado anteriormente **EC2-SSM+S3Crypto** y haga click en **Next: Add Storage**
- __7.8.__ Haga click en **Next Add Tags** y después en **Add Tag**
- __7.9.__ En el campo **Key** llene con **Name** y en el campo **Value** llene con el nombre **cloudhsm-mgmt**
- __7.10.__ Haga click en **Next: Configure Security Group**
- __7.11.__ Deje seleccionado **Create a new security group**
- __7.12.__ En **Security group name** llene con el nombre **cloudhsm-mgmt-sg**
- __7.13.__ En **Description** llene con **CloudHSM Management Group**
- __7.14.__ Borre la configuración de SSH haciendo click en el **X** al fin de la linea de configuración
- __7.15.__ Haga click en **Review and Launch** y después en **Launch**
- __7.16.__ Remplaze la opción "Choose an existing key pair" por **Proceed without a key pair**
- __7.17.__ Seleccione **I acknowledge that I will not (...)** y haga click en **Launch Instance**

### 8. Descargue el CSR y lo deje disponible para firmar en el bucket S3
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __8.1.__ Vuelva a la pestaña o ventana del navegador en el servicio [AWS CloudHSM](https://console.aws.amazon.com/cloudhsm/home)
- __8.2.__ Descargue el archivo para firmar el certificado del cluster, haciendo click en **Cluster CSR** y grabelo en tu laptop
- __8.2.__ Vuelva a la pestaña o ventana del navegador en el servicio [S3](https://console.aws.amazon.com/s3/home)
- __8.2.__ Haga click en el bucket creado anteriormente **crypto-&lt;su_userid&gt;**
- __8.2.__ Haga click en **Upload** y despues en **Add files**
- __8.2.__ Elija el archivo con el CSR descargado anteriormente (archivo con un nombre parecido a **cluster-5xr767yr3wr_ClusterCsr.csr**)
- __8.2.__ Haga click en **Upload**

### 9. Use la instancia de gestión para firmar el CSR
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __9.1.__ Abra una nueva pestaña o ventana del navegador en el servicio [AWS Systems Manager](https://console.aws.amazon.com/ssm/home)
- __9.2.__ En el menu lateral, haga click **Session Manager**
- __9.3.__ Haga click en **Start session**
- __9.4.__ Seleccione la instancia con nombre **cloudhsm-mgmt** y haga click en **Start session**
- __9.5.__ Una nueva pestaña se abrirá, haga click en la ventana y ejecute **cd &lt;enter&gt;**
- __9.6.__ Ejecute los comandos:
```
sudo yum update -y
wget https://s3.amazonaws.com/cloudhsmv2-software/CloudHsmClient/EL6/cloudhsm-client-latest.el6.x86_64.rpm
sudo yum install ./cloudhsm-client-latest.el6.x86_64.rpm -y 
sudo yum install jq -y
aws configure
```

- __9.7.__ No cambie las configuraciones estándar **AWS Access Key ID** y **AWS Secret Access Key**
- __9.8.__ En la opción **Default region name** llene con **us-east-1**
- __9.9.__ No cambie las configuracion estándar **Default output format**
- __9.10.__ Ejecute los comandos:
```
aws s3 cp s3://crypto-&lt;su_userid&gt;/cluster-&lt;id_del_cluster&gt;_ClusterCsr.csr .
```
- __9.11.__ Crea un par de llaves y un certificado root con los comandos:
```
openssl genrsa -aes256 -out customerCA.key 2048
```

- __9.12.__ Elija un pass phrase para las llaves
```
openssl req -new -x509 -days 3652 -key customerCA.key -out customerCA.crt
```

- __9.12.__ Use el pass phrase creado anteriormente y elija las opciones que quiera para las preguntas que siguen
- __9.13.__ Firme el CSR con el comando:
```
openssl x509 -req -days 3652 -in cluster-&lt;id_del_cluster&gt;_ClusterCsr.csr \
-CA customerCA.crt \
-CAkey customerCA.key \
-CAcreateserial \
-out CustomerHsmCertificate.crt
```
- __9.14.__ Use el pass phrase creado anteriormente
- __9.15.__ Inicialice el cluster con el comando:
```
aws cloudhsmv2 initialize-cluster --cluster-id cluster-&lt;cluster_id&gt; \
--signed-cert file://CustomerHsmCertificate.crt \
--trust-anchor file://customerCA.crt
```

- __9.16.__ Vuelva a la pestaña o ventana del navegador en el servicio [AWS CloudHSM](https://console.aws.amazon.com/cloudhsm/home)
- __9.17.__ Haga click en el botón de refrescar y vea que el status del cluster esta en **Initialize in progress**
- __9.19.__ El processo de inicialización del cluster puede llevar cerca de 5 minutos. Siga a la proxima etapa mientras el proceso sigue.


### 10. Use el cliente de CloudHSM para conectar al cluster
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __10.1.__ Vuelva a la pestaña o ventana del navegador del [AWS Systems Manager](https://console.aws.amazon.com/ssm/home) en la terminal de la instancia
- __10.1.__ Configure el cliente con el comando:
```
sudo cp customerCA.crt /opt/cloudhsm/etc/customerCA.crt
```

- __10.1.__ 
```
echo '#!/bin/sh' >> cloudhsm_client.sh
echo '/opt/cloudhsm/bin/cloudhsm_client /opt/cloudhsm/etc/cloudhsm_client.cfg' >> cloudhsm_client.sh
chmod +x cloudhsm_client.sh
echo '#!/bin/sh' >> cloudhsm_mgmt_util.sh
echo '/opt/cloudhsm/bin/cloudhsm_mgmt_util /opt/cloudhsm/etc/cloudhsm_mgmt_util.cfg' >> cloudhsm_mgmt_util.sh
chmod +x cloudhsm_mgmt_util.sh
```

- __10.1.__ Vea la dirección IP del HSM en el cluster con el comando:
```
aws cloudhsmv2 describe-clusters | jq '.Clusters[].Hsms[].EniIp'
```

- __10.1.__ Use la dirección IP del HSM para configurar el agente con el comando
```
sudo /opt/cloudhsm/bin/configure -a &lt;IP address&gt;
```

- __10.1.__ Lanze el agente de gestión del CloudHSM con el comando:
```
./cloudhsm_mgmt_util.sh
```

- __10.1.__ En ese momento no vas a poder conectar pues faltan las reglas de firewall (Security Groups) para acceder al CloudHSM. Lo haremos en el paso siguiente

### 11. Configure las reglas de firewall para conectar al CloudHSM
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __11.1.__ Vuelva a la pestaña o ventana del navegador en el servicio [AWS EC2](https://console.aws.amazon.com/ec2/home)
- __11.1.__ En el menu lateral haga click en **Instances**
- __11.1.__ Seleccione la instancia con nombre **cloudhsm-mgmt**
- __11.1.__ Haga click en **Actions** y debajo de **Networking** haga click en **Change Security Groups**
- __11.1.__ Seleccione un Security Group adicional con "Group Name" parecido a **cloudhsm-cluster-5xr767yr3wr-sg** (generado automaticamente por el Cluster)
- __11.1.__ Haga click en **Assign Security Groups**
- __11.1.__ Vuelva a la pestaña o ventana del navegador del [AWS Systems Manager](https://console.aws.amazon.com/ssm/home) en la terminal de la instancia
- __11.1.__ Intente de nuevo conectar al HSM con el comando:
```
./cloudhsm_mgmt_util.sh
```

- __11.1.__ Vea los usuarios estándar en el sistema con el comando:
```
listUsers
```

- __11.1.__ 
```
loginHSM PRECO admin password
```

- __11.1.__ 
```
changePswd PRECO admin <NewPassword>
```

- __11.1.__ 
```
listUsers
```

- __11.1.__ 
```
quit
```

