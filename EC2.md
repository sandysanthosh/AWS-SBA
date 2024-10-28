To deploy a Spring Boot application on AWS EC2, you need to package your Spring Boot application as a JAR file, then upload and configure it on an EC2 instance. Here’s a step-by-step guide to set up a Spring Boot application on an EC2 instance:

### Step 1: Prepare your Spring Boot Application
First, make sure your Spring Boot application is packaged as a JAR file:
```bash
mvn clean package
# or if you use Gradle
./gradlew build
```
This will generate a JAR file (usually located in the `target` directory if you’re using Maven).

### Step 2: Set Up EC2
1. **Launch an EC2 instance**:
   - Open the [AWS Management Console](https://aws.amazon.com/console/), navigate to EC2, and launch a new instance.
   - Choose an Amazon Machine Image (AMI) (e.g., Amazon Linux 2).
   - Choose an instance type (e.g., t2.micro for testing or a more robust type for production).
   - Configure instance details, such as setting the security group to allow SSH (port 22) and HTTP/HTTPS (port 80/443).

2. **SSH into the instance**:
   - Copy the public IP address of your EC2 instance.
   - Connect to it using SSH:
     ```bash
     ssh -i "path_to_your_pem_file.pem" ec2-user@your_ec2_ip_address
     ```

### Step 3: Install Java on EC2
Most EC2 instances do not come with Java pre-installed. Install Java by running the following commands:
```bash
# Update packages
sudo yum update -y

# Install Java 11 or Java 17 (depending on your app’s requirements)
sudo amazon-linux-extras install java-openjdk11 -y
```

### Step 4: Transfer the Spring Boot JAR to EC2
Use `scp` to copy your Spring Boot JAR file to your EC2 instance:
```bash
scp -i "path_to_your_pem_file.pem" path_to_your_jar_file.jar ec2-user@your_ec2_ip_address:/home/ec2-user
```

### Step 5: Run the Spring Boot Application on EC2
1. **Run the JAR file**:
   ```bash
   java -jar path_to_your_jar_file.jar
   ```
   
   This command will start the Spring Boot application on the default port (8080). To keep the application running, you might want to run it with a process manager like `nohup`:
   ```bash
   nohup java -jar path_to_your_jar_file.jar > output.log 2>&1 &
   ```
   
2. **Access your application**:
   - If your Spring Boot app is running on port 8080, ensure your security group allows inbound traffic on port 8080.
   - Access the application using the URL `http://your_ec2_ip_address:8080`.

### Example Code to Test Your Setup
Here’s a minimal Spring Boot controller to test your deployment:

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/")
    public String hello() {
        return "Hello from AWS EC2!";
    }
}
```

Deploy the application using the steps above, and navigate to `http://your_ec2_ip_address:8080/` to see the message.

### Step 6: (Optional) Configure EC2 to Run the Application on Startup
For production, you might want to ensure that the application starts automatically if the instance reboots. You can add the startup command to a startup script.

1. Edit the `rc.local` file:
   ```bash
   sudo nano /etc/rc.local
   ```

2. Add the startup command:
   ```bash
   nohup java -jar /home/ec2-user/path_to_your_jar_file.jar > /home/ec2-user/output.log 2>&1 &
   ```

This setup will run your Spring Boot application on AWS EC2. Let me know if you need help with AWS configuration or a load balancer setup!
