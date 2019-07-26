## AWS CloudHSM
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

</details>

### 1. Cree un VPC con subnets para hostear sus recursos que usan servicios del CloudHSM
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __1.1.__ En un navegador, abra el sitio <CFN_Github>
- __1.2.__ Haga click en **Raw** e descargue el archivo a su maquina con el nombre cloudhsm-landing-zone.json.
- __1.3.__ Abra la Consola de AWS, en el servicio CloudFormation.
- __1.4.__ Implemente el ambiente de inicio usando el servicio CloudFormation con el archivo descargado.
- __1.5.__ Tome nota del VPC Id que fue creado, y de las 6 subnets

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
- __5.5.__ Filtre de nuevo por **s3** y elija la politica creada en la etapa anterior **S3CryptoBucket**
- __5.6.__ Haga click en **Next: Tags** y después en **Next: Review**
- __5.7.__ En **Role name** llene con el nombre **EC2-SSM+S3Crypto** y haga click en **Create role**

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

### 8. Siga con la inicialización del cluster
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __8.1.__ Vuelva a la pestaña o ventana del navegador en el servicio [AWS CloudHSM](https://console.aws.amazon.com/cloudhsm/home)
- __8.2.__ Descargue el archivo para firmar el certificado del cluster, haciendo click en **Cluster CSR** y grabelo en tu laptop
- __8.3.__ Abra una nueva pestaña o ventana del navegador en el servicio [AWS Systems Manager](https://console.aws.amazon.com/ssm/home)
- __8.3.__ En el menu lateral, haga click **Session Manager**
- __8.3.__ Haga click en **Start session**
- __8.3.__  
- __8.3.__  
- __8.3.__  
- __8.3.__  
- __8.3.__  
- __8.3.__  
- __8.3.__  
- __8.3.__  
