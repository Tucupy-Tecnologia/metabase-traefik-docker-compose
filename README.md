# Metabase Traefik Docker Compose

A production-ready docker compose file to deploy Metabase. Please refer to the official Metabase documentation for usage instructions.

## The compose file uses the following images:

- [Metabase](https://www.metabase.com/) – self-service BI reporting tool
- [Postgres](https://www.postgresql.org/) – database server used by the Metabase application
- [Traefik](https://traefik.io/) – a reverse proxy that sits in front of Metabase and provides SSL termination (and generates [Let's Encrypt](https://letsencrypt.org/) certificates)

⚠️ Docker images are continuously updated so please check the version tags and decide if a newer version of the container would be more appropriate for your use case.

## Requirements:

- [Docker](https://www.docker.com/)
- [Docker Compose](https://docs.docker.com/compose/)

# Getting started (Local Deployment)

1. Clone the template somewhere sensible, i.e. `/srv/metabase`
2. Duplicate the .env.example file and rename it to .env
3. Update the environment variables as needed
4. Update `db_password.txt`, `db_user.txt` and `mb_key.txt` following the instructions in each file.
5. Run `docker compose up -d` to create or update the docker containers from the compose file
6. Run `docker compose down` to shut down the containers
7. Ensure DNS is pointed correctly and you have ports `80` and `443` open on your firewall

## Available environment variables

`MB_DBNAME`: name of the database

`MB_HOST`: the public URL that people will access Metabase from (assumes DNS is pointed correctly)

`LE_EMAIL`: for expiry notices when your [Let's Encrypt](https://letsencrypt.org/) certificate is coming up for renewal

# Deploying to AWS VPC

1. Set up an AWS account if you haven't already.
2. Create a VPC:
   - Go to the AWS VPC Dashboard
   - Click "Create VPC"
   - Choose "VPC and more" to create a VPC with subnets, route tables, and other components
   - Configure the VPC settings as needed (e.g., name, IP range, subnets)
3. Launch an EC2 instance:
   - Go to the EC2 Dashboard
   - Click "Launch Instance"
   - Choose an Amazon Linux 2 AMI
   - Select an instance type (t2.micro for testing, larger for production)
   - Configure the instance details, selecting the VPC you created
   - Add storage as needed
   - Configure security group to allow inbound traffic on ports 22 (SSH), 80 (HTTP), and 443 (HTTPS)
   - Launch the instance and create or select an SSH key pair
4. Connect to your EC2 instance via SSH
5. Install Docker and Docker Compose on the EC2 instance:
   ```
   sudo yum update -y
   sudo amazon-linux-extras install docker
   sudo service docker start
   sudo systemctl enable docker
   sudo usermod -a -G docker ec2-user
   sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose
   ```
   Note: You may need to log out and log back in for the group changes to take effect.
6. Clone your Metabase repository onto the EC2 instance:
   ```
   sudo yum install git -y
   git clone <your-repository-url>
   ```
7. Follow steps 2-7 from the "Getting started" section above
8. Configure your domain's DNS to point to your EC2 instance's public IP

# Deploying to DigitalOcean

1. Create a DigitalOcean account if you haven't already
2. Create a VPC:
   - Go to the Networking section
   - Click "VPC"
   - Click "Create VPC Network"
   - Choose a region and IP range
3. Create a Droplet:
   - Click "Create" > "Droplets"
   - Choose an image (Ubuntu is recommended)
   - Select a plan based on your needs
   - Choose a datacenter region (same as your VPC)
   - Select additional options as needed
   - Choose the VPC network you created
   - Add your SSH key
   - Create the Droplet
4. Connect to your Droplet via SSH
5. Install Docker and Docker Compose on the Droplet:
   ```
   sudo apt update
   sudo apt install docker.io docker-compose
   sudo usermod -aG docker ${USER}
   ```
6. Clone your Metabase repository onto the Droplet
7. Follow steps 2-7 from the "Getting started" section above
8. Configure your domain's DNS to point to your Droplet's IP address

# Security Considerations

- Always use strong, unique passwords for your database and Metabase admin account
- Keep your Docker images and host system updated
- Use a firewall to restrict access to your server
- Consider using AWS Security Groups or DigitalOcean Cloud Firewalls for additional network security
- Regularly backup your Metabase database

# Maintenance

- Regularly update your Docker images:
  ```
  docker-compose pull
  docker-compose up -d
  ```
- Monitor your server's resources and scale as needed
- Keep an eye on your SSL certificate expiration dates

# Troubleshooting

- Check Docker logs: `docker-compose logs`
- Ensure all required ports are open and accessible
- Verify that your DNS settings are correct
- Check that your .env file and secrets are correctly configured

# Contributing

Changes welcome! Please don't hesitate to open an issue if you spot a problem or make a pull request to propose an enhancement.

# License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
