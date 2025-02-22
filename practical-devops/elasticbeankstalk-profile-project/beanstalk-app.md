This is a project that depllys an applicationusing serverless clouds services AWS. it was firstly tested on he local machine before final deployment in the cloud.

# Deploying a Scalable Web Application on AWS Elastic Beanstalk

## **Project Overview**
This project is a **highly available, scalable web application** deployed on **AWS Elastic Beanstalk** with **RabbitMQ, Elastic Cache, RDS, and an Ubuntu instance**. The application follows a **microservices-based architecture** and provides a **CRUD** interface for user interactions.

## **Tech Stack & Services Used**
- **Backend**: Java (Spring Boot, Spring MVC)
- **Frontend**: JSP, HTML, CSS
- **Database**: Amazon RDS (MySQL)
- **Message Queue**: RabbitMQ
- **Caching**: AWS Elastic Cache (Memcached/Redis)
- **Application Hosting**: AWS Elastic Beanstalk
- **Instance**: Ubuntu Server
- **Build & Package Management**: Maven
- **Version Control**: GitHub

---

## **Project Architecture**
### **Infrastructure Components:**
1. **AWS Elastic Beanstalk** → Deploys and scales the application.
2. **Amazon RDS (MySQL)** → Manages relational database storage.
3. **RabbitMQ** → Handles message queueing for microservices communication.
4. **AWS Elastic Cache** → Caches database queries for faster access.
5. **Ubuntu EC2 Instance** → Hosts additional services and configurations.

### **Workflow Diagram:**
```plaintext
User → Load Balancer → Elastic Beanstalk (App) → RDS (MySQL)
                               ↳ Elastic Cache (Redis/Memcached)
                               ↳ RabbitMQ (Message Queue)
```
---

##  Step 1: Install & Set Up AWS CLI and EB CLI( This is necessary if you are using the Eb cli, i will show you how to configure both the eb cli and usikng the management console)
 Elastic Beanstalk (EB) uses the AWS CLI (Command Line Interface).

### 🔹 1.1 Install AWS CLI
 Download and install AWS CLI:

