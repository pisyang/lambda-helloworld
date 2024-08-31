# DEPLOY AWS LAMBDA FUNCTION CI/CD JENKINS

Repositori ini menjabarkan langkah-langkah bagaimana cara untuk men-deploy AWS Lambda Function print 'Hello World!' phyton dengan menggunakan GitHub dan CI/CD automation server Jenkins. Dengan beberapa metode yaitu Jenkins Freestyle Project dan Jenkins Pipeline.  
<br> 

## Alat yang diperlukan pada cara ini:

  1. Windows: Perangkat yang digunakan pada cara ini
  2. Jenkins: Aplikasi server otomatis
  3. AWS Console: Lambda, API Gateway, Credentials
  4. Ngrok: HTTP Tunnel
<br> 

## Menyiapkan AWS Lambda 
### 1. Siapkan AWS Lambda function sebagai serverless untuk deploy file dari GitHub.
<img width="580" alt="Screenshot_1" src="https://github.com/user-attachments/assets/bfaff337-b894-4a15-9e84-b7c71f6f465a">
<img width="580" alt="Screenshot_2" src="https://github.com/user-attachments/assets/458f6ee7-c2ee-479b-a7ae-3e03ac8eccf2"> <br> 

-   **Buka AWS Lambda** sebagai layanan serverless 
-   **Create Function** untuk membuat fungsi baru
-   **author from scratch** untuk membuat fungsi baru tanpa template
-   function name: bebas 
-   runtime: Python 3.8 
-   architecture: x86_64 
-   executing role: use an existing role 
-   exiting role: labrole 
-   lalu **create function** 
<br>

### 2. Maka hasilnya akan tertampil seperti pada contoh gambar berikut.
<img width="580" alt="Screenshot_3" src="https://github.com/user-attachments/assets/ab34432c-ec13-4471-8681-2022036528fd">
<br><br>

### 3. Scroll ke bawah, pada bagian Code isi dengan kode bahasa Python untuk menampikan teks 'Hello World' lalu pilih Deploy.
<img width="580" alt="Screenshot_4" src="https://github.com/user-attachments/assets/e0b6e022-6f61-4e3f-94b9-ab2912c5eed3"> <br>
>```
>import json
>
>def lambda_handler(event, context):
>    # TODO implement
>    return {
>        'statusCode': 200,
>        'body': json.dumps('Hello World!')
>    }
>
>```
> kode ini berfungsi untuk menampilkan teks 'Hello World'
<br>

### 4. Kemudian buat Trigger HTTP API Gateway supaya memudahkan untuk melihat hasilnya.
<img width="580" alt="Screenshot_5" src="https://github.com/user-attachments/assets/b8993ac9-0fc4-4577-b91f-15e2d1c75a75"><br>
- Pilih **Add Trigger**
- Pilih **API Gateway**
- Intent: Create new API
- API type: HTTP API
- Security: Open

### 5. Maka hasilnya akan tertampil seperti pada contoh gambar berikut. Kemudian pergi ke navigasi Configuration, Triggers, dan buka URL pada API endpoint untuk menampilkan teks melalui web HTTP.
<img width="580" alt="Screenshot_6" src="https://github.com/user-attachments/assets/33609d9e-2b70-4c63-a1bc-800f261a1c4b">
<img width="580" alt="Screenshot_7" src="https://github.com/user-attachments/assets/d37a2621-b466-4d9d-af38-95b8f88deb5a">

<br>
<br>

## Menyiapkan GitHub
### 1. Siapkan repositori pada GitHub berisi file dengan nama 'lambda_function.py' yang berfungsi untuk menyimpan kode yang sama dengan kode pada AWS Lambda function di atas.
<img width="580" alt="Screenshot_8" src="https://github.com/user-attachments/assets/8e6bf381-a03f-4dd1-86a9-dd30c748d2e4"><br> 

>```
>import json
>
>def lambda_handler(event, context):
>    # TODO implement
>    return {
>        'statusCode': 200,
>        'body': json.dumps('Hello World!')
>    }
>
>```
> kode ini berfungsi untuk menampilkan teks 'Hello World' yang sama pada Lambda function

<br>

