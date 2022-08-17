# outline-minio-docker-compose
A guide on how to use outline wiki in combination with cloudflared &amp; minio as S3 storage

Advantages of using cloudflared:
- Free SSL Certificate
- No need to use a reverse proxy as cloudflare handles everything
- Secure, as you don't need to expose more ports as needed. 

## Requirements
- Create a Cloudlare Tunnel at https://dash.teams.cloudflare.com/ to obtain the tunnel token and think about 2 domain names 
  - One for the access to outline wiki and one for the minio storage 
  - For example: outline.mydomain.com & storage.mydomain.com
- Have docker https://docs.docker.com/get-docker/ & docker-compose https://docs.docker.com/compose/install/ installed 

## Step 1
Create a docker-compose.yml file with the following contents and paste the TUNNEL_TOKEN= which you obtained from cloudflare
You can download it via: ```wget https://github.com/Nytra-it/outline-minio-docker-compose/blob/main/docker-compose.yml``` 

## Step 2: 
Create the docker.env file in the same directory ( You can download it via: ```wget https://github.com/Nytra-it/outline-minio-docker-compose/blob/main/docker.env``` and customize the following options:
- Generate 2 Keys with the command openssl rand -hex 32 and paste them in SECRET_KEY= & UTILS_SECRET=
- Replace the POSTGRES credentials with your own one
- Replace the postgres credentials in the DATABASE_URL & DATABASE_URL_TEST
- Customize the URL= with your outline domain, in my example: outline.mydomain.com
- Customize MINIO_ROOT_USER= & MINIO_ROOT_PASSWORD= 
- Replace AWS_ACCESS_KEY_ID=minio with MINIO_ROOT_USER  & AWS_SECRET_ACCESS_KEY=miniopassword with the MINIO_ROOT_PASSWORD= 
- Replace the AWS_S3_UPLOAD_BUCKET_URL with the storage URL from the requirements in my example: https://storage.mydomain.com
- You have to decide whether you want to use Slack, Microsoft or Google as your login.
  - For SLACK customize: SLACK_CLIENT_ID=get_a_key_from_slack & SLACK_CLIENT_SECRET=get_the_secret_of_above_key
  - For Google customize: GOOGLE_CLIENT_ID= & GOOGLE_CLIENT_SECRET=
  - For Microsoft/azure customize: AZURE_CLIENT_ID=, AZURE_CLIENT_SECRET= & AZURE_RESOURCE_APP_ID=
- If you want to use emails, such as you`ve been invited, customize the SMTP Part at the lower end of the docker.env  file  


## Step 3
- After creating and customizing the two files, run the ```docker-compose up -d ``` command to create the containers.
- Now go to the minio administrative dashboard and create a new bucket. You can reach the minio admin via: http://yourip:9001 or http://yourservername:9001
  - Create a new bucket with the name minio.
- Go to Cloudflare https://dash.teams.cloudflare.com/ and navigate to Access, Tunnels
  - You should see your new created tunnel - click on Configure and Public Hostname 
  - Click Add a public hostname 
  - As subdomain choose your chosen outline wiki subdomain in my example (outline.mydomain.com) type in outline as subdomain and as domain mydomain.com 
  - As Service choose http and behind the :// type in: outline:3000
  - Click Save hostname 
- Add another public hostname for your storage 
  -   As your subdomain type in your storage subdomain, in my example (storage.mydomain.com) type in storage as subdomain and mydomain.com as domain 
  - As Service choose http and behind the :// type in: storage:9000
  - Click Save hostname
- In your Tunnel dashboard you should see your tunnel status as ACTIVE 
- You can now access your own outline instance via: https://outline.mydomain.com

## Rework
- After finishing the installation you can close the ports of minio as you don`t need to access it anymore. 
  - To do that edit the docker-compose.yml file and either remove the ports section or uncomment it.
  ```   
  #ports:
   # - "9000:9000"
   # - "9001:9001"
 - To further secure your outline instance you can use cloudflare application access tool. 
