This is simple ansible playbook to manage ssh public keys.

### Usage

After replacing hosts.example, public_keys/*.example with real one.

```
# check if master user (the person who run the playbook) has proper access to remote servers
$ ansible -i ./hosts all -u ubuntu -m ping

# run ansible playbook
$ ansible-playbook -i ./hosts ./playbook.yml
```

There are two ways to revoke existing keys:

Here is kind of scary approach,

```
# first, revoke every keys except master's key.
$ ansible-playbook -i ./hosts ./revoke_all.yml

# then, setup keys according to playbook
$ ansible-playbook -i ./hosts ./playbook.yml
```

Here is safer approach: use `state=absent` parameter to revoke each key for each server as below,

```
# in playbook.yml
---
- hosts: some_server
  ...
  tasks:
    ...
    - authorized_key: user=ubuntu key="{{ item }}" state=absent
      with_file:
        - public_keys/bad_guy
```

### Reference

- http://docs.ansible.com/ansible/authorized_key_module.html
- https://dan.langille.org/2014/07/20/ssh-key-management/
