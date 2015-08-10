# Vagrant box

By default, the example `Vagrantfile` now uses the `ubuntu/trusty64` box. Formerly, it used a custom `roots/bedrock` box, which was the regular `ubuntu/trusty64` base box pre-provisioned with this playbook (except for the `wordpress-sites` role) in order to speed the provisioning process. The rationale for switching back to the regular `ubuntu/trusty64` box is documented in [#260](https://github.com/roots/trellis/pull/260).