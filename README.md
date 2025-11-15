# Virtual Environment Manager
Easily manage virtual environments.

## Requirements
[supported platforms](https://github.com/r-pufky/ansible_avenv/blob/main/meta/main.yml)

## Role Variables
[defaults](https://github.com/r-pufky/ansible_avenv/tree/main/defaults/main/)

### Generated Variables & Scripts
After successful execution the following variables are available (standard role
variable scope):

 Variable    | type | Description
-------------|------|----------------------------------------------
 _avenv_venv | str  | Absolute path to created virtual environment.


A helper script is written to `{{ _avenv_venv }}/bin/exec` which allows for
executing commands within the virtual environment. This cannot be executed in
a subshell.

```yaml
- name: 'Run pip list in venv'
  ansible.builtin.shell: 'set -o pipefail && source /var/venv/app/bin/exec pip list'
  args:
    executable: '/bin/bash'
  changed_when: false
  register: pip_list
```

## Dependencies
**galaxy-ng** roles cannot be used independently. Part of
[r_pufky.deb](https://github.com/r-pufky/ansible_collection_deb) collection.

## Example Playbook
This role is intended to be called multiple times in other roles with most
options being set per repository release being downloaded. Multiple virtual
environments require calling role multiple times.

Both [**PIP**](https://pip.pypa.io/en/stable/getting-started/) and
[**UV**](https://docs.astral.sh/uv/) managers may be used.

### Create my_app venv with 'stopwords' package installed.
``` yaml
- name: 'Create my_app'
  r_pufky.deb.avenv:
  vars:
    avenv_name: 'my_app'
    avenv_pips:
      - name: 'stopwords'
```

### Create my_app venv with requirements from ansible controller.
May be used in conjunction with `avenv_[pip|uv]_requirements_install` and
`avenv_[uv_]pips[].requirements`.

``` yaml
- name: 'Create my_app with requirements from ansible'
  r_pufky.deb.avenv:
  vars:
    avenv_name: 'my_app'
    avenv_pip_requirements_source: 'host_vars/venv.example.com/data/requirements.txt'
```

### Create my_app venv with requirements from remote directory.
Requirements may be found on the remote directory and installed automatically.
Very useful for installing source packages from github. May be used in
conjunction with `avenv_[pip|uv]_requirements_source` and
`avenv_[uv_]pips[].requirements`.

``` yaml
- name: 'Create my_app with requirements from remote'
  r_pufky.deb.avenv:
  vars:
    avenv_name: 'my_app'
    avenv_pip_requirements_install: '/opt/my_app'
```

### Create my_app venv using UV manager with 'stopwords' package installed.
``` yaml
- name: 'Create my_app'
  r_pufky.deb.avenv:
  vars:
    avenv_name: 'my_app'
    avenv_manager: 'uv'
    avenv_uv_pips:
      - name: 'stopwords'
```

### Permissions
Virtual environment ownership and permissions may also at the same time. Users
and groups must be managed outside of the role.

``` yaml
- name: 'Create my_app with a restricted user and locked down permissions'
  r_pufky.deb.avenv:
  vars:
    avenv_name: 'my_app'
    avenv_user: 'some_user'
    avenv_group: 'some_group'
    avenv_mode: 'a-st,g-w,o-rwx'
```

### Putting it all together (create venv for a real app)
Create a complete virtual environment for paperless-ngx.

```yaml
- name: 'Install | create virtual environment'
  ansible.builtin.include_role:
    name: 'r_pufky.deb.avenv'
  vars:
    avenv_name: 'paperless-ngx'
    avenv_user: 'paperless'
    avenv_group: 'paperless'
    avenv_pip_extra_args: '--upgrade'
    avenv_pip_requirements_install: '/opt/paperless'
```

## Development
Configure [environment](https://r-pufky.github.io/ansible_collection_docs/ansible/environment)

Run all unit tests:
``` bash
molecule test --all
```

### Releases
Major release versions track Debian release versions:

* **[13.x.x](https://github.com/r-pufky/ansible_avenv)**: 13 Trixie.
* **[12.x.x](https://github.com/r-pufky/ansible_avenv/tree/12.x)**: 12 Bookworm.

### Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License](https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0)
 [(direct link)](https://github.com/r-pufky/ansible_avenv/blob/main/LICENSE)

## Author Information
PGP Fingerprint: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9](https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9)
| [github gist](https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22)
