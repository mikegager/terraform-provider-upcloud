---
# generated by https://github.com/hashicorp/terraform-plugin-docs
page_title: "upcloud Provider"
subcategory: ""
description: |-
  UpCloud
---

# upcloud Provider

The UpCloud Terraform Provider enables organisations to control resources
provided by the UpCloud platforw.

## Example Usage

```hcl
terraform {
  required_version = ">= 0.12.0"
}

provider "upcloud" {
  username = "<Your username>"
  password = "<Your password>"
}

resource "upcloud_server" "myserver" {
  # ...
}
```

<!-- schema generated by tfplugindocs -->
## Schema

### Optional

- **password** (String) Password for UpCloud API user
- **retry_max** (Number) Maximum number of retries
- **retry_wait_max_sec** (Number) Maximum time to wait between retries
- **retry_wait_min_sec** (Number) Minimum time to wait between retries
- **username** (String) UpCloud username with API access