## Task 5: Create a Terraform config that reads from the state on Terraform Cloud

Now that we have our state stored in Terraform Cloud we will create another project, configuration, and workspace to read from it.

### Step 5.1.1

Start by creating a new directory and `main.tf` file:

```shell
mkdir -p /workstation/terraform/cloud_state_demo/read_state && cd $_
```

```shell
touch main.tf
```

### Step 5.1.2

Just as we did in Step 2.2.2, we need to setup our configuration to use the `remote` backend, once again replacing `ORGANIZATION NAME`. We will also create a new `random` resource to compare against:

```bash
# read_state/main.tf
terraform {
  backend "remote" {
    hostname = "app.terraform.io"
    organization = "<ORGANIZATION NAME>"

    workspaces {
      name = "lab_2_read_state"
    }
  }
}

resource "random_id" "random" {
  keepers = {
    uuid = uuid()
  }

  byte_length = 8
}
```

### Step 2.3.3

In order to read from our `lab_2_write_state` workspace, we will need to setup a `terraform_remote_state` data source. Data sources are used to retrieve read-only data from sources outside of our project. It supports several cloud providers, but we'll be using `remote` as the `backend`.

```bash
# read_state/main.tf
data "terraform_remote_state" "write_state" {
  backend = "remote"

  config = {
    hostname = "app.terraform.io"
    organization = "<ORGANIZATION NAME>"

    workspaces = {
      name = "lab_2_write_state"
    }
  }
}
```

### Step 2.3.4

Now that we have access to our remote `lab_2_write_state` workspace, we can retrieve the `random` output contained within it. We'll also output `random` which we created in this configuration, confirming that they are distinct.

```bash
# read_state/main.tf
output "random" {
  value = random_id.random.hex
}

output "write_state_random" {
  value = data.terraform_remote_state.write_state.outputs.random
}
```

### Step 2.3.5

To verify that we have successfully retrieved the state from out `lab_2_write_state` workspace, we can run our configuration and validate our outputs.

Run `init` again to install the necessary supporting files.

```shell
terraform init
```

Run `apply` to see the output.

```shell
terraform apply -auto-approve
```

```
Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:
random = c1597ca0fbba3997
write_state_random = 0de9168d0b78ead6
```

It worked! You've now successfully stored your states remotely and read from those remote states.
