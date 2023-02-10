# Terraform Module to create EC2 with one additional EBS Volume

##### Below is the Example to call this module
```
data "aws_vpc" "thinknyx" {
  filter {
    name   = "tag:Name"
    values = ["thinknyx-thinknyx"]
  }
}
data "aws_subnets" "thinknyx" {
  filter {
    name   = "vpc-id"
    values = [data.aws_vpc.thinknyx.id]
  }
  filter {
    name   = "tag:type"
    values = ["public"]
  }
}
data "aws_security_group" "thinknyx" {
  filter {
    name   = "tag:Name"
    values = ["thinknyx-thinknyx"]
  }
}

module "ec2_ubuntu" {
  for_each        = toset(data.aws_subnets.thinknyx.ids)
  source          = "kmayer10/ec2/aws"
  subnet_id       = each.value
  security_groups = [data.aws_security_group.thinknyx.id]
  Client          = "IBM"
}

output "public_ip" {
  value = [
    for ec2 in module.ec2_ubuntu: ec2.public_ip
  ]
}
```
