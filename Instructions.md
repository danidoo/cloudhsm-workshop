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
- __2.4.__ La creación del cluster puede llevar hasta 10 minutos.

### 3. Cree un bucket S3 para almacenamiento de objetos
<details open>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __3.1.__ En una nueva pestaña o ventana del navegador, abra la consola de AWS en el servicio [Amazon S3](https://console.aws.amazon.com/s3/home)
- __3.2.__ Haga click en **Create bucket**
- __3.2.__ En **Bucket name** llene con el nombre **crypto-&lt;su_userid&gt;** (los buckets tinen que tener nombres únicos globalmente)
- __3.2.__ Elija la región **US East (N. Virginia)**
- __3.2.__ Haga click en **Create**

### 4. Cree una politica IAM de acceso al bucket S3
<details closed>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __4.1.__ En una nueva pestaña o ventana del navegador, abra la consola de AWS, en el servicio [Amazon IAM](https://console.aws.amazon.com/iam/home)
- __4.2.__ Haga click en **Policies** en el menu lateral y después en **Create policy**
- __3.2.__ Haga click en **Choose a service**, filtre por **S3** haga click en **S3**
- __3.2.__ Seleccione **All S3 actions (s3:*)**
- __3.2.__ Haga click **Resources** y despues en **Add ARN** del item **bucket**
- __3.2.__ En **Bucket name** llene con el nombre del bucket creado anteriormente (**crypto-&lt;su_userid&gt;**)
- __3.2.__ 
- __3.2.__ 

### 5. Cree un role IAM para instancias EC2 gestionadas por el SSM Systems Manager
<details closed>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __4.1.__ En la consola del servicio [Amazon IAM](https://console.aws.amazon.com/iam/home)
- __4.2.__ Haga click en **Roles** en el menu lateral y después en **Create role**
- __4.3.__ Haga click en **EC2** y después en **Next: Permissions**
- __4.4.__ Filtre las politicas por **ssm** y elija la politica **AmazonEC2RoleforSSM**
- __4.5.__ Filtre de nuevo por **s3** y elija la politica **AmazonS3FullAccess**
- __4.6.__ Haga click en **Next: Tags** y después en **Next: Review**
- __4.7.__ En **Role name** llene con el nombre **EC2-SSM+S3** y haga click en **Create role**


### 5. Cree un CloudHSM en el cluster
<details closed>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __5.1.__ Vuelva a la pestaña del servicio AWS CloudHSM
- __5.2.__ Cuando el estado del cluster figure **Uninitialized**, haga click en **Initialize**
- __5.3.__ Cree un nuvo CloudHSM en el cluster, seleccionando la subnet en la AZ **us-east-1a** y haciendo click en **Create** (Si recibes el mensaje "CreateHsm request failed", elija la siguiente AZ - b, c...)
- __5.4.__ El processo de creación de un nuevo CloudHSM en el cluster puede llevar cerca de 5 minutos. Haga refresh del estado a cada ~1 minuto

### 6. Cree una instancia EC2 para gestión del CloudHSM
<details closed>
<summary><strong>(abrir detalles)</strong></summary>
<br />

- __6.1.__ Abra la consola AWS el servicio [Amazon EC2](https://console.aws.amazon.com/vpc/home).
- __6.2.__ Haga click en **Running Instances** y después en **Launch Instance**.
- __6.3.__ Seleccióne la versión de Sistema Operativo **Amazon Linux 2 AMI (64-bit x86)** haciendo click en **Select** al lado de esta versión de AMI.
- __6.4.__ En el tipo de instancia, dele seleccionado **t2.micro** y haga click en **Next: Configure Instance Details**
- __6.5.__ En **Network** elija la VPC con nombre **cloudhsm-vpc**
- __6.6.__ En **IAM role** elija 
- __6.7.__ 



- __2.8.__ Descargue el archivo CSR del cluster, haciendo click en **Cluster CSR**
- __2.9.__ 
- __2.10.__ 
