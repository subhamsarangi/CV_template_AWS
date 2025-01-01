# CV_template_AWS
### write code and make a repo

### setup aws ec2 instance and download a keypair pem file

### GITHUB repo settings action secrets: 
EC2_SSH_KEY :: contents of the pem file
HOST_DNS :: public ipv4 dns
USERNAME :: ubuntu
TARGET_DIR :: home

### GITHUB WORKFLOW
jobs:
  deploy:
    name: Deploy to EC2 on master branch push
    steps:
      - name: Checkout the files
      - name: Deploy to Server
      - name: Executing remote ssh commands using ssh key