## Konfigurasi Jenkins Freestyle Project
### 1. Instal Jenkins terlebih dahulu kemudian atur AWS Credential yang akan digunakan dengan cara masuk ke **Dashboard, Manage Jenkins, Credentials,** lalu tambahkan **AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, AWS_SESSION_TOKEN** sesuai yang Anda miliki.
<img width="580" alt="Screenshot_24" src="https://github.com/user-attachments/assets/a9b5881f-bb24-499a-aab8-818be83d70fe">  
<br>
<br>

### 2. Berikutnya adalah membuat CI/CD Freestyle project dengan cara mengikuti langkah-langkah di bawah ini.
<img width="580" alt="Screenshot_9" src="https://github.com/user-attachments/assets/0dcd2e15-0883-47b3-a9d4-7c5e8dcf854d">
<img width="580" alt="Screenshot_10" src="https://github.com/user-attachments/assets/c8bb0940-60ee-40f0-bff0-49e90bfc1195">
<img width="580" alt="Screenshot_11" src="https://github.com/user-attachments/assets/19f32822-d5a6-4c11-b290-25fc5abd1788">
<img width="580" alt="Screenshot_12" src="https://github.com/user-attachments/assets/a7ae1c25-509e-43f7-baa5-7aaefd884d6e">
<img width="580" alt="Screenshot_13" src="https://github.com/user-attachments/assets/f6c38a38-56e2-4b66-89af-3031403cb989">
<img width="580" alt="Screenshot_14" src="https://github.com/user-attachments/assets/cd833f5d-2fa1-41dd-868a-734918ac603a"><br>

- Pada bagian **Dashboard**, pilih **New Item**.
- Beri nama bebas lalu pilih **Freestyle project** dan **OK**.
- Pada bagian **Configure, General,** masukkan Description (Opsional).
- Pada bagian **Configure, Source Code Management,** pilih **Git** dan tempel URL repositori GitHub yang akan digunakan, kemudian ubah **Branch** menjadi main.
- Pada bagian **Configure, Build Environment,** pilih **Use secret text or file** dan sesuaikan dengan Credentials yang akan digunakan.
- Pada bagian **Configure, Build Steps,** pilih **Execute shell** dan masukkan perintah berikut.
>```
>powershell Compress-Archive -Update lambda_function.py lambda-arya.zip
>```
>Perintah ini digunakan untuk memperbarui file arsip (.zip) yang ada dengan menambahkan atau menggantikan file di dalam arsip.
>```
>aws --version
>
>aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
>aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
>aws configure set aws_session_token $AWS_SESSION_TOKEN
>aws configure set region us-east-1
>
>aws lambda update-function-code --function-name lambda-arya --zip-file fileb://lambda-arya.zip
>```
>Perintah ini digunakan untuk mengonfigurasi kredensial AWS dan memperbarui kode untuk fungsi AWS Lambda.
- Pastikan konfigurasi yang diatur sudah benar kemudian pilih **Save**.
- Berikutnya pilih **Build Now** untuk membangun hasil konfigurasi dan pastikan ikon indikator yang tertampil ceklis/berhasil.
<br>

### 3. Selanjutnya yaitu lakukan uji percobaan terhadap CI/CD yang sudah dibuat dengan melakukan cara seperti berikut.
<img width="580" alt="Screenshot_15" src="https://github.com/user-attachments/assets/bb42d077-b470-48cc-9277-2255a966b00b">
<img width="580" alt="Screenshot_16" src="https://github.com/user-attachments/assets/d864a060-87c6-48ed-b6a5-30dd40ef7c6e">
<img width="580" alt="Screenshot_17" src="https://github.com/user-attachments/assets/b845fbc7-0a15-409c-a71f-11de1632a5ff"><br>

- Pergi ke file kode di GitHub kemudian edit isi file tersebut, lalu pilih **Commit changes.**
- Kembali ke Jenkins lalu pilih **Build Now** untuk membangun ulang secara dan pastikan ikon indikator yang tertampil ceklis/berhasil.manual.
- Berikutnya pergi ke URL HTTP tadi lalu segarkan halaman dan pastikan perubahan terjadi yang menandakan konfigurasi CI/CD Freestyle Project sudah berhasil.

