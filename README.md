# Bankist - Modern Banking Application

A minimalist banking application deployed on Amazon Web Services (AWS) using modern cloud infrastructure and containerization technologies.

## 🌐 Live Applications

- **AWS ECS Deployment**: [http://project-alb-1953564500.ap-south-1.elb.amazonaws.com](http://project-alb-1953564500.ap-south-1.elb.amazonaws.com)
- **Vercel Deployment**: [https://bankist-bank.vercel.app/](https://bankist-bank.vercel.app/)

## 🏗️ Project Overview

This project demonstrates a complete CI/CD pipeline and cloud deployment strategy for a modern banking web application using AWS services. The application is containerized using Docker and deployed on Amazon ECS with load balancing capabilities.

## 🚀 Features

- **100% Digital Banking**: Complete online banking experience
- **Instant Transfers**: Real-time money transfers with zero fees
- **Instant Loans**: Quick loan approvals and processing
- **Free Debit Card**: No hidden fees, worldwide acceptance
- **Smart Savings**: Competitive interest rates and growth tools
- **Minimalist Design**: Clean, modern user interface

## 🛠️ Technology Stack

- **Frontend**: HTML5, CSS3, JavaScript/TypeScript
- **Containerization**: Docker
- **Cloud Platform**: Amazon Web Services (AWS)
- **Container Orchestration**: Amazon ECS (Elastic Container Service)
- **Container Registry**: Amazon ECR (Elastic Container Registry)
- **Load Balancing**: Application Load Balancer (ALB)
- **Compute**: EC2 Instances & Fargate
- **Infrastructure**: Infrastructure as Code (IaC)

## 📋 Deployment Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Client        │    │   ALB           │    │   ECS Cluster   │
│   Browser       │───▶│   Load Balancer │───▶│   (Fargate)     │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                                                        │
                                                        ▼
                                               ┌─────────────────┐
                                               │   Docker        │
                                               │   Container     │
                                               │   (Nginx)       │
                                               └─────────────────┘
```

## 🚀 Complete Deployment Process

### 1. Launch EC2 Instance and Install Docker

- **Instance Type**: t2.micro (Free Tier)
- **Operating System**: Amazon Linux 2
- **Docker Installation**: Latest Docker Engine
- **Security Groups**: Configured for SSH and HTTP access

```bash
# Install Docker on Amazon Linux
sudo yum update -y
sudo yum install -y docker
sudo service docker start
sudo usermod -a -G docker ec2-user
```

### 2. Build Docker Image and Containerize Application

- **Base Image**: nginx:alpine (Lightweight and secure)
- **Application**: Static web files served via Nginx
- **Port**: 80 (HTTP)

```dockerfile
FROM nginx:alpine
COPY . /usr/share/nginx/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### 3. Create Amazon ECR Repository

- **Repository Name**: bankist-app
- **Region**: ap-south-1 (Mumbai)
- **Access**: IAM roles and policies configured
- **Image Scanning**: Enabled for security

### 4. Configure AWS IAM and Credentials

- **IAM User**: Created dedicated user for ECR access
- **Access Keys**: Generated and configured locally
- **Policies**: Attached ECR and ECS permissions
- **Credentials**: Configured using AWS CLI

```bash
aws configure
# Enter Access Key ID, Secret Access Key, Region, and Output Format
```

### 5. Push Docker Image to ECR

```bash
# Login to ECR
aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.ap-south-1.amazonaws.com

# Tag and push image
docker tag bankist-app:latest <account-id>.dkr.ecr.ap-south-1.amazonaws.com/bankist-app:latest
docker push <account-id>.dkr.ecr.ap-south-1.amazonaws.com/bankist-app:latest
```

### 6. Create Application Load Balancer (ALB)

- **Type**: Application Load Balancer
- **Scheme**: Internet-facing
- **Subnets**: Public subnets across multiple AZs
- **Security Groups**: HTTP (80) and HTTPS (443) access
- **Target Groups**: ECS service integration

### 7. Configure ECS Cluster and Services

- **Cluster Type**: Fargate (Serverless)
- **Task Definition**: CPU and memory specifications
- **Service**: Auto-scaling and load balancer integration
- **Health Checks**: Application health monitoring

## 📁 Project Structure

```
bankist/
├── bankist-code/
│   ├── css/
│   │   ├── login.css
│   │   ├── responsive.css
│   │   └── style.css
│   ├── img/
│   │   └── [application images]
│   ├── login/
│   │   ├── login.html
│   │   ├── login.js
│   │   └── login.ts
│   ├── index.html
│   ├── index.js
│   ├── index.ts
│   ├── Dockerfile
│   ├── package.json
│   └── tsconfig.json
├── README.md
└── .gitignore
```

## 🔧 Local Development

### Prerequisites

- Docker installed
- Node.js (for TypeScript compilation)
- Git

### Running Locally

```bash
# Clone the repository
git clone <repository-url>
cd bankist

# Build Docker image
cd bankist-code
docker build -t bankist-app .

# Run container locally
docker run -p 8080:80 bankist-app

# Access application
open http://localhost:8080
```

## 🚀 Deployment Commands

### Build and Push to ECR

```bash
# Build image
docker build -t bankist-app .

# Tag for ECR
docker tag bankist-app:latest <account-id>.dkr.ecr.ap-south-1.amazonaws.com/bankist-app:latest

# Push to ECR
docker push <account-id>.dkr.ecr.ap-south-1.amazonaws.com/bankist-app:latest
```

### Update ECS Service

```bash
# Update service with new image
aws ecs update-service --cluster bankist-cluster --service bankist-service --force-new-deployment
```

## 🔒 Security Considerations

- **IAM Roles**: Least privilege access
- **Security Groups**: Minimal required ports
- **Image Scanning**: ECR vulnerability scanning
- **HTTPS**: SSL/TLS encryption (recommended for production)
- **Container Security**: Non-root user execution

## 📊 Monitoring and Logging

- **CloudWatch Logs**: Application and container logs
- **CloudWatch Metrics**: Performance monitoring
- **ALB Access Logs**: Request tracking
- **ECS Service Events**: Deployment and health status

## 🔄 CI/CD Pipeline (Future Enhancement)

```yaml
# Example GitHub Actions workflow
name: Deploy to ECS
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Build and push to ECR
        # Build and push Docker image
      - name: Deploy to ECS
        # Update ECS service
```

## 🛠️ Troubleshooting

### Common Issues

1. **ECR Login Issues**
   ```bash
   aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.ap-south-1.amazonaws.com
   ```

2. **ECS Service Not Starting**
   - Check task definition CPU/memory limits
   - Verify ECR image exists and is accessible
   - Review CloudWatch logs for errors

3. **ALB Health Check Failures**
   - Ensure application responds on port 80
   - Check security group rules
   - Verify target group configuration

## 📈 Performance Optimization

- **Container Optimization**: Multi-stage Docker builds
- **CDN Integration**: CloudFront for static assets
- **Auto Scaling**: ECS service auto-scaling policies
- **Caching**: Application-level caching strategies

## 🔮 Future Enhancements

- [ ] HTTPS/SSL certificate implementation
- [ ] Multi-region deployment
- [ ] Blue-green deployment strategy
- [ ] Automated testing pipeline
- [ ] Infrastructure as Code (Terraform/CloudFormation)
- [ ] Monitoring and alerting setup
- [ ] Database integration
- [ ] Authentication system

## 📞 Support

For issues related to:
- **Application**: Check the application code and logs
- **Infrastructure**: Review AWS CloudWatch and ECS service events
- **Deployment**: Verify ECR image and ECS task definition

## 📄 License

This project is for educational purposes. The Bankist application design and content are created by Jonas Schmedtmann & Ekaterine Mitagvaria for learning purposes.

---

**Deployed by**: [Your Name]  
**Last Updated**: [Current Date]  
**AWS Region**: ap-south-1 (Mumbai)
