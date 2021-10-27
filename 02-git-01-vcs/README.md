**/.terraform/* - игнорирует все файлы в скрытой дериктории .terraform, которая может находиться в любой поддиректории (вложенной директории)
*.tfstate - игнорирует все файлы с расширением .tfstate
*.tfstate.* - игнорирует все файлы с двойным расширением, в котором встречается .tfstate (например .tfstate.backup)
crash.log - игнорирует файл лога сбоев (ошибок)
*.tfvars - игнорирует все файлы с расширением .tfvars
override.tf - игнорирует файл override.tf
override.tf.json - игнорирует файл override.tf.json
*_override.tf - игнорирует все файлы с окончанием _override и расширением .tf
*_override.tf.json - игнорирует все файлы с окончанием _override и двойным расширением .tf.json
.terraformrc - игнорирует скрытый файл .terraformrc
terraform.rc - игнорирует файл terraform.rc