- **Windows:** [AWS CLI for Windows](https://aws.amazon.com/cli/)
- **Mac:**
  ```sh
  brew install awscli
  ```
- **Linux:**
  ```sh
  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
  unzip awscliv2.zip
  sudo ./aws/install
  ```
👉 Verify AWS CLI is installed:
  ```sh
  aws --version
  ```

### 🔹 1.2 Configure AWS CLI
👉 Run the following command to link AWS CLI to your account:
  ```sh
  aws configure
  ```
✅ Enter your AWS Access Key, Secret Key, Region, and Output Format (json).

### 🔹 1.3 Install EB CLI
👉 Install Elastic Beanstalk CLI:

- **Windows:** [EB CLI for Windows](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install.html)
- **Mac/Linux:**
  ```sh
  pip install awsebcli --upgrade --user
  ```
👉 Verify EB CLI is installed:
  ```sh
  eb --version
  ```

---
![Screenshot 2025-02-19 013116](https://github.com/user-attachments/assets/17bf41f6-b305-4233-95f8-dbb48d134b47)
![Screenshot 2025-02-16 022247](https://github.com/user-attachments/assets/f59255c9-77d4-44aa-8efa-cbf0be2e465b)
![Screenshot 2025-02-16 022238](https://github.com/user-attachments/assets/e0cf4c41-c27b-4cd1-9628-ebc201623cab)
![Screenshot 2025-02-16 022225](https://github.com/user-attachments/assets/186423d9-84fb-4791-9e49-11e031b5dc9a)
![Screenshot 2025-02-16 021110](https://github.com/user-attachments/assets/81be5d97-6c9c-48ae-8759-1c86f5710779)
![Screenshot 2025-02-16 021102](https://github.com/user-attachments/assets/721df51b-4008-4a61-ab6d-96acd518cbd8)
![Screenshot 2025-02-16 021042](https://github.com/user-attachments/assets/3f15e8bf-b007-40e1-abd9-521289d50d76)

1. **Clone the repository**
   ```sh
   git clone https://github.com/yourusername/your-repo-name.git
   cd your-repo-name
   ```

2. **Set up the environment variables**

## **Setup & Installation**
### **1. Install Java and Maven on Windows**
#### **Install Java 17**
1. Download Java 17 from [Adoptium](https://adoptium.net/temurin/releases/) or [Oracle JDK 17](https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html).
2. Install Java by following the installer instructions.
3. Set environment variables:
   - Open **System Properties** → **Advanced** → **Environment Variables**.
   - Set `JAVA_HOME` to `C:\Program Files\Java\jdk-17`
   - Add `C:\Program Files\Java\jdk-17\bin` to the `PATH` variable.
4. Verify installation:
   ```bash
   java -version
   ```

#### **Install Maven**
1. Download Apache Maven from [Maven Official Website](https://maven.apache.org/download.cgi).
2. Extract the ZIP file and place it in `C:\Program Files\Apache Maven`.
3. Set environment variables:
   - Add `C:\Program Files\Apache Maven\bin` to the `PATH` variable.
4. Verify installation:
   ```bash
   mvn -version
   ```
![Screenshot 2025-02-19 050037](https://github.com/user-attachments/assets/148eb03b-91e7-4d88-a1fe-63d56264188a)
![Screenshot 2025-02-19 110227](https://github.com/user-attachments/assets/242c84a8-495c-4d63-89e7-04c68b5d883f)
![Screenshot 2025-02-19 101105](https://github.com/user-attachments/assets/ccc13ee3-c3d8-4e3b-9e69-874216109b02)
![Screenshot 2025-02-19 100459](https://github.com/user-attachments/assets/4fe45563-4896-4431-b138-55431b149853)
 
 ### **2. Install MySQL Client and Move Database to EC2 Instance**
#### **Install MySQL Client on Windows**
1. Download and install oon both your local host and the instance **MySQL Community Edition** from [MySQL Downloads](https://dev.mysql.com/downloads/mysql/).
2. Ensure MySQL Client (`mysql.exe`) is installed and accessible in the command prompt.
3. Verify installation:
   ```bash
   mysql --version
   ```
![Screenshot 2025-02-19 155300](https://github.com/user-attachments/assets/6ef3d689-1a6d-46ad-a4a3-b3f2c13221e3)

#### **Move Local Database to EC2 MySQL RDS**
1. Dump your local database into a SQL file:
   ```bash
   mysqldump -u root -p your_database_name > database_backup.sql
   ```
2. Copy the database dump file to the EC2 instance: this will copy your database from the local host to the cloud, depending on what you are working with, sometimes you may have to tar the database and untar it in the cloud or you can as well move it like that.
   ```bash
   scp -i your-key.pem database_backup.sql ubuntu@your-ec2-public-ip:/home/ubuntu/
   ```
3. Connect to the EC2 instance via SSH:
   ```bash
   ssh -i your-key.pem ubuntu@your-ec2-public-ip
   ```
   ![Screenshot 2025-02-19 153657](https://github.com/user-attachments/assets/86375a65-b341-4ec3-9748-5c331e1f7881)

   creating inbound rules in your security group is very important these will allow services to communicate with ezch other within the same vpc and you can also extend and connect more than one vpc depending on the job you are doing
   the number of security groups created differs on preference but i created one for all the services, this was easier to navigate and connect services together
   ![Screenshot 2025-02-19 220721](https://github.com/user-attachments/assets/bf1d56cc-6c6b-49e2-ad00-bf1f814cf31e)
![Screenshot 2025-02-19 153718](https://github.com/user-attachments/assets/1d947bab-5f8e-445e-829a-0b8195495740)

5. Import the database into MySQL RDS:
   ```bash
   mysql -h your-rds-endpoint -u admin -p your_database_name < /home/ubuntu/database_backup.sql
   ```
   ![Screenshot 2025-02-19 215843](https://github.com/user-attachments/assets/83c17f55-b3a3-4039-a465-b8829c148faf)
![Screenshot 2025-02-19 220520](https://github.com/user-attachments/assets/1dc979ea-60c3-4e35-9990-7a48c4751b4d)
![Screenshot 2025-02-19 220313](https://github.com/user-attachments/assets/74c99ccf-4598-4ff7-a937-179346ba6688)
![Screenshot 2025-02-19 153729](https://github.com/user-attachments/assets/79e9b070-0c69-4b60-bcd2-b949dfa2f93e)

## 📌 Architecture Overview

```text
┌──────────────────────────────┐
│      Elastic Beanstalk       │
├──────────────┬──────────────┤
│      RDS     │  ElastiCache │
├──────────────┴──────────────┤
│       RabbitMQ Messaging    │
└──────────────────────────────┘
```


The next time was to create a Amazon ElastiCache, this is used to monitor real-time performance for real-time applications,
what this means is it helps in performance of applications especially if its a live wants to scale traffic and connection to data. it serves a link between the database and the app, instead of going back and forth and having lags this helps in smooth recall of data and scalability.
Serves as a memory cashe i.e, memory story for later recalls, incase a user logs in now and has to log out later, the users information is already in the memory, all the app has to do is to recall the information or data of the user.Delivers real-time performance, and enables applications to scale instantly. ElastiCache is compatible with three open-source caching solutions: Valkey, Memcached and Redis OSS.

![Screenshot 2025-02-22 091130](https://github.com/user-attachments/assets/480e9743-6ffe-40ac-be13-9a690b0c1174)

Amazon MQ is a managed message broker service for Apache ActiveMQ and RabbitMQ that makes it easy to set up and operate message brokers in the cloud, so you can migrate your messaging and applications without rewriting code.
this is sends a signal or message prompting the other servics to respond, its more or less like the combination of SQS and SNS, more advance.
![Screenshot 2025-02-22 091414](https://github.com/user-attachments/assets/d37cb4b1-abb2-46a7-b593-5920a25aec90)
![Screenshot 2025-02-22 091334](https://github.com/user-attachments/assets/14c5c5ad-6024-4879-9aef-7ec4d864f49d)
![Screenshot 2025-02-22 091344](https://github.com/user-attachments/assets/b1ce64d2-1bc6-419a-ba9a-ffd4373169f6)

Next is to create an Elactic beanstall environment to host the app and connect it to all the services and security group. Remember to connect all the services created to the security grroup  created for them that allows necessary permissions
upload the .war file that was built by the mvn package, rerun the mvn clean package command to get this
![Screenshot 2025-02-19 110227](https://github.com/user-attachments/assets/170ef839-2c50-43e4-93e3-bf5529347c68)

![Screenshot 2025-02-19 222419](https://github.com/user-attachments/assets/97a21bf1-a5fb-4d1a-84f8-0ae3ba72a650)
![Screenshot 2025-02-19 222349](https://github.com/user-attachments/assets/3c9b3ac1-39a0-489b-9182-b1b0b32c8884)
![Screenshot 2025-02-19 222118](https://github.com/user-attachments/assets/db41c0ec-5484-440a-b8a8-91dca7665e73)
![Screenshot 2025-02-19 222059](https://github.com/user-attachments/assets/abc1be2c-3b21-43b5-9826-1972d2c4c1e5)
![Screenshot 2025-02-19 222047](https://github.com/user-attachments/assets/9c5e8262-2ac5-4dcd-bebe-29bacc13fcdb)
![Screenshot 2025-02-19 230901](https://github.com/user-attachments/assets/228a97a9-69ac-4f96-b72c-bbb33793539d)
![Screenshot 2025-02-19 223720](https://github.com/user-attachments/assets/a3a439be-c35b-4e18-aad4-144c1251a83c)
![Screenshot 2025-02-19 223640](https://github.com/user-attachments/assets/683110ee-a130-47f0-861d-3b1dbbb04980)
![Screenshot 2025-02-19 222905](https://github.com/user-attachments/assets/9c8f68e2-3e78-4165-8275-081cbe0d79c2)

check the ec2 instance, you would see that another instance has been created by the beankstalk environment

![Screenshot 2025-02-19 231118](https://github.com/user-attachments/assets/ff773769-777d-4071-8990-e6c8eaa9bae3)

## 🔧 Troubleshooting

- **Issue: Connection to RDS fails**
  - Ensure the RDS security group allows your IP
  - Verify database credentials

- **Issue: Elastic Beanstalk deployment fails**
  - Run `eb logs` to check error logs if using ebcli or check eb console for any errors and log 
  - Ensure dependencies are installed in `requirements.txt` or `package.json`

### ✨ Contributions & Feedback
Feel free to open issues or submit PRs to improve this project!
