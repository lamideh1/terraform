# terraform
Here’s a complete guide from start to finish for your **Terraform EC2 & AMI creation mini project**:

---

## **Project Overview**

**Goal**: Use Terraform to automate:

* EC2 instance creation.
* Amazon Machine Image (AMI) generation from the EC2 instance.

---

## **Prerequisites**

1. **AWS CLI installed** and configured (`aws configure`).
2. **Terraform installed** (version 1.0+).
3. An **AWS key pair** (for SSH access).
4. A **suitable AMI ID** (e.g., Amazon Linux 2).
5. Adequate IAM permissions.

---

## **Step-by-Step Guide**

### **Step 1: Create Project Directory**

```bash
mkdir terraform-ec2-ami
cd terraform-ec2-ami
```

---

### **Step 2: Create Terraform Configuration File**

Create `main.tf`:

```hcl
provider "aws" {
  region = "us-east-1"  # Change if needed
}

resource "aws_instance" "example_instance" {
  ami           = "ami-0c55b159cbfafe1f0"  # Change to your region's valid AMI
  instance_type = "t2.micro"
  key_name      = "your-key-pair-name"     # Replace with your AWS key pair name

  provisioner "remote-exec" {
    inline = [
      "sudo yum update -y",
      "sudo yum install -y httpd",
      "sudo systemctl start httpd",
      "sudo systemctl enable httpd"
    ]

    connection {
      type        = "ssh"
      user        = "ec2-user"
      private_key = file("~/.ssh/your-private-key.pem") # Replace with your private key path
      host        = self.public_ip
    }
  }

  tags = {
    Name = "Terraform-EC2"
  }
}

resource "aws_ami" "example_ami" {
  name        = "terraform-created-ami"
  description = "AMI created from Terraform-provisioned EC2 instance"
  instance_id = aws_instance.example_instance.id

  depends_on = [aws_instance.example_instance]
}
```

---

### **Step 3: Initialize Terraform**

```bash
terraform init
```

---

### **Step 4: Apply the Configuration**

```bash
terraform apply
```

* Type `yes` to confirm.
* This will:

  * Launch an EC2 instance.
  * Provision it with HTTP server.
  * Create an AMI from it.

---

### **Step 5: Clean Up Resources**

To avoid AWS charges:

```bash
terraform destroy
```

---

## **Optional Enhancements**

* Add output variables:

```hcl
output "instance_ip" {
  value = aws_instance.example_instance.public_ip
}
```

* Add security group for port 22 and 80 access.

---

## **Documentation (README.md)**

Include:

* Project purpose.
* Required tools (AWS CLI, Terraform).
* Setup steps.
* Notes on provisioning.
* Cleanup instructions.

---

Would you like me to generate the `README.md` and the Terraform files as downloadable content?
