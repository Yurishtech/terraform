# State Management

### Inspecting Infrastructure

#### Graphing Infrastructure

`terraform graph`

```
terraform graph | dot -T svg > graph.svg
```

<pre><code><strong>terraform output
</strong></code></pre>

#### Showing Terraform State

```
terraform show
```

```
terraform state list
```

```
terraform state show ADDR
```

### Importing Infrastructure

1. Terraform configuration must exist in code
2. Must import to state file using command `terrafrom import`

After import set all the resource properties until `terraform plan` shows no changes

### Manipulating State

#### Replacing Resources with `-replace`

#### Replacing Resources with Taint

#### Moving Resources

from one module to other by each resource

```
terraform state mv module.todo.data.aws_ami.ubuntu module.todo_v2.data.aws_ami.ubuntu
```

or add block

```
+moved {
+  from = module.todo_v2
+  to   = module.todo
+}
+
```

#### Removing Resources

`terraform state rm`

### Refreshing State

### Secrets in State
