# ran
Note - skip backtick (`). the code or contents of a file will be enclosed within triple backticks like ```
exp 2 -------------
(exp 4)

Build the Maven Project: mvn clean install
Execute the Maven Project: mvn exec:java
Clean the Project: mvn clean 

exp 4 ------------------
1. generate project
mvn archetype:generate -DgroupId=com.example -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

2. Package the application :
mvn package
3. Run
java -jar target/my-app-1.0-SNAPSHOT.jar


4. generate java gradle project
gradle init --type java-application
5.  Create a new directory in gradle root 
mkdir -p src/main/java/com/example
6. java file maven to gradle
mv src/main/java/App.java src/main/java/com/example/ 

7. edit build.gradle file, update application part
mainClassName = 'com.example.App' 
8. gradle build
9. gradle run

exp 5 --------------------
sudo apt update
sudo apt upgrade -y
sudo apt install openjdk-11-jdk -y
java -version

sudo sh -c 'echo "deb https://pkg.jenkins.io/debian-stable binary/" > /etc/apt/sources.list.d/jenkins.list'

sudo apt update 
sudo apt install jenkins -y 

sudo systemctl start jenkins 
sudo systemctl status jenkins

sudo cat /var/lib/jenkins/secrets/initialAdminPassword

http://localhost:8080/



exp 6 -------------------
1. install plugins - "Maven Integration Plugin" and "Pipeline"
2. ssh public key, Personal Access Token added to Github account.

sudo update-alternatives --config java

sudo apt update && sudo apt install git -y
git --version


git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
git config --global init.defaultBranch main

# Generate SSH Key (Press Enter for defaults)

ssh-keygen -t rsa -b 4096 -C "your-email@example.com" -f ~/.ssh/id_rsa -N ""

eval "$(ssh-agent -s)"  # use one that you got after ssh-keygen

ssh-add ~/.ssh/id_rsa   (use without .pub)

cat ~/.ssh/id_rsa.pub

ssh -T git@github.com


# Github PAT tokens neded to create repository
Settings >> Developor settings >> Tokens classic >> Generate new token

curl -H "Authorization: token YOUR_TOKEN" \
-d '{"name":"hello-maven", "private":false}' \
https://api.github.com/user/repos

3. http://localhost:8080
4. on “New Item” name it Maven-CI or Gradle-CI and Select “Pipeline project”
5. Select GitHub project
Put Project url ( https://github.com/azadCMRIT/hello-maven.git )
6. run the below script then save and apply. then build.
pipeline { 
agent any 
	stages { 
	 stage('Checkout') { 
		 steps { 
			 git branch: 'main', url: 'https://github.com/azadCMRIT/hello-maven.git' // check repository URL
		 } 
	 } 
	 stage('Build') { 
		 steps { 
			 // 'mvn clean package' or 'gradle build' 
			 sh 'mvn clean package' 
		 } 
	 } 
	 stage('Test') { 
		 steps { 
		 	sh 'mvn test' // comment if you dont wanna test 
		 } 
	 } 
}
}


// example POM file
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>hello-maven</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>hello-maven</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <build>
    <plugins>
        <plugin>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
                <source>11</source>
                <target>11</target>
            </configuration>
        </plugin>
    </plugins>
</build>

</project>
```


exp 7 ----------------

1. sudo apt install ansible -y
2. ansible --version
3. nano hosts.ini 
[local]
localhost ansible_connection=local
4. nano setup.yml
```
---
- name: Basic Server Setup
  hosts: localhost
  become: yes  # for sudo privilege

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Install curl
      apt:
        name: curl
        state: present
```
5. exectue
sudo ansible-playbook -i hosts.ini setup.yml

exp 8 ----------------
a. Creating Maven project
b. Installing Jenkins
c. Settting Git
d. Pushing Maven project to Github
e. Running Maven project through Jenkins  pipeline script	

cd /var/lib/jenkins/workspace/<maven-ci>
sudo touch deploy.yml hosts.ini 

sudo hosts.ini
```
[local]  
localhost ansible_connection=local
```
then
sudo nano deploy.yml
```
---
- name: Deploy Artifact to Localhost
  hosts: localhost
  become: true
  become_user: student-devops
  become_method: su

  tasks:
    - name: Copy the artifact to the target location
      copy:
        src: "/var/lib/jenkins/workspace/maven/target/hello-maven-1.0-SNAPSHOT.jar"
        dest: "/home/student-devops/Desktop/t.jar"
```
pipeline script for jenkins
use github = https://github.com/azadCMRIT/hello-maven2.git

```
pipeline { 
    agent any 
    
    stages { 

        stage('Checkout') { 
            steps { 
                git branch: 'main', url: 'https://github.com/azadCMRIT/hello-maven2.git' 
            } 
        } 

        stage('Build') { 
            steps { 
                sh 'mvn clean package' 
            } 
        }

        stage('Test') { 
            steps { 
                sh 'mvn test' 
            } 
        } 

        stage('Archive Artifacts') { 
            steps { 
                archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true 
            } 
        } 

        stage('Deploy') { 
            steps { 
                sh """ 
                export ANSIBLE_HOST_KEY_CHECKING=False 
                ansible-playbook -i /var/lib/jenkins/workspace/<maven2>/hosts.ini /var/lib/jenkins/workspace/<maven2>/deploy.yml --extra-vars='ansible_become_pass=<sudo_pass>' 
                """ 
                // Enter student password above 
            } 
        } 
    } 
}
```
above replace <maven2> with actual maven folder name that you got by performing previous exp


exp 9 -----------------
goto https://dev.azure.com
Create an Azure DevOps Organization
Select a Region
Review Your Organization’s Dashboard
Navigate to “New Project” on your organization dashboard
give project name & description
choose project's Visibility: public or private
Explore Your Project Dashboard
--
▪Repos: Where your code is stored.
▪Pipelines: For build and release automation.
▪Boards: For work tracking and agile planning.
▪Test Plans: For managing and running tests.
▪Artifacts: For hosting packages.
--

exp 10 -------------------
1. goto dev.azure.com and select pipeline for the sidebar
2. choose github and yaml option
3.  Select the created repository “sample” from the available repositories. 
4. Give permissions if prompted or sign in to your GitHub account if needed. 
5. choose a maven pipeline from given option
6. An YAML file should be automatically generated based on the pom.xml
7. press the save and run button
8. click on the created Job from the Jobs table

exp 11 & 12 ----------------

1.  Go to organization setting -> settings -> off the “Disable creation of classic release pipelines” option
2. goto pipeline -> release
3. select new release pipeline
4. Select a successfully run pipeline as Artifact
5. select "Add an artifat"
6. Select a pipeline which is run successfully in “Source (build pipeline)”
7. Select "Add"
8. Select "Add a stage"
9. Select Empty job on top right under “Select a template Or start with an" option
10. Give a stage name “Stage 2 “ for example
11. Go to “pre deployment conditions” by clicking on Stage
12. Select Manual only
13. Now “save”  the release  pipeline from top right corner 
14. Create folder 
15. Select “Create release” option on top right then Select “Create”
16. In pipeline -> Releases, you will find Release -1 
17. Go to deploy
18. Select Stage and go to “Deploy stage”
19. Select “Deploy “ and wait for sometime 
20. In pipeline -> Releases it shows a green tick and has run successfully
