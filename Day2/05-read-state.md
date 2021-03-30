## Lab 5: Create a Terraform config that reads from the state on Terraform Cloud

Now that we have our state stored in Terraform Cloud we will create another project, configuration, and workspace to read from it.

### Step 5.1.1

Start by creating a new directory and `main.tf` file:

```shell
mkdir -p /workstation/terraform/read_state && cd $_
```

```shell
touch main.tf
```

### Step 2.3.3

In order to read from our `training-<INITIALS>-dev` workspace, we will need to setup a `terraform_remote_state` data source. Data sources are used to retrieve read-only data from sources outside of our project. It supports several cloud providers, but we'll be using `remote` as the `backend`.

```bash
# read_state/main.tf
data "terraform_remote_state" "server_state" {
  backend = "remote"

  config = {
    hostname = "app.terraform.io"
    organization = "training-nyl"

    workspaces = {
      name = "training-<INITIALS>-dev"
    }
  }
}
```

### Step 2.3.4

Now that we have access to our remote `training-<INITIALS>-dev` workspace, we can retrieve the output contained within it. We'll also output `random` which we created in this configuration, confirming that they are distinct.

```bash
# read_state/main.tf
output "read_ips" {
  value = data.terraform_remote_state.server_state.outputs.public_ip
}
```

### Step 2.3.5

To verify that we have successfully retrieved the state from out `training-<INITIALS>-dev` workspace, we can run our configuration and validate our outputs.

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
server_state_ip = 0de9168d0b78ead6
```

It worked! You've now successfully stored your states remotely and read from those remote states.
