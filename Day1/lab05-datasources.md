# Lab 5: Data Source

This lab  uses the [aws_ami](https://www.terraform.io/docs/providers/aws/d/ami.html) data source to fetch the same AMI that we have been using all along and then makes the aws_instance web use that AMI. You can read about data sources [here](https://www.terraform.io/docs/configuration/data-sources.html). An example of making an aws_instance resource use the AMI returned by an aws_ami data source is in the [aws_instance](https://www.terraform.io/docs/providers/aws/r/instance.html) resource documentation.

Duration: 10 minutes

- Task 1: Add an aws_ami data source
- Task 2: Make the aws_instance web use the AMI returned by the data source

## Task 1: Add an aws_ami data source

### Step 5.1.1

Add an aws_ami data source called "ubuntu_16_04" in `main.tf` after the
variable definitions. It will find the most recent instance of a Ubuntu 16.04
AMI from Canonical (owner ID 099720109477).

```hcl
data "aws_ami" "ubuntu_16_04" {
  most_recent = true

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-xenial-16.04-amd64-server-*"]
  }

  owners = ["099720109477"]
}
```

## Task 2: Make the aws_instance use the returned AMI

### Step 5.2.1

Edit the aws_instance in `main.tf` so that its ami argument uses the AMI returned by the data source.

```hcl
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu_16_04.image_id
  instance_type = "t2.nano"
...
}
```

### Step 5.2.2

Additionally, since we no longer need the ami variable, remove it or comment it
out (with initial "#") both in `main.tf` and your variable definition.

### Step 5.2.3

Run `terraform apply` one last time to apply the changes you made.

```shell
terraform apply
```
