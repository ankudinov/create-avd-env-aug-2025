---
marp: true
theme: default
# class: invert
author: Petr Ankudinov
size: 16:9
paginate: true
math: mathjax
style: |
    img[alt~="custom"] {
      float: right;
    }
    .columns {
      display: grid;
      grid-template-columns: repeat(2, minmax(0, 1fr));
      gap: 1rem;
    }
    .columns3 {
      display: grid;
      grid-template-columns: repeat(3, minmax(0, 1fr));
      gap: 1rem;
    }
    .columns1fr2fr {
      display: grid;
      grid-template-columns: 1fr 2fr;
      gap: 1rem;
      height: 80%
    }
    .columns1fr3fr {
      display: grid;
      grid-template-columns: 1fr 3fr;
      gap: 1rem;
      height: 80%
    }
    footer {
      font-size: 14px;
    }
    section::after {
      font-size: 14px;
    }
    img {
      background-color: transparent;
    }
    img[alt~="center"] {
      display: block;
      margin: 0 auto;
    }
---

# Building AVD Environment

![bg right:50%](img/pexels-obviouslyarthur-1296265.jpg)

<style scoped>section {font-size: 26px;}</style>

<!-- Do not add page number on this slide -->
<!--
_paginate: false
-->

```bash
$ id -P "$USER" | awk -F: '{print "Author: " $1 ", " $8}'
Author: pa, Petr Ankudinov
#        └─ @arista.com

$ date +"%b %Y"
Aug 2025
```

<!-- Add footer starting from this slide -->
<!--
footer: '![h:20](https://www.arista.com/assets/images/logo/Arista_Logo.png)'
-->

---

# AVD Installation Options

<style scoped>section {font-size: 24px;}</style>

<div class="columns">
<div>

