#    Installing Nexus Artifact Repository on AWS

## What is an Artifact Repository Manager?

An artifact repository manager is a tool that allows you to store and manage different built artifacts, such as software packages, libraries, and dependencies. It facilitates the storage, retrieval, and distribution of artifacts, making it an integral part of any DevOps toolchain.

##Nexus: Your Artifact Repository Manager

Nexus is a popular artifact repository manager that offers a range of features to support artifact management in DevOps pipelines. It allows you to create and manage your personal repositories, as well as proxy public repositories like the Maven Central Repository. Additionally, Nexus offers a flexible and user-friendly interface and provides REST APIs for integration with other tools.

This guide will walk you through the process of installing Nexus on an AWS instance running Ubuntu, using the latest version of Nexus. Nexus is a versatile artifact repository manager that can be a valuable addition to your DevOps toolchain.

This guide will walk you through the process of installing Nexus on an AWS instance running Ubuntu, using the latest version of Nexus. Nexus is a versatile artifact repository manager that can be a valuable addition to your DevOps toolchain.


## Prerequisites

    An AWS EC2 instance running Ubuntu.
    SSH access to the AWS instance.
    Superuser (root) privileges or sudo access on the instance.

Step 1: Update the System

    sudo apt update

Step 2: Install OpenJDK 1.8


    sudo apt install openjdk-8-jre-headless -y

Step 3: Create a Directory and Download Nexus
  
    sudo mkdir /opt/nexus
    cd /opt/nexus

# Download the latest Nexus version (replace URL with the latest version).

    sudo wget -O nexus.tar.gz https://download.sonatype.com/nexus/3/latest-unix.tar.gz
    tar -xvf nexus.tar.gz
    sudo mv nexus-3.X.X-X nexus  # Replace X.X.X-X with the actual version number

Step 4: Create a Nexus User and Set Ownership

    sudo adduser nexus
    sudo chown -R nexus:nexus /opt/nexus

Step 5: Configure Nexus to Run as the Nexus User

Open the Nexus run configuration file:

    sudo vi /opt/nexus/bin/nexus.rc

Uncomment the run_as_user parameter and set it to "nexus":

    run_as_user="nexus"

Step 6: Configure the Nexus Data Directory (Optional)

You can specify a custom data directory. Open the Nexus properties file and modify the -Dkaraf.data parameter to your preferred location. For example:

    sudo vi /opt/nexus/bin/nexus.vmoptions

Example configuration for a custom data directory:


    -Xms2703m
    -Xmx2703m
    -XX:MaxDirectMemorySize=2703m
    -XX:+UnlockDiagnosticVMOptions
    -XX:+UnsyncloadClass
    -XX:+LogVMOutput
    -XX:LogFile=../sonatype-work/nexus3/log/jvm.log
    -XX:-OmitStackTraceInFastThrow
    -Djava.net.preferIPv4Stack=true
    -Dkaraf.home=.
    -Dkaraf.base=.
    -Dkaraf.etc=etc/karaf
    -Djava.util.logging.config.file=etc/karaf/java.util.logging.properties
    -Dkaraf.data=/your/custom/data/directory
    -Djava.io.tmpdir=../sonatype-work/nexus3/tmp
    -Dkaraf.startLocalConsole=false

Step 7: Running Nexus as a System Service

To manage Nexus using systemd, create a Nexus systemd unit file:

    sudo vi /etc/systemd/system/nexus.service

Add the following contents to the unit file:

    [Unit]
    Description=Nexus service
    After=network.target
    
    [Service]
    Type=forking
    LimitNOFILE=65536
    ExecStart=/opt/nexus/bin/nexus start
    ExecStop=/opt/nexus/bin/nexus stop
    User=nexus
    Restart=on-abort
    TimeoutSec=600
    
    [Install]
    WantedBy=multi-user.target

Enable the Nexus service to start on boot:

    sudo systemctl enable nexus

Start the Nexus service:

    sudo systemctl start nexus

Conclusion

You've successfully installed Nexus on an AWS instance running Ubuntu, using the latest version of Nexus. By following these steps, you have a powerful artifact repository manager ready to support your DevOps workflows. Nexus can help you manage artifacts efficiently, streamline your CI/CD processes, and enhance your DevOps toolchain.
