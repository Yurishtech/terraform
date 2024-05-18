# Quick Summary



## Files

* main.tf
* terraform.tfstate - state file
* myvars.tfvars - Variables file

## Commands

* terrafrom init
* terraform plan
* terraform apply
  * terraform apply -replace aws\_security\_group.foo (Recreates resource during next apply)
* terraform output
  * terraform output --json
* terraform graph
  * terraform graph | dot -T svg > graph.svg
* terraform show (shows statefile in human readable format)
* terraform state list
  * terraform state show ADDR
  * terraform state mv
  * terraform state rm NAME (remove from being managed by terraform)
* terraform import
* terraform refresh
* terraform state pull
  * terraform state push state.tfstate
* terraform workspace \[new, list, show, select and delete]

Destroy

* Remove the resource not needed in the config file. and then do apply

## Resources

Resources represent the infrastructure that Terraform should create, read, update, **Meta Arguments** Resources support the following meta-arguments that can be used to change their behavior:

* depends\_on - Define explicit dependencies.
* count - Create multiple resources using indexes.
* for\_each - Create multiple resources using uniquely named keys.
* provider - Use a specific aliased provider.
* lifecycle - Customize the resource lifecycle.
* provisioner - Provision a given resource after creation or destruction.

**Data Sources.** Data sources are a special kind of Terraform resource that reads information from the target API and exposes that information for use within your Terraform configuration.

Data sources can be used for a number of reasons; but their goal is to **do something** and then **give you data**. It will instead just give you information, and not create anything. This example in particular will call out to the describe-images AWS API call, pass in a few --filter options as specified, and return an object that you can get information from - take a look at these [~~attributes~~](https://www.terraform.io/docs/providers/aws/d/ami.html#attributes-reference)!

Fetches information from aws/azure/ by applying filters.

## Provisioners

* `local-exec`
* `remote-exec`
* `file`
* `null_resource`
