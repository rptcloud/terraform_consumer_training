# Lab 8: Lifecycles

Duration: 15 minutes

This lab demonstrates how to use lifecycle directives to control the order in which Terraform creates and destroys resources.

- Task 1: Use `prevent_destroy` with an instance

## Task 1: Use `prevent_destroy` with an instance

We'll demonstrate how `prevent_destroy` can be used to guard an instance from being destroyed.

### Step 8.1.1: Use `prevent_destroy`

Add `prevent_destroy = true` to the same `lifecycle` stanza.

```bash
resource "aws_instance" "web" {
  count         = var.num_webs
  ami           = data.aws_ami.ubuntu_16_04.image_id
  instance_type = "t2.micro"

  # ...

  lifecycle {
    prevent_destroy = true
  }
}
```

Attempt to destroy the existing infrastructure. You should see the error that follows.

```shell
terraform destroy -force
```

```
Error: Instance cannot be destroyed

  on main.tf line 8:
   8: resource "aws_instance" "web" {

Resource aws_instance.web[0] has lifecycle.prevent_destroy set, but the plan
calls for this resource to be destroyed. To avoid this error and continue with
the plan, either disable lifecycle.prevent_destroy or reduce the scope of the
plan using the -target flag.
```

### Step 8.1.2: Destroy cleanly

Now that you have finished the steps in this lab, destroy the infrastructure you have created.

Remove the `prevent_destroy` attribute.

```bash
resource "aws_instance" "web" {
  count         = var.num_webs
  ami           = data.aws_ami.ubuntu_16_04.image_id
  instance_type = "t2.micro"

  # ...

  # lifecycle {
    # Comment out or delete this line
    # prevent_destroy = true
  # }
}
```

Finally, run `destroy`.

```shell
terraform destroy -force
```

The command should succeed and you should see a message confirming `Destroy complete! Resources: 2 destroyed.`