- Ansible CE (Community Edition)
  - free to use
  - check [AVD docs](https://avd.arista.com/) for the installation manual
- Ansible Automation Platform
  - paid RedHat support
  - check [AAP guide here](https://avd.arista.com/devel/docs/getting-started/avd-aap.html)
  - out of scope
- AVD Studios on CloudVision
  - coming soon

</div>
<div>

After 4.9 (PyAVD is the foundation ⚠️)

```bash
# ansible-core will be installed as PyAVD requirement
pip install "pyavd[ansible]"
ansible-galaxy collection install arista.avd
# install community.general to support callback plugins, etc.
ansible-galaxy collection install community.general
```

- ⚠️ [PyAVD](https://pypi.org/project/pyavd/) is not intended to be used directly for customer projects
- If you have [an exception](https://github.com/arista-netdevops-community/CloudVisionPortal-Examples/tree/master/pyavd_examples) - you know what to do 🤓 or have Arista PS support
- Ansible provides a lot of value, for ex. inventory management - use Ansible ⚠️

</div>
</div>

---

# How to Cook AVD Inventory

<style scoped>section {font-size: 22px;}</style>

![bg right](img/pexels-cottonbro-4253320.jpg)

- Be structured.
- Craft your environment and confirm that it works with some basic test.
- Create a minimalistic inventory and generate first configs.
- Grow your environment slowly, keep it clean and easy to read. Avoid workarounds.
- When required - step back to the last working setup
- Iterate
- Cloning existing repo and adjusting to the new setup is not always a good idea.
  - Make sure that you understand every single knob in the cloned inventory

---

# AVD Repository Building Blocks

<style scoped>section {font-size: 20px;}</style>

![bg right](img/pexels-isaiah-56332-833109.jpg)

- <mark>Environment</mark>
- <mark>ansible.cfg</mark>
- inventory
- variables
- playbooks
- life quality improvements
  - shortcuts
  - useful hacks

---

# Keep Your AVD Environment Clean

<style scoped>section {font-size: 24px;}</style>

![bg](img/pexels-goumbik-349609.jpg)

- ⛔ NEVER use "handcrafted" installation direclty on your machine. [It will break](https://xkcd.com/1987/)! Troubleshooting that is wasted time. ⏱️ → 🗑️
- ⛔ NEVER use root account!
- Feasible options:
  - Dedicated and well maintained VM
  - Virtual environment
  - Containers
    - AVD container images are in preview: fully functional, but breaking changes can happen any time
    - No Arista TAC support possible for customers who ordered AVD support
  - AnsibleEEs
    - This one is for RedHat support 💵

---

# Virtual Environment vs Containers

<style scoped>section {font-size: 22px;}</style>

<div class="columns">
<div>

venv/pyenv 📦

- Pro:
  - simple and lightweight
  - no special tools required
- Breaks **often**. Troubleshooting complexity: **average**
- How it breaks:
  - multiple Pythons
  - incorrect requirements installation
  - broken path, custom ansible.cfg, tweaks, etc.
  - `../../../<ansible-collection>` 🤦 🙈

</div>
<div>

Containers 🐳

- Pro:
  - stable, portable
  - high level of isolation
- Breaks **rarely**. Troubleshooting complexity: **high**
- How it breaks:
  - permission issues 👑
    - check [this document](https://code.visualstudio.com/remote/advancedcontainers/add-nonroot-user) to UID requirements
  - broken Docker installation or host OS
  - tools can be "too heavy" for some users 🔨
  - Podman and SELinux require advanced skills

</div>
</div>

---

# Start AVD Environment in CLI

<style scoped>section {font-size: 18px;}</style>

![bg right:20%](img/demo-time.jpeg)

- Check [available AVD images](https://github.com/aristanetworks/avd/pkgs/container/avd%2Funiversal)
- Set alias:

  ```bash
  # use ~/.bashrc if ZSH is not installed
  echo 'alias avd="docker run --rm -it -v avd-playground:/home/avd/playground -w /home/avd/playground ghcr.io/aristanetworks/avd/universal:python3.11-avd-v5.5.1"' >> ~/.zshrc
  # copy an example
  cp -r /home/avd/.ansible/collections/ansible_collections/arista/avd/examples/<example-name>/* .
  ```

- Start AVD container in interactive mode:

  ```bash
  avd
  ```

- Check installed Ansible collections:

  ```bash
  avd ➜ ~ $ ansible-galaxy collection list | grep avd
  # /home/avd/.ansible/collections/ansible_collections
  arista.avd        5.5.1
  ```

- You can exit container any time with `exit` command

> ⚠️ To start fresh delete the volume: `docker volume rm avd-playground`

---

# Create AVD Environment in VSCode

<style scoped>section {font-size: 18px;}</style>

![bg right:40% fit](img/architecture-containers.png)

- To simplify your life open AVD environment in a [dev container using VSCode](https://code.visualstudio.com/docs/devcontainers/containers)
- Use the following snippet in your terminal:

```bash
# create devcontainer.json
mkdir -p avd-playground/.devcontainer
cat <<EOF > avd-playground/.devcontainer/devcontainer.json
{
    "image": "ghcr.io/aristanetworks/avd/universal:python3.11-avd-v5.5.1",
    "remoteUser": "avd",
    "onCreateCommand": "mkdir -p /home/avd/playground",
    "workspaceFolder": "/home/avd/playground",
    "workspaceMount": "source=avd-playground,target=/home/avd/playground,type=volume"
}
EOF
# start the AVD environment in VSCode
cd avd-playground
code .
```

- Click `Reopen in Container` button or use VSCode command pallete to find `Dev Container: Reopen in Container`

> 💡 if `code .` not working, open [VSCode command pallete](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette) and find and use "Install `code` command in the PATH"

---

# Keep Your `ansible.cfg` Small

```text
[defaults]
inventory = inventory.yml
jinja2_extensions =  jinja2.ext.loopcontrols,jinja2.ext.do,jinja2.ext.i18n
```

> WARNING: If you need longer ansible.cfg - your environment is likely suboptimal.

- Avoid custom collection path, etc. when you don't need it.
- Avoid any kind of relative path, like `../..`
- Test your installation and .cfg on different machines and make sure it works.

---

# Environment Troubleshooting Cheatsheet

<style scoped>section {font-size: 24px;}</style>

<div class="columns">
<div>

- PyAVD is critical in latest AVD versions

  ```bash
  pip freeze | grep pyavd
  ```

- You can encounter Ansible "world writable directory" error in CI, remote containers, etc. due to very relaxed permissions and [Ansible thinking it's not secure](https://docs.ansible.com/ansible/latest/reference_appendices/config.html#avoiding-security-risks-with-ansible-cfg-in-the-current-directory). Fix:

  ```bash
  $ printenv | grep ANSIBLE
  ANSIBLE_CONFIG=ansible.cfg
  ```

- Confirm that your environment is not isolated

  ```bash
  curl --user <login>:<password> --data "show version"
  --insecure https://<switch-mgmt-ip>:443/command-api --verbose
  ```

</div>
<div>

```bash
# Ansible collection setup
ansible --version
# check collections versions and install location
ansible-galaxy collection list
which ansible
# requirements
pip3 freeze
python3.XX -m pip freeze # may not be the same
# and where to find them
pip --version
python3 -c "import ansible as _; print(_.__file__)"
# python
python3 --version
which python
which python3
which python3.XX
python3.11 -c "import sys;print(sys.path)"
# user
whoami
id -u
id -g
echo ${HOME}
# check the PATH
echo ${PATH}
```

</div>
</div>

---

# Fun with iFrames

<!--
_class: invert
-->

<style scoped>section {font-size: 14px;}</style>

<div class="columns1fr3fr">
<div>

- Check your environment

```bash
pip freeze | grep pyavd
printenv | grep ANSIBLE
ansible --version
# check collections versions and install location
ansible-galaxy collection list
which ansible
# requirements
pip3 freeze
python3.XX -m pip freeze # may not be the same
# and where to find them
pip --version
python3 -c "import ansible as _; print(_.__file__)"
# python
python3 --version
which python
which python3
which python3.XX
python3.11 -c "import sys;print(sys.path)"
# user
whoami
id -u
id -g
echo ${HOME}
# check the PATH
echo ${PATH}
```

- Test connection to Arista switch

```bash
curl --user <login>:<password> --data "show version"
--insecure https://<switch-mgmt-ip>:443/command-api --verbose
```

- Finally run something

```bash
make build
```

</div>
<div>

<iframe src="http://localhost:8080" title="Slides" frameborder="0" width="100%" height="100%" allowfullscreen></iframe>

</div>
</div>

---

# Credits and Attributions

<style scoped>section {font-size: 20px;}</style>

![bg right:40% fit](img/marp.png)

- Slides are created in [Marp](https://github.com/marp-team/marp) by Yuki Hattori
- Check [Marp awesome list](https://github.com/marp-team/awesome-marp) and build your next slide deck in Marp!
- Most images are from [Pexels](https://www.pexels.com/)
- VSCode remote development picture is taken from [code.visualstudio.com](https://code.visualstudio.com/docs/remote/remote-overview)
- iFrame was relying on [Coder](https://github.com/coder)

---

# THE END

![bg left](img/pexels-valeriia-miller-3020919.jpg)

- [Ansible AVD](https://avd.arista.com/)
- [This repository](https://github.com/ankudinov/create-avd-env-aug-2025)

```diff
- One more slide!
+ No more slides.
```

```bash
git commit -m "The END!"
```

<!-- Add footer starting from this slide -->
<!--
footer: '![h:20](https://www.arista.com/assets/images/logo/Arista_Logo.png)'
-->
