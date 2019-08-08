# AWS Key Management Service

### 1. Cree un perfil para una instancia EC2
<details open>
<summary>(detalles)</summary>

- __1.1.__ En una nueva pestaña o ventana del navegador, abra la consola de AWS en el servicio  [AWS Identity and Access Management](https://console.aws.amazon.com/iam/home)
- __1.2.__ En el menu lateral, haga click en **Roles** y después en **Create role**
- __1.3.__ Deje las selecciones por defecto de entidades confiadas como **AWS service**, haga click en el servicio **EC2** y después en **Next: Permissions**
- __1.4.__ Filtre las politicas por **SSM**, seleccione la política **AmazonEC2RoleforSSM** y haga click en **Next: Tags**
- __1.5.__ Haga click en **Next: Review** (Tags no van a ser necesarios en ese momento)
- __1.6.__ En el campo **Role name** llene con el nombre **EC2-Role** y haga click en **Create role**
- __1.7.__ En el topo de la pantalla aparecerá el mensaje "The role EC2-SSM-S3 has been created", haga click en el enlace **EC2-Role**
- __1.8.__ Tome nota del ARN (Amazon Resource Name) en **Role ARN** (formato _arn:aws:iam::XXXXXXXXXXXX:role/EC2-SSM-S3_)

    Entre el **ARN** del perfil: <input type="text" id="ec2-role-arn" onkeyup="copyval(this);"/><br>
<br>
</details>

### 2. Cree un bucket S3
<details open>
<summary>(detalles)</summary>

- __2.1.__ Vuelva a la pestaña o ventana del navegador del [AWS S3](https://console.aws.amazon.com/s3/home)
- __2.2.__ Haga click en **+ Create bucket**
- __2.3.__ En el campo **Bucket name** llene el texto **s3encrypted-&lt;su user id&gt;**
- __2.4.__ Verifique que em **Region** está selecionado **US East (N. Virginia) e clique em **Create**

    Entre el **Nombre final** del bucket: <input type="text" id="bucket-name" onkeyup="copyval(this);"/><br>
<br>
</details>

### 3. Cree una instancia EC2
<details open>
<summary>(detalles)</summary>

- __3.1.__ Vuelva a la pestaña o ventana del navegador del [Amazon EC2](https://console.aws.amazon.com/ec2/home)
- __3.2.__ Haga click en **Launch Instance**
- __3.3.__ Haga click en **Select** en la linea que corresponde a la AMI **Amazon Linux 2 AMI (HVM), SSD Volume Type**
- __3.4.__ Deje seleccionado el tipo de instancia **t2.micro** y haga click en **Next: Configure Instance Details**
- __3.5.__ Mantenga las configuraciones por defecto y en el punto **IAM role** seleccione el perfil **EC2-Role** creado anteriormente
- __3.6.__ Haga click en **Review and Launch** y en la pantalla siguiente en **Launch**
- __3.7.__ Remplaze la opción "Choose an existing key pair" por **Proceed without a key pair**
- __3.8.__ Seleccione **I acknowledge that I will not (...)** y haga click en **Launch Instance**
</details>

### 4. Pruebe el acceso de la instancia al bucket S3
<details open>
<summary>(detalles)</summary>

- __4.1.__ Abra una nueva pestaña o ventana del navegador en el servicio [AWS Systems Manager](https://console.aws.amazon.com/systems-manager/home)
- __4.2.__ En el menu lateral, haga click **Session Manager**
- __4.3.__ Haga click en **Start session**
- __4.4.__ Seleccione la instancia creada anteriormente y haga click en **Start session**
- __4.5.__ Una nueva pestaña se abrirá, haga click en la ventana y ejecute **cd &lt;enter&gt;**
- __4.6.__ Para listar los buckets S3, ejecute el comando<br>

    aws s3 ls

- __4.7.__ El acceso sera denegado pues la instancia no tiene permiso de acceso al servicio S3
- __4.8.__ Mantenga la pestaña o ventana del navegador abierta pues se la usaremos en otras etapas
</details>

### 5. Agregue permissos de acceso al S3 a la instancia
<details open>
<summary>(detalles)</summary>

- __5.1.__ Vuelva a la pestaña o ventana del navegador del [Amazon IAM](https://console.aws.amazon.com/iam/home)
- __5.2.__ HEn el menu lateral haga click en **Roles** y filtre por **ec2**
- __5.3.__ Haga click en el perfil **EC2-Role** creado anteriormente
- __5.4.__ Haga click en **Attach policies**
- __5.5.__ Filtre las politicas por **S3**, seleccione la politica **AmazonS3FullAccess** y haga click en **Attach policy**
- __5.6.__ Vuelva a la pestaña o ventana del navegador del [AWS Systems Manager](https://console.aws.amazon.com/ssm/home) en la terminal de la instancia
- __5.7.__ Ejecute nuevamente el comando para listar los buckets S3

    aws s3 ls

- __5.7.__ Ahora con los permisos establecidos, el acceso debe ser permitido y una lista de buckets sera mostrada
</details>

### 6. Cree una Customer Master Key
<details open>
<summary>(detalles)</summary>

- __6.1.__ Abra una nueva pestaña o ventana del navegador en el servicio [AWS Key Management Service](https://console.aws.amazon.com/kms/home)
- __6.2.__ Haga click en **Create Key**
- __6.3.__ En el campo **Alias** llene con el texto **KMS-CMK**
- __6.4.__ En **Advanced options** deje seleccionado **KMS** como origen del material de llaves y haga click en **Next**
- __6.5.__ Haga click en**Next**
- __6.6.__ En las opciones de elección de administradores, deje los valores estándar y haga click en **Next**
- __6.7.__ En las opciones de administradores de llaves, filtre por **user1**, seleccione el usuario **user1** y haga click en **Next**
- __6.8.__ Haga click en **Finish**

    Entre el **Key Id** de la llave creada: <input type="text" id="cmk-key-id" onkeyup="copyval(this);"/><br>
<br>
</details>

### 7. Configure el bucket para encripción, agregue un objeto y pruebe el acceso
<details open>
<summary>(detalles)</summary>

- __7.1.__ Vuelva a la pestaña o ventana del navegador del [AWS S3](https://console.aws.amazon.com/s3/home)
- __7.2.__ Haga click en el numbre del bucket creado anteriormente <span class="bucket-name">&lt;nombre del bucket&gt;</span>
- __7.3.__ Haga click en la pestaña **Properties** y después en **Default encryption**
- __7.4.__ Seleccione encripción **KMS-CMK**, elija la llave **KMS-CMK** y haga click en **Save**
- __7.5.__ Cree o elija un archivo no confidencial o personal en su computadora que no sea muy grande
- __7.6.__ Vuelva a la pestaña **Overview** y haga click en **Upload**
- __7.7.__ Haga click en **Add files** y seleccione el archivo; o arrastre el archivo a la pantalla
- __7.8.__ Haga click en **Upload**

    Entre el **nombre del archivo**: <input type="text" id="file-name" onkeyup="copyval(this);"/><br>
<br>

- __7.9.__ Vuelva a la pestaña o ventana del navegador del [AWS Systems Manager](https://console.aws.amazon.com/ssm/home) en la terminal de la instancia
- __7.10.__ Intente descargar el archivo (no será posible pues la instancia no tiene permiso a la llave de encripción)

    aws s3 cp s3://<span class="bucket-name">&lt;nombre del bucket&gt;</span>/<span class="file-name">&lt;nombre del archivo&gt;</span> .
</details>

### 8. Cambie los permisos de acceso a la llave y intente de nuevo
<details open>
<summary>(detalles)</summary>

- __8.1.__ Vuelva a la pestaña o ventana del navegador del [AWS Key Management Service](https://console.aws.amazon.com/kms/home)
- __8.2.__ En el menu lateral, haga click en **Customer managed keys**
- __8.3.__ Seleccione el alias de la llave generada anteriormente **KMS-CMK**
- __8.4.__ En la sección **Key users** haga click en **Add**
- __8.5.__ Filtre por **ec2**, seleccione **EC2-Role** y haga click en **Add**
- __8.6.__ Vuelva a la pestaña o ventana del navegador del [AWS Systems Manager](https://console.aws.amazon.com/ssm/home) en la terminal de la instancia

    aws s3 cp s3://<span class="bucket-name">&lt;nombre del bucket&gt;</span>/<span class="file-name">&lt;nombre del archivo&gt;</span> .

- __8.7.__ En esta vez el acceso al archivo sera permitido pues el perfil de la instancia tiene permisión para uso de la llave
</details>

### 9. Configure el rotacionamiento automatico de la llave
<details open>
<summary>(detalles)</summary>

- __9.1.__ Vuelva a la pestaña o ventana del navegador del [AWS Key Management Service](https://console.aws.amazon.com/kms/home)
- __9.2.__ En el menu lateral, haga click en **Customer managed keys**
- __9.3.__ Seleccione el nombre de la llave generada anteriormente **KMS-CMK**
- __9.4.__ En la pestaña **Key policy** haga click en **Edit**
- __9.5.__ Haga click en la pestaña **Key rotation** 
- __9.6.__ Seleccione la opción **Automatically rotate this CMK every year.**
- __9.7.__ Haga click en **Save**

### 10. Agregue permissos de acceso al AWS KMS a la instancia
<details open>
<summary>(detalles)</summary>

- __10.1.__ Vuelva a la pestaña o ventana del navegador del [Amazon IAM](https://console.aws.amazon.com/iam/home)
- __10.2.__ HEn el menu lateral haga click en **Roles** y filtre por **ec2**
- __10.3.__ Haga click en el perfil **EC2-Role** creado anteriormente
- __10.4.__ Haga click en **Attach policies**
- __10.5.__ Filtre las politicas por **S3**, seleccione la politica **AmazonS3FullAccess** y haga click en **Attach policy**
- __10.6.__ Vuelva a la pestaña o ventana del navegador del [AWS Systems Manager](https://console.aws.amazon.com/ssm/home) en la terminal de la instancia
- __10.7.__ Ejecute nuevamente el comando para listar los buckets S3

    aws s3 ls

- __10.8.__ Ahora con los permisos establecidos, el acceso debe ser permitido y una lista de buckets sera mostrada
</details>

### 11. Haga encripción del lado del cliente usando llaves del KMS
<details open>
<summary>(detalles)</summary>

- __11.1.__ Vuelva a la pestaña o ventana del navegador del [AWS Systems Manager](https://console.aws.amazon.com/ssm/home) en la terminal de la instancia
- __11.2.__ Instale las herramientas de encripcion de AWS con el comando:

    sudo install python3 python-pip3 -y
    sudo pip3 install aws-encryption-sdk-cli

- __11.3.__ Descargue un archivo para cifrar con el comando:

    cd /tmp
    wget https://d1.awsstatic.com/whitepapers/KMS-Cryptographic-Details.pdf

- __11.1.__ Verifique el formato del archivo usando el comando:

    file KMS-Cryptographic-Details.pdf

- __11.1.__ Usando la herramienta de encripción de AWS, cifre el archivo usando la llave creada anteriormente:

    aws-encryption-cli --encrypt --input KMS-Cryptographic-Details.pdf \<br>
                       --master-keys key=<span class="cmk-key-id">&lt;cmk key id&gt;</span> \<br>
                       --encryption-context purpose=test \<br>
                       --metadata-output ~/metadata \<br>
                       --output .<br>

- __11.1.__ Verifique el formato del archivo cifrado usando el comando:

    file KMS-Cryptographic-Details.pdf.encrypted

- __11.1.__ Decifre el archivo usando el comando:

    aws-encryption-cli --decrypt --input KMS-Cryptographic-Details.pdf.encrypted \<br>
                       --encryption-context purpose=test \<br>
                       --metadata-output ~/metadata \<br>
                       --output .<br>

- __11.1.__ Verifique el formato del archivo cifrado usando el comando:

    file KMS-Cryptographic-Details.pdf.encrypted

- __11.1.__ Verifique si los archivos son identicos con el comando:

    sha256sum KMS-Cryptographic-Details.pdf KMS-Cryptographic-Details.pdf.encrypted.decrypted

