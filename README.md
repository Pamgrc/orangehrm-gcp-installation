# OrangeHRM on Google Cloud Platform
This repository documents the steps taken to set up and configure OrangeHRM on Google Cloud Platform (GCP).

## Table of Contents
- [Prerequisites](#prerequisites)
- [Setting up Google Cloud Platform](#setting-up-google-cloud-platform)
- [Installing OrangeHRM](#installing-orangehrm)
- [Configuring OrangeHRM](#configuring-orangehrm)
- [Troubleshooting](#troubleshooting)
- [References](#references)

## Prerequisites
- Google Cloud Platform account
- SSH access to the VM instance
- Basic knowledge of Linux command line

## Setting up Google Cloud Platform

### 1. Create a VM Instance
1. Log in to your GCP account.
2. Navigate to the Compute Engine.
3. Click on `Create Instance`.
4. Configure the instance:
    - Name: `orangehrm-instance`
    - Region: Choose your preferred region
    - Machine type: `e2-medium` (or according to your requirements)
    - Boot disk: Ubuntu 20.04 LTS
5. Click `Create`.

### 2. Set Up SSH Access
1. Open the SSH terminal from the GCP console.
2. Update the package list and install necessary packages:
    ```bash
    sudo apt update
    sudo apt install apache2 mysql-server php libapache2-mod-php php-mysql php-mbstring php-xml php-curl php-zip php-intl unzip
    ```

## Installing OrangeHRM

### 1. Download and Extract OrangeHRM
1. Download the latest version of OrangeHRM:
    ```bash
    wget https://orangehrm.com/download-link
    ```
2. Extract the downloaded file:
    ```bash
    unzip orangehrm-*.zip -d /var/www/html/
    ```

### 2. Set Up the Database
1. Log in to MySQL:
    ```bash
    sudo mysql -u root -p
    ```
2. Create a new database and user:
    ```sql
    CREATE DATABASE orangehrm;
    CREATE USER 'orangehrmuser'@'localhost' IDENTIFIED BY 'yourpassword';
    GRANT ALL PRIVILEGES ON orangehrm.* TO 'orangehrmuser'@'localhost';
    FLUSH PRIVILEGES;
    EXIT;
    ```

### 3. Configure Apache
1. Create a new virtual host file for OrangeHRM:
    ```bash
    sudo nano /etc/apache2/sites-available/orangehrm.conf
    ```
2. Add the following configuration:
    ```apache
    <VirtualHost *:80>
        ServerAdmin admin@example.com
        DocumentRoot /var/www/html/orangehrm
        ServerName yourdomain.com

        <Directory /var/www/html/orangehrm/>
            Options Indexes FollowSymLinks
            AllowOverride All
            Require all granted
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
    ```
3. Enable the new virtual host and rewrite module:
    ```bash
    sudo a2ensite orangehrm.conf
    sudo a2enmod rewrite
    sudo systemctl restart apache2
    ```

## Configuring OrangeHRM
1. Open your browser and navigate to `http://yourdomain.com`.
2. Follow the on-screen instructions to complete the OrangeHRM setup:
    - Database Host: `localhost`
    - Database Name: `orangehrm`
    - Database User: `orangehrmuser`
    - Database Password: `yourpassword`

## Troubleshooting

### Common Issues
- **Database Connection Error**: Ensure MySQL is running and the database credentials are correct.
- **Permission Issues**: Ensure the web server has the necessary permissions to access the OrangeHRM files.

## References
- [OrangeHRM Documentation](https://orangehrm.com/documentation/)
- [Google Cloud Platform Documentation](https://cloud.google.com/docs)
