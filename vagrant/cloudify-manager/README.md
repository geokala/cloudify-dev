Cloudify Vagrant Manager
========================

When developing features that should be cloud agnostic, it can be very useful to use vagrant to spin up VM's. This way you don't have to worry about credentials and sporadic network issues.
Plus, the configuration is much simpler and the launch time is significantly faster.
This Vagrantfile allows you to have a fully functioning manager with just one command line. It contains 3 VM definitions.
<br>
<br>
All VM's use the [Simple Manager Blueprint](https://github.com/cloudify-cosmo/cloudify-manager-blueprints/tree/master/simple) to simply (get it?) bootstrap cloudify on an existing machine.

**Important: This vagrant environment requires vagrant version 1.7.1 and above**

## The *prod_docker* VM


All this VM does is bootstrap the manager using the the cloudify docker container. You cannot change neither the code nor the packages running on the manager. To start it just run:

```bash
vagrant up prod_docker
```

or, since this is the default machine:

```bash
vagrant up
```

## The *prod-packages* VM

All this VM does is bootstrap the manager using the deb packages. You cannot change neither the code nor the packages running on the manager. To start it just run:

```bash
vagrant up prod-packages
```

## The *dev-packages* VM

This VM does exactly what the *prod-packages* VM does, but it also executes the *setup-dev-env* task on the newly started manager.
You can read about this task [Here](https://github.com/cloudify-cosmo/cloudify-dev/blob/master/tasks), but basically, it allows you to easily make changes to your code and apply them to the manager. To start the VM just run:

```bash
vagrant up dev-packages
```

Now comes the nifty part. After you make changes to your code, **ssh into the vm by running `vagrant ssh dev`**.
You will automatically be placed inside a directory where you can use the `cfy` command.
If the changes you made do not affect the agent package, just run:

```bash
cfy dev --tasks-file /home/vagrant/cloudify/cloudify-dev/tasks/tasks.py --task restart-services
```

Otherwise, run:

```bash
cfy dev --tasks-file /home/vagrant/cloudify/cloudify-dev/tasks/tasks.py --task update-agent-package
cfy dev --tasks-file /home/vagrant/cloudify/cloudify-dev/tasks/tasks.py --task restart-services
```

**Note**

There are some useful environment variables you can use:

- MANAGER_BLUEPRINTS_BRANCH - The manager blueprint branch you want to use to bootstrap with. Defaults to *master*
- CLOUDIFY_SOURCE_FOLDER - Path to a folder on your host that contains the cloudify source code projects. If this is not specified,
it assumes the sources are located next to the cloudify-dev project and constructs a relative path.
- CLI_BRANCH - The cli branch used to bootstrap. The default value will be the value set for MANAGER_BLUEPRINTS_BRANCH.
- USE_TARZAN - Set this variable to any string to use local tarzan URL's instead of amazon s3 buckets.
