[Cli index](cli-index) 

# Basics

```sh
# Adding verbosity
export TF_LOG=TRACE # DEBUG # INFO # WARN # ERROR
terraform plan
```

# Making a bash script 

```sh
# This is for a github stack
export TF_LOG=ERROR
terraform apply \
    -var='github_token=<token>' \
    -var='github_owner=<owner'
```