## Inicialización del Cluster
En este laboratório se hará la inicialización de un cluster de CloudHSM.

### 1. Cree una VPC
<details open>
<summary><strong>(detalles)</strong></summary>
<br />
Si la infraestructura básica fue creada en el modulo anterior, podrás saltar este paso.

- __1.1.__ En un navegador, abra la URL

[https://github.com/danidoo/danidoo.github.io/blob/master/cloudhsm-workshop/CFN-CloudHSMWorkshop.yml](https://github.com/danidoo/danidoo.github.io/blob/master/cloudhsm-workshop/CFN-CloudHSMWorkshop.yml)

- __1.2.__ Haga click en **Raw** e descargue el archivo a su maquina con el nombre cloudhsm-landing-zone.json.
- __1.3.__ Abra la Consola de AWS, en el servicio [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/home).
- __1.4.__ Verifique que estas con la consola en la región de "N. Virginia" (esa información esta en la parte superior derecha de la pantalla)
- __1.5.__ Haga click en **Create stack**
- __1.6.__ Seleccione el "Template source" como **Upload a template file**
- __1.7.__ Haga click en **Choose a file** y seleccione el archivo descargado anteriormente
- __1.8.__ Haga click en **Next**
- __1.9.__ En el campo **Stack name** llene con el nombre **CloudHSM-Stack** y haga click en **Next**
- __1.10.__ Haga click en **Next** y despues en **Create stack**
</details>

### 2. Cree un cluster
<details open>
<summary><strong>(detalles)</strong></summary>
<br />

- __2.1.__ En una nueva pestaña o ventana del navegador, abra la consola de AWS en el servicio [AWS CloudHSM](https://console.aws.amazon.com/cloudhsm/home) en la región de N. Virginia
- __2.2.__ Haga click en **Create cluster**, y usando los Ids del VPC y las subnets, elija los parametros adecuados
- __2.3.__ Deje la opción **Create new cluster** seleccionada y haga click en **Next: Review** y después en **Create cluster**
- __2.4.__ La creación del cluster puede llevar hasta 10 minutos. Siga con la proxima etapa mientras el proceso sigue.
</details>

### 3. Cree un bucket S3 para almacenamiento de objetos
<details open>
<summary><strong>(detalles)</strong></summary>
<br />

Entre su user id o un valor aleatorio para generar objetos unicos <input type="text" id="user-id" onkeyup="copyval(this);"/><br>
<br>

- __3.1.__ En una nueva pestaña o ventana del navegador, abra la consola de AWS en el servicio [Amazon S3](https://console.aws.amazon.com/s3/home)
- __3.2.__ Haga click en **Create bucket**
- __3.2.__ En **Bucket name** llene con el nombre **crypto-<span class="user-id">&lt;su user id&gt;</span>** (los buckets tinen que tener nombres únicos globalmente)
- __3.2.__ Elija la región **US East (N. Virginia)**
- __3.2.__ Haga click en **Create**
</details>

### 4. Cree un bucket S3 para almacenamiento de sessiones
<details open>
<summary><strong>(detalles)</strong></summary>
<br />

- __4.2.__ Haga click en **Create bucket**
- __4.2.__ En **Bucket name** llene con el nombre **session-logs-<span class="user-id">&lt;su user id&gt;</span>** (los buckets tinen que tener nombres únicos globalmente)
- __4.2.__ Elija la región **US East (N. Virginia)**
- __4.2.__ Haga click en **Create**
</details>

### 5. Cree una politica IAM de acceso al bucket S3 creado anteriormente
<details open>
<summary><strong>(detalles)</strong></summary>
<br />

- __5.1.__ En una nueva pestaña o ventana del navegador, abra la consola de AWS, en el servicio [Amazon IAM](https://console.aws.amazon.com/iam/home)
- __5.2.__ Haga click en **Policies** en el menu lateral y después en **Create policy**
- __5.3.__ Haga click en **Choose a service**, filtre por **S3** haga click en **S3**
- __5.4.__ Seleccione **All S3 actions (s3:*)**
- __5.5.__ Haga click **Resources** y despues en **Add ARN** del item **bucket**
- __5.6.__ En **Bucket name** llene con el nombre del bucket creado anteriormente (**crypto-<span class="user-id">&lt;su user id&gt;</span>**) y haga click en **Add**
- __5.7.__ Haga click en **Add ARN** del item **object**
- __5.8.__ En **Bucket name** llene con el nombre del bucket creado anteriormente (**crypto-<span class="user-id">&lt;su user id&gt;</span>**)
- __5.9.__ En **Object name** seleccione **Any** y haga click en **Add**
- __5.10.__ Haga click en **Review policy**
- __5.11.__ En **Name** llene con el nombre **S3CryptoBucket** y después en **Create policy**
</details>

### 6. Cree un role IAM para instancias EC2 gestionadas por el SSM Systems Manager
<details open>
<summary><strong>(detalles)</strong></summary>
<br />

- __6.1.__ Aún en la consola del servicio [Amazon IAM](https://console.aws.amazon.com/iam/home)
- __6.2.__ Haga click en **Roles** en el menu lateral y después en **Create role**
- __6.3.__ Haga click en **EC2** y después en **Next: Permissions**
- __6.4.__ Filtre las politicas por **ssm** y elija la politica **AmazonEC2RoleforSSM**
- __6.5.__ Filtre de nuevo por **cloudhsm** y elija la politica **AWSCloudHSMFullAccess**
- __6.6.__ Filtre de nuevo por **s3** y elija la politica creada en la etapa anterior **S3CryptoBucket**
- __6.7.__ Haga click en **Next: Tags** y después en **Next: Review**
- __6.8.__ En **Role name** llene con el nombre **EC2-SSM+S3Crypto** y haga click en **Create role**
</details>

### 7. Cree un CloudHSM en el cluster
<details open>
<summary><strong>(detalles)</strong></summary>
<br />

- __7.1.__ Vuelva a la pestaña del servicio [AWS CloudHSM](https://console.aws.amazon.com/cloudhsm/home)
- __7.2.__ Cuando el estado del cluster figure **Uninitialized**, haga click en **Initialize**

    Entre el Id del cluster (en el formato **cluster-xxxxxxxxxxx**) <input type="text" id="cluster-id" onkeyup="copyval(this);"/><br>
<br>

- __7.3.__ Cree un nuvo CloudHSM en el cluster, seleccionando la subnet en la AZ **us-east-1a** y haciendo click en **Create** (Si recibes el mensaje "CreateHsm request failed", elija la siguiente AZ - b, c...)
- __7.4.__ El processo de creación de un nuevo CloudHSM en el cluster puede llevar cerca de 5 minutos. Siga a la proxima etapa mientras el proceso sigue.
</details>

### 8. Cree una instancia EC2 para gestión del CloudHSM
<details open>
<summary><strong>(detalles)</strong></summary>
<br />

- __8.1.__ Abra la consola AWS el servicio [Amazon EC2](https://console.aws.amazon.com/vpc/home).
- __8.2.__ Haga click en **Running Instances** y después en **Launch Instance**.
- __8.3.__ Seleccióne la versión de Sistema Operativo **Amazon Linux 2 AMI (64-bit x86)** haciendo click en **Select** al lado de esta versión de AMI.
- __8.4.__ En el tipo de instancia, dele seleccionado **t2.micro** y haga click en **Next: Configure Instance Details**
- __8.5.__ En **Network** elija la VPC con nombre **cloudhsm-vpc**
- __8.6.__ En **Subnet** elija la subnet **private-subnet-us-east-1b** (o preferencialmente la de la región donde fue creado el CloudHSM)
- __8.7.__ En **IAM role** elija el perfil creado anteriormente **EC2-SSM+S3Crypto** y haga click en **Next: Add Storage**
- __8.8.__ Haga click en **Next Add Tags** y después en **Add Tag**
- __8.9.__ En el campo **Key** llene con **Name** y en el campo **Value** llene con el nombre **cloudhsm-mgmt**
- __8.10.__ Haga click en **Next: Configure Security Group**
- __8.11.__ Deje seleccionado **Create a new security group**
- __8.12.__ En **Security group name** llene con el nombre **cloudhsm-mgmt-sg**
- __8.13.__ En **Description** llene con **CloudHSM Management Group**
- __8.14.__ Borre la configuración de SSH haciendo click en el **X** al fin de la linea de configuración
- __8.15.__ Haga click en **Review and Launch** y después en **Launch**
- __8.16.__ Remplaze la opción "Choose an existing key pair" por **Proceed without a key pair**
- __8.17.__ Seleccione **I acknowledge that I will not (...)** y haga click en **Launch Instance**
</details>

### 9. Descargue el CSR y lo deje disponible para firmar en el bucket S3
<details open>
<summary><strong>(detalles)</strong></summary>
<br />

- __9.1.__ Vuelva a la pestaña o ventana del navegador en el servicio [AWS CloudHSM](https://console.aws.amazon.com/cloudhsm/home)
- __9.2.__ Descargue el archivo para firmar el certificado del cluster, haciendo click en **Cluster CSR** y grabelo en tu laptop
- __9.3.__ Vuelva a la pestaña o ventana del navegador en el servicio [S3](https://console.aws.amazon.com/s3/home)
- __9.4.__ Haga click en el bucket creado anteriormente **crypto-<span class="user-id">&lt;su user id&gt;</span>**
- __9.5.__ Haga click en **Upload** y despues en **Add files**
- __9.6.__ Elija el archivo con el CSR descargado anteriormente (archivo con el nombre **<span class="cluster-id">&lt;cluster-xxxxxxxxxxxx&gt;</span>_ClusterCsr.csr**)
- __9.7.__ Haga click en **Upload**
</details>

### 10. Use la instancia de gestión para firmar el CSR
<details open>
<summary><strong>(detalles)</strong></summary>
<br />

- __10.1.__ Abra una nueva pestaña o ventana del navegador en el servicio [AWS Systems Manager](https://console.aws.amazon.com/systems-manager/home)
- __10.2.__ En el menu lateral, haga click **Session Manager**
- __10.3.__ Haga click en **Configure Preferences** o en la pestaña **Preferences**
- __10.4.__ En la sección **Write session output to an Amazon S3 bucket** seleccione **S3 bucket** y desabilite **Encrypt log data**
- __10.5.__ Deje seleccionado **Choose a bucket name from the list** y seleccione el bucket creado anteriormente con nombre **session-logs-<span class="user-id">&lt;su user id&gt;</span>**
- __10.6.__ Haga click en **Save** y despues en la pestaña **Sessions**

### 11. Use la instancia de gestión para firmar el CSR
<details open>
<summary><strong>(detalles)</strong></summary>
<br />

- __11.3.__ Haga click en **Start session**
- __11.4.__ Seleccione la instancia con nombre **cloudhsm-mgmt** y haga click en **Start session**
- __11.5.__ Una nueva pestaña se abrirá, haga click en la ventana y ejecute **cd &lt;enter&gt;**
- __11.6.__ Ejecute los comandos:

    sudo yum update -y<br>
    wget https://s3.amazonaws.com/cloudhsmv2-software/CloudHsmClient/EL7/cloudhsm-client-latest.el7.x86_64.rpm<br>
    sudo yum install ./cloudhsm-client-latest.el7.x86_64.rpm -y<br>
    sudo yum install jq -y<br>
    aws configure<br>

- __11.7.__ No cambie las configuraciones estándar **AWS Access Key ID** y **AWS Secret Access Key**
- __11.8.__ En la opción **Default region name** llene con **us-east-1**
- __11.9.__ No cambie las configuracion estándar **Default output format**
- __11.10.__ Ejecute los comandos:

    aws s3 cp s3://crypto-<span class="user-id">&lt;su_user_id&gt;</span>/<span class="cluster-id">&lt;cluster-xxxxxxxxxxxx&gt;</span>_ClusterCsr.csr .

- __11.11.__ Crea un par de llaves y un certificado root con los comandos:

    openssl genrsa -aes256 -out customerCA.key 2048

- __11.12.__ Elija un pass phrase para las llaves

    openssl req -new -x509 -days 3652 -key customerCA.key -out customerCA.crt

- __11.12.__ Use el pass phrase creado anteriormente y elija las opciones que quiera para las preguntas que siguen
- __11.13.__ Firme el CSR con el comando:


    openssl x509 -req -days 3652 -in <span class="cluster-id">&lt;cluster-xxxxxxxxxxxx&gt;</span>_ClusterCsr.csr \<br>
    -CA customerCA.crt \<br>
    -CAkey customerCA.key \<br>
    -CAcreateserial \<br>
    -out CustomerHsmCertificate.crt<br>

- __11.14.__ Use el pass phrase creado anteriormente
- __11.15.__ Inicialice el cluster con el comando:

    aws cloudhsmv2 initialize-cluster --cluster-id <span class="cluster-id">&lt;cluster-xxxxxxxxxxxx&gt;</span> \<br>
    --signed-cert file://CustomerHsmCertificate.crt \<br>
    --trust-anchor file://customerCA.crt<br>

- __11.16.__ Vuelva a la pestaña o ventana del navegador en el servicio [AWS CloudHSM](https://console.aws.amazon.com/cloudhsm/home)
- __11.17.__ Haga click en el botón de refrescar y vea que el status del cluster esta en **Initialize in progress**
- __11.10.__ El processo de inicialización del cluster puede llevar cerca de 5 minutos. Siga a la proxima etapa mientras el proceso sigue.
</details>

### 12. Use el cliente de CloudHSM para conectar al cluster
<details open>
<summary><strong>(detalles)</strong></summary>
<br />

- __12.1.__ Vuelva a la pestaña o ventana del navegador del [AWS Systems Manager](https://console.aws.amazon.com/systems-manager/home) en la terminal de la instancia
- __12.2.__ Configure el cliente con el comando:

    sudo cp customerCA.crt /opt/cloudhsm/etc/customerCA.crt

- __12.3.__ 

    echo '#!/bin/sh' >> cloudhsm_mgmt_util.sh<br>
    echo '/opt/cloudhsm/bin/cloudhsm_mgmt_util /opt/cloudhsm/etc/cloudhsm_mgmt_util.cfg' >> cloudhsm_mgmt_util.sh<br>
    chmod +x cloudhsm_mgmt_util.sh<br>
    ln -s /opt/cloudhsm/bin/key_mgmt_util key_mgmt_util<br>

- __12.4.__ Vea la dirección IP del HSM en el cluster con el comando:

    aws cloudhsmv2 describe-clusters | jq '.Clusters[].Hsms[].EniIp'

- __12.5.__ Use la dirección IP del HSM para configurar el agente con el comando

    sudo /opt/cloudhsm/bin/configure -a &lt;IP address&gt;

- __12.6.__ Lanze el agente de gestión del CloudHSM con el comando:
 
    ./cloudhsm_mgmt_util.sh

- __12.7.__ En ese momento no vas a poder conectar pues faltan las reglas de firewall (Security Groups) para acceder al CloudHSM. Lo haremos en el paso siguiente
</details>

### 13. Configure las reglas de firewall para conectar al CloudHSM
<details open>
<summary><strong>(detalles)</strong></summary>
<br />

- __13.1.__ Vuelva a la pestaña o ventana del navegador en el servicio [AWS EC2](https://console.aws.amazon.com/ec2/home)
- __13.2.__ En el menu lateral haga click en **Instances**
- __13.3.__ Seleccione la instancia con nombre **cloudhsm-mgmt**
- __13.4.__ Haga click en **Actions** y debajo de **Networking** haga click en **Change Security Groups**
- __13.5.__ Seleccione un Security Group adicional con "Group Name" **cloudhsm-<span class="cluster-id">&lt;cluster-xxxxxxxxxxxx&gt;</span>-sg** (generado automaticamente por el Cluster)
- __13.6.__ Haga click en **Assign Security Groups**
- __13.7.__ Vuelva a la pestaña o ventana del navegador del [AWS Systems Manager](https://console.aws.amazon.com/systems-manager/home) en la terminal de la instancia
- __13.8.__ Intente de nuevo conectar al HSM con el comando:

    ./cloudhsm_mgmt_util.sh

- __13.9.__ Vea los usuarios estándar en el sistema con el comando:

    listUsers

- __13.10.__ Haga login con el usuario y contraseña inicial con el comando:

    loginHSM PRECO admin password

- __13.11.__ Cambie la contraseña con el comando:

    changePswd PRECO admin defaultPassword

- __13.12.__ Cuando recibir el prompt **Do you want to continue(y/n)?** digita **y &lt;enter&gt;**
- __13.13.__ Usa el comando “createUser“ para crear un crypto user (CU).

    createUser CU user1 defaultPassword

- __13.14.__ Vea que el CloudHSM cabia el tipo de usuario del crypto officer y muestra el nuevo usuario **user1**:

    listUsers

- __13.15.__ Termine la sesión en el agente con el comando:

    quit