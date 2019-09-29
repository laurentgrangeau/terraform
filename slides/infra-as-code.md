## Les bases de Terraform

----

### Les providers

Terraform est utilisé pour créer, gérer et mettre à jour des ressources d'infrastructure telles que des machines physiques, des machines virtuelles, des commutateurs réseau, des conteneurs, etc. Presque tous les types d'infrastructure peuvent être représentés comme une ressource dans Terraform.

----

### Les providers

Un fournisseur est responsable de la compréhension des interactions API et de l'exposition des ressources. Les fournisseurs sont généralement un IaaaS (par exemple Alibaba Cloud, AWS, GCP, Microsoft Azure, OpenStack), PaaS (par exemple Heroku) ou des services SaaS (par exemple Terraform Cloud, DNSimple, CloudFlare).

----

### Les providers

```json
# Configure the AWS Provider
provider "aws" {
  version = "~> 2.0"
  region  = "us-east-1"
}
```

----

### Les ressources

Les ressources sont l'élément le plus important du langage Terraform. Chaque bloc de ressources décrit un ou plusieurs objets d'infrastructure, tels que les réseaux virtuels, les instances de calcul ou les composants de niveau supérieur tels que les enregistrements DNS.

----

### Les ressources

```json
resource "aws_instance" "web" {
  ami           = "${data.aws_ami.ubuntu.id}"
  instance_type = "t2.micro"

  tags = {
    Name = "HelloWorld"
  }
}
```

----

### Les datasources

Les sources de données permettent d'extraire ou de calculer des données pour les utiliser ailleurs dans la configuration Terraform. L'utilisation de sources de données permet à une configuration Terraform d'utiliser des informations définies en dehors de Terraform, ou définies par une autre configuration Terraform distincte.

----

### Les datasources

```json
data "aws_ami" "ubuntu" {
  most_recent = true

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-trusty-14.04-amd64-server-*"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }

  owners = ["099720109477"] # Canonical
}
```

----

### Les provisioners

Lors du déploiement de machines virtuelles ou d'autres ressources de calcul similaires, nous avons souvent besoin de transmettre des données sur d'autres infrastructures connexes dont le logiciel sur ce serveur aura besoin pour faire son travail.

----

### Les provisioners

Les différents fournisseurs qui interagissent avec les serveurs distants via SSH ou WinRM peuvent potentiellement être utilisés pour transmettre ces données en se connectant au serveur et en les fournissant directement.

----

### Les provisioners

```json
resource "aws_instance" "web" {
  # ...

  provisioner "remote-exec" {
    inline = [
      "puppet apply",
      "consul join ${aws_instance.web.private_ip}",
    ]
  }
}
```

----

### Le workspace

Chaque configuration Terraform est associée à un backend qui définit comment les opérations sont exécutées et où les données persistantes telles que l'état Terraform sont stockées.

----

### Le workspace

Les données persistantes stockées dans le backend appartiennent à un espace de travail. Initialement, le backend n'a qu'un seul espace de travail, appelé "default", et il n'y a donc qu'un seul état Terraform associé à cette configuration.

----

### Le workspace

```bash
$ terraform workspace new bar
Created and switched to workspace "bar"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.
```

----