<br>
<br>

## Konfigurasi Jenkins Pipeline
### 1. Setelah Jenkins sudah diinstal dan AWS Credential sudah diatur, lakukan langkah-langkah di bawah untuk membuat CI/CD Pipeline.
<img width="580" alt="Screenshot_18" src="https://github.com/user-attachments/assets/1cd5407e-da47-4298-bc1e-2d2ed3792f97">
<img width="580" alt="Screenshot_19" src="https://github.com/user-attachments/assets/32cf1305-b24e-4cf8-be00-f39958951349">
<img width="580" alt="Screenshot_20" src="https://github.com/user-attachments/assets/58e970b7-6500-4260-ab41-92a0aa4cb827">
<br>

- Pada bagian **Dashboard**, pilih **New Item**.
- Beri nama bebas lalu pilih **Pipeline** dan **OK**.
- Pada bagian **Configure, General,** masukkan Description (Opsional).
- Pada bagian **Configure, Advanced Project Options,** pilih **Pipeline script** dan masukkan perintah berikut.
>```
>pipeline {
>    agent any
>
>    environment {
>        AWS_REGION = 'us-west-2'
>        LAMBDA_FUNCTION_NAME = 'hello-world-didan'
>        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
>        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY') 
>        AWS_SESSION_TOKEN = credentials('AWS_SESSION_TOKEN')
>    }
>
>    stages {
>        stage('Checkout Source Code') {
>            steps {
>                git branch: 'main', url: 'https://github.com/Didanrama/Task-Lambda-Function-CI-CD.git'
>            }
>        }
>
>        stage('Build and Package') {
>            steps {
>                script {
>                    bat 'powershell -Command "Compress-Archive -Path lambda_function.py -DestinationPath hello-world-didan.zip -Update"'
>                }
>            }
>        }
>
>        stage('Update Lambda Function') {
>            steps {
>                script {
>                    bat """
>                        aws lambda update-function-code \
>                        --function-name ${LAMBDA_FUNCTION_NAME} \
>                        --zip-file fileb://hello-world-didan.zip \
>                        --region ${AWS_REGION}
>                    """
>                }
>            }
>        }
>    }
>
>    post {
>        success {
>            echo 'Lambda function updated successfully!'
>        }
>        failure {
>            echo 'Failed to update the Lambda function.'
>        }
>    }
>```
>Perintah-perintah dalam skrip Jenkins Pipeline di atas digunakan untuk mengotomatisasi proses Continuous Integration/Continuous Deployment (CI/CD) untuk fungsi AWS Lambda
<br>

### 2. Hubungkan GitHub dengan Ngrok supaya ketika kode pada repositori GitHub sudah diedit, maka CI/CD pada Jenkins melakukan **Build** secara otomatis
<img width="580" alt="Screenshot_25" src="https://github.com/user-attachments/assets/690343ae-db5e-4c85-bd61-2057f03a3321">

<br>

### 2. Selanjutnya yaitu lakukan uji percobaan terhadap CI/CD yang sudah dibuat dengan melakukan cara seperti berikut.
<img width="580" alt="Screenshot_21" src="https://github.com/user-attachments/assets/14c84a3c-e849-4a97-ae42-1cabc08543c6">
<img width="580" alt="Screenshot_22" src="https://github.com/user-attachments/assets/0926d383-ad3a-412b-8b75-65779bf7ce5c">
<img width="580" alt="Screenshot_23" src="https://github.com/user-attachments/assets/b1285764-c4e1-4a72-a344-d23d45042928">

- Pergi ke file kode di GitHub kemudian edit isi file tersebut, lalu pilih **Commit changes.**
- Karna pada CI/CD Pipeline suda menggunakan Ngrok, maka ketika kembali ke Jenkins CI/DC Pipeline membangun ulang secara otomatis dan pastikan ikon indikator yang tertampil ceklis/berhasil.
- Berikutnya pergi ke URL HTTP tadi lalu segarkan halaman dan pastikan perubahan terjadi yang menandakan konfigurasi CI/CD Freestyle Project sudah berhasil.
