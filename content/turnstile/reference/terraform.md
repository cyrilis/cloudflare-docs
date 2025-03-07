---
pcx_content_type: how-to
title: Terraform
weight: 11
---

# Manage Turnstile with Terraform

{{<Aside type="note"header="Requirements">}}
This guide assumes that you have the [Terraform](https://developer.hashicorp.com/terraform/tutorials/certification-associate-tutorials/install-cli) command installed on your machine.
{{</Aside>}}

[Terraform](https://developer.hashicorp.com/terraform/tutorials/certification-associate-tutorials/install-cli) is a tool for building, changing, and versioning infrastructure, and provides components and documentation for building [Cloudflare resources](https://registry.terraform.io/providers/cloudflare/cloudflare/latest/docs). Listed below are examples to help you get started with Turnstile using Terraform. For a more generalized guide on configuring Cloudflare and Terraform, visit our [Getting Started with Terraform and Cloudflare](https://blog.cloudflare.com/getting-started-with-terraform-and-cloudflare-part-1/) blog post.

## Create a Turnstile widget with Terraform

### Create an API token

Create an [API Token](/fundamentals/api/get-started/create-token/) with the **Account > Turnstile > Edit** permission. Next, you need to export this secret in our environment variables:

```sh
---
header: Export your token
---
$ export CLOUDFLARE_API_TOKEN=<API_TOKEN>
```

### Create a Turnstile widget

See the example configuration below when creating a Turnstile widget.

```tf
---
header: cloudflare.tf
highlight: 15-32
---
terraform {
    required_providers {
        cloudflare = {
            source = "cloudflare/cloudflare"
            version = "~> 4"
        }
    }
}

variable "account_id" {
    description = "Your Cloudflare Account ID."
    # eg: 6be2041a37d48aaaa9c686434f1709f0
}

resource "cloudflare_turnstile_widget" "example" {
    account_id   = var.account_id
    name         = "My Terraform-managed widget"
    domains      = ["example.com"]
    mode         = "managed"
}

output "turnstile_example_sitekey" {
    description = "Sitekey"
    value       = cloudflare_turnstile_widget.example.id
    # Note: the `id` is your sitekey.
}

output "turnstile_example_secretkey" {
    description = "Secret key"
    value       = cloudflare_turnstile_widget.example.secret
    sensitive   = true
}

```

{{<Aside type="note">}}
The `id` field in the `cloudflare_turnstile_widget.example` resource is your Turnstile widget's sitekey.
{{</Aside>}}

### Initialize Terraform and the Cloudflare provider

Run the command `terraform init` to set up your Terraform working directory, enabling it to interact with Cloudflare services. This process involves downloading the required provider plugins, establishing backend storage for your state files, and creating a local `.terraform` directory to store configuration data.

```sh
---
header: Initialize command
---
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Reusing previous version of cloudflare/cloudflare from the dependency lock file
- Installing cloudflare/cloudflare v4.5.0...
- Installed cloudflare/cloudflare v4.5.0 (self-signed, key ID C76001609EE3B136)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://www.terraform.io/docs/cli/plugins/signing.html

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

### Review the Terraform plan

You can run `terraform plan`, which will output any proposed changes. This will prompt you for your Cloudflare Account ID. Make sure to review the plan carefully.

```sh
---
header: Review command
---
$ terraform plan

var.account_id
    Your Cloudflare Account ID.

    Enter a value: 6be2041a37d48aaaa9c686434f1709f0

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
    + create

Terraform will perform the following actions:

    # cloudflare_turnstile_widget.example will be created
    + resource "cloudflare_turnstile_widget" "example" {
        + account_id = "6be2041a37d48aaaa9c686434f1709f0"
        + domains    = [
            + "example.com",
        ]
        + id         = (known after apply)
        + mode       = "managed"
        + name       = "My Terraform-managed widget"
        + secret     = (sensitive value)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
    + turnstile_example_secretkey = (sensitive value)
    + turnstile_example_sitekey   = (known after apply)

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

### Apply the Terraform changes

Once the changes look accurate and you are comfortable moving forward, apply them using the `terraform apply` command.

```sh
---
header: Apply command
---
$ terraform apply --auto-approve

var.account_id
    Your Cloudflare Account ID.

    Enter a value: 6be2041a37d48aaaa9c686434f1709f0

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
    + create

Terraform will perform the following actions:

    # cloudflare_turnstile_widget.example will be created
    + resource "cloudflare_turnstile_widget" "example" {
        + account_id     = "6be2041a37d48aaaa9c686434f1709f0"
        + domains        = [
            + "example.com",
        ]
        + id             = (known after apply)
        + mode           = "managed"
        + name           = "My Terraform-managed widget"
        + secret         = (sensitive value)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
    + turnstile_example_secretkey = (sensitive value)
    + turnstile_example_sitekey   = (known after apply)
cloudflare_turnstile_widget.example: Creating...
cloudflare_turnstile_widget.example: Creation complete after 1s [id=0x4AAAAAAAEe4wQdBshJxBeK]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

turnstile_example_secretkey = <sensitive>
turnstile_example_sitekey = "0x4AAAAAAAEe4wQdBshJxBeK"
```

You have successfully created a Turnstile widget. Go to the [Cloudflare dashboard](https://dash.cloudflare.com/?to=/:account/turnstile) to view its configuration and analytics in a user-friendly interface.

### Retrieve the secret key
Use `terraform output` to get your secret key.

```sh
---
header: Secret key
---
$ terraform output turnstile_example_secretkey
"0x4AAAAAAAEe4xWueFq9yX8ypjlimbk1Db4"
```

{{<Aside type="note">}}
For advanced usage, refer to our [Terraform resource documentation](https://registry.terraform.io/providers/cloudflare/cloudflare/latest/docs/resources/turnstile_widget).
{{</Aside>}}

## Import a Turnstile widget

### Prerequisites

Before you can import the Turnstile widget in Terraform, you must:

- [Install the `cf-terraforming` tool](/terraform/advanced-topics/import-cloudflare-resources/#cf-terraforming).
- [Create a Cloudflare API token](/fundamentals/api/get-started/create-token/).
- [Initialize Terraform and the Cloudflare provider](/turnstile/reference/terraform/#initialize-terraform-and-the-cloudflare-provider).

### Import Turnstile widgets in Terraform

Run the `cf-terraforming generate` command and add the output below to your `.tf` file.

```sh
---
header: Generate command
---
$ cf-terraforming generate --resource-type cloudflare_turnstile_widget --account 6be2041a37d48aaaa9c686434f1709f0

output: 

resource "cloudflare_turnstile_widget" "terraform_managed_resource_0x4AAAAAAAEk5sP3rwf91fe8" {
  account_id = "6be2041a37d48aaaa9c686434f1709f0"
  domains    = ["example.net"]
  mode       = "managed"
  name       = "test site"
  region     = "world"
}

resource "cloudflare_turnstile_widget" "terraform_managed_resource_0x4AAAAAAAE0wwg0H1StXlOx" {
  account_id = "6be2041a37d48aaaa9c686434f1709f0"
  domains    = ["example.com"]
  mode       = "managed"
  name       = "My Terraform-managed widget"
}

resource "cloudflare_turnstile_widget" "terraform_managed_resource_0x4AAAAAAAF1z4LbxEka5UBh" {
  account_id = "6be2041a37d48aaaa9c686434f1709f0"
  domains    = ["example.org"]
  mode       = "managed"
  name       = "My website"
}
```

Run the `cf-terraforming import` command and the resulting commands below.

```sh
---
header: Import command
---
$ cf-terraforming import --resource-type cloudflare_turnstile_widget --account 6be2041a37d48aaaa9c686434f1709f0

output: 

terraform import cloudflare_turnstile_widget.terraform_managed_resource_0x4AAAAAAAEg5sP3rwf91fe8 6be2041a37d48aaaa9c686434f1709f0/0x4AAAAAAAEk5sP3rwf91fe8
terraform import cloudflare_turnstile_widget.terraform_managed_resource_0x4AAAAAAAE0gwg0H1StXlOx 6be2041a37d48aaaa9c686434f1709f0/0x4AAAAAAAE0wwg0H1StXlOx
terraform import cloudflare_turnstile_widget.terraform_managed_resource_0x4AAAAAAAE2z4LbxEka5UBh 6be2041a37d48aaaa9c686434f1709f0/0x4AAAAAAAF1z4LbxEka5UBh 
```