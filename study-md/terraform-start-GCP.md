

## install terraform

install Terraform
```bash
$ brew install terraform
```

check version
```bash
$ terraform -v
```

## Build infrastructure

### Prerequisites
* Have GCP account.
* Enable the Google Compute Engine API for your project int the GCO console.
* Create a service account key

### Write configuration

 Create a directory for your
 configuration in terminal
 ```bash
 $ mkdir learn-terraform-gcp
 ```
Change into the directory
```bash
$ cd learn-terraform-gcp
```
Create a main.tf file for your configuration
```bash
$ touch main.tf
```
Open main.tf in your text editor, and paste in the configuration below. Be sure to replace <ProJect_ID>, us-centrall, <NAME>.json with your project's ID, your project's resion and service account key, and save the file.
```bash
terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
      version = "4.51.0"
    }
  }
}

provider "google" {
  credentials = file("<NAME>.json")

  project = "<PROJECT_ID>"
  region = "us-centrall"
  zone = "us-centrall-c"
}

resource "google_compute_network" "vpc_network" {
  name = "terraform-network"
}
```
### Initialize the directory
When you create a new configuration — or check out an existing configuration from version control — you need to initialize the directory with terraform init. 

initialize the directory.
```bash
$ terraform init
```
successful
```bash
Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/google versions matching "4.51.0"...
- Installing hashicorp/google v4.51.0...
- Installed hashicorp/google v4.51.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

### Format and validate the configuration
Terraform will print out the names of the files it modified.
```bash
$ terraform fmt
```
You can also make sure your configuration is syntactically valid and internally consistent by using the terraform validate command

Validate your configuration. The example configuration provided above is valid, so Terraform will return a success message.
```bash
$ terraform validate
Success! The configuration is valid.
```

### Create infrastructure
Apply the configuration now with the terraform apply command.
```bash
$ terraform apply
```

successful
```bash
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # google_compute_network.vpc_network will be created
  + resource "google_compute_network" "vpc_network" {
      + auto_create_subnetworks         = true
      + delete_default_routes_on_create = false
      + gateway_ipv4                    = (known after apply)
      + id                              = (known after apply)
      + ipv4_range                      = (known after apply)
      + name                            = "terraform-network"
      + project                         = (known after apply)
      + routing_mode                    = (known after apply)
      + self_link                       = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value:
```
Terraform will indicate what infrastructure changes it plans to make, and prompt for your approval before it makes those changes.

This output shows the execution plan, describing which actions Terraform will take in order to create infrastructure to match the configuration. The output format is similar to the diff format generated by tools such as Git. The output has a + next to resource "google_compute_network" "vpc_network", meaning that Terraform will create this resource. Beneath that, it shows the attributes that will be set. When the value displayed is (known after apply), it means that the value will not be known until the resource is created.

Terraform will now pause and wait for approval before proceeding. If anything in the plan seems incorrect or dangerous, it is safe to abort here with no changes made to your infrastructure.

In this case the plan looks acceptable, so type yes at the confirmation prompt to proceed. It may take a few minutes for Terraform to provision the network.
```bash
  Enter a value: yes

google_compute_network.vpc_network: Creating...
google_compute_network.vpc_network: Still creating... [10s elapsed]
google_compute_network.vpc_network: Still creating... [20s elapsed]
google_compute_network.vpc_network: Still creating... [30s elapsed]
google_compute_network.vpc_network: Creation complete after 38s [id=projects/testing-project/global/networks/terraform-network]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```
You have now created infrastructure using Terraform! Visit the GCP console to see the network you provisioned. Make sure you are looking at the same region and project that you configured in the provider configuration.

## Change infrastructure
In the previous tutorial, you created your first infrastructure with Terraform: a VPC network. In this tutorial, you will modify your configuration, and learn how to apply changes to your Terraform projects.

Infrastructure is continuously evolving, and Terraform was built to help manage resources over their lifecycle. When you update Terraform configurations, Terraform builds an execution plan that only modifies what is necessary to reach your desired state.

When using Terraform in production, we recommend that you use a version control system to manage your configuration files, and store your state in a remote backend such as HCP Terraform or Terraform Enterprise.

### Create a new resource

You can create new resources by adding them to your Terraform configuration and running terraform apply to provision them.

Add the following configuration for a Google compute instance resource to main.tf.
```bash
resource "google_compute_instance" "vm_instance" {
  name         = "terraform-instance"
  machine_type = "f1-micro"

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
    }
  }

  network_interface {
    network = google_compute_network.vpc_network.name
    access_config {
    }
  }
}
```

Now run terraform apply to create the compute instance. Terraform will prompt you to confirm the operation.
```bash
$ terraform apply
```
successful
```bash
google_compute_network.vpc_network: Refreshing state... [id=projects/testing-project/global/networks/terraform-network]

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # google_compute_instance.vm_instance will be created
  + resource "google_compute_instance" "vm_instance" {
      + can_ip_forward       = false
      + cpu_platform         = (known after apply)
      + deletion_protection  = false
      + guest_accelerator    = (known after apply)
      + id                   = (known after apply)
      + instance_id          = (known after apply)
      + label_fingerprint    = (known after apply)
      + machine_type         = "f1-micro"
      + metadata_fingerprint = (known after apply)
      + min_cpu_platform     = (known after apply)
      + name                 = "terraform-instance"
      + project              = (known after apply)
      + self_link            = (known after apply)
      + tags_fingerprint     = (known after apply)
      + zone                 = (known after apply)

      + boot_disk {
          + auto_delete                = true
          + device_name                = (known after apply)
          + disk_encryption_key_sha256 = (known after apply)
          + kms_key_self_link          = (known after apply)
          + mode                       = "READ_WRITE"
          + source                     = (known after apply)

          + initialize_params {
              + image  = "debian-cloud/debian-11"
              + labels = (known after apply)
              + size   = (known after apply)
              + type   = (known after apply)
            }
        }

      + network_interface {
          + name               = (known after apply)
          + network            = "terraform-network"
          + network_ip         = (known after apply)
          + subnetwork         = (known after apply)
          + subnetwork_project = (known after apply)

          + access_config {
              + nat_ip       = (known after apply)
              + network_tier = (known after apply)
            }
        }

      + scheduling {
          + automatic_restart   = (known after apply)
          + on_host_maintenance = (known after apply)
          + preemptible         = (known after apply)

          + node_affinities {
              + key      = (known after apply)
              + operator = (known after apply)
              + values   = (known after apply)
            }
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value:
```
Once again, answer yes to the confirmation prompt.
```bash
  Enter a value: yes

google_compute_instance.vm_instance: Creating...
google_compute_instance.vm_instance: Still creating... [10s elapsed]
google_compute_instance.vm_instance: Creation complete after 11s [id=projects/testing-project/zones/us-central1-c/instances/terraform-instance]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```
This is a fairly straightforward change — you added a google_compute_instance resource named vm_instance to the configuration, and Terraform created the resource in GCP.

### Modify configuration
In addition to creating resources, Terraform can also make changes to those resources.

Add a tags argument to your vm_instance resource block.
```bash
 resource "google_compute_instance" "vm_instance" {
   name         = "terraform-instance"
   machine_type = "f1-micro"
+  tags         = ["web", "dev"]
   ## ...
 }
```

Run terrraform apply again.
```bash
$ terraform apply
```
successful
```bash
google_compute_network.vpc_network: Refreshing state... [id=projects/testing-project/global/networks/terraform-network]
google_compute_instance.vm_instance: Refreshing state... [id=projects/testing-project/zones/us-central1-c/instances/terraform-instance]

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # google_compute_instance.vm_instance will be updated in-place
  ~ resource "google_compute_instance" "vm_instance" {
        id                   = "projects/testing-project/zones/us-central1-c/instances/terraform-instance"
        name                 = "terraform-instance"
      ~ tags                 = [
          + "dev",
          + "web",
        ]
        # (15 unchanged attributes hidden)



        # (3 unchanged blocks hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value:
```
You can apply this change now by responding yes, and Terraform will add the tags to your instance.

## Destory infrastructure
You have created and modified infrastructure using Terraform. You will now learn how to destroy your Terraform-managed infrastructure.

Once you no longer need infrastructure, you might want to destroy it to reduce your security exposure and costs. For example, you may remove a production environment from service, or manage short-lived environments like build or testing systems. In addition to building and modifying infrastructure, Terraform can destroy or recreate the resources it manages.

### Destory
The terraform destory command terminates resources managed by your Terraform project. This command is the inverse of terraform apply in that it terminates all the resources specified in your Terraform state. It does not destory resources running elsewhere that are not managed by the current Terraform project.
```bash
$ terraform destory
```
successful
```bash
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # google_compute_instance.vm_instance will be destroyed
  - resource "google_compute_instance" "vm_instance" {
      - can_ip_forward       = false -> null
      - cpu_platform         = "Intel Haswell" -> null
      - deletion_protection  = false -> null
      - enable_display       = false -> null
      - guest_accelerator    = [] -> null
      - id                   = "projects/testing-project/zones/us-central1-c/instances/terraform-instance" -> null
      - instance_id          = "1820538232654796756" -> null
      - label_fingerprint    = "42WmSpB8rSM=" -> null
      - machine_type         = "f1-micro" -> null

      ## ...

    }

  # google_compute_network.vpc_network will be destroyed
  - resource "google_compute_network" "vpc_network" {
      - auto_create_subnetworks         = true -> null
      - delete_default_routes_on_create = false -> null
      - id                              = "projects/testing-project/global/networks/terraform-network" -> null
      - name                            = "terraform-network" -> null
      - project                         = "testing-project" -> null
      - routing_mode                    = "REGIONAL" -> null
      - self_link                       = "https://www.googleapis.com/compute/v1/projects/testing-project/global/networks/terraform-network" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value:
```
As with apply, Terraform shows its execution plan and waits for approval before making any changes.

Answer yes to execute this plan and destroy the infrastructure.
```bash
  Enter a value: yes

google_compute_instance.vm_instance: Destroying... [id=projects/testing-project/zones/us-central1-c/instances/terraform-instance]
google_compute_instance.vm_instance: Still destroying... [id=projects/testing-project/zones/...entral1-c/instances/terraform-instance, 10s elapsed]
google_compute_instance.vm_instance: Destruction complete after 16s
google_compute_network.vpc_network: Destroying... [id=projects/testing-project/global/networks/terraform-network]
google_compute_network.vpc_network: Still destroying... [id=projects/testing-project/global/networks/terraform-network, 10s elapsed]
google_compute_network.vpc_network: Still destroying... [id=projects/testing-project/global/networks/terraform-network, 20s elapsed]
google_compute_network.vpc_network: Still destroying... [id=projects/testing-project/global/networks/terraform-network, 30s elapsed]
google_compute_network.vpc_network: Destruction complete after 37s

Destroy complete! Resources: 2 destroyed.
```
As with terraform apply, Terraform determines the order in which resources must be destroyed. GCP will not destroy a VPC network if there are other resources still in it, so Terraform waits until the instance is destroyed first. When performing operations, Terraform creates a dependency graph to determine the correct order of operations. In more complicated cases with multiple resources, Terraform will perform operations in parallel when it's safe to do so.