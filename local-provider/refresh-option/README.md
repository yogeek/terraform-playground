# Understanding `refresh` option with local provider tests

Terraform apply and plan commands 'refresh' the state by default. 

Using Docker :
```
alias terraform='docker run -it -v $(pwd):/app -w /app hashicorp/terraform:light'
```

## INIT phase (normal workflow)

```
# Initial files : nothing
ls | grep -v config
--> 

# Initial plan
terraform plan

--> Terraform will perform the following actions:
-->   + local_file.foo
--> Plan: 1 to add, 0 to change, 0 to destroy.

# Initial apply
terraform apply -auto-approve

--> local_file.foo: Creating...
--> Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

# Result files : foo.bar is present (with TF state)
ls 

--> foo.bar terraform.state

# Remove file 
rm -f foo.bar

# Plan
terraform plan

--> Terraform will perform the following actions:
-->   + local_file.foo
--> Plan: 1 to add, 0 to change, 0 to destroy.

# Apply
terraform apply

--> local_file.foo: Creating...
--> Apply complete! Resources: 1 added, 0 changed, 0 destroy

# Check result : the file is present again
ls 

--> foo.bar terraform.state terraform.state.backup 

# Clean up
terraform destroy -auto-approve

--> Destroy complete! Resources: 1 destroyed.

```

## TEST phase (degraded workflow)

```
terraform apply -auto-approve

--> local_file.foo: Creating...
--> Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

# Remove file manually
rm -f foo.bar

# Plan or apply without refreshing state : no change detected !
terraform plan -refresh=false

--> No changes. Infrastructure is up-to-date.

terraform apply -refresh=false

--> Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

```